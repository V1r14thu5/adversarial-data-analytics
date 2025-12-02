# Adversarial Data Analytics in LLM-Augmented Pipelines

### Measuring Structural Inference Leakage in RAG-Enabled Enterprise Systems

**Conceptual Framework Proposal**

**Status:** Conceptual / Framework Proposal\
**Validation:** All metrics and thresholds are hypothetical and require
empirical testing\
**Version:** 0.1.0-alpha\
**Date:** November 2024

## Abstract

Large Language Models (LLMs) are increasingly embedded in enterprise
workflows as analytic engines: they consume internal documents and
public data via retrieval-augmented generation (RAG), produce summaries
and reports, and those outputs are often fed back into the
organization's knowledge base.

This paper proposes that information leakage in such systems should be
understood, and measured, as an analytics property of the pipeline, not
simply as *"the model leaking training data"*. In particular, we focus
on **Structural Inference Leakage Reconstruction (SILR)** -- situations
where a redacted or partially masked business document can be partially
reconstructed by an attacker (or an analyst) using:

-   The remaining document structure and wording\
-   Retrieved related documents (via RAG)\
-   The language priors of a powerful LLM

We introduce a set of proposed metrics -- including a **Structural
Leakage Index (SLI)** and a **Noise-to-Adversary Separation Coefficient
(NASC)** -- as a starting point for quantifying how "reconstructable" a
redacted document or analytic pipeline is.

This is not an experimental paper: all formulas and numeric examples are
illustrative and intended to guide future measurement and implementation
work.

## 1. Executive Summary

### 1.1 Problem in One Paragraph

Enterprises are deploying LLMs that can read internal documents, pull in
public information, and generate reports that are then reused across the
organization. Even when sensitive values are redacted, the structure of
those reports plus retrieved similar documents and LLM priors can allow
an attacker to reconstruct some of the hidden content or, at minimum,
infer business logic and strategy.

### 1.2 Key Ideas

-   Leakage is not only "the model blurting out secrets". It can occur
    when document structure + retrieval + LLM inference make secrets
    guessable.
-   We call this **Structural Inference Leakage Reconstruction (SILR)**.
-   We propose metrics and procedures for measuring how vulnerable a
    given document or pipeline is to such reconstruction.
-   We frame this as a **data analytics task for enterprise teams**, not
    as a purely theoretical ML attack.

### 1.3 Intended Audience

-   **Primary:** Enterprise data security / data analytics teams working
    with RAG and LLM integrations\
-   **Secondary:** Adversarial ML and privacy researchers\
-   **Tertiary:** Governance, risk and compliance teams

## 2. Motivating Example: How a Redacted Report Still Leaks

Consider an anonymized internal report:

    Client: ███████████████
    Region: EMEA
    Risk Rating: ███ / 10
    Key Issue: Concentration of exposure in ███████ sector
    Mitigation Plan:
      1. Reduce exposure by ███% over █ months
      2. Transfer portion of portfolio to ███████████
      3. Require additional covenants above ███ threshold

With: - Access to historical non-redacted reports\
- RAG retrieval over similar documents\
- An LLM able to generalize

An attacker may ask:

> "Given this redacted report and similar historical reports, what are
> the most likely values in the masked fields? Explain your reasoning."

The LLM may infer sectors, risk ratings, ranges, and mitigation
strategies based on statistical patterns.

This is **partial reconstruction**, and it is the target of SILR
analysis.

## 3. Problem Statement and Threat Model

### 3.1 Informal Problem Statement

We want to assess:

> **"How much of the masked content of a document can be reconstructed
> by leveraging structure, retrieval, and inference?"**

### 3.2 Threat Model

**Attacker can:** - View public redacted docs\
- Query an LLM with RAG\
- Iterate indefinitely

**Attacker cannot:** - Access internal DBs\
- Access model training data\
- Execute arbitrary code

This is a weak attacker operationally, but a strong attacker
analytically.

## 4. Concept: Structural Inference Leakage Reconstruction (SILR)

### 4.1 Definition

