**Definition**

**Heterogeneous Information Network.** A HIN is defined as a directed graph G = (V, E) with an entity type mapping function ϕ : V → A and a link type mapping function φ : E → R. A and R denote the sets of predefined entity and link types, where |A| + |R| > 2.

In HIN, **network schema** is proposed to describe the meta structure of a network, which illustrates the object types and their interaction relations.

**Meta-path.** A meta-path ρ is defined as a path in the form of A1 R1−−→ A2R2−−→ · · ·Rl−−→ Al+1 (abbreviated as A1A2 · · ·Al+1), which describes a composite relation R1 ◦R2 ◦ · · · ◦Rl between object A1 and Al+1, where “ ◦ ” denotes the composition operator on relations.

一个元路径ρ可能包含多个特定的路径实例，一个路径实例可以被表示为**p**

As we have illustrated above, the **implicit feedback matrix R** indicates the interaction result between a user and an item. We are particularly interested in the meta-paths that connect a user and an item in HIN, which can reveal semantic context for a user-item interaction.

**Meta-path based Context.** Giving a user u and an item i, the meta-path based context is defined as the aggregate set of path instances under the considered meta-paths connecting the two nodes on the HIN.

**HIN based Top-N Recommendation.** Given a heterogeneous information network G with user implicit feedback matrix R, for each user u ∈ U, we aim to recommend a ranked list of items that are of interest to u.