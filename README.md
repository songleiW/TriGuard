<div align="center">

<!-- (Optional) logo -->
<!-- <img src="./fig/logo.png" width=160> -->

<h1>TriGuard<br/>Maliciously Secure Triangle Counting for Streaming Graphs</h1>

<!-- Badges (edit as needed) -->
[![License](https://img.shields.io/badge/License-MIT-green.svg)](#license)
<!-- [![Paper](https://img.shields.io/badge/Paper-CCS%202026-blue.svg)](#citation) -->

</div>

This repository contains a prototype implementation of the protocols proposed in:

> **TriGuard: Enabling Maliciously Secure Triangle Counting for Streaming Graphs** (CCS 2026 submission)

TriGuard is the first framework that supports **private, verifiable, and lossless** triangle counting on **streaming directed graphs**, securely handling **both cycle triangles and flow triangles** under a **malicious adversary** model. It integrates replicated secret sharing with information-theoretic MACs to ensure computation integrity in the secret-sharing domain.  

---

## Table of Contents
- [0 Necessary Backgrounds](#0-necessary-backgrounds)
- [1 Introduction](#1-introduction)
- [2 Quick Set Up](#2-quick-set-up)
- [3 Full Evaluation](#3-full-evaluation)
- [4 Notes / Caveat](#4-notes--caveat)
- [Citation](#citation)
- [License](#license)

---

## 0 Necessary Backgrounds

### 0.1 Triangle Counting on Streaming Directed Graphs
TriGuard considers a **directed and unweighted streaming graph** as a growing edge sequence, where each edge arrives with a timestamp.  
Given a time window size **W**, TriGuard maintains the number of **cycle triangles** and **flow triangles** in the current **snapshot graph** (i.e., edges with timestamps in the interval **(t−W, t]**) at each time point `t`.

### 0.2 Threat Model & Goal
TriGuard targets an **outsourced** setting with **three non-colluding servers** and an **active (malicious) adversary**, where at most one server may deviate arbitrarily. The goal is to:
- keep the **graph structure** private from the servers,
- return **correct triangle counts** (cycle + flow),
- and **detect tampering** via verifiable integrity mechanisms.

---

## 1 Introduction

### 1.1 Core Idea (High-Level)
TriGuard reduces secure triangle counting to:
1) **secure neighbor aggregation** over streaming snapshots, and  
2) **secret-shared dot products** between (one-hot encoded) neighbor vectors.  

To achieve efficiency, TriGuard uses a **single-round oblivious message passing** protocol with **bidirectional edge propagation** for neighbor aggregation, and then applies lightweight secure arithmetic to compute triangle counts. To achieve malicious security, TriGuard integrates **information-theoretic MACs** with replicated secret sharing and addresses challenges specific to **unbounded streams**, including MAC creation for streaming values and correctness of MAC verification under secure multiplications.

### 1.2 Repository Layout (suggested)
> Please update paths/names to match your codebase.

```bash
.
├── src/                 # TriGuard protocols + streaming triangle counting
├── tests/               # unit tests
├── apps/                # binaries: owner/client + server(s)
├── eval/                # evaluation scripts/logs/plots (paper figures)
│   ├── scripts/
│   ├── log/
│   └── plot/
├── third_party/         # dependencies (optional)
├── CMakeLists.txt
├── build.py             # build helper (optional)
└── README.md
