---
title: "Our Data Model and PODIO"
teaching: 15
exercises: 0
questions:
- "FIXME?"
objectives:
- "FIXME"
keypoints:
- "FIXED ME"
---

## Outline

Key points:
  - Illustrate basics of PODIO usage in
    C++, Python
  - Illustrate navigating links, associations,
    and relations

Skeleton:

2. PODIO
    - Framework for generating, managing EDMs
    - Defined in a .yml file (include link)
    - Navigating the model:
      - Example: Cluster
        - Explanation of fields, "guessing" the
          setter/getters
        - Note: doxygen and /opt/include
      - Example: Track
      - Example: MCParticle 
    - Relations: "adjacent" connections between
      objects
      - Encode "causal" connections
      - Or "combinatorial" connections
    - Links/Associations: "orthogonal" connections
      - Encodes connections which may (or may not)
        be present

{% include links.md %}
