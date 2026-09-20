### Why ?  
**Small Chunk Fail limitation (pros/cons)** - small chunk isolate key information but suffer from servere context blindness. When a question have multiple constraint 1 single correct information is not enough. 

**Large Chunk Fail limitation (pros/cons)** - large chunk retain broader context, constraint and supporting evidences/facts but suffer from noise and context dilution the larger it get. 

-> **Conclusion:** Choosing fixed chunk size guarantees suboptimal retrieval across varies query. 
+ ! Large Chunk have more noise but contain more relavant information (supportive infor beside just the main infor) whereas Small Chunk (ie. often chunk with exact keyword) contain accurate answer but lack of relavant context.
+ ? So why Both ? because small chunks fail when Question become too complex when you **need multiple info (ie. multiple answer and constraint) to narrow down the rootcause information ?** -> you need both precise context from small chunks and broade context of large chunks.  

-> **Proposed Solution:**  Chunk size should be dynamic, what if we queries the best Chunks from multiple chunk-size database -> RRP to rerank every best chunks  -> **Final top-K Chunks contain both small and large chunks, not fixed.**   
+ @ RRP at database level instead of just chunk level -> x2-5 Cost the Chunks you Embed and Store + More parallel queries at retrieval -> Increase Accuracy. 
```
[User Query]
     │
     ├───> Query DB_50   ──> Top Chunk A_50   ──> Maps to Document ID: Doc_101 (Rank 1)
     ├───> Query DB_200  ──> Top Chunk B_200  ──> Maps to Document ID: Doc_101 (Rank 2)
     └───> Query DB_1000 ──> Top Chunk C_1000 ──> Maps to Document ID: Doc_205 (Rank 1)
                                                       │
                                                       ▼
                                         [RRF at the Document Level]
                                          Doc_101 gets votes from DB_50 & DB_200
                                                       │
                                                       ▼
                                     [Return Top-K Parent Documents to LLM]
```
1. **Step 1 (Parallel Retrieval):** Retrieve the top chunks independently from each index ($DB_{50}$, $DB_{100}$, $DB_{200}$, $DB_{1000}$).
2. **Step 2 (Document Resolution):** Map every retrieved chunk back to its original **Document ID** (or section ID).
3. **Step 3 (Document-Level RRF):** The rankings across the $N$ databases vote on the **Document ID**, not the raw chunk:    
4. $$S(\text{Doc}) = \sum_{w \in \text{databases}} \frac{1}{k + r_w(\text{Doc})}$$
5. **Step 4 (Delivery to LLM):** The system returns the winning **parent document** (or a uniform parent passage) to the LLM.
-> Each chunk size acts as a multi-resolution lens to detect whether a document is relevant, while passing a clean, complete context block to the generator.
![[Pasted image 20260920161454.png|630]]


### Technical Terms
**Semantic Dilution** - when chunks is too large so key information is blended/diluted with unrelated information -> Chunks/Embeddings/Language Representation **lose clarity**
+ @ Text chunks mix relevant facts with noise,
+ ! Contain more Noise 
+ $ Large Chunk give Broader Context
-> Use **Confusion Matrix to visualize the relationship between Noise and Chunk Size** with Precision & Recall & F1 Score. 

In retrieval systems, true positives (TP), false positives (FP), and false negatives (FN) are defined per **retrieved chunk/document relative to query relevance:**
- **Precision ($\frac{\text{TP}}{\text{TP} + \text{FP}}$):** Out of all text retrieved, how much is actually relevant ?
- **Recall ($\frac{\text{TP}}{\text{TP} + \text{FN}}$):** Out of all relevant facts in the corpus, how many did we capture ?
+ **F1 Score:** find Equilebrium point for Precision and Recall balance **(Value that balance Relavance to Chunk Size)**
	-> Enough context for the answer to be coherent, with minimal off-topic bloat.


Here is how chunk size directly affects those metrics:
```ad-example
**Chunk Size: Small** (e.g., 50 tokens)
├── High Precision (Low Noise): Chunks contain dense, exact matches; little off-topic text is retrieved.
└── Low Recall (High False Negatives): Missing surrounding context, cross-sentence dependencies, or multi-hop facts.

**Chunk Size: Large** (e.g., 1,000 tokens)
├── High Recall (Low False Negatives): The entire context and background are captured in one pass.
└── Low Precision (High Noise / Semantic Dilution): Contains excess irrelevant text; embedding dilution can also cause top-K ranking to miss the target entirely.
```

### Multi-Scale Indexing
Pick the best chunk from the Database (each database have a different chunk size N)
![[Pasted image 20260920161903.png|831]]

**We can't know the right size at query time. So stop choosing.** ![[Pasted image 20260920162101.png]]
+ ! Prior work like Contextual Retrieval, Late Chunking RAPTOR still relied on picking a base chunk size. 
+ @ solution **Multi-scale indexing**, why commit to 1 when we could commit to several. (e.g., one index with 50-token chunks, another with 200 tokens, another with 1,000 tokens)
![[Pasted image 20260920161454.png|630]]

Retrieval = Voting -> Every **chunk votes for its parent document.**
Aggregate votes across all sizes > Reciprocal Rank Fusion.

Why Voting by Rank but not Retrieval Score - because similarity is not comparible ![[Pasted image 20260920162506.png]]

