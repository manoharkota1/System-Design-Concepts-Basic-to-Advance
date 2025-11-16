# Context

Context is everything you provide to an AI model to help it understand what you want and generate relevant responses.

## Types of context

### 1. System context
Instructions that set the model's behavior and role:
```
You are a helpful coding assistant specialized in Python.
```

### 2. Conversation history
Previous messages in the conversation that maintain continuity.

### 3. Additional information
Documents, code, or data you want the model to reference.

## Context window

The context window is the maximum amount of text (in tokens) that a model can consider at once.

**Common context windows:**
- GPT-3.5: 4K or 16K tokens
- GPT-4: 8K, 32K, or 128K tokens
- Claude: 200K tokens
- Gemini: Up to 1M tokens

## Managing context effectively

### Best practices:

1. **Prioritize relevant information**: Put the most important context first or last
2. **Remove outdated context**: Clean up old conversation turns
3. **Summarize when needed**: Compress long histories into summaries
4. **Use structured formats**: JSON, XML, or markdown for organization
5. **Break up large tasks**: Split into smaller chunks if hitting limits

## Context strategies

### Sliding window
Keep only the most recent N messages, discarding older ones.

### Summarization
Periodically compress conversation history into summaries.

### Retrieval augmented generation (RAG)
Fetch only relevant context from a larger knowledge base.

### Hierarchical context
Organize information in layers of importance.
