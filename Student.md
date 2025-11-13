# LLM Classifier Tutorial - Student Responses

**Student Name:** Student
**Date Started:** 2025-11-13
**Tutorial:** First LLM Classifier for Campaign Finance Analysis

---

## Part 1: Understanding the Mission

### Question 1: Why are we building this?
**The George Santos problem and why traditional approaches fall short:**

[Your response here]

---

### Question 2: What makes LLMs different?
**Key advantages of LLMs over traditional machine learning:**

[Your response here]

---

### Question 3: Your use case
**Another dataset or problem where an LLM classifier might be useful:**

[Your response here]

---

## Part 2: Environment Setup

### Question 1: API Keys & Ethics
**Ethical considerations when using an LLM API to analyze public data:**

[Your response here]

---

### Question 2: Why Groq?
**Why we might choose Groq for this project:**

[Your response here]

---

## Part 3: Your First LLM Prompts

### Question 1: Different system prompts
**System prompts you tried and what happened:**

[Your response here]

---

### Question 2: What you learned
**How system prompts affect responses and why this is powerful:**

[Your response here]

---

### Question 3: Model comparison
**Differences noticed when switching models:**

[Your response here]

---

## Part 4: Structured Responses and Validation

### Question 1: Edge case strategies
**THREE strategies for handling teams not in your categories:**

[Your response here]

---

### Question 2: Your choice
**Which strategy you chose and why (tradeoffs considered):**

[Your response here]

---

### Question 3: Temperature setting
**Should we set temperature=0? Why or why not? What does temperature control?**

[Your response here]

---

## Part 5: Advanced Prompt Engineering

### Question 1: Few-shot prompting
**Explanation of what few-shot prompting is and why it works:**

[Your response here]

---

### Question 2: When to use it
**Two scenarios where few-shot is CRITICAL and two where it's overkill:**

[Your response here]

---

### Question 3: The retry pattern
**Why retry on ValueError specifically? What failure modes does this handle?**

[Your response here]

---

## Part 6: Bulk Processing with JSON

### Question 1: Tradeoffs
**Advantages and disadvantages of bulk vs. one-at-a-time processing:**

[Your response here]

---

### Question 2: Batch size decision
**For 10,000 records, what batch size would you choose and why?**

[Your response here]

---

### Question 3: Failure modes
**What could cause mismatched input/output lengths? How to make it more robust?**

[Your response here]

---

## Part 7: Real-World Application - Campaign Finance

### Question 1: Data exploration
**What you see in the sample data and what makes it challenging:**

[Your response here]

---

### Question 2: Category design
**Are Restaurant/Bar/Hotel/Other the RIGHT categories? What would you change?**

[Your response here]

---

### Question 3: False positives vs. false negatives
**Which is worse for investigative journalism and how should this affect strategy?**

[Your response here]

---

## Part 8: Building the Payee Classifier

### Question 1: Prompt critique
**What's essential, what could be removed, what would you add or change?**

[Your response here]

---

### Question 2: Few-shot examples
**Are the provided examples good? What makes a good few-shot example?**

[Your response here]

---

### Question 3: Run and analyze
**Your sample results - anything surprising or obviously wrong?**

[Your response here]

---

## Part 9: Batch Processing Pipeline

### Question 1: Rate limiting
**Why sleep between batches? What wait time for 10,000 records?**

[Your response here]

---

### Question 2: Error recovery
**How would you modify the code to handle failures gracefully?**

[Your response here]

---

### Question 3: Cost estimation
**Estimate cost to classify all ~16,000 payees at $0.10 per 1,000 tokens:**

[Your response here]

---

## Part 10: Scientific Evaluation

### Question 1: Training vs. Test
**Why we split the data and what would happen if we didn't:**

[Your response here]

---

### Question 2: The 67/33 split
**Tradeoffs of using more or less test data:**

[Your response here]

---

### Question 3: Random seed (42)
**Why set random_state=42? Why does reproducibility matter?**

[Your response here]

---

### Question 4: Sample size
**Is 250 labeled examples enough? How to decide?**

[Your response here]

---

## Part 11: Measuring Performance

### Question 1: Precision vs. Recall
**Explain the difference in plain English using Restaurant as an example:**

[Your response here]

---

### Question 2: The 94% accuracy
**Is this good enough for journalism? Where would you draw the line?**

[Your response here]

---

### Question 3: Confusion matrix analysis
**Patterns you see, where mistakes happen, what they tell you:**

[Your response here]

---

### Question 4: Category imbalance
**How does imbalance affect evaluation? Should you weight categories differently?**

[Your response here]

---

## Part 12: Comparison with Traditional ML

### Question 1: LLM vs. Traditional ML
**Key differences in performance between the two approaches:**

[Your response here]

---

### Question 2: Code complexity
**Comparison of code complexity and ease of understanding:**

[Your response here]

---

### Question 3: When to use which
**Scenarios where traditional ML might be preferable:**

[Your response here]

---

### Question 4: The "free knowledge"
**Why does the LLM perform better without being trained on our data?**

[Your response here]

---

## Part 13: Improvement with Few-Shot Training

### Question 1: Did it improve?
**Comparison of results before and after few-shot training:**

[Your response here]

---

### Question 2: Diminishing returns
**At what point does the LLM approach lose its advantage?**

[Your response here]

---

### Question 3: The hybrid approach
**Best of both worlds or awkward compromise?**

[Your response here]

---

## Part 14: Error Analysis and Iteration

### Question 1: Pattern analysis
**Patterns in the errors you observed:**

[Your response here]

---

### Question 2: Prompt refinement
**Your improved prompt based on error analysis:**

```
[Your refined prompt here]
```

---

### Question 3: The iteration cycle
**How many iterations would you expect? When would you stop?**

[Your response here]

---

### Question 4: Edge case handling
**Should you add specific rules? Is this good practice or overfitting?**

[Your response here]

---

## Part 15: Final Reflection and Next Steps

### Question 1: The George Santos question
**Could this classifier have helped catch his suspicious expenses?**

[Your response here]

---

### Question 2: Limitations
**Key limitations of this approach:**

[Your response here]

---

### Question 3: Scaling up
**What would you need to change to run on millions of records?**

[Your response here]

---

### Question 4: Your own project
**Describe a specific dataset/problem where you could apply this:**

[Your response here]

---

### Question 5: Ethics and transparency
**How would you explain your methodology to readers?**

[Your response here]

---

### Question 6: The future
**Where will LLM classifiers be in 5 years? What concerns might emerge?**

[Your response here]

---

## Additional Notes and Observations

[Add any additional thoughts, code snippets, or insights here]

---

## Completion Checklist

- [ ] Completed all 15 parts of the tutorial
- [ ] Answered all reflection questions
- [ ] Built a working classifier
- [ ] Evaluated performance scientifically
- [ ] Compared with traditional ML
- [ ] Performed error analysis
- [ ] Refined the prompt based on errors

**Date Completed:** ___________

**Final Thoughts:**

[Your final reflections on the tutorial and what you learned]
