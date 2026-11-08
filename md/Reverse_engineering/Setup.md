# Environment setup

The recommended setup uses [App Sandbox](https://github.com/jamesstringer90/appsandbox)
to keep analysis and capture work off the host. App Sandbox runs full desktop
VMs and supports GPU paravirtualisation, SSH, snapshots, branches, and a
headless API. On Windows it uses Virtual Machine Platform through the Windows
HCS/HCN APIs; Hyper-V Manager and a Windows Pro licence are not required.

## 1. Layout

Use either of these layouts:

- Preferred: a `dbtb-analysis` guest for Ghidra and the MCP client, and a
  `dbtb-capture` guest for the game, mitmproxy, and Wireshark.
- Minimum: one Windows 11 guest containing all tools. Keep Ghidra projects
  and raw captures in separate directories.

Two guests prevent target execution from changing the analysis environment and
make it easier to restore the capture guest after installing a proxy CA. The
capture guest should be Windows 11 because the analysed client is a Windows
application. The game normally needs GPU-PV; Ghidra does not.

Do not install the target on the host, sign in with a personal account, or copy
private evidence into the documentation repository. A VM reduces exposure, but
you still need host updates, trusted installation media, and careful handling
of shared clipboard/files.

## 2. Create the AppSandbox guests

### Host requirements

The upstream project currently documents Windows 11 on an x64 processor for a
Windows host. Check its README and release notes for changes before installing.

1. Enable **Virtual Machine Platform** in Windows Features and reboot if asked.
2. Download App Sandbox from its
   [signed releases](https://github.com/jamesstringer90/appsandbox/releases).
3. Obtain a Windows 11 ISO from Microsoft's official source.
4. In App Sandbox, create a Windows 11 guest and select the ISO. Allocate enough
   disk space for the game, several full-match captures, and snapshots. Full
   match traffic can be large; monitor free space during long captures.
5. Enable GPU-PV for the capture guest. The AppSandbox README links a
   `gpu-test.exe` utility that checks several rendering APIs inside the guest.
6. Install guest updates and the authorised game/test client. Use a dedicated
   test account with no unrelated data.
7. Shut down the guest and take a stopped-VM snapshot named, for example,
   `clean-tools-<date>`. Create a branch or restore this snapshot for each new
   investigation.

AppSandbox's documented default credentials are `user` / `test123`. Change the
password before connecting a guest to any untrusted network or sharing its
image. Do not reuse a personal password.

Snapshots contain account data, keys, proxy CAs, and executable material. Keep
them private. AppSandbox snapshots and branches are host-specific features;
consult the [headless API examples](https://github.com/jamesstringer90/appsandbox/tree/main/tools/headless-api)
if VM creation or branching needs automation.

## 3. Guest directories

Create these directories inside each guest as applicable:

```text
C:\DBTB\
  target\          # authorised target copy and build hash
  ghidra\          # private Ghidra projects
  evidence\        # one private directory per capture run
  tools\           # locally reviewed scripts and decoders
  exports\         # sanitised material approved for documentation
```

Do not place `evidence`, `ghidra`, VM images, or proxy configuration containing
a private CA inside `DBTB-API`.

## 4. Install Ghidra and GhidraMCP

These instructions use [LaurieWired/GhidraMCP](https://github.com/LaurieWired/GhidraMCP).
Its current installation guide specifies Ghidra 12.1.4 and Java 21. Use a
GhidraMCP extension built for the installed Ghidra version; do not mix an
extension and application from incompatible releases.

1. Install 64-bit Java 21 and the matching Ghidra release in the analysis
   guest.
2. Install [uv](https://docs.astral.sh/uv/), or Python 3.10+ and the
   GhidraMCP Python package as described upstream.
3. Download the matching GhidraMCP release archive.
4. In Ghidra, choose **File → Install Extensions**, click **+**, and select the
   `..._GhidraMCP.zip` release archive. Restart Ghidra.
5. Open **File → Configure → Developer** and enable `GhidraMCPPlugin`.
6. Leave **Edit → Tool Options → GhidraMCP HTTP Server** on
   `127.0.0.1:8080` unless there is a specific reason to change it. Keep the
   Ghidra HTTP plugin and MCP bridge off the LAN and internet.
7. Create a Ghidra project under `C:\DBTB\ghidra`, import the authorised
   executable, and run auto-analysis. Save the project after analysis.

The simplest MCP topology runs Ghidra, the MCP bridge, and the MCP client in
the same analysis guest. Then `127.0.0.1` has an unambiguous meaning. If the
client runs elsewhere, use an authenticated SSH tunnel supported by AppSandbox
and keep the unauthenticated service bound to loopback.

### MCP client configuration

For an MCP client that launches stdio servers with JSON configuration, the
upstream equivalent is:

```json
{
  "mcpServers": {
    "ghidra": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/LaurieWired/GhidraMCP",
        "ghidra-mcp",
        "--ghidra-server",
        "http://127.0.0.1:8080/"
      ]
    }
  }
}
```

Use the client's own configuration file and server-registration procedure.
Clients do not all read this filename or schema. For VS Code, the upstream
README provides the corresponding `.vscode/mcp.json` example.

For a client that connects to a URL, start the bridge explicitly:

```powershell
uvx --from git+https://github.com/LaurieWired/GhidraMCP ghidra-mcp `
  --transport streamable-http --mcp-host 127.0.0.1 --mcp-port 8081 `
  --ghidra-server http://127.0.0.1:8080/
```

Then configure `http://127.0.0.1:8081/mcp` in that client. Keep the bridge
running while it is in use. With the stdio configuration, the client normally
starts the bridge itself.

### Verify the connection

1. Open the target program in Ghidra and keep it active.
2. Start or restart the MCP client after changing its configuration.
3. Ask it to list the available Ghidra tools and identify the active program.
4. Request a read-only operation, such as finding the string `sys/get_env_v3`
   and listing its references.
5. Confirm that returned addresses belong to the intended executable build.
   Make no bulk renames during this smoke test.

If this fails, check in order: the extension matches Ghidra, the plugin is
enabled, the program is open, `uvx` is on the client's PATH, port 8080 is free,
and the client is connecting to the guest rather than to its own loopback.

## 5. Install Wireshark and mitmproxy

Install these in the capture guest:

- [Wireshark](https://www.wireshark.org/download.html), including the capture
  driver and the TShark/Dumpcap/Editcap command-line tools;
- [mitmproxy](https://docs.mitmproxy.org/stable/overview/installation/), which
  supplies the keylog mechanism used by the project's recovered captures.

Configure the target's supported proxy setting to use mitmproxy and install the
mitmproxy CA only in the disposable capture guest if interception is required.
Confirm that the intended application traffic appears in mitmproxy. A
system-wide proxy setting does not guarantee that every Steam or game component
uses it. If the client rejects the proxy, record that limitation. Do not weaken
the host's security or assume that capture succeeded.

The project's historical workflow used `MITMPROXY_SSLKEYLOGFILE`. The upstream
[mitmproxy TLS guide](https://docs.mitmproxy.org/stable/howto/wireshark-tls/)
documents both that variable and `SSLKEYLOGFILE`; the mitmproxy-specific name
avoids enabling key logging in unrelated applications.

`SSLKEYLOGFILE` can also be set directly for an application whose TLS library
supports it. Support depends on the library, so check for written keys rather
than relying on the environment variable alone. A server private key generally
cannot decrypt modern TLS 1.3 or ephemeral-DH sessions.

## 6. Pre-run checklist

- [ ] Host and guests are updated; target and scope are authorised.
- [ ] A clean stopped-VM snapshot exists.
- [ ] The test account contains no unrelated personal data.
- [ ] The target executable's SHA-256 and build/version are recorded.
- [ ] Ghidra auto-analysis has completed and the MCP smoke test passed.
- [ ] mitmproxy sees the intended client flows and writes a non-empty keylog.
- [ ] Wireshark lists the guest's active capture interface.
- [ ] Evidence is stored outside the documentation repository.
- [ ] Sufficient disk space is available for an unfiltered full-match capture.
- [ ] The proxy CA and test credentials will be removed by restoring the clean
      snapshot when the investigation ends.

## References

- [App Sandbox](https://github.com/jamesstringer90/appsandbox)
- [GhidraMCP installation and configuration](https://github.com/LaurieWired/GhidraMCP)
- [Wireshark TLS decryption](https://wiki.wireshark.org/TLS)
- [mitmproxy Wireshark/TLS key logging](https://docs.mitmproxy.org/stable/howto/wireshark-tls/)

---

[Back to reverse-engineering guide](README.md) · [Back to document map](../README.md)
