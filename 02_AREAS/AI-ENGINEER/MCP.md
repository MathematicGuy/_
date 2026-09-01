MCP server contain 
+ Tools
+ Connected to Relavant Resources
+ Template Prompt (already Context Engineer Prompt, Quality Prompt)
e.g. Web Search MCP - it contain the web resource, well evaluated searching prompt for LLM, Tools to search on the internet.

MCP's 2026-07-28 spec makes the entire protocol **stateless**, **deleting the initialize handshake and the Mcp-Session-Id header that pinned clients to one server.** That fixes round robin load balancing, removes the Redis instance holding your sessions, and lets a server scale to zero on Workers or Cloud Run. It also brings new Mcp-Method and Mcp-Name HTTP headers, ttlMs and cacheScope caching hints, elicitation rebuilt as Multi Round-Trip Requests, and Tasks graduating from experimental into an official extension.