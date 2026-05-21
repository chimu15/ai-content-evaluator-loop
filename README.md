# ai-content-evaluator-loop
A self-correcting, multi-agent workflow built in n8n that uses LLM-as-a-judge to evaluate and rewrite text until it meets strict quality standards.
The Problem: Single-prompt AI generations are often lazy, robotic, or off-brand.
The Solution: A multi-agent evaluation loop (Agentic workflow) built in n8n that forces the AI to grade and rewrite its own work until it meets a strict corporate standard.
The Architecture: * Trigger: Webhook (Intake)
Node 1: Gemini AI (The Writer)
Node 2: Gemini AI (The Critic/Eval)
Logic Gate: The Judge (>= 8 score routing)
Output: Gmail API (Delivery)
What I Learned / Trade-offs: Mention the infinite loop constraint you ran into and how you managed state/data flow between nodes. 
