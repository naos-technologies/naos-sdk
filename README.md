# Prism SDK

**Independent verification of AI agent oversight proofs.**

Prism is the standalone verification layer extracted from [Naos Technologies](https://naos-security.com). It lets any third party — an insurer, an auditor, a customer — verify that an AI agent was actually supervised, without trusting Naos infrastructure.

## How it works

Every agent action produces a signed proof pack:

```json
{
  "alg": "ed25519",
  "signature": "...",
  "public_key_raw": "A4Bdxar9O1XzdtXqHXf7La/L9GKwoWes4CJ8MhXQLpQ=",
  "key_sources": {
    "github": "https://github.com/naos-technologies/prism-sdk/raw/main/keys/assurance.pub",
    "dns_txt": "_prism-pubkey.naos-security.com"
  }
}
```

The public key is published here (GitHub) and in DNS — two sources independent of Naos servers. A verifier checks the signature against the public key fetched from either source. No Naos account, no API key, no trust required.

## Verify a proof

```python
from cryptography.hazmat.primitives.asymmetric.ed25519 import Ed25519PublicKey
import base64

def verify(proof: dict, payload: bytes) -> bool:
    pub = Ed25519PublicKey.from_public_bytes(
        base64.b64decode(proof["public_key_raw"])
    )
    pub.verify(base64.b64decode(proof["signature"]), payload)
    return True  # raises InvalidSignature if tampered
```

Or fetch the public key yourself from DNS:

```bash
dig TXT _prism-pubkey.naos-security.com +short
```

## Public key

`keys/assurance.pub` — the current Ed25519 public key in use.

The key is rotated annually or on compromise. Each rotation gets a new commit on `main` with a signed tag.

## What Prism attests

- **Control heartbeats** — periodic signed records that a human-in-the-loop was active
- **Insurability attestations** — sealed evidence packages for cyber insurance underwriters
- **Agentic proof packs** — per-action audit trail with RFC 3161 timestamps

## Contact

[contact@naos-security.com](mailto:contact@naos-security.com) · [naos-security.com](https://naos-security.com)
