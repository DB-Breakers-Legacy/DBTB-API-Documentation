# Analysis workflow

Start with a specific claim, gather static and dynamic evidence for it, and
publish the result with its provenance. Avoid broad, unrecorded exploration
followed by conclusions whose source cannot be traced.

## 1. Define the investigation

Write a short private run note before starting:

```text
Question: Which serializer builds battle/get_connection_server_info, and
          does the observed response match its field-read order?
Target build/version: <recorded value>
Target SHA-256: <hash>
Ghidra program: <project/program name>
Capture run ID: <directory name>
Scenario: <exact UI actions>
Expected observation: <route, command, or state transition>
```

Use one build per Ghidra program and one concise scenario per capture. Addresses
such as `FUN_1407ce250` are build-specific. Record the build hash whenever an
address, vftable, or `.rdata` offset enters documentation.

Useful initial questions already present in this vault include:

- request/response class and route mapping in [Executable.md](Executable.md);
- request common fields and response metadata in
  [MessagePack.md](../Structure/MessagePack.md);
- endpoint key handouts in
  [get_diarkis_matching_server_info.md](../API/battle/get_diarkis_matching_server_info.md);
- unresolved items in [Version_Skew.md](../Structure/Version_Skew.md) and
  [Diarkis.md](../Structure/Diarkis.md#remaining-unknowns-each-with-a-named-source).

## 2. Establish the executable baseline

1. Restore the analysis guest to its clean snapshot or open a fresh branch.
2. Copy the authorised target into the guest and calculate its SHA-256 in
   PowerShell:

   ```powershell
   Get-FileHash -Algorithm SHA256 C:\DBTB\target\<executable>
   ```

3. Import it into a build-specific Ghidra project. Run auto-analysis and save.
4. Record the Ghidra version, importer/language, image base, analysis options
   that differ from defaults, and target hash in the private run note.
5. Keep a backup of the original Ghidra project before scripted or bulk MCP
   changes. MCP edits normally change the analysis database rather than the
   executable, but they can still destroy careful manual analysis.

Do not copy addresses from another build without rebasing and validating them
against strings, RTTI, or byte patterns in the current program.

## 3. Use Ghidra MCP for static analysis

Start with read-only prompts. Require addresses and evidence in every answer,
and verify the model's suggested function names and field types yourself.

### Discover routes and classes

Example prompts:

```text
In the active program, find the exact string "sys/get_env_v3".
Return its address and every code/data reference. Do not rename anything.
```

```text
Find RTTI or strings for ServerApiRequestGetEnvironmentV3 and
ServerApiResponseGetEnvironmentV3. Identify nearby vftables and list their
functions with addresses. Distinguish confirmed references from guesses.
```

```text
Find immediate constants 0x2ee0 and 0x55f0 and their comparisons or table
entries. Show the containing functions and cross-references. Do not assume the
public Diarkis command flow.
```

For HTTP APIs, connect this chain:

```text
route string / RTTI
       -> request class and vftable
       -> send/serialize function
       -> response class and parser
       -> fields stored at concrete object offsets
```

For Diarkis, start with the observed command constants and follow send and
receive paths independently. Use the command-name table for labels and decrypted
captures for runtime call order.

### Inspect serializers and parsers

```text
Decompile the function at <address>. List every field read or write in order,
including width, byte order if visible, string/array handling, and callees that
affect the wire format. Cite addresses for each conclusion.
```

```text
Compare this request serializer with the captured shape [common object,
endpoint arguments]. Identify the common serializer and the endpoint-specific
serializer separately. State anything the decompilation does not establish.
```

Review the decompilation and relevant disassembly before accepting MCP-proposed
changes. Decompiler output can hide byte order, signedness, array boundaries,
and virtual-call targets. Infer names from behaviour, then use them consistently.

After review, a narrow mutation prompt is acceptable:

```text
Rename only the function at <address> to <reviewed_name> and apply the reviewed
prototype <prototype>. Do not rename callees, change data types elsewhere, or
run bulk analysis.
```

Record renames and type decisions in the run note. Never ask an MCP agent to
rename all functions or "fix" the whole program without a reviewable plan.

### Static-analysis exit criteria

A static result is ready for capture correlation when it has:

- the exact executable build and relevant addresses;
- route/class/function relationships supported by references;
- field operation order and container boundaries where visible;
- unresolved decompiler ambiguity and a capture plan for resolving it;
- capture support for every claim about observed runtime behaviour.

## 4. Capture one scripted scenario

Follow [Capture_and_Verification.md](Capture_and_Verification.md) to start the
per-run TLS keylog and packet capture. Then:

1. Start with the target closed. Begin proxying and packet capture before launch
   so the capture contains the TLS handshakes.
2. Record the guest time immediately before each meaningful UI action.
3. Perform only the planned scenario. Avoid unrelated store, message, friend,
   or account activity.
4. Record visible outcomes and errors, including matchmaking role, queue entry,
   match found, and match end where applicable.
5. Close the target cleanly, stop the capture, then stop the proxy. Preserve the
   original files before filtering or exporting them.

A marker such as `T+00:42 entered queue` is often enough to locate the relevant
packets. Do not insert real credentials or tokens into the run note.

## 5. Correlate the evidence

Work from route to body to function:

1. Filter decrypted HTTP/2 by `:path` and identify request/response frames.
2. Confirm method, authority, build/version fields, and ordering against the
   existing [HTTP structure](../Structure/HTTP.md).
3. Decode MessagePack offline. Preserve the original byte stream and note any
   HTTP/2 reassembly required before decoding.
4. Compare the body with the serializer/parser operation order from Ghidra.
5. Label each field as confirmed, layout confirmed / meaning inferred, or
   unknown. Include capture name, frame number, and function address.
6. Repeat the scenario in a second run before claiming stable semantics for a
   value. Constants in one run may be identifiers, timestamps, or random keys.

Example evidence entry:

| Claim | Status | Static evidence | Dynamic evidence |
|---|---|---|---|
| `battle/get_connection_server_info` has empty endpoint arguments | confirmed | empty-args serializer at reviewed build address | capture ID, request frame |
| response contains host, port, SID, AES key, IV, HMAC key | confirmed by correlation | response parser at reviewed build address | response frame plus matching UDP SYN/HMAC validation |
| two trailing integers mean queue flags | unknown/inferred | storage offsets only | no varying observation; do not publish as fact |

### Diarkis correlation

TLS keylogs decrypt HTTPS, while the custom Diarkis UDP crypto envelope requires
separate credentials:

1. Read the server handout from the decrypted HTTPS response.
2. Extract endpoint, port, SID, AES key, fixed IV, and HMAC key using the order
   documented in [Diarkis key roles](../Structure/Diarkis.md#key-roles).
3. Match the SID to the UDP SYN and cleartext client-to-server SID prefix.
4. Validate HMAC-SHA256 over ciphertext before AES-CBC decoding. A successful
   structured decode without HMAC validation is insufficient.
5. Reassemble fragments before parsing fragmented commands. Account for
   client/server header asymmetry and type-5 retransmissions.
6. Compare command IDs and sub-IDs with Ghidra senders/receivers, but derive the
   actual sequence from decrypted captures.

Do not copy the legacy `RUDPserver.py` per-packet-IV interpretation or the
legacy `unpackmsg.py` SID/IV labels; the existing notes document why both are
wrong.

## 6. Turn findings into tests and documentation

For every decoder or implementation change:

- add or update a minimal golden fixture derived from a verified frame;
- test both directions and every relevant container/fragmentation variant;
- include wrong-key, modified-ciphertext, truncation, and bad-length negative
  cases;
- compare generated bytes with the golden input where the implementation is a
  serializer, or compare parsed values where it is a parser;
- record expected retransmission/duplicate behaviour instead of counting every
  duplicate as a new logical command;
- run existing project tests as well as the new case.

See the detailed matrix in
[Capture_and_Verification.md](Capture_and_Verification.md#5-verification-matrix).

Documentation should identify its source directly:

```text
Confirmed (pcap + Ghidra): <capture ID>, frame <n>; <function> at <address>
in target SHA-256 <short hash>. Meaning remains inferred because <reason>.
```

Update the relevant endpoint or protocol note as well as the run log. Add newly
discovered disagreements to [Version_Skew.md](../Structure/Version_Skew.md) and
move resolved items out of remaining-unknowns lists. Remove or amend comments
that describe superseded behaviour.

## 7. Finish the run

1. Hash and preserve raw artifacts privately; create sanitised exports only in
   `C:\DBTB\exports`.
2. Check that documentation contains no keylog, live session token, real user
   ID, account name, private IP, or unreviewed captured payload.
3. Save the Ghidra project privately and export only the reviewed conclusions.
4. Shut down and restore/branch from the clean AppSandbox snapshot to remove
   proxy trust and capture-account state.
5. Confirm that no evidence files appeared in Git status before committing
   documentation.

---

[Back to reverse-engineering guide](README.md) · [Back to document map](../README.md)
