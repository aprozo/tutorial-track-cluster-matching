---
title: "Using PODIO to Work With Our Data Model"
teaching: 20
exercises: 5
questions:
- "What is our data model?"
- "What is PODIO?"
- "How do I interface with our data model?"
objectives:
- "Understand relationship between the data model and PODIO"
- "Become familiar with PODIO concepts and synatx"
- "Identify where to find data model documentation"
keypoints:
- "A data model is how represent our data in software"
- "PODIO is a toolkit to generate and interface with data models like EDM4eic"
- "The core concepts of PODIO are collections (lists of objects), relations (direct"
  " connections between objects), and links/associations optional connections between"
  " objects)"
- "Data model
---

## The EDM4eic Data Model

![Overview of the EDM4eic data model](./../assets/img/tutorial/EDM4eicOverview.png)

DATA MODEL IS HOW WE REPRESENT OUR DATA IN MEMORY. WE WANT IT TO
BE STANDARDIZED, POD, INTUITIVE, AND MORE. DEFINED IN A YAML FILE.

WE ALSO USE EDM4HEP, WHICH IS FOR THE KEY4HEP PROJECT AND IS WIDELY
USED AT OTHER FUTURE COLLIDERS.

## PODIO Introduction

PODIO IS A TOOLKIT TO GENERATE AND INTERFACE WITH DATA MODELS LIKE
EDM4EIC. IT TAKES THAT YAML FILE AND TURNS IT INTO ALL OF THE C++
CODE YOU NEED.

POINTS TO HIT:
- STORAGE VS USER LAYER
- COLLECTIONS (READ-ONLY)
- COMPONENTS VS CLASSES
- MEMBERS AND VECTOR MEMBERS
- use getX, setX
- DOXYGEN PAGE

## Relations

RELATIONS DIRECTLY CONNECT ONE OBJECT TO ANOTHER.

### One-to-One Relations

NAME SAYS IT ALL

![Diagram of a one-to-one relation](./../assets/img/tutorial/OneToOneRelation.png)

### One-to-Many Relations

NAME SAYS IT ALL

![Diagram of a one-to-many relation](./../assets/img/tutorial/OneToManyRelation.png)

## Links/Associations

LINKS AND ASSOCIATIONS CONNECT DISPARATE OJECTS. THESE CONNECTIONS
MAY OR MAY NOT EXIST. LINKS HAVE DIRECTIONALITY, ASSOCIATIONS DON'T.
ASSOCIATIONS ARE BEING DEPRECATED.

![Diagram of a link](./../assets/img/tutorial/MCRecoParticleLink.png)

LINKS HAVE A LOT OF BENEFITS SUCH AS MORE CONSISTENT SYNTAX. AND
YOU HAVE THE LINK NAVIGATOR WHICH IS OUT-OF-THE-SCOPE OF THIS TUTORIAL.

![Diagram of an association](./../assets/img/tutorial/MCRecoParticleAssociations.png)

## References

[podio]: https://github.com/AIDASoft/podio
[edm4eic]: https://github.com/eic/EDM4eic
[edm4hep]: https://github.com/key4hep/EDM4hep

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
