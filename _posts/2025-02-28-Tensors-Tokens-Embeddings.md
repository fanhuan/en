---
layout: post
title: Tensors, Tokens and Embeddings
categories: [notes]
tags: [ML]
---

Don't let the jargons scare you away is the thing that you need to remind yourself constantly. They are here for a lack of better words. 

# Tensors (张量)

Tensor is actually a pretty fundamental concept.



__Scaling laws__ in the context of natural language processing (NLP) and computer vision refer to the predictable relationships between the __size of a model__ (e.g., number of parameters), the __amount of training data__, and the __model's performance__ (e.g., accuracy, loss, or other metrics). These laws describe how performance improves as you scale up key factors like model size, dataset size, and computational resources. But before we can understand its importance, we need to first understand __power law__.

# Power Law

A **power-law relationship** is a mathematical relationship between two quantities where one quantity varies as a power of the other. In other words, one quantity is proportional to the other raised to an exponent. Mathematically, it is expressed as:

\[
y = k \cdot x^n
\]

Where:
- \( y \) is the dependent variable (e.g., model performance),
- \( x \) is the independent variable (e.g., model size, dataset size, or compute),
- \( k \) is a constant (proportionality factor),
- \( n \) is the exponent (a constant that determines the shape of the relationship).

---

# Key Characteristics of Power-Law Relationships:
1. **Non-linear**: Unlike linear relationships (\( y = mx + b \)), power-law relationships are non-linear. This means that changes in \( x \) lead to disproportionate changes in \( y \).
2. **Scale-invariant**: Power-law relationships appear the same at all scales. If you zoom in or out on the data, the relationship retains its shape.
3. **Heavy-tailed distribution**: In many real-world systems, power-law relationships describe phenomena where small events are common, but large events are rare (e.g., word frequency in language, city sizes, or income distribution).

---

# Examples of Power-Law Relationships:
1. **Natural Language Processing (NLP)**:
   - Model performance (e.g., perplexity or accuracy) often improves as a power-law function of model size, dataset size, or compute. For example:
     \[
     \text{Performance} \propto (\text{Model Size})^n
     \]
   - This means doubling the model size might lead to a less-than-doubling improvement in performance, depending on the exponent \( n \).

2. **Computer Vision**:
   - Image recognition accuracy often scales as a power-law function of the number of training images or model parameters.

3. **Real-World Phenomena**:
   - **Zipf's Law**: In linguistics, the frequency of a word is inversely proportional to its rank in the frequency table (e.g., the most common word appears twice as often as the second most common word).
   - **Pareto Principle (80/20 Rule)**: 80% of outcomes often come from 20% of causes (e.g., 80% of wealth is owned by 20% of the population).
   - **Network Science**: The distribution of connections in many networks (e.g., social networks, the internet) follows a power law, where a few nodes have many connections, and most nodes have few.

---

# Visualizing a Power-Law Relationship:
When plotted on a **log-log scale** (where both axes are logarithmic), a power-law relationship appears as a straight line. This is because taking the logarithm of both sides of the equation \( y = k \cdot x^n \) gives:

\[
\log(y) = \log(k) + n \cdot \log(x)
\]

This is the equation of a straight line with slope \( n \) and intercept \( \log(k) \).

---

# Why Power-Law Relationships Matter in AI:
1. **Predictability**: Power-law relationships allow researchers to predict how performance will improve as they scale up resources (e.g., model size, data, compute).
2. **Optimization**: Understanding power-law scaling helps allocate resources efficiently. For example, if performance improves slowly with larger models, it might be better to invest in more data or better algorithms.
3. **Benchmarking**: Power-law relationships provide a framework for comparing different models and architectures.

---

# Example in AI Scaling:
In OpenAI's research on scaling laws for language models, they found that:
\[
\text{Test Loss} \propto (\text{Model Size})^{-\alpha} \cdot (\text{Dataset Size})^{-\beta} \cdot (\text{Compute})^{-\gamma}
\]
Here, \( \alpha \), \( \beta \), and \( \gamma \) are exponents that describe how performance improves with scaling.

---

In summary, a **power-law relationship** describes how one quantity changes as a power of another. It is a fundamental concept in AI scaling, as well as in many natural and social phenomena.