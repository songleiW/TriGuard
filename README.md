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

## 1 Introduction

The organization of this codebase and basic information on each folder/file are as below:

```bash
└── 📁aby3
    └── 📁aby3
    └── 📁aby3_tests
    └── 📁aby3-DB
        └── CMakeLists.txt
        └── OblvSwitchNet.cpp # The Oblivious Extended Permuation (OEP) Implementation
        └── OblvSwitchNet.h
        └── OblvPermutation.cpp # The Oblivious Permuation (OP) Implementation
        └── OblvPermutation.h
    └── 📁aby3-DB_tests
        └── CMakeLists.txt
        └── PermutaitonTests.cpp  # Unit tests for OEP and OP
        └── PermutaitonTests.h
    └── 📁aby3-Graph
        └── CMakeLists.txt
        └── cognn_cc.cpp # 3PC-based CoGNN Implementation
        └── cognn_cc.h 
        └── graphsc.cpp # 3PC-based GraphSC (Graphiti) Implementation
        └── graphsc.h
        └── OEP.cpp # OEP Wrapper
        └── OEP.h
        └── OGA.cpp # OGA Implementation
        └── OGA.h
        └── operators.cpp # Some basic circuits
        └── operators.h
        └── ours.cpp # RingSG Implementation
        └── ours.h
        └── shuffle.cpp # 3PC-based secret-shared shuffle (for GraphSC)
        └── shuffle.h
        └── sort.cpp # 3PC-based secure sort (for GraphSC)
        └── sort.h
        └── utils.cpp
        └── utils.h
    └── 📁aby3-Graph_tests # Unit tests for graph-related protocols
        └── CMakeLists.txt
        └── graph_tests.cpp
        └── graph_tests.h
        └── tests.cpp
        └── tests.h
    └── 📁eval # Folder of evaluation scripts
        └── 📁scripts # Scripts for setting network namespaces (for simulating the running environment of each party)
        └── 📁log # Logs of each experiment
        └── 📁plot
            └── 📁fig
            └── plot_3pc_cmp.py  
            └── plot_ablation.py  
            └── plot_e2e.py  
            └── plot_num_parties.py  
            └── plot_scales.py  
            └── plot_vertex_degrees.py  
        └── CMakeLists.txt
        └── eval_func.cpp
        └── eval_func.h
        └── main.cpp # Entrance of evaluation executable
        └── tmp_run_cluster.py # Script for running all (or each part of) evaluations
    └── 📁frontend
    └── 📁thirdparty # Third-party dependencies
    └── .gitignore
    └── build.py # The building script
    └── CMakeLists.txt
    └── LICENSE
    └── README.md
```

## 2 Quick Set Up

We provide a build-from-source Docker image for your convenience.
- Nevertheless, you can also set up TriGuard according to the original [aby3](https://github.com/ladnir/aby3) library's setup instructions, which might be non-trivial do to the dependencies setup process.

### 2.1 Environmental Requirements

We summarize the required hardware resources and software conditions for running the Docker image we provide.

**Hardware Resources**
- An x86_64 Linux server (at least 64GB RAM, 256GB spare disk)
    - We tested on Intel(R) Xeon(R) Gold 6348 CPU @ 2.60GHz with 512GB RAM

**Software Resources**
- Operating System
    - We tested on Ubuntu 20.04 (with APT package manager)
- Docker
    - We tested on Docker version 27.4.0

### 2.2 Step-by-Step Instructions

> Please don't hesitate to reach out for us if you met any problems during this process. Please kindly attach the error information. Thanks!

Pull the image (~10min, depending on your network condition):

```bash
sudo docker pull cbackyx/ae:build-from-source-v2
```

Now start the container and build the artifacts from source (~5min):

```bash
sudo docker run -it --rm --privileged --security-opt apparmor=unconfined cbackyx/ringsg-ae:build-from-source-v2 /bin/bash
python build.py --setup
python build.py
