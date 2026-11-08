# Capture, testing, and verification

Use this guide to collect evidence that another analyst can reproduce. Record
an identified build, an unfiltered packet capture, the matching TLS secrets, a
timestamped scenario, and reviewed decoder output. Keep all raw artifacts
private.

## 1. Start a capture run

Complete [Setup.md](Setup.md) first. Run these commands in the capture guest,
not in the documentation repository.

### Create the run and keylog

In the PowerShell window that will run mitmproxy:

```powershell
$runId = Get-Date -Format "yyyyMMdd-HHmmss"
$runDir = "C:\DBTB\evidence\$runId"
New-Item -ItemType Directory -Path $runDir
$env:MITMPROXY_SSLKEYLOGFILE = "$runDir\tls-keys.txt"
mitmdump --listen-host 127.0.0.1 --listen-port 8080
```

The variable must contain a writable file path, not just a directory, and must
be set before mitmproxy starts. Use a new keylog for each run. Confirm that the
file becomes non-empty after the first intercepted TLS connection.

This example listens on loopback and assumes the target and proxy are in the
same guest. Configure the target using its supported proxy mechanism and verify
that its flows appear in mitmproxy. If the proxy runs elsewhere, use its actual
private guest address and firewall rules; never expose an open proxy.

### Capture packets

In a second PowerShell window, set `$runDir` to the directory just created and
list interfaces:

```powershell
$runDir = "C:\DBTB\evidence\<run-id>"
$dumpcap = "$env:ProgramFiles\Wireshark\dumpcap.exe"
& $dumpcap -D
& $dumpcap -i <interface-number> -w "$runDir\traffic.pcapng"
```

Select the interface carrying the guest's game/proxy traffic. Administrator
rights may be required. Leave the capture filter empty for protocol analysis:
filtering only TCP/443 would discard Diarkis, STUN, and direct peer-to-peer UDP.
Use display filters later instead. Press **Ctrl+C** to stop Dumpcap cleanly.

You can use Wireshark's GUI instead: select the active interface, start capture,
perform the scenario, and stop before closing the target. For long captures,
Dumpcap/TShark support size- or duration-based file rotation. A ring buffer
overwrites old files, so use it only when that loss is intentional; monitor disk
space regardless.

### Run and close the scenario

Launch the target only after the proxy and packet capture are running. Record
actions and guest timestamps in `$runDir\run-notes.txt`. Close the target, stop
the capture, and then stop mitmproxy. Keep the beginning of the capture intact;
later TLS secrets cannot reconstruct handshakes that were never recorded.

Clear the variable from the proxy shell when finished:

```powershell
Remove-Item Env:MITMPROXY_SSLKEYLOGFILE
```

This leaves the keylog and proxy trust in place. Restore the clean guest
snapshot at the end of the investigation.

## 2. Load TLS secrets in Wireshark

1. Open `traffic.pcapng` in Wireshark.
2. Choose **Edit → Preferences → Protocols → TLS**.
3. Set **(Pre)-Master-Secret log filename** to this run's `tls-keys.txt`.
4. Leave TCP stream reassembly enabled. If capture occurred out of order, also
   review Wireshark's **Reassemble out-of-order segments** TCP preference.
5. Apply `tls and (http or http2)` and confirm that decrypted HTTP/2 traffic is
   visible.

For command-line checks:

```powershell
$runDir = "C:\DBTB\evidence\<run-id>"
$tshark = "$env:ProgramFiles\Wireshark\tshark.exe"
& $tshark -r "$runDir\traffic.pcapng" `
  -o "tls.keylog_file:$runDir\tls-keys.txt" `
  -Y "tls.handshake.type == 1" -T fields -e frame.number
& $tshark -r "$runDir\traffic.pcapng" `
  -o "tls.keylog_file:$runDir\tls-keys.txt" `
  -Y "http2.headers.path" -T fields `
  -e frame.number -e http2.headers.path
```

The first command checks for captured ClientHello messages; the second checks
for usable HTTPS decryption and HTTP/2 path decoding. Empty HTTP/2 output does
not prove that the application made no requests. Check proxy visibility, keylog
coverage, handshakes, TCP reassembly, and whether the capture contains only a
pre-filtered subset.

To export a private dissection for searching:

```powershell
& $tshark -r "$runDir\traffic.pcapng" `
  -o "tls.keylog_file:$runDir\tls-keys.txt" `
  -Y "http2 || udp.port == 7100 || udp.port == 7101 || udp.port == 7102" `
  -V > "$runDir\decoded-packets.txt"
```

The text export can contain the same identifiers and payloads as the capture.
Keep it with the private evidence, outside Git.

### Optional embedded secrets

Editcap can embed TLS secrets in a private pcapng copy so Wireshark does not
need a separate preference:

