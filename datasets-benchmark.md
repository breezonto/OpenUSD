# Public Datasets and Experimental Guide for Knowledge Graph Integration with OpenUSD

## 1. Introduction

This document provides a practical guide to selecting public datasets and designing experiments for research on integrating **Knowledge Graphs (KGs)** with **OpenUSD (Universal Scene Description)**.

The central research idea is to introduce a semantic knowledge layer alongside OpenUSD. Rather than replacing USD as the underlying representation of 3D scenes, the knowledge graph provides an additional layer for representing and querying semantic information, domain knowledge, entity identities, relationships, and provenance.

A possible architecture is:

```text
                 Applications / Agents
                          |
                  Semantic Queries
                          |
                 Knowledge Graph
              (RDF / OWL / SHACL)
                          |
               Mapping & Synchronization
                          |
                    OpenUSD
          (Stage / Prim / Attribute / Layer)
                          |
                 3D Scene Data
       (Geometry / Materials / Animation)
```

The architecture separates two complementary responsibilities:

- **OpenUSD:** Stores and manages 3D scene descriptions, geometry, materials, animation, references, variants, and composition.
- **Knowledge Graph:** Represents semantic entities, domain concepts, relationships, metadata, provenance, and cross-scene connections.

This separation is important because large geometric structures and animation data are generally better managed by USD, while semantic relationships and domain-level queries can benefit from graph-based representations.

The following sections introduce public datasets that can support different aspects of this research, followed by an experimental roadmap.

---

## 2. Understanding the Research Requirements

Before selecting datasets, it is useful to identify what needs to be evaluated.

The integration of OpenUSD and Knowledge Graphs involves several research problems.

| Research dimension | Description | Example research question |
|---|---|---|
| Semantic extraction | Extracting semantic information from USD scenes | Can USD primitives and attributes be mapped into RDF entities and properties? |
| Entity identification | Establishing stable identities for objects | Can the same real-world object be identified across different USD stages? |
| Relationship extraction | Representing relationships between scene objects | Can USD relationships and spatial relations be represented as KG edges? |
| Composition awareness | Understanding USD composition and layered scene descriptions | Can the KG preserve information about references, payloads, variants, and layers? |
| Provenance | Tracking the origin of semantic information | Can the system identify which USD layer or asset introduced a particular fact? |
| Semantic validation | Checking semantic consistency | Can SHACL detect inconsistencies that ordinary USD validation does not cover? |
| Semantic querying | Supporting cross-scene and domain-level queries | Can SPARQL answer queries that are difficult to express through USD APIs alone? |
| Incremental synchronization | Keeping the KG consistent with changing USD scenes | Can updates to USD stages be reflected efficiently in the KG? |
| Scalability | Evaluating performance on large datasets | How does the integration behave as the number of USD assets and RDF triples increases? |

A useful distinction is between two types of graph representations:

1. **USD structural graph:** Represents the internal structure of USD, including prim hierarchies, attributes, relationships, layers, and composition arcs.
2. **Semantic knowledge graph:** Represents domain-level entities and their meanings, such as buildings, rooms, equipment, components, materials, and functional relationships.

These graphs may overlap, but they are not identical. A USD prim hierarchy does not necessarily represent the complete semantic structure of a real-world environment.

For example, two USD prims may represent the same physical component in different stages, while two geometrically adjacent objects may have no explicit USD relationship.

This distinction should be reflected in the experimental design.

---

## 3. Public Datasets

The datasets below are organized into four categories:

1. Native OpenUSD datasets.
2. BIM and IFC datasets.
3. 3D scene understanding datasets.
4. Explicit 3D scene graph datasets.

Not every dataset is directly compatible with OpenUSD. Some provide USD-native assets, while others provide semantic annotations or reference graphs that can be mapped into USD and RDF.

### 3.1 Native OpenUSD Datasets

These datasets are particularly important because they allow experiments to operate directly on USD files without first converting another 3D representation.

#### 3.1.1 NVIDIA Omniverse USD Assets

