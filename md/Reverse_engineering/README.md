# Reverse-engineering workflow

Use this workflow to analyse the game executable, capture its network traffic,
and verify the resulting protocol documentation. Read it alongside the recovered
findings in [Executable.md](Executable.md).

Only analyse software and capture traffic that you are authorised to inspect.
Use a test account and an isolated environment. Do not replay captured requests
to production services as a substitute for offline tests.

## Guides

1. [Environment setup](Setup.md): AppSandbox, Ghidra, GhidraMCP, Wireshark,
   mitmproxy, snapshots, and directory layout.
2. [Analysis workflow](Workflow.md): static analysis with Ghidra MCP,
   correlation with captures, and documentation rules.
3. [Capture, testing, and verification](Capture_and_Verification.md): TLS
   keylogs, Wireshark/TShark commands, Diarkis checks, test cases, and evidence
   packaging.

## Workflow at a glance

```text
Authorised target and test account
              |
              v
   Hash executable; record build
              |
              v
  Ghidra: strings, RTTI, serializers
              |
              v
  AppSandbox: run one scripted scenario
  Wireshark + mitmproxy: capture traffic
              |
              v
  Decrypt HTTPS; decode MessagePack
              |
              v
  Recover per-session Diarkis credentials
  from HTTPS; decode UDP offline
              |
              v
  Cross-check static and dynamic evidence
              |
              v
  Golden fixtures + negative tests
              |
              v
  Sanitised, frame-referenced documentation
```

Static and dynamic analysis answer different questions:

- Ghidra shows which classes, routes, field reads/writes, constants, and
  serializers exist in a particular executable build.
- Wireshark records what a particular running build sent, when it sent it, and
  how the server answered.
- Tests check that a decoder or implementation reproduces the evidence and
  rejects malformed input.

When sources disagree, follow the project's existing evidence order:
**decrypted captures > executable RTTI/code > public Diarkis documentation >
exploratory notes**. Record the analysed build and capture version instead of
silently combining different versions. See
[Version skew and source disagreements](../Structure/Version_Skew.md).

## Evidence and privacy

Keep raw evidence outside every documentation checkout. A suitable guest path
is `C:\DBTB\evidence`; use a separate directory for each run.

Treat all of the following as private:

- packet captures and decrypted packet exports;
- TLS keylogs and pcapng files with embedded decryption secrets;
- session tokens, Diarkis keys, account identifiers, and platform identifiers;
- executable copies, Ghidra projects, and VM snapshots containing licensed or
  private material;
- screen recordings, proxy flows, and run notes containing account activity.

Deleting a separate keylog does not make a `.pcapng` file safe to share because
the capture can already contain a Decryption Secrets Block. Do not upload
private MessagePack bytes to an online decoder. Publish only reviewed, minimised
examples with stable placeholders and the frame references needed to audit them
privately.

## First run

1. Complete [Environment setup](Setup.md) and take a clean, stopped-VM
   snapshot.
2. Hash the target executable and create a run directory.
3. Import the executable into Ghidra and let auto-analysis finish.
4. Start GhidraMCP and verify that the MCP client can inspect the active
   program.
5. Start the proxy with a per-run TLS keylog, then start Wireshark before
   launching the target.
6. Perform one small scenario, such as startup through `sys/get_env_v3`.
7. Stop the target and capture cleanly, hash the artifacts, and verify that
   Wireshark shows HTTP/2 paths rather than only TLS records.
8. Follow [Analysis workflow](Workflow.md) and record only claims supported by
   named evidence.

---

[Back to document map](../README.md)
