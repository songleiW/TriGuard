# TriGuard

TriGuard is a system for **privacy-preserving and integrity-aware triangle counting** over **outsourced streaming directed graphs**. It continuously reports the numbers of **cycle triangles** and **flow triangles** within a **sliding window**, while hiding the underlying graph topology from cloud servers and supporting result verification under a **malicious (active) adversary** model.

This repository contains the reference implementation and scripts to reproduce the main experiments in our ACM CCS submission:

> **TriGuard: Enabling Maliciously Secure Triangle Counting for Streaming Graphs** (CCS 2026 submission)

---

## Highlights

- **Streaming directed graphs + sliding window**: maintain triangle counts per time point over the most recent `W` edges/timestamps.
- **Supports two directed triangle types**: **cycle triangles** and **flow triangles**.
- **Three-server distributed trust**: computation is jointly performed by three non-colluding servers.
- **Privacy-preserving**: servers operate on secret-shared representations; the graph topology and intermediate states are protected.
- **Malicious security + verifiability**: detects deviations by a malicious server and allows the graph owner to abort upon inconsistency.

---

## System Model

TriGuard follows a standard **3-party honest-majority** setting:

- **Graph Owner (client)**: holds the plaintext stream, encodes/secret-shares updates, and recovers outputs.
- **Servers S1/S2/S3**: non-colluding computation parties that store/process the secret-shared stream and produce secret-shared counts.

At most **one** server may be malicious.

---

## Repository Layout (suggested)

> Adjust to your actual codebase.
