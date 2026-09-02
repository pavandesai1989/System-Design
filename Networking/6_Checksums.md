
# Checksums and Data Integrity

A **checksum** is a small value calculated from data to detect whether the data was corrupted or changed.

```text
Data
  │
  ▼
Compute Checksum
  │
  ▼
Store / Transfer
  │
  ▼
Read / Receive
  │
  ▼
Compute Checksum Again
  │
  ├── Match     → Data is intact
  └── Mismatch  → Data is corrupted
````

## Why Checksums Are Needed

Data can be corrupted during:

* Network transmission
* Disk/storage operations
* Database writes
* Replication
* Backup/restore
* File/object transfers

A checksum **detects** corruption but does not repair it.

Recovery requires:

* Retransmission
* Another replica
* Backup restore
* Data reconstruction

---

## Common Integrity Mechanisms

| Mechanism              | Purpose                                      |
| ---------------------- | -------------------------------------------- |
| **Parity**             | Very basic error detection                   |
| **CRC**                | Fast accidental corruption detection         |
| **Cryptographic Hash** | Strong content fingerprint                   |
| **HMAC**               | Integrity + authenticity using shared secret |
| **Digital Signature**  | Integrity + proof of signer                  |

### CRC

Used for fast detection of accidental corruption.

Examples:

* Network frames
* Storage blocks

> CRC is **not secure against attackers**.

### Cryptographic Hash

Examples: **SHA-256, SHA-512, BLAKE3**

Used for:

* File verification
* Content identification
* Container/package verification
* Deduplication
* Merkle trees

### HMAC / Digital Signature

Used when we need to verify **who produced the data**, not just whether it changed.

```text
HMAC
→ Shared secret
→ Integrity + authenticity

Digital Signature
→ Private key signs
→ Public key verifies
```

---

## Where Checksums Are Used

```text
Checksums / Hashes
        │
 ┌──────┼────────┬──────────┐
 ▼      ▼        ▼          ▼
Network Storage Database Replication
        │
        └── Backups / Object Storage
```

Common uses:

* Network packet/frame integrity
* Database pages
* Disk/storage blocks
* Backup verification
* Object uploads
* Replica comparison and repair

---

## Checksums in Distributed Systems

Checksums/hashes can help compare replicas without immediately comparing every byte.

```text
Replica A ──→ Hash ──┐
                     ├── Compare
Replica B ──→ Hash ──┘
```

If hashes differ, the system can identify and repair the affected data using another replica or backup.

---

## Checksums vs Encryption

| Mechanism         | Purpose                         |
| ----------------- | ------------------------------- |
| Checksum / CRC    | Detect corruption               |
| Hash              | Strong content fingerprint      |
| HMAC              | Integrity + authenticity        |
| Digital Signature | Integrity + signer verification |
| Encryption        | Hide/protect data               |

---

## Mental Model

```text
Checksum
→ Detect corruption

CRC
→ Fast accidental corruption detection

Hash
→ Strong content fingerprint

HMAC / Signature
→ Verify authenticity

Checksum
≠ Repair
```

> **Key takeaway:** Checksums help systems detect corrupted data; replicas, retries, and backups are responsible for recovering it.

```
```
