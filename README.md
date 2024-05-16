# Bash: sponge-based cryptographic algorithms

![](figs/bash-logo-small.png)

[![build](https://github.com/bcrypto/bash/actions/workflows/build.yaml/badge.svg)](https://github.com/bcrypto/bash/actions/workflows/build.yaml)

## What is Bash?

Bash is a series of cryptographic algorithms that follow the [Sponge-Based 
Crypto](https://keccak.team/files/CSF-0.1.pdf) paradigm and 
are based on the sponge function [Bash-f](https://eprint.iacr.org/2016/587.pdf). 
This function is a permutation over binary strings of length 1536. 

Bash algorithms are standardized in Belarus.

## BashV1

The first version of the Bash standard, STB 34.101.77, was released in 
2016. This version defines: 
- `bash-f` — the algorithm of Bash-f;
- `bash-hash` — hashing.

In `bash-hash`, sponge-based hashing in the overwrite mode is implemented.
Hash lengths run from 4 to 64 bytes in steps of 4. The hash rate (as well as
security guarantees) depends on the hash length.

## BashV2

The second version of STB 34.101.77 is going to be standardized in 2021.

BashV2 additionally defines:
- a cryptographic automaton (machine) based on `bash-f`;
- programmable algorithms based on Bash automata;
- `bash-prg-hash` — programmable hashing;
- `bash-prg-ae` — programmable authenticated encryption.

The Bash automaton is a finite state machine that implements 7 commands:
- `start` (initialize the machine using a customization string and/or nonce and/or key);
- `restart` (reinitialize);
- `absorb` (input public data);
- `squeeze` (output a hash, authentication tag or pseudorandom numbers);
- `encrypt` (process a plaintext);
- `decrypt` (process a ciphertest);
- `ratchet` (transform machine's state in an irreversible manner).

The automaton is parameterized by the security level `l` (128, 192 or 256)
and the capacity level `d` (1 or 2). The latter parameter tunes the security 
guarantees in `bash-prg-hash` and determines the key quotas in `bash-prg-ae`.

A programmable algorithm is a sequence of commands of one or more automata.
Example with 3 automata: 

    α.start[256, 2](⊥, K)
    α.absorb(I)
    α.ratchet(⊥)
    K1 ← α.squeeze(128)
    β.start[128, 1](A1, K1)
    γ ← β
    γ.restart(A2, ⊥)
    Y1 ← β.encrypt(X)
    Y2 ← γ.encrypt(X)
    ...

Here `⊥` is the empty string, `K` and `K1` are keys, 
`A1` and `A2` are *announcements*, that is, arbitrary 
mixtures of customization strings and nonces.

The `bash-prg-hash` algorithm implements the following program

    α.start[l, d](A, ⊥)
    α.absorb(X)
    Y ← α.squeeze(n)
    return Y

It provides more flexible functionality than `bash-hash`: 
additionally processes an announcement `A`, outputs a hash `Y` 
of arbitrary given length `n`.

The `bash-prg-ae` algorithm impements the program

    α.start[l, d](A, K)
    while (X, I) is provided:
      α.absorb(I)
      Y ← α.encrypt(X)
      T ← α.squeeze(l)
      yield (Y, T)

Here `X` is a plaintext, `I` is associated data,
`Y` is a ciphertext, and `T` is an authentication tag.

## What is this repo?

In this repo, we are discussing BashV2.

The latest releases of BashV2 can be found at 
[Releases](https://github.com/bcrypto/bash/releases).

Comments and proposals are processed at 
[Issues](https://github.com/bcrypto/bash/issues). 

