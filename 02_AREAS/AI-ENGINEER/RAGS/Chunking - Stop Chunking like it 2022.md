 there is no right chunk size - there are question that needed large piece of chunk size like Q contain 2 or more piece of information, Whereas simple query only require 1. 
 + ! So large chunk doesn't give accurate information while small chunksize doesn't give overall context.
  + $ Solution -> Use Both by creating 2 database - 1 for Large Chunk and 1 for Small Chunk then use RPR to retrieve, score then re-rank them -> best of both world. 
  + @ RRP at database level instead of just chunk level.
![[Pasted image 20260920161454.png|630]]

Pick the best chunk from the Database (each database have a different chunk size N)
![[Pasted image 20260920161903.png]]

**We can't know the right size at query time. So stop choosing.** ![[Pasted image 20260920162101.png]]
+ ! Prior work like Contextual Retrieval, Late Chunking RAPTOR still relied on picking a base chunk size. 
+ @ solution **Multi-scale indexing**, why commit to 1 when we could commit to several. 
![[Pasted image 20260920161454.png|630]]

Retrieval = Voting -> Every **chunk votes for its parent document.**
Aggregate votes across all sizes > Reciprocal Rank Fusion.

Why Voting by Rank but not Retrieval Score - because similarity is not comparible ![[Pasted image 20260920162506.png]]