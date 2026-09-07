All async tasks run in parallel - Which ever come first get processed first. 

### 1. High-Level Concept: The Restaurant Analogy

```
BEFORE ASYNC (Synchronous Waiter)
Waiter takes Table 1 order ──► Stays in kitchen waiting for food (10 mins) ──► Delivers to Table 1
                                                                               (Tables 2 & 3 starve)

AFTER ASYNC (Asynchronous Waiter)
Waiter takes Table 1 order ──► Kitchen starts cooking (await)
Waiter takes Table 2 order ──► Kitchen starts cooking (await)
Waiter takes Table 3 order ──► Kitchen starts cooking (await)
  └─► Whichever dish finishes first, the waiter serves immediately!
```

---

### 2. Timeline Comparison: Discovering Tools from 3 MCP Servers

When connecting to `research`, `fetch`, and `filesystem` servers during startup:

#### ❌ BEFORE ASYNC: Synchronous (Blocking)
Each request completely blocks the entire program thread while waiting for I/O over subprocess pipes.

```text
Time ──► 0s       1s       2s       3s       4s       5s       6s
         ┌────────┐
Chatbot  │Research│ (Wait 2s)
Process  └───┬────┘
             ▼
             ┌─────┐
             │Fetch│ (Wait 1.5s)
             └──┬──┘
                ▼
                ┌──────────┐
                │Filesystem│ (Wait 2.5s)
                └────┬─────┘
                     ▼
                 Total startup time: 6.0 seconds ⏳
                 (The CPU is doing 99% idle waiting, UI/CLI is frozen)
```

---

#### AFTER ASYNC: Asynchronous (Event Loop + `await`)
While the chatbot is waiting for the external `research` process to respond, Python yields control back to the event loop to fire off requests to `fetch` and `filesystem` concurrently.

```text
Time ──► 0s       1s       2s       3s
         ┌───────────────┐
Research │═══════════════│──► Response received (2.0s)
         ├───────────┐   │
Fetch    │═══════════│───┼──► Response received (1.5s)
         ├────────────────────┐
Filesys  │════════════════════│──► Response received (2.5s)
         └───────────────┬────┘
                         ▼
             Total startup time: 2.5 seconds ⚡ (Down from 6.0s!)
```

---

### 3. Execution Flow: Inside the Chat Loop

Look at what happens to your chatbot process while calling an external MCP tool:

#### ❌ Before Async (Blocking Function)
```text
Chatbot Thread
      │
      ├─── 1. Sends tool request to Research Server
      │
      ████ [BLOCKED / FROZEN]  <─── Cannot receive user keystrokes
      ████ [BLOCKED / FROZEN]  <─── Cannot process ping / heartbeat
      ████ [BLOCKED / FROZEN]  <─── Cannot handle cancellation or timeouts
      │
      ├─── 2. Research Server replies with data
      │
      └─── 3. Resume execution
```

####  After Async (`await client.call_tool(...)`)
```text
Chatbot Thread (Event Loop)
      │
      ├─── 1. Send JSON-RPC request to subprocess pipe
      │
      ├─── 2. "await" yields control back to Event Loop 
      │       │
      │       ├─── Can check cancellation token / user hit Ctrl+C
      │       ├─── Can log progress or spin a loading spinner
      │       └─── Can stream intermediate tokens or handle another request
      │
      ├─── 3. Pipe triggers I/O notification: "Data is ready!"
      │
      └─── 4. Event loop wakes up coroutine to continue with the result
```

---

### 4. Code Comparison: Before vs. After

#### ❌ Before Async (Synchronous Style)
```python
# Everything is stuck waiting sequentially
def connect_and_discover(server_list):
    for server in server_list:
        client = Client(server)
        tools = client.list_tools()      # 🛑 BLOCKS until server responds
        register_tools(tools)
    # The entire program is frozen during every network / stdio roundtrip
```

####  After Async (In [L6_2026_updated.ipynb](file:///c:/WORK/LIVE-AGENTS/L6_2026_updated.ipynb))
```python
# Non-blocking, cooperatively scheduled
async def connect_and_discover(server_list):
    for server in server_list:
        client = await exit_stack.enter_async_context(Client(server))
        tools = await client.list_tools()  #  YIELDS to event loop while waiting for I/O
        register_tools(tools)
```

----

### AsyncExitStack() - Automatically Tracks Jobs and Exit them automatically/dynamically
+ @ When you turn on a server like MCP server, there are always 2 steps, Startup and Shutdown, `AsyncExitStack()` make sure everything that startup get shutdown completely to prevent resource leak.   
+ ! Without this function, you have to manually track all Jobs (ie. MCP server and multiple resources/sub-process that go with it) and shut them down.
```
AsyncExitStack() is like a Backpack that collects & cleanups jobs dynamically (whatever come in are marked for clean up later)
   ┌─────────────────────────────────┐
   │  [Top]    Client #3 (Filesystem)│
   │           Client #2 (Fetch)     │
   │  [Bottom] Client #1 (Research)  │
   └─────────────────────────────────┘
```

```python
class MCP_ChatBot:
    def __init__(self) -> None:
        self.clients: list[Client] = []
        self.exit_stack = AsyncExitStack()
        self.anthropic = Anthropic()

        self.available_tools: list[ToolDefinition] = []
        self.tool_to_client: dict[str, Client] = {}

    async def connect_to_server(
        self,
        server_name: str,
        server_config: dict[str, Any],
    ) -> None:
        """Launch and connect to one stdio MCP server."""
        try:
            # unpack key from list e.g. ["run", "research_server.py"]
            params = StdioServerParameters(**server_config)
			
            client = await self.exit_stack.enter_async_context(
                Client(params)
            )
            self.clients.append(client)
            response = await client.list_tools()

            print(
                f"\nConnected to {server_name} "
                f"(protocol {client.protocol_version}) with tools:",
                [tool.name for tool in response.tools],
            )

            for tool in response.tools:
                # Tool names exposed to one model must be unique.
                if tool.name in self.tool_to_client:
                    raise ValueError(
                        f"Duplicate MCP tool name {tool.name!r}. "
                        "Namespace or rename colliding tools."
                    )

                self.tool_to_client[tool.name] = client
                self.available_tools.append(
                    {
                        "name": tool.name,
                        "description": tool.description or "",
                        "input_schema": tool.input_schema,
                    }
                )

        except Exception as exc:
            print(f"Failed to connect to {server_name}: {exc}")
```