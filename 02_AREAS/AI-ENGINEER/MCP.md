MCP server contain 
+ Tools
+ Connected to Relavant Resources
+ Template Prompt (already Context Engineer Prompt, Quality Prompt)
e.g. Web Search MCP - it contain the web resource, well evaluated searching prompt for LLM, Tools to search on the internet.

**28-7-2026 Update:** MCP's client (AI Agent) and server (MCP) become stateless, specifically the **Session ID Protocol become stateless.** 
**Problem:**
+ **in 2025,** MCP have **sticky sessions and/or shared session stores** -> Request from 1 MCP Server stick to 1 MCP server.
+ **Issue for Load balancer,** so if Server/Instance A is disconnected because of Overload or DDOS, that session ID still get pointing to that Disconnected Server and not Server B.
	Note: Server A,B,C have the same MCP server.
+ **Solution:** move MCP's Server Session ID to Application ID (i.e. browser ID) since a application could have multiple MCP behind it.  This solve, if 1 MCP server get disconnected, other MCP server could take over when using the load balancer. 
```md
Agent 
│ 
│ browser_id=B123 
▼ 
Stateless MCP 
│ 
▼ 
Application 
│ 
└── browser B123
```

**Visualize** calling a MCP Server/Instance pinning the Server Session ID 
![[Pasted image 20260904163339.png|762]] 
[2026-07-28-release-candidate](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/?utm_source=chatgpt.com)
![[Pasted image 20260904163111.png|493]]


### Why MCP ?
Note: **Tools ~ Function (e.g. API function, regular function) that your AI Model can call**
1. MCP basically API/Tool Wrapper with context -> with Context, **Model can flexibly choose which API to called**. ![[Pasted image 20260904153507.png|668]]
2. **Standardize API/Tool Connector** -> **instead of Custom Integration Code for each AI Model** (e.g. Codex, Claude) which is hard to build and maintain/swap models/tools. ![[Pasted image 20260904153050.png|560]]
3. **Security Reason: Since secret stay inside MCP Server, Secret doesn't get expose to Model**

**MCP Architecture** Note that there are Open Source MCP server
![[Pasted image 20260904153630.png|721]]

Custom MCP server allow better Control through 1 single endpoint -> better security overall and more efficient to manage. 
![[Pasted image 20260904153912.png|653]]

### How does MCP work
![[Pasted image 20260904164935.png|713]]

