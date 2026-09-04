MCP server contain 
+ Tools
+ Connected to Relavant Resources
+ Template Prompt (already Context Engineer Prompt, Quality Prompt)
e.g. Web Search MCP - it contain the web resource, well evaluated searching prompt for LLM, Tools to search on the internet.

MCP's 2026-07-28 spec makes the entire protocol **stateless**, **deleting the initialize handshake and the Mcp-Session-Id header that pinned clients to one server.** That fixes round robin load balancing, removes the Redis instance holding your sessions, and lets a server scale to zero on Workers or Cloud Run. It also brings new Mcp-Method and Mcp-Name HTTP headers, ttlMs and cacheScope caching hints, elicitation rebuilt as Multi Round-Trip Requests, and Tasks graduating from experimental into an official extension.

### Why MCP ?
Note: **Tools ~ Function (e.g. API function, regular function) that your AI Model can call**
1. MCP basically API/Tool Wrapper with context -> with Context, **Model can flexibly choose which API to called**. ![[Pasted image 20260904153507.png|668]]
2. **Standardize API/Tool Connector** -> **instead of Custom Integration Code for each AI Model** (e.g. Codex, Claude, etc..) which is hard to build and maintain/swap models/tools , like a USB-C port.![[Pasted image 20260904153050.png|560]]
3. **Security Reason: Since secret stay inside MCP Server, Secret doesn't get expose to Model**

**MCP Architecture** Note that there are Open Source MCP server
![[Pasted image 20260904153630.png|721]]

Custom MCP server allow better Control through 1 single endpoint -> better security overall and more efficient to manage. 
![[Pasted image 20260904153912.png|721]]


