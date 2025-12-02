Adversarial Data Analytics in LLM-Augmented Pipelines
Measuring Structural Inference Leakage in RAG-Enabled Enterprise Systems
Conceptual Framework · Research Proposal · Enterprise Security

By Nuno Lopes

The Problem
When enterprises deploy LLMs with Retrieval-Augmented Generation (RAG), they face a new class of information leakage: Structural Inference Leakage Reconstruction (SILR). Even with redacted documents, attackers can exploit:

Document templates and structure

Retrieval of similar content from the RAG corpus

Language priors of powerful LLMs

...to partially reconstruct sensitive information.

This isn't about models "blurting out secrets"—it's about document design and pipeline architecture creating predictable, measurable vulnerabilities.

What This Project Is
A conceptual framework proposing that information leakage in LLM-augmented systems should be treated as a measurable analytics property of pipelines, not just a model training issue.

Key Concepts
SILR (Structural Inference Leakage Reconstruction): The core vulnerability where document structure + retrieval + LLM inference enables reconstruction

SLI (Structural Leakage Index): A proposed metric for scoring how vulnerable a document/pipeline is to reconstruction

NASC (Noise-to-Adversary Separation Coefficient): Monitoring query/response patterns to detect unusual extraction attempts

Practical Protocols: Methods for measuring and mitigating reconstruction risks in enterprise settings

Current Status
IMPORTANT: This is a conceptual/framework proposal

All metrics, formulas, and thresholds presented here are hypothetical and require empirical validation. This repository documents:

Proposed vulnerability models

Theoretical metrics and measurement approaches

Defense strategies for discussion

We welcome:

Collaborators for experimental validation

Real-world case studies from enterprise deployments

Feedback from security, data, and risk teams

Repository Structure

--> Pending Update