SILR is the measurable capability to infer masked content from: -
Template structure\
- Surviving textual context\
- Retrieved similar documents\
- LLM priors

### 4.2 Relation to Existing Attacks

SILR is **not**: - Membership inference\
- Prompt injection\
- RAG poisoning\
- Weight extraction

It is **document reconstruction via inference**.

## 5. Structural Leakage Index (SLI)

### 5.1 Purpose

A single interpretable score indicating: \> **"How predictable is this
redacted document?"**

### 5.2 Components

  -----------------------------------------------------------------------
  Component              Symbol             Description
  ---------------------- ------------------ -----------------------------
  Template Certainty     `E_t`              Is the document from a rigid
                                            template?

  Context Inferability   `E_c`              How much domain context
                                            leaks?

  Numeric Pattern        `N_p`              Number of unredacted numeric
  Visibility                                patterns

  Cross-reference        `X_r`              Internal references that leak
  Density                                   structure

  Number of Redacted     `S_n`              Total masked fields
  Spans                                     

  Stylistic Fingerprint  `α`                Distinctiveness of writing
                                            style
  -----------------------------------------------------------------------

### 5.3 Proposed Formula

               α·E_t + E_c + N_p + X_r
    SLI   =   -------------------------
                         S_n

### 5.4 Example

    E_t = 0.8
    E_c = 0.7
    N_p = 4
    X_r = 3
    S_n = 10
    α = 1.1

    SLI ≈ (1.1·0.8 + 0.7 + 4 + 3) / 10 ≈ 0.935

High predictability.

## 6. Leakage Measurement Protocol

1.  Collect real internal documents\
2.  Create synthetic redacted versions\
3.  Run inference attempts via LLM+RAG\
4.  Measure accuracy:
    -   Exact matches\
    -   Approximate ranges\
    -   Business category correctness\
5.  Correlate accuracy vs SLI

## 7. RAG vs Graph-OSINT Tools

### 7.1 Capabilities

Graph tools → relationship mapping\
RAG → inference-driven reconstruction

### 7.2 Comparison

  Aspect                     RAG-LLM      Graph-OSINT
  -------------------------- ------------ -------------
  Relationship discovery     Moderate     Strong
  Provenance tracing         Weak         Strong
  Masked span inference      **Strong**   None
  Cross-document reasoning   Strong       Weak
  Fact source traceability   Hard         Easy

## 8. Detecting Suspicious Prompting (NASC)

    NASC = observed divergence / typical variation

-   NASC ≈ 1 → normal\
-   NASC ≫ 1 → potential extraction

Uses distributional deviation metrics.

## 9. Defensive Measures

### 9.1 Redaction & Design

-   Avoid rigid templates\
-   Alter ordering in public docs\
-   Remove cross-refs\
-   Use variable redaction spans

### 9.2 RAG Architecture

-   Segregate corpora\
-   Log retrieval provenance\
-   Prevent autogenerated content from re-entering RAG unchecked

### 9.3 Monitoring & Governance

-   Track sensitive themes\
-   Detect repeated probing\
-   Human review for flagged activity

## 10. Limitations & Future Work

-   Metrics unvalidated\
-   No real case study\
-   Formulas intentionally simple

Future work: - Case studies\
- SLI validation\
- Redaction comparison\
- Prototype tooling

## 11. Conclusion

SILR reframes leakage as a **pipeline and analytics issue**, not only a
model issue.\
Document structure, template consistency, and retrieval make redacted
content inferable.

**SLI and NASC** are proposed metrics to begin quantifying this risk.

## 12. Glossary

  Term          Definition
  ------------- ---------------------------------------------
  SILR          Structural Inference Leakage Reconstruction
  SLI           Structural Leakage Index
  NASC          Noise-to-Adversary Separation Coefficient
  RAG           Retrieval-Augmented Generation
  Graph-OSINT   Graph-based relationship mapping tools

------------------------------------------------------------------------

**Author:** Nuno Lopes\
**License:** CC BY 4.0 / MIT\
**Contact:** https://www.linkedin.com/in/viriathus/\
**Repo:** https://github.com/V1r14thu5/adversarial-data-analytics
