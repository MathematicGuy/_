MCP server contain 
+ Tools
+ Connected to Relavant Resources
+ Template Prompt (already Context Engineer Prompt, Quality Prompt)
e.g. Web Search MCP - it contain the web resource, well evaluated searching prompt for LLM, Tools to search on the internet.

MCP's 2026-07-28 spec makes the entire protocol **stateless**, **deleting the initialize handshake and the Mcp-Session-Id header that pinned clients to one server.** That fixes round robin load balancing, removes the Redis instance holding your sessions, and lets a server scale to zero on Workers or Cloud Run. It also brings new Mcp-Method and Mcp-Name HTTP headers, ttlMs and cacheScope caching hints, elicitation rebuilt as Multi Round-Trip Requests, and Tasks graduating from experimental into an official extension.

### Why MCP ?
Note: **Tools ~ F**

1. MCP basically API/Tools Wrapper with context -> with Context, **Model can flexibly choose which API to called**.  
2. **Standardize API/Tools Connector** -> Don't need to rewrite connector function for diff AI Provider (ie. Codex, Claude, etc..), like a USB-C port.![[Pasted image 20260904153050.png|560]]
3. **Secrity Reason: Secret stay inside MCP Server**
