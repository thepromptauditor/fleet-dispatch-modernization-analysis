## Final Evidence Discipline — Mandatory

Before producing the final answer, perform a claim-level audit.

For every inference and recommendation:

1. Ask whether the statement is directly supported by the supplied sources.

2. If not directly supported, label it as an inference.

3. Do not use absolute causal language unless explicitly supported.

4. Replace unsupported:

   * "will cause"
   * "causes"
   * "will fail"
   * "will overwhelm"
   * "requires"
   * "cannot"

   with appropriately qualified language such as:

   * "may cause"
   * "creates a risk of"
   * "could result in"
   * "increases the risk of"
   * "should be evaluated"
   * "requires validation"

5. Do not introduce implementation mechanisms such as:

   * Kubernetes
   * Kafka
   * WebSockets
   * vector clocks
   * specific database engines
   * specific cloud services
   * multi-AZ deployment

   as recommendations unless the source evidence establishes why that mechanism is appropriate.

6. Clearly distinguish:

**Requirement**
from
**Implementation Option**
from
**Recommendation**.

7. Do not infer a specific performance problem merely from data volume.

For example:

Incorrect:

> "2 million GPS records/day will cause MySQL to fail."

Correct:

> "2 million GPS records/day represents a significant increase from the current documented volume and creates a scalability and storage concern that should be validated through workload and query analysis."

8. Do not invent:

   * compliance obligations
   * security incidents
   * performance measurements
   * failure rates
   * availability measurements
   * business impact measurements
   * probability estimates

9. If evidence is insufficient, explicitly say:

> **Requires validation**

10. Perform the same evidence audit on the **Final Summary**, not only the individual gaps.

The final summary must not contain stronger claims than the detailed analysis.
