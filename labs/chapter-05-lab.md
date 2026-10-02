# Chapter 5 Lab: Kubernetes and Run:ai for AI Infrastructure

**Estimated time:** 38 minutes

## Exercise

Deploy GPU Operator on a K8s cluster, create a namespace 'research' with quota of 4 GPUs, install Run:ai CLI, create a Run:ai project 'nlp-team' with quota 8 GPUs and over-quota 12, submit a fractional GPU job (0.5 GPU), and verify scheduling with 'runai list jobs'

## Key takeaway

Run:ai fractional GPUs use time-slicing (software), MIG uses hardware partitioning—the exam tests when to use which, and the kubectl/runai commands to configure both.
