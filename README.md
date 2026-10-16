# Topology-Property-Pipeline

# Polymer Network Topology–Property Analysis

This repository presents a computational workflow for following the formation of a thermoset network during molecular-dynamics curing simulations. The analysis converts reaction-resolved LAMMPS structures into molecular graphs, measures how connectivity develops with conversion, and relates the resulting network descriptors to simulated thermophysical properties.

The complete implementation is maintained privately. This public repository documents the scientific methodology, representative results, and interpretation framework.

## Project Motivation

Degree of conversion alone does not fully describe a cured polymer network. Two systems at similar conversion can differ in connectivity, loop formation, dangling material, spatial heterogeneity, and participation of individual formulation components. Those differences can influence density, shrinkage, glass-transition behavior, thermal expansion, and mechanical response.

This project therefore treats each curing snapshot as both:

- an atomistic LAMMPS structure, and
- a molecular network in which original molecules are nodes and newly formed intermolecular covalent bonds are edges.

The resulting graph representation makes it possible to monitor the transition from disconnected monomers to a connected, cycle-rich network and to test which structural descriptors provide information beyond conversion alone.


The framework is not conceptually restricted to this formulation. Species definitions, molecular weights, replicate identifiers, and snapshot locations are supplied separately for each analysis.

## Analysis Workflow

```mermaid
flowchart LR
    A[Uncured LAMMPS structure] --> B[Identify original molecules]
    C[Conversion-resolved curing snapshots] --> D[Compare bond lists]
    B --> D
    D --> E[Detect new intermolecular links]
    E --> F[Construct molecular multigraph]
    F --> G[Connected components]
    F --> H[2-core and dangling material]
    F --> I[Cycle and link statistics]
    F --> J[Spatial heterogeneity]
    G --> K[Topology descriptor table]
    H --> K
    I --> K
    J --> K
    L[Simulated material properties] --> M[Conversion matching]
    K --> M
    M --> N[Topology–property models]
    N --> O[Replicate statistics and figures]
```

### Processing stages

1. **Establish the molecular reference.** The uncured data file defines the original molecules and their atom membership.
2. **Read conversion snapshots.** Each saved LAMMPS structure represents a stage of the curing trajectory.
3. **Detect reaction-created bonds.** Bonds present in a cured snapshot but absent from the uncured reference are identified.
4. **Separate link classes.** New intermolecular bonds form the molecular network; newly formed intramolecular bonds are recorded separately.
5. **Construct the graph.** Each original molecule becomes a node. Every new intermolecular covalent bond becomes an edge. Parallel edges are retained because multiple bonds may connect the same pair of molecules.
6. **Calculate topology descriptors.** Connected components, component incorporation, cycle rank, 2-core structure, dangling fractions, link chemistry, and spatial statistics are evaluated at each conversion.
7. **Merge topology and property data.** Property observations are paired with the closest compatible conversion snapshot within a prescribed tolerance.
8. **Compare statistical models.** Conversion-only baselines are compared with models containing topology descriptors.
9. **Aggregate replicates.** Independent trajectories are processed separately before calculating cross-replicate means and sample standard deviations.

## Molecular-Graph Representation


## Principal Descriptors

| Descriptor | Interpretation |
|---|---|
| Largest-component number fraction | Fraction of original molecules belonging to the largest connected molecular cluster |
| Largest-component mass fraction | Fraction of total molecular mass contained in that cluster |
| Component incorporation | Fraction of each chemical species incorporated into the largest cluster |
| 2-core incorporation | Fraction of each species retained in the iteratively pruned network backbone |
| Dangling fraction | Portion of the largest cluster excluded from the 2-core |
| Cycle rank | Number of independent cycles in the molecular graph |
| Core-link density | Number of links within the 2-core divided by simulation-box volume |
| Link-pair composition | Chemical identity distribution of newly formed intermolecular links |
| Spatial link heterogeneity | Coefficient of variation of link counts over a three-dimensional voxel grid |
| Cluster-merging link | A new edge that joins two previously disconnected molecular components |
| Cycle-forming link | A new edge whose endpoints are already connected by another graph path |


## Topology–Property Modeling Strategy

The property-analysis stage compares a conversion-only baseline with models that add one network descriptor. Candidate variables include core-link density, cycle rank, dangling fraction, 2-core mass fraction, and spatial link heterogeneity.

For multiple independent trajectories, the intended evaluation uses:

- a replicate-level random intercept when supported by the available data,
- leave-one-replicate-out validation,
- training and validation error reported separately, and
- descriptor selection based on validation performance rather than visual correlation alone.

Single-trajectory results are treated as exploratory because conversion snapshots from one trajectory are temporally related and are not independent experimental replicates.

## Reproducibility Principles

The analysis was designed around the following practices:

- Preserve the uncured structure as the bond-reference state.
- Process each replicate independently before aggregation.
- Use consistent species definitions and atom-to-molecule mappings.
- Retain parallel molecular links instead of reducing the network to a simple graph.
- Record intramolecular and intermolecular bond formation separately.
- Keep topology tables in a machine-readable format.
- Report the matching tolerance used to pair topology and property observations.
- Carry replicate variability into plotted uncertainty.
- Avoid treating snapshots from a single trajectory as independent samples.

## Tools and Methods

- **LAMMPS** for molecular-dynamics and curing simulations
- **Python** for LAMMPS data parsing, bond comparison, graph construction, and descriptor calculation
- **R** for replicate statistics, statistical modeling, and scientific visualization
- **Graph theory** for component, 2-core, cycle-rank, and link-classification analysis

## Source-Code Availability

The complete executable pipeline is maintained in a private repository. Access may be considered for academic collaboration, reproducibility review, or research partnership upon reasonable request.

The figures and documentation in this repository are provided to communicate the scientific workflow and representative outcomes. Public visibility does not grant permission to reuse the underlying implementation.