```powershell
& "$env:ProgramFiles\Wireshark\editcap.exe" `
  --inject-secrets "tls,$runDir\tls-keys.txt" `
  "$runDir\traffic.pcapng" "$runDir\traffic-with-secrets.pcapng"
```

The resulting file contains decryption secrets and is exactly as sensitive as
the keylog. Prefer separate per-run files when there is any chance of sharing a
capture. Wireshark 4.2+ can export only session keys referenced by selected
packets, which helps minimise private derivatives.

## 3. Useful display filters

| Purpose | Wireshark display filter |
|---|---|
| TLS with decoded HTTP | `tls and (http or http2)` |
| HTTP/2 request paths | `http2.headers.path` |
| DBTB API paths | `http2.headers.path contains "/api/"` |
| HTTPS responses carrying MessagePack | `http2.header.value contains "messagepack"` |
| Diarkis/STUN service ports | `udp.port == 3478 || udp.port == 7099 || udp.port == 7100 || udp.port == 7101 || udp.port == 7102` |
| RUDP command-envelope magic at payload offset 4 | `udp.payload[4:4] == fe:be:de:ef` |
| Direct P2P magic families | `udp.payload[0:4] == 0b:0e:0e:0d || udp.payload[0:4] == 0d:0e:0a:0f` |

Use **Statistics → Conversations** and **Statistics → UDP Multicast Streams**
where applicable to understand endpoints, then **Follow → HTTP/2 Stream** for a
request/response exchange. UDP stream numbers and frame numbers are local to a
capture; always include the capture ID when citing them.

The envelope filter finds only unfragmented packets whose magic is at the
expected offset. It cannot validate or decrypt Diarkis. Use the project's
offline decoder for fragment reassembly, HMAC verification, AES decoding, and
command parsing.

## 4. Decode and inspect application data

### HTTPS and MessagePack

For a selected request or response:

1. Confirm HTTP/2 method/path/status and associate the DATA frames with their
   stream.
2. Use Wireshark's reassembled data view; do not decode a single DATA frame if
   the body spans multiple frames.
3. Copy the body as a hex stream into an offline MessagePack decoder.
4. Check the common request object and endpoint argument array against
   [MessagePack.md](../Structure/MessagePack.md).
5. Save decoded values privately with capture ID, HTTP/2 stream, frame numbers,
   direction, and body hash.

The request's observed `application/x-www-form-urlencoded` content type does not
change the fact that the captured body is MessagePack. Do not "correct" a test
fixture to a form body. A successful MessagePack decode also leaves field
semantics unproven; correlate those with Ghidra and repeated captures.

The online converter linked from older notes is suitable only for already
sanitised examples. Never paste live account traffic into it.

### Diarkis UDP

TLS keys and Diarkis credentials are different:

```text
TLS keylog
   -> decrypt HTTPS handout
   -> endpoint, port, SID, AES-128 key, fixed IV, HMAC-SHA256 key
   -> authenticate/decrypt the matching Diarkis UDP session offline
```

