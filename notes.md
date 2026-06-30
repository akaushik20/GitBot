# RAG Chatbot Evaluation Frameworks - Research Notes

**Date:** 2026-06-29  
**Project:** GitBot - RAG-based GitHub Repository Chatbot  
**Goal:** Find evaluation frameworks to ensure accurate answers and prevent hallucinations

---

## Current Stack
- **Framework:** LangChain
- **LLM:** AWS Bedrock (ChatBedrock)
- **Vector Store:** FAISS
- **Embeddings:** BedrockEmbeddings
- **Use Case:** Answer questions about GitHub repositories

---

## Top Evaluation Frameworks for RAG Systems

### 1. ⭐ RAGAS (Recommended)

**Overview:**
- Most popular framework specifically designed for RAG evaluation
- 14.6k+ GitHub stars
- Recommended by OpenAI, LangChain, and LlamaIndex
- Website: https://www.ragas.io/
- GitHub: https://github.com/explodinggradients/ragas

**Key Metrics:**
1. **Faithfulness** - Detects hallucinations by checking if answers are grounded in retrieved context
2. **Answer Relevancy** - Ensures answers directly address the question
3. **Context Precision** - Measures quality of retrieved context (ranking)
4. **Context Recall** - Measures completeness of retrieval (did it get all relevant info?)

