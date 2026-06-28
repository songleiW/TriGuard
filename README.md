<div align="center">
<!-- (Optional) logo -->
<!-- <img src="./fig/logo.png" width=160> -->
<h1>TriGuard<br/>Enabling Oblivious Triangle Counting for Streaming Graphs</h1>
<!-- Badges (edit as needed) -->
<!-- [![Paper](https://img.shields.io/badge/Paper-VLDB%202026-blue.svg)](#citation) -->
</div>

This repository contains a prototype implementation of the protocols proposed in:

TriGuard: Enabling Oblivious Triangle Counting for Streaming Graphs

TriGuard is a framework that supports private and lossless triangle counting on streaming directed graphs, securely handling both cycle triangles and flow triangles.

⸻

Table of Contents

* 0 Necessary Background
* 1 Introduction
* 2 Codebase Organization
* 3 Quick Setup
* 4 Full Evaluation
* 5 Notes / Caveats
* Citation
* License

⸻

0 Necessary Background

0.1 Triangle Counting on Streaming Directed Graphs

TriGuard considers a directed and unweighted streaming graph as a growing edge sequence, where each edge arrives with a timestamp.

Given a time window size W, TriGuard maintains the number of cycle triangles and flow triangles in the current snapshot graph, i.e., the graph formed by edges with timestamps in the interval (t−W, t], at each time point t.

0.2 Threat Model and Goal

TriGuard targets an outsourced setting with three non-colluding servers. The goal is to:

* keep the graph structure private from the servers, and
* return triangle counts for both cycle triangles and flow triangles.

⸻

1 Introduction

1.1 Core Idea

TriGuard reduces secure triangle counting to:

1. secure neighbor aggregation over streaming snapshots, and
2. secret-shared dot products between one-hot encoded neighbor vectors.

To achieve efficiency, TriGuard uses a single-round oblivious message passing protocol with bidirectional edge propagation for neighbor aggregation, and then applies lightweight secure arithmetic to compute triangle counts.

⸻

2 Codebase Organization

The organization of this codebase and basic information on each folder/file are shown below:

└── 📁aby3
    └── 📁aby3
    └── 📁aby3_tests
    └── 📁aby3-DB
        └── CMakeLists.txt
        └── OblvSwitchNet.cpp # Oblivious Extended Permutation (OEP) implementation
        └── OblvSwitchNet.h
        └── OblvPermutation.cpp # Oblivious Permutation (OP) implementation
        └── OblvPermutation.h
    └── 📁aby3-DB_tests
        └── CMakeLists.txt
        └── PermutaitonTests.cpp # Unit tests for OEP and OP
        └── PermutaitonTests.h
    └── 📁aby3-Graph
        └── CMakeLists.txt
        └── cognn_cc.cpp # 3PC-based CoGNN implementation
        └── cognn_cc.h
        └── graphsc.cpp # 3PC-based GraphSC / Graphiti implementation
        └── graphsc.h
        └── OEP.cpp # OEP wrapper
        └── OEP.h
        └── OGA.cpp # OGA implementation
        └── OGA.h
        └── operators.cpp # Basic circuits
        └── operators.h
        └── ours.cpp # TriGuard implementation
        └── ours.h
        └── shuffle.cpp # 3PC-based secret-shared shuffle
        └── shuffle.h
        └── sort.cpp # 3PC-based secure sort
        └── sort.h
        └── utils.cpp
        └── utils.h
    └── 📁aby3-Graph_tests # Unit tests for graph-related protocols
        └── CMakeLists.txt
        └── graph_tests.cpp
        └── graph_tests.h
        └── tests.cpp
        └── tests.h
    └── 📁eval # Evaluation scripts
        └── 📁scripts # Scripts for setting network namespaces
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
        └── main.cpp # Entry point of the evaluation executable
        └── tmp_run_cluster.py # Script for running evaluations
    └── 📁frontend
    └── 📁thirdparty # Third-party dependencies
    └── .gitignore
    └── build.py # Build script
    └── CMakeLists.txt
    └── LICENSE
    └── README.md

⸻

3 Quick Setup

We provide a build-from-source Docker image for convenience.

You may also set up TriGuard according to the original aby3 library’s setup instructions, although this can be non-trivial due to the dependency setup process.

3.1 Environmental Requirements

We summarize the required hardware resources and software conditions for running the Docker image.

Hardware Resources

* An x86_64 Linux server with at least 64GB RAM and 256GB spare disk space
    * We tested on Intel(R) Xeon(R) Gold 6348 CPU @ 2.60GHz with 512GB RAM

Software Resources

* Operating System
    * We tested on Ubuntu 20.04 with the APT package manager
* Docker
    * We tested on Docker version 27.4.0

3.2 Step-by-Step Instructions

Please do not hesitate to reach out to us if you encounter any problems during this process. Kindly attach the error information when reporting issues.

Pull the Docker image. This may take around 10 minutes depending on your network condition.

sudo docker pull <your-dockerhub-namespace>/triguard:build-from-source

Start the container and build the artifacts from source. This may take around 5 minutes.

sudo docker run -it --rm --privileged --security-opt apparmor=unconfined \
  <your-dockerhub-namespace>/triguard:build-from-source /bin/bash
python build.py --setup
python build.py

Alternatively, you may build the project with CMake.

mkdir -p build && cd build
cmake ..
make -j

Run a small sanity test.

cd eval
python tmp_run_cluster.py --sanity --smallest

Check the available evaluation options.

python tmp_run_cluster.py -h

⸻

4 Full Evaluation

The evaluation scripts are located under the eval directory.

cd eval
python tmp_run_cluster.py -h

Please refer to the command-line help message for available options. The generated logs are stored under:

eval/log

The plotting scripts are located under:

eval/plot

⸻

5 Notes / Caveats

* This repository contains a prototype implementation intended for research evaluation.
* The current implementation focuses on reproducing the main experimental results of TriGuard.
* The Docker image is recommended for a smoother setup experience.
* Some evaluation scripts may require sufficient memory and disk space, depending on the selected dataset and parameter setting.



License

This project is licensed under the MIT License. See LICENSE for details.
