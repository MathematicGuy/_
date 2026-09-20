### Why ?  
**Small Chunk Fail limitation (pros/cons)** - small chunk isolate key information but suffer from servere context blindness. When a question have multiple constraint 1 single correct information is not enough. 

**Large Chunk Fail limitation (pros/cons)** - large chunk retain broader context, constraint and supporting evidences/facts but suffer from noise and context dilution the larger it get. 

-> **Conclusion:** Choosing fixed chunk size guarantees suboptimal retrieval across varies query. 
-> **Proposed Solution:**  Chunk size should be dynamic, what if we have multiple database for multiple chunk size from small to large and retrieve the best Chunk from each of them ->    

+ ! Large Chunk have more noise but contain more relavant information (supportive infor beside just the main infor) whereas Small Chunk (ie. often chunk with exact keyword) contain accurate answer but lack of relavant context.
	So why Both ? because Small could contain the Answer but does it contain the information of the previous fixed as well -> Fail when Question become complex. What if you **need multiple correct answers info instead of just 1 and require** multiple constraint to narrow down the information ? -> this is when short chunk fail and Large Chunk Triump.

+ $ Solution -> Use Both by creating 2 database - 1 for Large Chunk and 1 for Small Chunk then use RPR to retrieve, score then re-rank them -> best of both world. 
+ @ RRP at database level instead of just chunk level.
+ ? For Chunk Size of 50 in db1, 100 in db2, 200 tokens in db3, 1000 tokens in db4 -> take the best chunk in each db -> use RRF to get the top-K chunks (so the Top-K chunk could contain chunk of 50 tokens, 200 token and even 1000 tokens at the same time) 
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

