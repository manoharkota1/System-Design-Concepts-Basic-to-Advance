# Tokens & Pricing

Understanding tokens is crucial for working with AI models effectively and managing costs.

## What are tokens?

Tokens are the basic units that AI models use to process text. A token can be:
- A word (e.g., "hello")
- Part of a word (e.g., "un-" + "believable")
- A punctuation mark
- A space

### Token examples:

- "Hello world!" = 3 tokens: ["Hello", " world", "!"]
- "ChatGPT is amazing" = 5 tokens
- "I'm learning" = 3 tokens: ["I", "'m", " learning"]

## Why tokens matter

1. **Context limits**: Models have a maximum token limit (e.g., 4K, 8K, 32K, 128K)
2. **Pricing**: Most APIs charge per token used
3. **Performance**: Longer contexts can affect response time

## Pricing models

Different providers charge differently:

- **Input tokens**: Text you send to the model
- **Output tokens**: Text the model generates (usually more expensive)
- **Cached tokens**: Some providers offer discounts for repeated context

### Example pricing (typical):
- Input: $0.003 per 1K tokens
- Output: $0.015 per 1K tokens

## Tips for optimization

- Be concise in your prompts
- Remove unnecessary context
- Use system messages efficiently
- Consider caching for repeated content
- Monitor token usage in your applications
