### **Definition**

**Heterogeneous Information Network.** A HIN is defined as a directed graph G = (V, E) with an entity type mapping function ϕ : V → A and a link type mapping function φ : E → R. A and R denote the sets of predefined entity and link types, where |A| + |R| > 2.

In HIN, **network schema** is proposed to describe the meta structure of a network, which illustrates the object types and their interaction relations.

**Meta-path.** A meta-path ρ is defined as a path in the form of A1 R1−−→ A2R2−−→ · · ·Rl−−→ Al+1 (abbreviated as A1A2 · · ·Al+1), which describes a composite relation R1 ◦R2 ◦ · · · ◦Rl between object A1 and Al+1, where “ ◦ ” denotes the composition operator on relations.

一个元路径ρ可能包含多个特定的路径实例，一个路径实例可以被表示为**p**

As we have illustrated above, the **implicit feedback matrix R** indicates the interaction result between a user and an item. We are particularly interested in the meta-paths that connect a user and an item in HIN, which can reveal semantic context for a user-item interaction.

**Meta-path based Context.** Giving a user u and an item i, the meta-path based context is defined as the aggregate set of path instances under the considered meta-paths connecting the two nodes on the HIN.

MPbC 表示

**HIN based Top-N Recommendation.** Given a heterogeneous information network G with user implicit feedback matrix R, for each user u ∈ U, we aim to recommend a ranked list of items that are of interest to u.



### **Model**

##### **1.Model Overview**

![1](C:\Users\Dell\Desktop\笔记\论文\Meta-path\1.PNG)

The **meta-path based context** is first modeled into a low-dimensional embedding using a hierarchical neural network.



##### **2.User & Item Embedding**

We set up a lookup layer to transform the one-hot representations of users and items into low-dimensional dense vectors, called embeddings.

Formally, given a user-item pair ⟨u, i⟩, let pu ∈ R|U|×1 and qi ∈ R|I|×1 denote their one-hot representations.

![2](C:\Users\Dell\Desktop\笔记\论文\Meta-path\2.PNG)



##### **3.Characterizing Meta-path based Context for Interaction**

We propose to use a similar pretrain technique to measure the priority degree of each candidate out-going nodes.

The basic idea is to first learn a latent vector for each node with historical user-item interaction records by using traditional matrix factorization method without meta-path information.

Then, we can measure the priority degree by the similarity between the current node and candidate out-going nodes.

(To pretrain the latent factors for all the entities in HIN, we train the feature based matrix factorization framework SVDFeature, adapted to implicit feedback with the pairwise loss in, on all the available historical interaction records.)

预训练的核心思想在于测量先为通过没有元路径的矩阵分解方法每个节点训练历史交互信息记录来学习特征向量，然后我们通过当前节点和候选外部节点的相似度衡量优先级。

We can incorporate the entities from HIN related to an interaction as the context of a training instance. With the learned latent factors, we can compute the pairwise similarities between two consecutive nodes along a path instance, and then average these similarities for ranking the candidate path instances. Finally, given a metapath, we only keep top K path instances with the highest average similarities.

**Path Instance Embedding**

Here, we adopt the commonly used Convolution Neural Network (CNN) to deal with sequences of variable lengths. The structure of CNN consists of a convolution layer (producing new features with the convolution operation) and a max pooling layer.

![3](C:\Users\Dell\Desktop\笔记\论文\Meta-path\3.PNG)

**Xp** denotes the matrix of the path instance p and **Θ** denotes all the related parameters in CNNs

**Xp**是一条路径实例的长度

**Meta-path Embedding**

We further apply the max pooling operation to derive the embedding for a meta-path. Let ![hkp=1](C:\Users\Dell\Desktop\笔记\论文\Meta-path\hkp=1.PNG) denote the embeddings
for the K selected path instances from meta-path ρ.

![cp](C:\Users\Dell\Desktop\笔记\论文\Meta-path\cp.PNG)

**Simple Average Embedding for Meta-path based Context**

We apply the average pooling operation to derive the embedding for modeling the aggregate meta-path based context.

![cui](C:\Users\Dell\Desktop\笔记\论文\Meta-path\cui.PNG)

where **cu→i** is the embedding for meta-path based context and **Mu→i** is the set of the considered meta-paths for the current interaction. In this naive embedding method, each meta-path indeed receives equal attention, and the representation of meta-path based context fully depends on the generated path instances.

**4.Improving Embeddings for Interaction via Co-Attention Mechanism**
**Attention for Meta-path based Context** Since different meta-paths may have different semantics in an interaction, welearn the interaction-specific attention weights over meta-paths conditioned on the involved user and item. Given the user embedding
xu , item embedding yi , the context embedding cρ for a meta-path ρ, we adopt a two-layer architecture to implement the attention

![auip](C:\Users\Dell\Desktop\笔记\论文\Meta-path\auip.PNG)

The final meta-path weights are obtained by normalizing the above attentive scores over all the meta-paths using the softmax function

![softmax](C:\Users\Dell\Desktop\笔记\论文\Meta-path\softmax.PNG)

After we obtain the meta-path attention scores αu,i,ρ , the newembedding for aggregate meta-path context can be given as the following weighted sum:

![attention cui](C:\Users\Dell\Desktop\笔记\论文\Meta-path\attention cui.PNG)

**Attention for Users and Items**

Given a user and an item, the meta-path connecting them provide important interaction context, which is likely to affect the original representations of users and items. Giving original user and item latent embeddings **xu** and **yi** , and the meta-path based context embedding **cu→i** for the interaction between **u** and **i**, we use a single-layer network to compute the attention vectors **βu** and **βi** for user u and item i as：

![bui](C:\Users\Dell\Desktop\笔记\论文\Meta-path\bui.PNG)

Then, the final representations of user and item are computed by using an element-wise product “⊙" with the attention vectors：

![xuyi](C:\Users\Dell\Desktop\笔记\论文\Meta-path\xuyi.PNG)

To our knowledge, few HIN based recommendation methods are able to learn explicit representations for meta-paths, especially in an interaction-specific way.

**5.The Complete Model**

Until now, given an interaction between user u and item i, we have the embeddings for user u, item i and the meta-path connecting hem. We combine the three embedding vectors into a unified epresentation of the current interaction as below:

![xui](C:\Users\Dell\Desktop\笔记\论文\Meta-path\xui.PNG)

Hxu,i encodes the information of an interaction from three aspects: the involved user, the involved item and the corresponding meta-path based context. Following, we feed Hxu,i into a MLP component in order to implement a nonlinear function for modeling complicated interactions

![rui](C:\Users\Dell\Desktop\笔记\论文\Meta-path\rui.PNG)

With the premise that neural network models can learn more abstractive features of data via using a small number of hidden units for higher layers, we empirically implement a tower structure for the MLP component, halving the layer size for each successive higher layer.

Defining a proper objective function for model optimization is a key step for learning a good recommendation model. Traditional
point-wise recommendation models for the rating prediction task usually adopt the squared error loss.

While, in our task, we have only implicit feedback available. Following, we learn the parameters of our model with negative sampling and the objective for an interaction ⟨u, i⟩ can be formulated as follows:

![loss](C:\Users\Dell\Desktop\笔记\论文\Meta-path\loss.PNG)