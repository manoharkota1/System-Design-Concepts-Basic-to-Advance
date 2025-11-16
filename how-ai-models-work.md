# How AI Models Work

Let's talk about how AI models work.

You can think of them like super intelligent, general purpose API endpoints. Just like you would integrate the Stripe API to handle payments, or Twilio to send text messages, you can call an AI model to solve a variety of tasks.

The biggest difference is **you are not guaranteed to get the same results every time**.

## Deterministic vs. probabilistic

Traditional software is deterministic. Given some input, if you run the program again, you will get the same output. A developer has explicitly written code to handle the paved path.

AI models are not this way. They are probabilistic. This means there are many different paths the model might take given the same input.

## In practice

Real-world usage mixes deterministic guardrails with probabilistic exploration. Design prompts that welcome creative variance while still guiding the model toward the outcome you can safely ship.

### Key Concepts

- **Neural Networks**: The foundation of modern AI
- **Training Data**: Models learn patterns from vast datasets
- **Inference**: Running the model to get predictions
- **Parameters**: The learned weights that define model behavior
