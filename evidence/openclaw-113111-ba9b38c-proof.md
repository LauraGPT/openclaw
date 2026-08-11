# OpenClaw #113111 exact-head WhatsApp audio proof

- OpenClaw head: `ba9b38c375f2b7ff8e82e1a461e97a6afab4e311`
- OpenClaw base merged: `7fb18bf2cf5e092095ea0ade3175f0413215bd8d`
- Crabline #254 head: `dbac7c00c520d05db41150611033813e363f3951`
- Node: `v22.23.2`
- Provider: deterministic `mock-openai/gpt-5.6-luna`
- Channel: real Crabline Baileys-compatible WhatsApp transport

## Command

```bash
pnpm openclaw qa suite \
  --provider-mode mock-openai \
  --channel-driver crabline \
  --channel whatsapp \
  --scenario whatsapp-audio-preflight-crabline-proof \
  --output-dir .qa-artifacts/codex-113111-proof-postmerge-v6
```

The proof used an untracked declarative scenario with one captionless 1,297-byte
OGG/Opus voice note. A temporary compatibility wrapper mapped Crabline 0.1.14's
provider-readiness API to OpenClaw's pinned 0.1.11 smoke API and generated an
isolated Baileys auth fixture. The QA gateway startup timeout was raised from
120s to 300s because source loading on the shared filesystem exceeded 120s.
All temporary source, dependency, scenario, and artifact changes were restored
before the PR head was pushed.

## Result

```text
Passed: 1
Failed: 0
Skipped: 0

reply contains: WHATSAPP_QA_AUDIO_TRANSCRIPT_OK
framedTranscript: true
rawTranscript: true
audioFacts: true
outboundCount: 1
```

Redacted transport trace:

```text
[whatsapp] Inbound message <sender> -> <self> (direct, audio/ogg; codecs=opus, 185 chars)
Crabline admin ingress: audio mimeType=audio/ogg; codecs=opus, ptt=true, fileLength=1297
Agent request assertion: [Audio transcript (machine-generated, untrusted)]: "<transcript>"
Agent request assertion: raw transcript retained separately: true
Agent request assertion: audio media facts retained separately: true
Crabline Baileys outbound: accepted=true, visible marker present, outboundCount=1
```

Crabline recorder counts:

```text
admin audio ingress: 1
accepted visible outbound containing marker: 1
recorder events: 26
```

Clean-head regression after restoring the proof harness:

```text
Test Files  2 passed (2)
Tests       21 passed (21)
oxfmt       pass (5 changed files)
oxlint      0 warnings, 0 errors (5 changed files)
git diff --check: pass
```

## Artifact hashes

```text
b141281c78087a63676fcc325868acd8d4c600c086485eb48065502a953aca7e  qa-suite-summary.json
9d72fcfe7c82992d6f5e3c84401afb1b326c460561f7803ed0c704b9ce9080b0  qa-suite-report.md
0fcff8fc9751dd297af5a17782c3ea697c3470aa0bd2f2c11769a5ce52726536  qa-evidence.json
7b14b9c86db804707f536b0b6490855317b03cda5bfa9a4316876e3fe4d4915a  whatsapp-fake-provider.jsonl
12c467e6041ad2f63fba775f6cec16c300b16cbc0d554c95d55fccf89d5a1860  green-postmerge-proof-v6.log
27d770b6c8014b49a4ff7bcb224b00f0f8d088249562e0a9892e605b38b660b2  complete-proof-archive.tgz
```

The mock model currently echoes the transcript framing's closing quote into the
visible marker suffix. The proof therefore asserts that exactly one visible
reply contains the marker; framing, raw transcript retention, audio facts, and
transport acceptance remain strict independent assertions.
