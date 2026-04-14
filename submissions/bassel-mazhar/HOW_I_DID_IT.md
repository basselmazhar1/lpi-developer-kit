Overview

For Level 3, I built an AI agent that connects to the LPI MCP server, calls multiple tools, and generates clear, explainable answers using a local LLM (Ollama).

The goal was a simple end-to-end flow:
user query → tool calls → processed results → structured answer with sources.

What I Did

1. Set up the LPI sandbox
Installed dependencies and verified everything worked:

npm install
npm run build
npm run test-client

2. Tried the example agent
Started with:

python examples/agent.py "What is SMILE?"

It didn’t work out-of-the-box, so I debugged it.

3. Fixed the path issue
The agent used the wrong path to start the server:

["node", "dist/src/index.js"]

Since it runs from examples/, I changed it to:

["node", "../dist/src/index.js"]

This fixed the BrokenPipe error.

4. Improved subprocess reliability
The agent was writing to stdin without checking if the process was alive, which caused crashes. Fixing the path reduced most of these issues.

5. Set up Ollama (local LLM)

ollama serve
ollama pull qwen2.5:1.5b

Tested it with a curl request to confirm it worked.

6. Fixed Ollama API integration
Used the correct config:

OLLAMA_URL = "http://127.0.0.1:11434/api/generate"

Request:

{
  "model": OLLAMA_MODEL,
  "prompt": prompt,
  "stream": False
}

Response:

resp.json().get("response")

7. Final result
Running:

python3 agent.py "What is SMILE?"

The agent successfully:

called multiple tools
combined results
generated a clear answer
included provenance
What I Learned
How MCP tool-calling works
How to integrate Ollama locally
Debugging Python ↔ Node subprocess issues
Importance of correct file paths
Handling API mismatches
Challenges
Wrong relative path
BrokenPipe errors
Ollama setup issues
API differences across versions
Conclusion

After fixing a few issues in the example code, I ended up with a fully working agent that meets all Level 3 requirements.