Validate the SID against the SYN and client-to-server prefix. Validate HMAC over
ciphertext before accepting plaintext. Use the fixed per-session IV and the
zero-padding rules in [Diarkis.md](../Structure/Diarkis.md#crypto-envelope).
Trust authenticated content even where older decoders mishandle a zero declared
length or bytes beyond the declared length.

Do not treat the three historic `udp 7100 data*.pcapng` files as decrypted
golden inputs: the existing key audit says their TLS handshakes and key material
are unavailable. Consult the private keylog-coverage manifest before selecting
old evidence.

## 5. Verification matrix

Start with small deterministic cases, then exercise matchmaking and a complete
match. Adapt expected values to the analysed build. The timing and routing
findings below describe the currently documented captures; future versions may
differ.

| Case | Procedure | Required evidence / pass condition |
|---|---|---|
| MCP smoke test | Find `sys/get_env_v3` read-only | Correct active build; string address and references returned; no bulk edits |
| TLS smoke test | Launch through proxy and stop after first API call | Keylog non-empty; ClientHello captured; HTTP/2 path visible in Wireshark |
| MessagePack round trip | Decode a verified sanitised body, then encode with the implementation under test | Structure and types match; encoded bytes match when deterministic encoding is required |
| Empty endpoint arguments | Capture `sys/get_env_v3` or another known empty-args call | Outer request shape and `[]` arguments agree with the Ghidra serializer |
| Session evolution | Capture successive authenticated calls | Requests/responses occur in documented order; evolving session values are correlated without publishing them |
| Diarkis handout | Capture `battle/get_diarkis_matching_server_info` and following SYN | Host/port match; SID matches SYN; AES/IV/HMAC roles follow the verified handout order |
| Diarkis authentication | Decode both directions with the offline codec | Every selected packet HMAC-validates; wrong keys and modified ciphertext fail |
| Fragmentation | Select a multi-datagram command in each available direction | Reassembled command matches lengths and decodes; incomplete/reordered sets are not silently accepted as complete |
| Heartbeat | Observe an idle established Diarkis session | Command-1 pair and timing match the documented build within reasonable scheduling tolerance |
| Matchmaking sequence | Enter queue and observe match found | Observed custom 12000 sub-ID flow and pushes match [Matchmaking_Flow.md](../API/Calls/Matchmaking_Flow.md); no public-ticket flow is invented |
| Session-host redirect | Capture a handout using port 7100/7102 where redirect occurs | Redirect push, new port, and reused SID/key set match the observed flow |
| Complete match | Record through teardown | Room lifecycle, role assignment, P2P setup, relay/P2P split, and teardown are accounted for; capture remains within disk budget |
| Repeatability | Repeat a scenario in a fresh run | Stable structure survives changed sessions/keys/timestamps; differences are labelled rather than forced into one schema |
| Negative fixtures | Wrong key, flipped ciphertext bit, truncation, bad magic, invalid lengths | Verification fails closed with a useful error; no unauthenticated plaintext reaches command handlers |

For implementation tests, use checked-in sanitised fixtures only if the
implementation repository already has a test suite and its maintainers approve
the fixture data. Otherwise keep private golden tests with the private evidence.
Do not create a new public test corpus from live captures by default.

## 6. Preserve provenance

After the run, hash the evidence privately:

```powershell
Get-ChildItem -File $runDir | Get-FileHash -Algorithm SHA256 |
  Export-Csv -NoTypeInformation "$runDir\hashes.csv"
```

Record at least:

```text
run ID and UTC start/end time
guest snapshot/branch identifier
target path, version, and SHA-256
Ghidra version and program name
proxy, Wireshark, and decoder versions
capture and keylog filenames and SHA-256 values
exact scenario with timestamp markers
interface and any capture filter (normally none)
known gaps, proxy errors, dropped packets, or undecryptable streams
sanitised exports derived from this run
```

Keep original captures unchanged. Apply filters or trimming only to copies and
record the exact operation. A filtered capture with no TLS handshake or missing
UDP context is not interchangeable with the original.

## 7. Troubleshooting

- Only TLS records appear: verify proxy flows, keylog path and contents,
  ClientHello coverage, and TLS preference. Confirm that the keylog belongs to
  this run.
- The keylog is empty: the application/proxy did not write supported secrets,
  or no intercepted connection occurred. Check for key records instead of
  relying on the environment variable.
- HTTP headers appear but bodies do not decode: inspect TCP/HTTP2 reassembly,
  missing frames, and whether the body was copied from the complete reassembled
  data rather than one DATA frame.
- UDP stays encrypted after importing TLS keys: recover the Diarkis credentials
  from the decrypted HTTPS handout and use the custom codec.
- HMAC fails: check session pairing and key-role order first. Changing the AES
  IV cannot repair an HMAC computed over ciphertext.
- Only some UDP commands decode: inspect fragmentation, retransmission,
  direction-specific header sizes, zero/understated plaintext lengths, and
  keylog coverage before changing the cryptographic assumptions.
- A finding differs from an older note: check executable and capture versions,
  then follow the evidence hierarchy in
  [Version_Skew.md](../Structure/Version_Skew.md). Preserve the disagreement and
  its resolution.
- The game cannot connect through the proxy: record the failing stage and proxy
  logs. Restore the clean snapshot after testing; do not install trust
  exceptions on the host.

## 8. Publication gate

Before copying anything into `C:\DBTB\exports` or this vault:

- [ ] Every claim has a build/address or capture/frame citation and a confidence
      label.
- [ ] Examples contain placeholders instead of real user/session/key values.
- [ ] Raw pcaps, keylogs, secret-bearing pcapng files, full proxy flows, and
      private transcripts are absent.
- [ ] Decoder output has HMAC/structural validation, not just plausible text.
- [ ] A second run or independent static source supports generalised claims.
- [ ] Negative tests fail safely.
- [ ] Links and existing endpoint/protocol documents have been updated.
- [ ] Git status shows only intended documentation changes.

## References

- [Wireshark TLS decryption](https://wiki.wireshark.org/TLS)
- [TShark manual](https://www.wireshark.org/docs/man-pages/tshark.html)
- [Dumpcap manual](https://www.wireshark.org/docs/man-pages/dumpcap.html)
- [mitmproxy TLS key logging](https://docs.mitmproxy.org/stable/howto/wireshark-tls/)

---

[Back to reverse-engineering guide](README.md) · [Back to document map](../README.md)