**Why Perfect for GitBot:**
- ✅ Native LangChain integration (we're already using LangChain)
- ✅ RAG-specific metrics (not generic LLM metrics)
- ✅ Faithfulness metric directly prevents hallucinations
- ✅ Synthetic test data generation (can auto-generate eval datasets)
- ✅ Simple integration (~10 lines of code)
- ✅ Industry standard (Fortune 500 companies use it)

**Installation:**
```bash
pip install ragas
```

**Quick Start Example:**
```python
from ragas import evaluate
from ragas.metrics import (
    faithfulness,        # Prevents hallucination
    answer_relevancy,    # Accuracy metric
    context_precision,   # Retrieval quality
    context_recall       # Retrieval completeness
)
from datasets import Dataset

# Create evaluation dataset
dataset = Dataset.from_dict({
    "question": ["How do I use this function?"],
    "answer": [your_chatbot_response],
    "contexts": [retrieved_contexts],
    "ground_truth": [expected_answer]  # optional
})

# Run evaluation
result = evaluate(
    dataset,
    metrics=[faithfulness, answer_relevancy, context_precision, context_recall]
)
```

**Features:**
- Automatic metrics that understand RAG performance
- Synthetic evaluation data generation
- Online monitoring support
- Production-ready
- Git-based workflow support

---

### 2. DeepEval / Confident AI

**Overview:**
- 14k+ GitHub stars
- Open-source framework (DeepEval) + Managed platform (Confident AI)
- Website: https://www.confident-ai.com/
- Docs: https://docs.confident-ai.com/

**Key Features:**
- **LLM Tracing:** Full observability of every LLM call
- **Dataset Auto-Curation:** Turn production traces into test datasets
- **Multi-turn Chat Simulations:** Test conversational flows
- **Red Teaming:** AI security vulnerability testing
- **Team Collaboration:** Product, QA, and Engineering alignment
- **Compliance:** SOC 2, HIPAA, GDPR compliant

**Use Cases:**
- Production monitoring and observability
- Team collaboration on AI quality
- Enterprise deployments requiring compliance
- Continuous evaluation in CI/CD

**When to Use:**
- Need full observability platform (not just metrics)
- Want production monitoring with alerts
- Require team collaboration features
- Enterprise security/compliance requirements

**Installation:**
```bash
pip install deepeval
```

---

### 3. TruLens

**Overview:**
- 3.4k+ GitHub stars
- Created by TruEra (now part of Snowflake)
- OpenTelemetry-based tracing
- Website: https://www.trulens.org/
- GitHub: https://github.com/truera/trulens

**Key Features:**
- **RAG Triad Metrics:**
  1. Context Relevance - Quality of retrieval
  2. Groundedness - Prevents hallucinations
  3. Answer Relevance - Accuracy of responses
- **OpenTelemetry Standard:** Export to Jaeger, Grafana Tempo, Datadog
- **Agentic Evaluations:** 7 specialized metrics for AI agents
- **Batch & Inline Evaluation:** Run alongside app or on existing data
- **MCP Support:** Model Context Protocol integration

**Why Consider:**
- Enterprise-grade observability
- OTEL standard (interoperable with existing tools)
- Strong hallucination detection via "Groundedness" metric
- Works with LangChain and LlamaIndex
- Fine-grained instrumentation

**Supported Providers:**
- OpenAI / Azure OpenAI
- LiteLLM (Anthropic, Cohere, Mistral)
- Google Gemini
- AWS Bedrock ✅ (We're using this!)
- Snowflake Cortex
- HuggingFace

**Installation:**
```bash
pip install trulens-core
pip install trulens trulens-providers-bedrock  # For AWS Bedrock
pip install trulens trulens-apps-langchain     # For LangChain
```

**Quick Example:**
```python
from trulens.core import Metric, Selector

f_context_relevance = Metric(
    name="Context Relevance",
    implementation=provider.context_relevance,
    selectors={
        "input": Selector.select_record_input(),
        "context": Selector.select_context(),
    },
)
```

---

### 4. Promptfoo (Now Part of OpenAI)

**Overview:**
- 300k+ users
- 22.7k+ GitHub stars
- Recently acquired by OpenAI
- Website: https://promptfoo.dev/
- GitHub: https://github.com/promptfoo/promptfoo

**Key Features:**
- **AI Security Testing:** Automated vulnerability detection
- **Red Teaming:** Simulate attacks on your AI system
- **Threat Intelligence:** Real-time from 300k+ user community
- **CI/CD Integration:** Security testing in development workflow

**Security Tests:**
- Direct and indirect prompt injections
- Jailbreaks tailored to your guardrails
- Data and PII leaks
- Business rule violations
- Insecure tool use in agents
- Toxic content generation

**When to Use:**
- Security is a primary concern
- Need prompt injection detection
- Want automated red teaming
- Require PII leak prevention

**Installation:**
```bash
npx promptfoo@latest redteam setup
```

---

## Comparison Matrix

| Framework | Best For | Stars | Hallucination Focus | LangChain Integration | AWS Bedrock Support |
|-----------|----------|-------|---------------------|----------------------|---------------------|
| **RAGAS** | RAG-specific metrics | 14.6k | ✅✅✅ Faithfulness metric | ✅ Native | ✅ Yes |
| **DeepEval/Confident AI** | Full observability platform | 14k | ✅✅ Groundedness | ✅ Yes | ✅ Yes |
| **TruLens** | Enterprise OTEL tracing | 3.4k | ✅✅ Groundedness | ✅ Native | ✅ Yes |
| **Promptfoo** | Security & red teaming | 22.7k | ✅ Injection detection | ✅ Yes | ✅ Yes |

---

## Recommendation for GitBot

### Primary Choice: **RAGAS**

**Reasons:**
1. ✅ Built specifically for RAG systems (not generic LLM eval)
2. ✅ We're using LangChain → Native integration available
3. ✅ **Faithfulness metric** directly prevents hallucinations
4. ✅ Easiest to implement (10 lines of code)
5. ✅ Can generate synthetic test datasets from our repo
6. ✅ Industry standard used by Fortune 500
7. ✅ Active community and regular updates

### Secondary Addition: **TruLens** (for production)

**When to add:**
- Need real-time monitoring dashboard
- Want to track quality over time
- Need to share metrics with stakeholders
- Want OTEL-standard observability

### For Security: **Promptfoo**

**When to add:**
- Chatbot will be public-facing
- Concerned about prompt injection attacks
- Need to test for PII leaks
- Want continuous security testing

---

## Implementation Plan

### Phase 1: Basic Evaluation with RAGAS
1. Install RAGAS
2. Create evaluation dataset (questions about our GitHub repo)
3. Run baseline evaluation
4. Measure: Faithfulness, Answer Relevancy, Context Precision, Context Recall
5. Iterate on prompts/chunking strategy based on results

### Phase 2: Automated Testing
1. Generate synthetic test cases using RAGAS
2. Set up CI/CD evaluation pipeline
3. Define quality thresholds (e.g., Faithfulness > 0.8)
4. Block deployments that fail quality checks

### Phase 3: Production Monitoring (Optional)
1. Add TruLens for real-time tracing
2. Set up dashboard for stakeholders
3. Configure alerts for quality degradation
4. Track metrics over time

### Phase 4: Security (If needed)
1. Run Promptfoo red teaming
2. Test for prompt injections
3. Verify PII handling
4. Document security posture

---

## Key Metrics Explained

### Faithfulness (Most Important for Hallucination Prevention)
- **What it measures:** Are the answers grounded in the retrieved context?
- **How it works:** Checks if every claim in the answer can be traced back to the context
- **Score:** 0.0 to 1.0 (higher is better)
- **Target:** > 0.8 for production

### Answer Relevancy
- **What it measures:** Does the answer actually address the question?
- **How it works:** Semantic similarity between question and answer
- **Score:** 0.0 to 1.0 (higher is better)
- **Target:** > 0.7 for production

### Context Precision
- **What it measures:** Are the most relevant chunks ranked higher?
- **How it works:** Checks if relevant context appears at the top of retrieval results
- **Score:** 0.0 to 1.0 (higher is better)
- **Target:** > 0.6 for production

### Context Recall
- **What it measures:** Did we retrieve all relevant information?
- **How it works:** Compares retrieved context against ground truth
- **Score:** 0.0 to 1.0 (higher is better)
- **Target:** > 0.7 for production

---

## Integration with Current Code

Our current stack in `chatbot.py`:
- ✅ LangChain (ConversationalRetrievalChain)
- ✅ FAISS vector store
- ✅ AWS Bedrock (ChatBedrock)
- ✅ BedrockEmbeddings

**RAGAS can plug in directly** to capture:
- Questions asked
- Retrieved contexts (from FAISS)
- Generated answers (from Bedrock)
- Evaluate all three together

---

## Next Steps

1. **Install RAGAS:**
   ```bash
   pip install ragas
   ```

2. **Create evaluation script** that:
   - Uses existing chatbot
   - Collects sample Q&A pairs
   - Runs RAGAS evaluation
   - Reports metrics

3. **Build test dataset:**
   - Manual: Create 20-30 questions about the repo
   - Automated: Use RAGAS synthetic generation
   - Mix of easy and hard questions

4. **Set quality baseline:**
   - Run initial evaluation
   - Document current scores
   - Identify weak areas

5. **Iterate and improve:**
   - Adjust chunk size if Context Recall is low
   - Refine prompts if Faithfulness is low
   - Tune retrieval if Context Precision is low

---

## Resources

### RAGAS
- Docs: https://docs.ragas.io/
- GitHub: https://github.com/explodinggradients/ragas
- Discord: https://discord.gg/5qGUJ6mh7C

### DeepEval/Confident AI
- Docs: https://docs.confident-ai.com/
- GitHub: https://github.com/confident-ai/deepeval
- Slack: https://join.slack.com/t/confidentaicommunity/

### TruLens
- Docs: https://www.trulens.org/
- GitHub: https://github.com/truera/trulens
- Discourse: https://snowflake.discourse.group/c/ai-research-and-development-community/trulens/97

### Promptfoo
- Docs: https://promptfoo.dev/docs/
- GitHub: https://github.com/promptfoo/promptfoo
- Discord: https://discord.gg/promptfoo

---

## Additional Notes

### Why Hallucination Happens in RAG
1. **Poor Retrieval:** Irrelevant context retrieved
2. **Context Not Used:** LLM ignores provided context
3. **Hallucinated Details:** LLM adds information not in context
4. **Contradictory Context:** Multiple conflicting sources
5. **Incomplete Context:** Missing key information

### How Faithfulness Metric Helps
- **Validation:** Every answer claim is checked against context
- **Transparency:** Shows which parts are hallucinated
- **Actionable:** Pinpoints whether problem is retrieval or generation
- **Continuous:** Can monitor in production

### Best Practices
1. **Start Simple:** Basic metrics first, expand later
2. **Human Validation:** Verify eval metrics align with human judgment
3. **Iterate:** Use metrics to guide improvements
4. **Document:** Track changes and their impact on metrics
5. **Automate:** Move to CI/CD once confident in metrics

---

## Questions to Consider

1. Do we need real-time monitoring, or is batch evaluation enough?
2. Is the chatbot public-facing (security concerns)?
3. Do we need team collaboration features?
4. What's our quality threshold for production deployment?
5. How often will we re-evaluate?

---

## End Notes

**Recommendation:** Start with RAGAS for its RAG-specific metrics and ease of integration with our current LangChain + Bedrock stack. The Faithfulness metric directly addresses our hallucination concerns.

**Timeline:** Can be implemented in 1-2 days for basic evaluation, 1 week for full integration with CI/CD.

**Cost:** RAGAS is open-source and free. Uses existing LLM (Bedrock) for evaluation, so only incremental API costs.
