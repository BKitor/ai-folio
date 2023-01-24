---
layout: page
title: bk_scripts
description: Collection of tools to make my life easier
img:
importance: 1
category: Fun
github: https://github.com/bkitor/bk_scripts
---

A small number of scripts to automate tasks like remote file transfers, CSV processing, and document generation.

- `bk_clean_(omb/hvd)` is a python script for parsing program outputs and outputing .csv format. Can take Horovod / OSU-Microbenchmark files as inputs. 
- `mdpdfdt` is a shell script that wraps `pandoc`. It takes a markdonwn file as input and exports it to the destop as a pdf
- `chk` is for checking SLURM job status on ComputeCanada clusters. Calling `chk <cluster_name>` will make a `squeue` call over ssh and compare it to the previous call to chk.
- `fetch` is for transferring files on ComputeCanada clusters to the local machine. On the login node, populate ~/fetch.txt with the full path of files to be transferred (`echo $PWD/<file> > ~/fetch.txt`), the call `fetch <clustername>` locally, and the files in fetch.txt will be added to a local `./fetch` folder. 
