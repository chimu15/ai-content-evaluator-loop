# ai-content-evaluator-loop
A self-correcting, multi-agent workflow built in n8n that uses LLM-as-a-judge to evaluate and rewrite text until it meets strict quality standards.
# Self-Correcting AI Agent Loop (n8n)

A multi-agent workflow built in n8n that utilizes "LLM-as-a-judge" logic to autonomously evaluate, grade, and rewrite text until it meets strict corporate quality standards. 

##  The Problem
Single-prompt AI generation is often unreliable for production environments. Models can output robotic tones, include unwanted formatting (like emojis), or deviate from brand guidelines. Relying on a single pass requires heavy "human-in-the-loop" editing, defeating the purpose of automation.

## The Solution
Instead of a single prompt, this workflow uses an **Agentic Evaluation Loop**. It pits two LLMs against each other: a "Writer" agent and a "Critic" agent. By leveraging programmatic logic gates, the system forces the AI to continuously rewrite its own drafts until the output achieves a passing score based on a strict set of rules. 

## Workflow Architecture
This project is built entirely in **n8n**, utilizing the Google Gemini API for the agentic models.

1. **Intake (Webhook):** Catches raw, messy draft submissions via a web form.
2. **The Writer (AI Agent 1):** Ingests the rough draft and attempts to rewrite it into a highly professional corporate voice.
3. **The Critic (AI Agent 2 - Eval):** Reads the Writer's output and grades it on a strict 1-10 scale based on a custom system rubric (penalizing robotic tone, emojis, and filler words).
4. **The Judge (If Node):** Programmatic logic gate that converts the Critic's string output to a number. 
   * **If Score <= 7:** Routes the data back to the Writer for a complete rewrite.
   * **If Score >= 8:** Approves the text and passes it to delivery.
5. **Delivery (Gmail API):** Emails the final, approved draft directly to the user.

## Product Learnings & Trade-offs
Building this loop highlighted several practical constraints of working with agentic systems:
* **Infinite Loops & State Management:** Passing data backward in a workflow requires careful state management. If the Critic only passes a numerical score back to the Writer, the Writer loses the original source context. The loop must be structured to preserve the `rough_draft` variable across iterations to prevent execution failures.
* **Cost vs. Quality:** While autonomous self-correction raises the quality floor, looping LLMs increases API token usage and latency. In a high-volume production environment, configuring a maximum retry limit (e.g., max 3 loops before routing to a human) would be a necessary guardrail to control costs.
* **Eval Engineering:** AI models naturally output strings. Programmatic nodes (like n8n's "If" node) require numeric data to perform operators (>=). Coercing the LLM output into a strict number using `.toNumber()` was critical to bridging the gap between non-deterministic AI and deterministic workflow logic.

##  How to Use
1. Import the `workflow.json` file into your local or cloud n8n instance.
2. Add your Google Gemini API credentials to both AI Agents.
3. Connect your Gmail credentials to the final Send Message node.
4. Trigger the webhook with a sample text payload.
