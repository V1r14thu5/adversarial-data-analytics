
Where:

- If NASC is close to 1, we are probably seeing normal analytic noise.
- If NASC is, say, 5–10, we may be seeing a user pushing the system in unusual ways.

*Exact numbers would have to be established by monitoring a real system, not by theory.*

**The Main Point:** We can use familiar analytics techniques to flag "weird" prompt behavior without knowing the attacker's intent.

---

## 9. Practical Defensive Measures (Implementation-Oriented)

*This section focuses on what a data or security team could actually do, in check-list style.*

### 9.1 Redaction and Document Design

Avoid publishing redacted documents that:

- Use rigid, well-known internal templates;
- Expose many numeric patterns and thresholds;
- Contain lots of internal terminology.

When redaction is necessary, consider:

- Changing section ordering for public versions.
- Removing or rephrasing cross-references ("see section 3.2").
- Using variable-length redaction spans, not identical █████ blocks everywhere.

The aim is not to be "perfectly unguessable", but to reduce predictability.

### 9.2 RAG Configuration and Isolation

- **Separate internal and external retrieval where possible:**
  e.g., do not answer public-facing questions with a corpus that includes highly sensitive internal documents.
- **Log which documents are used as retrieval sources for particular answers.**
  This supports later investigation of suspected leakage.
- **Do not automatically feed LLM-generated summaries back into the same RAG corpus that generated them, without some gatekeeping.** This limits "recirculation loops".

### 9.3 Monitoring and Governance

- Define a small number of "sensitive themes" (e.g., major clients, internal scoring rubrics, unreleased products).
- Monitor queries around those themes for:
  - Unusually specific reconstructions,
  - Repeated attempts to get explanations of internal logic.
- Consider manual review of prompts and outputs that meet certain heuristic thresholds (e.g., "many follow-up questions about the same redacted public report").

### 9.4 Complexity vs Robustness

Avoid over-engineering defenses with many nested "gates" and routing rules, unless you can also monitor and test them.

Emphasize:

1. Good redaction practice,
2. Sensible RAG scoping, and
3. Analytics-based monitoring of queries and outputs.

---

## 10. Limitations and Future Work

This framework has several important limitations:

1. **No experimental results yet.**
   All thresholds, scores, and formulae are proposed for discussion. They must not be treated as validated.
2. **No real case study included.**
   The motivating example is constructed to be realistic, but it is not drawn from a live system.
3. **Metrics may be too simple.**
   The functional forms of SLI, NASC, etc., are deliberately simple for explanation. Real systems may require more complex models.

**Future Work Should Include:**

- One or more detailed case studies with real documents and real reconstruction attempts.
- Empirical evaluation of SLI: does a higher SLI actually correlate with higher reconstruction accuracy?
- Systematic comparison of redaction strategies (e.g., full redaction vs partial vs structural changes).
- Prototype tools that compute SLI-like scores and integrate with existing data-governance platforms.

---

## 11. Conclusion

This paper suggests that, in LLM-augmented enterprise systems, information leakage is as much an analytics problem as it is a model problem. When LLMs are combined with RAG and reused artifacts, redacted or partially masked documents can leak through:

1. Their structure,
2. Their templates, and
3. The availability of similar documents.

We call this **Structural Inference Leakage Reconstruction (SILR)** and propose a set of metrics and practices to start measuring it.

The key message is deliberately modest:

> Enterprise data teams should treat leakage as a measurable property of analytic pipelines and document design – not just as a question of whether the model "leaks training data".

The maths and metrics presented here are hypothetical and intended as starting points. The next step is for practitioners and researchers to test, refine, or discard them based on real-world evidence.

---

## 12. Glossary (Informal)

| Term | Definition |
|------|------------|
| SILR | Structural Inference Leakage Reconstruction – Leakage arising from the predictability of redacted content based on document structure and similar examples. |
| SLI | Structural Leakage Index – A proposed score reflecting how predictable the content of a redacted document is, given its template and visible features. |
| NASC | Noise-to-Adversary Separation Coefficient – Proposed measure of how far observed model behavior deviates from normal when pressured by suspicious prompts. |
| RAG | Retrieval-Augmented Generation – A pattern where the model retrieves documents and then generates answers conditioned on them. |
| Graph-OSINT tools | Systems that map entities and relationships into graphs (e.g., for threat intelligence), without doing deep content inference. |

---

**Framework Authors:** [Nuno Lopes]  
**License:** Creative Commons Attribution 4.0 International (for non-commercial research use) / MIT License  
**Contact:** https://www.linkedin.com/in/viriathus/  
**Repository:** https://github.com/V1r14thu5/adversarial-data-analytics  

*This document represents a conceptual framework proposal. All metrics, thresholds, and recommendations require empirical validation before implementation in production systems.*