**Source:** [NVIDIA Omniverse USD Downloadable Asset Packs](https://docs.omniverse.nvidia.com/usd/latest/usd_content_samples/downloadable_packs.html)

NVIDIA provides a collection of downloadable OpenUSD assets and sample scenes. The collection includes more than 250 GB of free USD assets.

Relevant examples include:

| Dataset / Asset Pack | Description | Potential research application |
|---|---|---|
| Core Demo Samples | Manufacturing, engineering, and physics-oriented scenes | Semantic extraction and object relationships |
| USD Explorer Factory | Industrial factory scene | Equipment and production-line knowledge graphs |
| Architectural Brownstone | Architectural models and building components | Building and spatial knowledge representation |
| Physics Demo Scenes | Scenes demonstrating physical simulation | Physical relationships and simulation metadata |
| XR Samples | Extended-reality-oriented USD examples | Scene metadata and application interoperability |
| Defect Detection Demo | Industrial inspection-related content | Asset metadata and inspection knowledge |

**Why it is useful**

These assets provide realistic USD structures for investigating:

- USD Stage and Prim traversal.
- Attribute and Relationship extraction.
- Layer organization and composition.
- References and payloads.
- Materials and schemas.
- Semantic mapping from USD structures into RDF.

In particular, the USD Explorer Factory scene is a useful starting point for constructing an industrial knowledge graph.

#### 3.1.2 ASWF / USD Working Group Sample Assets

**Source:** [ASWF USD Working Group Sample Assets](https://github.com/Autodesk/openusd-assets)

The OpenUSD ecosystem contains sample assets contributed by different organizations and projects.

Examples include assets associated with:

- Pixar.
- NVIDIA.
- Blender Institute.
- SideFX.
- Walt Disney Animation Studios.
- Intel.
- Poly Haven.

These examples are useful for testing whether a semantic integration approach can work across different asset sources and authoring conventions.

**Potential experiments**

- Extracting semantic information from heterogeneous USD files.
- Comparing different USD authoring styles.
- Testing whether the same semantic entity can be represented consistently across assets.
- Investigating differences between authored USD layers and composed USD stages.

#### 3.1.3 NVIDIA USD Example Datasets

**Source:** [NVIDIA USD Example Datasets](https://developer.nvidia.com/usd)

Some useful examples include:

| Dataset | Description | Research application |
|---|---|---|
| Residential Lobby | Building scene involving data from tools such as Revit, Rhino, and 3ds Max | Cross-tool semantic interoperability |
| da Vinci's Workshop | Large production-oriented USD scene | Large-scale scene representation and composition |

The Residential Lobby example is particularly interesting because it involves assets originating from different design tools.

This makes it useful for investigating whether a KG can provide a unified semantic representation across heterogeneous USD sources.

The Workshop example can be used to explore large-scale asset organization and composition.

---

### 3.2 BIM and IFC Datasets

Building Information Modeling (BIM) is one of the most promising application domains for OpenUSD and Knowledge Graph integration.

Unlike generic 3D models, BIM data contains explicit domain semantics.

For example:

- Buildings.
- Floors.
- Rooms.
- Walls.
- Doors.
- Windows.
- HVAC equipment.
- Electrical systems.
- Building materials.
- Spatial containment.
- Functional relationships.

IFC (Industry Foundation Classes) provides a standardized data model for representing this information.

Consequently, IFC datasets are particularly useful for evaluating the semantic interoperability between BIM, OpenUSD, and Knowledge Graphs.

#### 3.2.1 buildingSMART Certification Datasets

**Source:** [buildingSMART Technical Resources](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)

buildingSMART provides IFC-related technical resources and sample datasets covering different IFC versions.

Relevant versions include:

- IFC2x3.
- IFC4.
- IFC4.3.

**Potential experiments**

1. Convert IFC entities into USD prims.
2. Extract USD metadata and relationships.
3. Map IFC domain concepts into an RDF ontology.
4. Compare the semantic information preserved by IFC-to-USD conversion.
5. Validate the resulting graph using SHACL.

This dataset category is useful for investigating semantic interoperability rather than only geometric conversion.

#### 3.2.2 A Large-Scale Dataset of IFC-Compliant Building Models

**Source:** [Zenodo — A Large-Scale Dataset of IFC-Compliant Building Models](https://doi.org/10.5281/zenodo.20788324)

This dataset contains approximately 73,303 complete IFC4 residential building models.

Important characteristics:

- IFC4-compliant models.
- Large number of building instances.
- Deterministically generated data.
- CC BY 4.0 license.

**Potential research applications**

- Large-scale IFC-to-USD conversion.
- RDF graph generation.
- Ontology mapping.
- Semantic consistency validation.
- Query performance evaluation.
- Incremental processing experiments.

One limitation is that procedurally generated models may not fully represent the heterogeneity and complexity of real-world BIM authoring practices.

Nevertheless, the dataset is valuable for scalability experiments.

#### 3.2.3 IFCNet

**Source:** [IFCNet](https://github.com/IfcOpenShell/IfcNet)

IFCNet provides IFC-related data for machine learning and semantic analysis.

It focuses on individual IFC entities and includes semantic information alongside geometric representations.

Depending on the available release, representations include:

- IFC entity information.
- Semantic classes.
- Geometry.
- OBJ representations.
- Point-cloud representations.

**Potential research applications**

- Mapping IFC classes to OWL classes.
- Comparing IFC entities with USD prims.
- Building object-level semantic graphs.
- Evaluating semantic classification and entity alignment.

#### 3.2.4 Awesome BIM Datasets

**Source:** [Awesome BIM Datasets](https://github.com/ConstructionAutomation/Awesome-BIM-Datasets)

This repository is a catalog of public BIM-related datasets.

It includes references to datasets covering:

- IFC models.
- Point clouds.
- Urban environments.
- Floor plans.
- Building energy.
- Construction data.
- Building-related machine learning tasks.

It is particularly useful when searching for additional datasets for a specific BIM application.

---

### 3.3 3D Scene Understanding Datasets

These datasets are not necessarily distributed in USD format, but they provide useful semantic annotations and reconstructed 3D environments.

They can be used as external semantic ground truth for evaluating the knowledge graph generated from 3D scenes.

#### 3.3.1 3D-FRONT

**Source:** [3D-FRONT Dataset](https://tianchi.aliyun.com/specials/promotion/alibaba-3d-scene-dataset)

3D-FRONT is a synthetic indoor scene dataset containing furnished 3D environments.

It includes:

- Indoor room layouts.
- Furniture models.
- Object placements.
- Room-level scene configurations.
- Semantic information about furniture and room types.

**Potential research applications**

- Extracting semantic entities from indoor environments.
- Representing room-object relationships.
- Building spatial knowledge graphs.
- Testing object identity and semantic classification.

For example, a scene can be represented as:

```text
Apartment
 ├── LivingRoom
 │    ├── Sofa
 │    ├── Table
 │    └── Television
 │
 └── Bedroom
      ├── Bed
      ├── Wardrobe
      └── Desk
```

A knowledge graph can represent additional relationships such as:

```turtle
:Apartment01 a :Apartment .

:LivingRoom01 a :LivingRoom ;
    :isPartOf :Apartment01 .

:Sofa01 a :Sofa ;
    :isLocatedIn :LivingRoom01 .

:Table01 a :Table ;
    :isLocatedIn :LivingRoom01 .
```

#### 3.3.2 SG-FRONT

**Source:** [SG-FRONT](https://github.com/3D-FRONT/SG-FRONT)

SG-FRONT extends the 3D-FRONT ecosystem with semantic scene graph information.

It is particularly useful because it provides explicit semantic relationships between objects.

Examples of relationships include:

- Spatial relationships.
- Object associations.
- Material similarity.
- Hierarchical semantic categories.

**Potential research applications**

- Scene graph to RDF conversion.
- Semantic relationship extraction.
- Ontology alignment.
- Knowledge graph construction.
- Graph-based scene retrieval.

This dataset is more directly relevant to KG experiments than a dataset containing only geometry and object labels.

#### 3.3.3 ScanNet

**Source:** [ScanNet](https://www.scan-net.org/)

ScanNet is a large-scale RGB-D dataset of reconstructed indoor environments.

Its characteristics include:

- More than 1,500 scanned indoor scenes.
- Approximately 2.5 million RGB-D views.
- Camera poses.
- Reconstructed 3D surfaces.
- Instance-level semantic annotations.

**Potential research applications**

- Object-level semantic extraction.
- Scene reconstruction and USD representation.
- Semantic entity identification.
- Spatial knowledge graph construction.
- Object-level graph validation.

Access to the dataset requires agreement with its usage terms.

#### 3.3.4 ScanNet++

**Source:** [ScanNet++](https://scannetpp.mlsg.cit.tum.de/)

ScanNet++ provides high-quality 3D scene reconstructions and multimodal data.

It includes:

- DSLR images.
- iPhone RGB-D captures.
- High-quality scene reconstructions.
- Semantic annotations.
- 3D geometry.

The dataset contains approximately 1,006 scenes.

**Potential research applications**

- High-resolution semantic scene representation.
- Cross-modal object alignment.
- Semantic mapping between reconstructed scenes and USD.
- Evaluating graph construction from multimodal scene data.

The full dataset is large, and access requires registration and approval.

#### 3.3.5 HM3DSem

**Source:** [Habitat-Matterport 3D Semantic Dataset](https://aihabitat.org/datasets/hm3d-semantics/)

HM3DSem provides semantic annotations for reconstructed 3D environments.

Its characteristics include:

- 216 3D spaces.
- Approximately 3,100 rooms.
- 142,646 object-instance annotations.
- Object categories mapped to 40 Matterport semantic categories.

**Potential research applications**

- Object classification.
- Semantic entity extraction.
- Room-object relationships.
- Spatial knowledge graph construction.
- Evaluation of semantic mapping accuracy.

The dataset is available for academic, non-commercial research under its access conditions.

---

### 3.4 Explicit 3D Scene Graph Datasets

This category is especially important for Knowledge Graph research.

Unlike datasets that provide only object labels, scene graph datasets explicitly represent objects as nodes and their semantic relationships as edges.

They therefore provide a useful reference for evaluating graph construction and semantic relationship extraction.

#### 3.4.1 3DSSG: 3D Semantic Scene Graphs

**Sources:**

- [3DSSG Project Page](https://3dssg.github.io/)
- [3DSSG GitHub Repository](https://github.com/ShunChengWu/3DSSG)

3DSSG is one of the most relevant datasets for this research.

It provides semantic scene graphs constructed from 3RScan environments.

A scene graph contains:

- Object instances as nodes.
- Semantic relationships as edges.
- Object categories.
- Object attributes.
- Relationship predicates.

The full dataset contains approximately:

| Statistic | Quantity |
|---|---:|
| Scene graphs | 1,482 |
| Object nodes | 48,000 |
| Relationship edges | 544,000 |
| Object classes | 534 |
| Relationship predicates | 41 |

The exact statistics depend on the dataset release and split.

The repository also provides files such as:

```text
objects.json
relationships.json
affordances.txt
attributes.txt
classes.txt
relationships.txt
```

These files provide useful information for reconstructing semantic scene graphs.

**Example**

A simplified scene graph could look like:

```text
              Room
               |
          contains
               |
             Table
            /     \
       supports   near
          /         \
       Laptop      Chair
```

The same information can be represented using RDF:

```turtle
:Room01 a :Room ;
    :contains :Table01 .

:Table01 a :Table ;
    :supports :Laptop01 ;
    :near :Chair01 .

:Laptop01 a :Laptop .

:Chair01 a :Chair .
```

**Why 3DSSG is important**

3DSSG is probably the most useful dataset for evaluating the semantic graph component of an OpenUSD-KG integration architecture.

It can support experiments involving:

- Scene graph extraction.
- RDF graph generation.
- Semantic relationship mapping.
- Object identity resolution.
- Graph querying.
- Ontology alignment.
- Semantic retrieval.

However, 3DSSG is not a native USD dataset. Its underlying 3D scenes originate from 3RScan, so a conversion or reconstruction step is needed before using it as an OpenUSD integration benchmark.

#### 3.4.2 ScanNet-SG

**Source:** [ScanNet-SG](https://github.com/)

ScanNet-SG is a newer scene graph-oriented resource for semantic scene understanding and alignment.

The 2026 release information describes:

- More than 700,000 alignment samples.
- 509 ScanNet categories.
- Approximately 3,000 GPT-4o-derived categories.
- Semantic labels.
- BERT embeddings.
- Vision-language features.
- Point clouds.
- 3D bounding boxes.
- Frame-to-scan and subscan-to-subscan alignment.

**Potential research applications**

- Semantic entity resolution.
- Cross-scene graph alignment.
- Semantic identity matching.
- Multimodal knowledge graph construction.
- Evaluating semantic interoperability across different scene representations.

Because it is a recent resource, verify the current release, access conditions, and exact dataset structure before designing experiments around it.

---

## 4. Dataset Comparison

The following table summarizes the main datasets and their relevance to OpenUSD-KG research.

| Dataset | Native USD | Semantic labels | Explicit relationships | Main application |
|---|---|---|---|---|
| NVIDIA USD Assets | Yes | Varies | USD relationships and composition | OpenUSD integration |
| ASWF USD Assets | Yes | Varies | Varies | Heterogeneous USD testing |
| NVIDIA USD Examples | Yes | Varies | USD relationships and composition | Cross-tool interoperability |
| buildingSMART IFC | No | Yes | IFC relationships | BIM ontology mapping |
| Large-scale IFC Models | No | Yes | IFC relationships | Scalability |
| IFCNet | No | Yes | IFC structure | Entity-level semantic mapping |
| 3D-FRONT | No | Yes | Scene structure | Indoor scene semantics |
| SG-FRONT | No | Yes | Yes | Semantic scene graphs |
| ScanNet | No | Yes | Limited explicit scene graph annotations | 3D semantic extraction |
| ScanNet++ | No | Yes | Limited explicit scene graph annotations | Multimodal semantic integration |
| HM3DSem | No | Yes | Spatial and semantic annotations | Room-object semantics |
| 3DSSG | No | Yes | Yes | KG construction and evaluation |
| ScanNet-SG | No | Yes | Yes | Semantic alignment |

The table should not be interpreted as a direct quality ranking. These datasets address different experimental requirements.

In particular:

- Native USD datasets are necessary for testing the actual integration with OpenUSD.
- IFC datasets are useful for testing domain ontology alignment and semantic interoperability.
- Scene understanding datasets provide object-level semantic annotations.
- Scene graph datasets provide explicit relationships that can serve as reference graphs.

---

## 5. Recommended Dataset Combination

For a practical research project, it is unnecessary to use every dataset.

A more effective strategy is to select a small number of complementary datasets.

### 5.1 Core Dataset: 3DSSG + 3RScan

**Purpose:** Evaluate semantic knowledge graph construction.

3DSSG provides explicit scene graph annotations, while 3RScan provides the underlying reconstructed 3D environments.

This combination can be used to investigate:

1. How to represent 3D objects as KG entities.
2. How to map scene relationships into RDF predicates.
3. How to align object categories with an ontology.
4. How to evaluate the resulting graph against reference annotations.

This should be the primary dataset for evaluating the semantic graph component.

### 5.2 OpenUSD Dataset: NVIDIA USD Assets

**Purpose:** Evaluate the actual integration with OpenUSD.

Use NVIDIA's USD assets to investigate:

1. USD Stage traversal.
2. Prim and Attribute extraction.
3. Relationship extraction.
4. Layer and composition analysis.
5. USD-to-RDF mapping.
6. Provenance tracking.

This dataset is essential because a system evaluated only on 3DSSG would not demonstrate that the proposed architecture works with OpenUSD.

### 5.3 Domain Dataset: buildingSMART IFC Samples

**Purpose:** Evaluate domain ontology alignment and interoperability.

Use IFC samples to investigate:

1. Mapping IFC classes to OWL classes.
2. Mapping IFC relationships to RDF properties.
3. Converting IFC data into USD.
4. Preserving semantic information during conversion.
5. Validating semantic consistency using SHACL.

### 5.4 Scalability Dataset: Large-scale IFC Models

**Purpose:** Evaluate performance and scalability.

The 73,303 IFC building models can be used to investigate:

- RDF graph generation throughput.
- KG storage requirements.
- Query latency.
- Entity resolution at scale.
- Incremental synchronization.
- Memory consumption.

This dataset is particularly useful after the basic integration architecture has been implemented.

---

## 6. Experimental Methodology

A practical experimental design can be divided into six stages.

### Stage 1: USD Structural Extraction

Start by extracting information from USD files using the OpenUSD Python API.

Relevant concepts include:

| USD concept | Description | Possible KG representation |
|---|---|---|
| Stage | A composed scene | `usd:Stage` |
| Prim | A scene object or structural element | `usd:Prim` |
| Attribute | A property associated with a prim | RDF property or value node |
| Relationship | A connection between prims | RDF object property |
| Layer | An authored scene description | `usd:Layer` |
| Reference | An external composition dependency | `usd:Reference` |
| Payload | A deferred composition dependency | `usd:Payload` |
| Variant Set | Alternative scene configurations | `usd:VariantSet` |
| Schema | A structured definition of USD data | Ontology class or schema mapping |

For example, consider a simple USD scene:

```usda
def Xform "Building"
{
    def Scope "Rooms"
    {
        def Xform "Room01"
        {
            def Cube "Table"
            {
                double size = 1
            }

            def Cube "Chair"
            {
                double size = 1
            }
        }
    }
}
```

A simplified RDF representation could be:

```turtle
@prefix usd: <https://example.org/usd#> .
@prefix ex: <https://example.org/scene#> .

ex:Building a usd:Prim ;
    usd:hasChild ex:Room01 .

ex:Room01 a usd:Prim ;
    usd:hasChild ex:Table ;
    usd:hasChild ex:Chair .

ex:Table a usd:Prim ;
    usd:hasAttribute ex:TableSize .

ex:TableSize usd:value "1.0"^^<http://www.w3.org/2001/XMLSchema#double> .
```

This is only a simplified example. A production mapping should distinguish authored values from composed values, preserve USD types, and handle relationships and composition arcs explicitly.

### Stage 2: Semantic Mapping

The next step is to map structural USD information into domain-level concepts.

For example:

```text
USD Prim: /Building/Room01/Table
             |
             v
       Semantic Mapping
             |
             v
       KG Entity: Table01
             |
             v
       Ontology Class: Furniture
```

The semantic layer should not merely reproduce the USD prim hierarchy.

It should introduce domain concepts and relationships that are not necessarily represented directly in the original USD structure.

For example:

```turtle
:Table01 a :Furniture, :Table ;
    :locatedIn :Room01 ;
    :supports :Laptop01 .
```

The distinction between structural mapping and semantic enrichment should be explicit in the implementation.

### Stage 3: Entity Identity Resolution

A major challenge is that USD prim paths are not necessarily stable identifiers.

For example:

```text
/Building/Room01/Table
```

may become:

```text
/Building/Floor01/Room01/Table
```

after a scene restructuring operation.

If the KG uses the prim path as the permanent URI of the entity, this change may create a new entity even though the underlying physical object remains the same.

A better approach is to distinguish:

- USD prim path.
- USD layer and authored location.
- Persistent asset identifier.
- Semantic entity URI.
- External domain identifier.

For example:

```turtle
:Table01 a :Table ;
    usd:primPath "/Building/Room01/Table" ;
    :assetIdentifier "asset-000123" .
```

The exact identity strategy should depend on the available source metadata and the intended persistence guarantees.

### Stage 4: Relationship Mapping

Relationships are particularly important because they provide much of the value of a knowledge graph.

Possible relationship categories include:

| Relationship category | Example |
|---|---|
| Structural | `hasChild`, `partOf` |
| Spatial | `inside`, `near`, `adjacentTo` |
| Functional | `supports`, `connectedTo` |
| Semantic | `isA`, `hasMaterial` |
| Domain-specific | `suppliesPowerTo`, `belongsToSystem` |
| Composition | `referencesLayer`, `usesPayload` |
| Provenance | `derivedFrom`, `assertedInLayer` |

Not all these relationships can be extracted directly from USD.

Some are explicitly authored, while others must be inferred from geometry, metadata, domain rules, or external annotations.

The implementation should record the origin of each relationship.

For example:

```turtle
:Table01 :supports :Laptop01 .

:Table01 :hasProvenance :Assertion001 .

:Assertion001
    :sourceLayer :FurnitureLayer ;
    :generationMethod "semantic-inference" .
```

This makes it possible to distinguish source-authored facts from automatically inferred facts.

### Stage 5: Semantic Validation

Use OWL and SHACL to validate the generated graph.

Possible validation requirements include:

- Every room must belong to a building.
- Every door must connect two spatial regions, where required by the chosen domain model.
- Every equipment instance must have an appropriate semantic class.
- Every semantic entity must have a valid identity.
- Every inferred relationship must have provenance.
- Every referenced USD asset must resolve to a valid source.

Example SHACL shape:

```turtle
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix ex: <https://example.org/ontology#> .

ex:RoomShape
    a sh:NodeShape ;
    sh:targetClass ex:Room ;
    sh:property [
        sh:path ex:isPartOf ;
        sh:minCount 1 ;
        sh:class ex:Building
    ] .
```

This example requires each room to have at least one `isPartOf` relationship pointing to a building.

A useful experiment is to compare validation using:

1. USD-native structural validation.
2. KG-based semantic validation.
3. Combined USD and KG validation.

The goal is not to claim that SHACL replaces USD validation. The two mechanisms address different classes of constraints.

### Stage 6: Querying and Synchronization

Finally, investigate whether the KG enables semantic queries across multiple USD assets and scenes.

Example SPARQL query:

```sparql
PREFIX ex: <https://example.org/ontology#>

SELECT ?table ?room
WHERE {
    ?table a ex:Table .
    ?table ex:locatedIn ?room .
}
```

More advanced queries can combine information from multiple scenes:

- Find all tables made of a particular material.
- Find all machines belonging to a specific production line.
- Find all objects located in rooms with a particular function.
- Find all USD assets containing a specific type of equipment.
- Find all semantic entities derived from a particular source layer.

The synchronization experiment should investigate how changes in USD affect the KG.

For example:

```text
USD Change
    |
    v
Change Detection
    |
    v
Affected Prim Identification
    |
    v
KG Update
    |
    v
Consistency Validation
```

It is important to distinguish between changes to the composed stage and changes to individual authored layers.

A composed stage may contain information originating from multiple layers, references, and payloads. Therefore, provenance and incremental synchronization should be composition-aware.

---

## 7. Evaluation Metrics

The evaluation should measure not only whether the KG can be generated, but also whether it preserves useful semantic information and supports capabilities that are difficult to achieve through USD alone.

### 7.1 Semantic Mapping Accuracy

Compare generated RDF triples against reference semantic annotations.

Metrics:

- Precision.
- Recall.
- F1-score.

For example, compare the generated relationships with the ground-truth relationships in 3DSSG.

### 7.2 Entity Resolution Accuracy

Evaluate whether the system can correctly identify the same semantic entity across different USD stages or source datasets.

Metrics:

- Entity matching precision.
- Entity matching recall.
- Entity matching F1-score.

### 7.3 Relationship Preservation

Measure whether relationships from the source representation are preserved in the generated KG.

Metrics:

- Relationship precision.
- Relationship recall.
- Relationship F1-score.

It is useful to report structural relationships and semantic relationships separately.

### 7.4 Composition Fidelity

Evaluate whether the KG correctly represents information originating from:

- USD layers.
- References.
- Payloads.
- Variants.
- Inherited or overridden properties.

Possible metrics include:

- Composition dependency preservation.
- Authored-versus-composed value consistency.
- Correct source-layer attribution.

### 7.5 Provenance Completeness

Measure how many generated facts have sufficient source information.

For example:

$$
\text{Provenance Completeness}
=
\frac{\text{Facts with valid provenance}}
{\text{Total generated facts}}
$$

### 7.6 Semantic Validation

Evaluate the ability to detect semantic inconsistencies.

Possible metrics:

- Number of detected violations.
- Precision and recall of violation detection.
- Validation execution time.

### 7.7 Query Performance

Compare the performance of different query approaches.

For example:

| Query type | Baseline | Proposed approach |
|---|---|---|
| Prim hierarchy traversal | USD API | KG query |
| Attribute lookup | USD API | KG query |
| Cross-scene semantic search | Custom USD traversal | SPARQL |
| Domain relationship query | Custom USD logic | SPARQL |
| Provenance query | Layer inspection | SPARQL |

Measure:

- Query latency.
- Throughput.
- Query complexity.
- Implementation effort, where meaningful.

Do not assume that KG queries will be faster than native USD API operations. For simple structural traversal, the USD API may be more efficient.

The stronger research argument is that the KG provides semantic interoperability, cross-asset querying, explicit domain relationships, provenance, and validation capabilities.

### 7.8 Incremental Synchronization

Evaluate how efficiently the system handles USD modifications.

Metrics:

- Update correctness.
- Synchronization latency.
- Number of affected triples.
- Number of unnecessary updates.
- Consistency after repeated modifications.

### 7.9 Scalability

Use larger datasets, particularly the large-scale IFC collection, to evaluate:

- Graph construction throughput.
- Memory consumption.
- RDF triple count.
- Storage requirements.
- Query latency as the graph grows.
- Incremental update performance.

---

## 8. Suggested Experimental Scenarios

The following experiments can form a coherent research evaluation.

| Experiment | Dataset | Objective |
|---|---|---|
| E1: USD-to-RDF Mapping | NVIDIA USD Assets | Test extraction of USD structural information |
| E2: Semantic Scene Graph Construction | 3DSSG / 3RScan | Test semantic entity and relationship representation |
| E3: Ontology Alignment | buildingSMART IFC | Test mapping between IFC, USD, and RDF concepts |
| E4: Cross-Scene Semantic Querying | NVIDIA USD Assets + 3DSSG | Test semantic queries across heterogeneous scenes |
| E5: Provenance-Aware Mapping | NVIDIA USD Assets | Test source-layer and composition provenance |
| E6: Semantic Validation | IFC + generated RDF | Test OWL/SHACL-based semantic constraints |
| E7: Incremental Synchronization | Modified USD scenes | Test consistency after scene updates |
| E8: Scalability | Large-scale IFC Models | Test graph construction and query performance |

A reasonable implementation sequence is:

1. E1 — establish the USD extraction pipeline.
2. E2 — establish semantic graph construction and evaluation.
3. E3 — introduce domain ontology alignment.
4. E5 and E6 — investigate provenance and validation.
5. E4 — evaluate cross-scene semantic querying.
6. E7 and E8 — evaluate synchronization and scalability.

---

## 9. Suggested Technical Stack

A possible implementation stack is:

| Component | Suggested technology | Purpose |
|---|---|---|
| 3D scene representation | OpenUSD | Scene storage and composition |
| USD processing | OpenUSD Python API | Stage and Prim traversal |
| BIM processing | IfcOpenShell | IFC parsing and conversion |
| RDF generation | RDFLib | RDF graph construction |
| Knowledge graph storage | GraphDB or Apache Jena Fuseki | RDF storage and SPARQL |
| Ontology modeling | OWL / Protégé | Semantic modeling |
| Constraint validation | SHACL / pySHACL | Graph validation |
| Data mapping | Python scripts / custom mapping layer | USD-to-RDF and IFC-to-RDF mapping |
| Experiment tracking | MLflow or structured CSV logs | Performance and evaluation records |

A simple prototype can initially use:

```text
OpenUSD
   |
   v
Python Extraction Scripts
   |
   v
RDFLib
   |
   v
GraphDB
   |
   v
SPARQL + SHACL
```

For a larger implementation, it is worth separating the extraction, mapping, identity resolution, synchronization, and query services.

---

## 10. Important Research Considerations

### 10.1 Do Not Treat the KG as a Replacement for USD

USD already provides a powerful representation for hierarchical scene descriptions and composition.

The KG should provide additional semantic capabilities rather than duplicate all USD information without a clear purpose.

A useful design principle is:

- Keep geometry and large numerical data in USD.
- Keep semantic entities and domain relationships in the KG.
- Maintain explicit links between KG entities and USD objects.
- Preserve provenance so that graph facts can be traced to their sources.

### 10.2 Distinguish Authored Layers from Composed Stages

This is one of the most important OpenUSD-specific research challenges.

USD supports composition through layers, references, payloads, variants, and other composition mechanisms.

A property visible on a composed stage may originate from a different layer than the layer containing the prim's apparent location.

Therefore, the KG should ideally distinguish:

- The authored representation.
- The composed representation.
- The source layer.
- The composition dependency.
- The resolved value.

A KG that only records the final composed scene may lose important information about where a fact originated and how it was introduced.

### 10.3 Avoid Using Prim Paths as Permanent Semantic Identities

Prim paths are useful for locating objects in a particular USD stage, but they should not automatically be treated as persistent semantic identifiers.

Scene restructuring can change paths without changing the identity of the real-world object.

Consider separating:

```text
Semantic Entity URI
        |
        +---- USD Prim Path
        |
        +---- USD Layer
        |
        +---- Asset Identifier
        |
        +---- External Domain Identifier
```

This separation will be particularly important when integrating multiple USD assets or IFC models.

### 10.4 Separate Explicit and Inferred Relationships

Not all semantic relationships are directly represented in USD.

For example:

- `hasChild` may be derived from the USD hierarchy.
- `referencesLayer` may be derived from composition metadata.
- `locatedIn` may be derived from spatial containment.
- `supports` may be inferred from geometry or semantic annotations.

The KG should record whether a relationship is:

- Explicitly authored.
- Imported from an external dataset.
- Derived through a rule.
- Predicted by a machine-learning model.
- Manually asserted.

This distinction is important for evaluating the reliability of the generated graph.

### 10.5 Define the Added Value of the Knowledge Graph

A convincing experiment should demonstrate capabilities that are difficult to achieve with USD alone.

For example:

1. Cross-scene semantic querying.
2. Integration of USD with IFC and other external data sources.
3. Stable semantic identities across different scene representations.
4. Explicit domain-level relationships.
5. Provenance-aware semantic information.
6. Ontology-based validation.
7. Semantic retrieval for downstream applications and agents.

The research should not rely solely on showing that USD data can be converted into RDF. The more important question is what additional semantic capabilities the KG enables.

---

## 11. Final Recommendations

For an initial prototype, I recommend starting with three complementary resources:

| Priority | Dataset | Role |
|---|---|---|
| 1 | NVIDIA OpenUSD Assets | Actual OpenUSD integration and structural extraction |
| 2 | 3DSSG + 3RScan | Semantic graph construction and relationship evaluation |
| 3 | buildingSMART IFC Samples | Domain ontology alignment and semantic interoperability |

After the initial prototype works, introduce the large-scale IFC dataset to evaluate scalability.

The recommended research workflow is:

```text
                 NVIDIA USD Assets
                         |
                         v
                 USD Extraction
                         |
                         v
                  USD-to-RDF
                         |
                         v
                  Knowledge Graph
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
         Semantic    Provenance   Validation
         Mapping      Tracking      (SHACL)
             |           |           |
             +-----------+-----------+
                         |
                         v
                 Experimental Evaluation
                         ^
                         |
                  3DSSG / 3RScan
                Semantic Ground Truth
                         |
                         v
                  IFC / BIM Data
                Ontology Alignment
```

The most important distinction is that **3DSSG provides a useful semantic graph benchmark, while NVIDIA's assets provide the actual OpenUSD integration environment**. Neither dataset alone is sufficient to evaluate the entire architecture.

For a research prototype, this combination offers a practical balance between OpenUSD-specific engineering, semantic knowledge representation, and measurable experimental evaluation.

---

## 12. References and Dataset Links

### OpenUSD

1. [NVIDIA Omniverse USD Downloadable Asset Packs](https://docs.omniverse.nvidia.com/usd/latest/usd_content_samples/downloadable_packs.html)
2. [ASWF / USD Working Group Sample Assets](https://github.com/Autodesk/openusd-assets)
3. [NVIDIA USD Example Datasets](https://developer.nvidia.com/usd)

### BIM / IFC

4. [buildingSMART IFC Technical Resources](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)
5. [A Large-Scale Dataset of IFC-Compliant Building Models — Zenodo](https://doi.org/10.5281/zenodo.20788324)
6. [IFCNet](https://github.com/IfcOpenShell/IfcNet)
7. [Awesome BIM Datasets](https://github.com/ConstructionAutomation/Awesome-BIM-Datasets)

### 3D Scene Understanding

8. [3D-FRONT](https://tianchi.aliyun.com/specials/promotion/alibaba-3d-scene-dataset)
9. [SG-FRONT](https://github.com/3D-FRONT/SG-FRONT)
10. [ScanNet](https://www.scan-net.org/)
11. [ScanNet++](https://scannetpp.mlsg.cit.tum.de/)
12. [HM3DSem](https://aihabitat.org/datasets/hm3d-semantics/)

### 3D Scene Graphs

13. [3DSSG Project](https://3dssg.github.io/)
14. [3DSSG GitHub Repository](https://github.com/ShunChengWu/3DSSG)

---

*Note: Dataset availability, download procedures, licenses, and release statistics may change. Verify the current terms and documentation before using a dataset in published research.*