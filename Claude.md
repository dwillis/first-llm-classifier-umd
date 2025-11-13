# Interactive LLM Classifier Tutorial with Claude Code

Welcome! This interactive tutorial will guide you through building a large-language model classifier to analyze campaign finance data. We'll pause at important intervals for you to make design decisions and explain your understanding of key concepts.

Your responses will be captured in `Student.md` for your records.

---

## Part 1: Understanding the Mission

**Read the mission documentation first:**
- Open and read `docs/our-mission.md`
- Open and read `docs/llm-wtf.md`

**PAUSE FOR REFLECTION**

Before we continue, I need you to answer these questions in your own words:

1. **Why are we building this?** Explain the George Santos problem and why traditional approaches fall short for analyzing campaign finance data.

2. **What makes LLMs different?** Describe the key advantages of LLMs over traditional machine learning approaches. What's the "big deal" about not needing training data upfront?

3. **Your use case:** Can you think of another dataset or problem in journalism (or your field) where an LLM classifier might be useful?

*I'll wait for your responses before continuing to Part 2...*

---

## Part 2: Environment Setup

Now we'll set up your Python environment and get your API credentials.

**Steps to complete:**

1. **Get your Groq API key** (if you don't have one):
   - Go to https://console.groq.com/playground
   - Create an account (you can use GitHub login)
   - Click "API keys" in the left toolbar
   - Create a new API key and name it
   - Save it somewhere safe

2. **Check your environment:**
   ```bash
   ls _notebooks/
   ```

   You should see:
   - `classify.ipynb` - A working notebook we'll reference
   - `download.ipynb` - Data download scripts
   - Sample CSV files with campaign data

**PAUSE FOR UNDERSTANDING**

Before we start coding, answer these:

1. **API Keys & Ethics:** What are some important ethical considerations when using an LLM API to analyze public data? Think about costs, privacy, and data retention.

2. **Why Groq?** We're using Groq instead of OpenAI or Anthropic. Based on what you've read, why might we choose Groq for this project? (Hint: look at the docs/groq.md if needed)

*I'll wait for your responses before we start coding...*

---

## Part 3: Your First LLM Prompts

Now let's write some Python code! Open the existing notebook at `_notebooks/classify.ipynb` or create a new one.

**Code along with me:**

First, install the necessary packages:
```python
%pip install groq rich ipywidgets pandas retry matplotlib seaborn scikit-learn
```

Import the libraries:
```python
from rich import print
from groq import Groq
```

Set up your API key (replace with your actual key):
```python
api_key = "your-key-here"  # Or use os.environ.get("GROQ_API_KEY")
client = Groq(api_key=api_key)
```

Make your first prompt:
```python
response = client.chat.completions.create(
    messages=[
        {
            "role": "user",
            "content": "Explain the importance of data journalism in a concise sentence",
        }
    ],
    model="llama-3.3-70b-versatile",
)
print(response.choices[0].message.content)
```

**PAUSE FOR EXPERIMENTATION**

Now I want you to experiment:

1. **Try different system prompts:** Modify the code to add a system prompt that gives the LLM a specific personality or role. Try at least two different ones (enthusiastic supporter vs. skeptical critic).

2. **Explain what you learned:** How does the system prompt affect the response? Why is this powerful for classification tasks?

3. **Model comparison:** Try switching to a different model (like "gemma2-9b-it"). What differences do you notice in the responses?

*Share your findings before we move to Part 4...*

---

## Part 4: Structured Responses and Validation

Now we'll move beyond essays to structured classification.

**Code this classifier function:**

```python
def classify_team(name):
    prompt = """You are an AI model trained to classify text.

I will provide the name of a professional sports team.

You will reply with the sports league in which they compete.

Your responses must come from the following list:
- Major League Baseball (MLB)
- National Football League (NFL)
- National Basketball Association (NBA)
"""

    response = client.chat.completions.create(
        messages=[
            {
                "role": "system",
                "content": prompt,
            },
            {
                "role": "user",
                "content": name,
            }
        ],
        model="llama-3.3-70b-versatile",
    )

    answer = response.choices[0].message.content

    acceptable_answers = [
        "Major League Baseball (MLB)",
        "National Football League (NFL)",
        "National Basketball Association (NBA)",
    ]
    if answer not in acceptable_answers:
        raise ValueError(f"{answer} not in list of acceptable answers")

    return answer
```

**Test it:**
```python
team_list = ["Minnesota Twins", "Minnesota Vikings", "Minnesota Timberwolves"]
for team in team_list:
    league = classify_team(team)
    print([team, league])
```

**PAUSE FOR DESIGN DECISION**

Now you face a real design challenge:

1. **What happens with edge cases?** Try running `classify_team("Minnesota Wild")`. It will error. What are THREE different strategies you could use to handle teams not in your categories?

2. **Your choice:** Which strategy would YOU choose and why? Consider the tradeoffs of:
   - Adding an "Other" category
   - Expanding your validation list
   - Returning None or null values
   - Letting the error happen

3. **Temperature setting:** The code doesn't set temperature yet. Should we set `temperature=0` for this task? Why or why not? What does temperature even control?

*Implement your chosen solution and explain your reasoning...*

---

## Part 5: Advanced Prompt Engineering

Let's improve our classifier with few-shot prompting and retry logic.

**Add the retry decorator:**
```python
from retry import retry

@retry(ValueError, tries=2, delay=2)
def classify_team(name):
    # ... your previous code with the "Other" category added ...
```

**PAUSE FOR CONCEPTUAL UNDERSTANDING**

Answer these questions:

1. **Few-shot prompting:** In your own words, explain what "few-shot prompting" is. Look at the examples in the classify.ipynb notebook where we provide example user/assistant exchanges. Why does this work?

2. **When to use it:** Give me two scenarios where few-shot prompting would be CRITICAL and two where it might be overkill.

3. **The retry pattern:** Why do we retry on ValueError specifically? What failure modes does this handle vs. not handle?

*Explain your understanding before we move on...*

---

## Part 6: Bulk Processing with JSON

One-by-one classification is too slow and expensive. Let's batch our requests.

**Study this bulk classifier:**
```python
import json

@retry(ValueError, tries=2, delay=2)
def classify_teams(name_list):
    prompt = """You are an AI model trained to classify text.

I will provide a list of professional sports team names separated by new lines.

You will reply with the sports league in which they compete.

Your responses must come from the following list:
- Major League Baseball (MLB)
- National Football League (NFL)
- National Basketball Association (NBA)

If the team's league is not on the list, you should label them as "Other".

Your answers should be returned as a flat JSON list.

It is very important that the length of JSON list you return is exactly the same as the number of names you receive.

If I were to submit:
"Los Angeles Rams\nLos Angeles Dodgers\nLos Angeles Lakers\nLos Angeles Kings"

You should return the following:
["National Football League (NFL)", "Major League Baseball (MLB)", "National Basketball Association (NBA)", "Other"]
"""

    response = client.chat.completions.create(
        messages=[
            {
                "role": "system",
                "content": prompt,
            },
            {
                "role": "user",
                "content": "Chicago Bears\nChicago Cubs\nChicago Bulls\nChicago Blackhawks",
            },
            {
                "role": "assistant",
                "content": '["National Football League (NFL)", "Major League Baseball (MLB)", "National Basketball Association (NBA)", "Other"]',
            },
            {
                "role": "user",
                "content": "\n".join(name_list),
            }
        ],
        model="llama-3.3-70b-versatile",
        temperature=0,
    )

    answer_str = response.choices[0].message.content
    answer_list = json.loads(answer_str)

    acceptable_answers = [
        "Major League Baseball (MLB)",
        "National Football League (NFL)",
        "National Basketball Association (NBA)",
        "Other",
    ]
    for answer in answer_list:
        if answer not in acceptable_answers:
            raise ValueError(f"{answer} not in list of acceptable answers")

    try:
        assert len(name_list) == len(answer_list)
    except:
        raise ValueError(f"Number of outputs ({len(name_list)}) does not equal the number of inputs ({len(answer_list)})")

    return dict(zip(name_list, answer_list))
```

**PAUSE FOR CRITICAL THINKING**

This is a major architectural shift:

1. **Tradeoffs:** What are the advantages and disadvantages of bulk processing vs. one-at-a-time? Consider:
   - Cost
   - Speed
   - Error handling
   - Debugging
   - Reliability

2. **Design decision - Batch size:** The docs suggest limiting batches to "a few dozen inputs." If you had 10,000 campaign finance records to classify, what batch size would you choose and why?

3. **Failure modes:** What happens if the LLM returns 9 answers for 10 inputs? The code checks for this, but what could CAUSE this to happen? How could you make the system more robust?

*Explain your thinking before we apply this to real data...*

---

## Part 7: Real-World Application - Campaign Finance

Now let's apply everything to the actual campaign finance data!

**Load the data:**
```python
import pandas as pd
df = pd.read_csv("_notebooks/Form460ScheduleESubItem.csv")
df.sample(10)
```

**Look at the data structure.** What do you see?

**PAUSE FOR DOMAIN UNDERSTANDING**

Before we classify this data:

1. **Data exploration:** Look at the sample output. Describe what you see. What makes this data challenging to classify?

2. **Category design:** We're going to classify these payees as Restaurant, Bar, Hotel, or Other. Do you think these are the RIGHT categories for finding newsworthy expenses? What categories would YOU add or change and why?

3. **False positives vs. false negatives:** For investigative journalism, which is worse:
   - Missing a truly suspicious expense (false negative)?
   - Flagging a legitimate expense as suspicious (false positive)?

   How should this affect our classification strategy?

*Share your analysis before we build the classifier...*

---

## Part 8: Building the Payee Classifier

Now adapt the bulk classifier for campaign payees:

```python
@retry(ValueError, tries=2, delay=2)
def classify_payees(name_list):
    prompt = """You are an AI model trained to categorize businesses based on their names.

You will be given a list of business names, each separated by a new line.

Your task is to analyze each name and classify it into one of the following categories: Restaurant, Bar, Hotel, or Other.

It is extremely critical that there is a corresponding category output for each business name provided as an input.

If a business does not clearly fall into Restaurant, Bar, or Hotel categories, you should classify it as "Other".

Even if the type of business is not immediately clear from the name, it is essential that you provide your best guess based on the information available to you. If you can't make a good guess, classify it as Other.

For example, if given the following input:

"Intercontinental Hotel\nPizza Hut\nCheers\nWelsh's Family Restaurant\nKTLA\nDirect Mailing"

Your output should be a JSON list in the following format:

["Hotel", "Restaurant", "Bar", "Restaurant", "Other", "Other"]

Ensure that the number of classifications in your output matches the number of business names in the input.
"""
    response = client.chat.completions.create(
        messages=[
            {
                "role": "system",
                "content": prompt,
            },
            {
                "role": "user",
                "content": "Intercontinental Hotel\nPizza Hut\nCheers\nWelsh's Family Restaurant\nKTLA\nDirect Mailing",
            },
            {
                "role": "assistant",
                "content": '["Hotel", "Restaurant", "Bar", "Restaurant", "Other", "Other"]',
            },
            {
                "role": "user",
                "content": "Subway Sandwiches\nRuth Chris Steakhouse\nPolitical Consulting Co\nThe Lamb's Club",
            },
            {
                "role": "assistant",
                "content": '["Restaurant", "Restaurant", "Other", "Bar"]',
            },
            {
                "role": "user",
                "content": "\n".join(name_list),
            }
        ],
        model="llama-3.3-70b-versatile",
        temperature=0,
    )

    answer_str = response.choices[0].message.content
    answer_list = json.loads(answer_str)

    acceptable_answers = ["Restaurant", "Bar", "Hotel", "Other"]
    for answer in answer_list:
        if answer not in acceptable_answers:
            raise ValueError(f"{answer} not in list of acceptable answers")

    try:
        assert len(name_list) == len(answer_list)
    except:
        raise ValueError(f"Number of outputs ({len(name_list)}) does not equal the number of inputs ({len(answer_list)})")

    return dict(zip(name_list, answer_list))
```

**Test it on a small sample:**
```python
sample_list = list(df.sample(10).payee)
classify_payees(sample_list)
```

**PAUSE FOR PROMPT REFINEMENT**

You've just written your first real-world classifier prompt!

1. **Prompt critique:** Read the prompt carefully. What parts are essential? What parts could be removed? What would YOU add or change?

2. **Few-shot examples:** We provided two few-shot examples. Are these good examples? What makes a good few-shot example? Should we add more?

3. **Run and analyze:** Run the classifier on your sample. Share your results. Did anything surprise you? Any obvious mistakes?

*Share your analysis and any prompt improvements you'd make...*

---

## Part 9: Batch Processing Pipeline

Let's build the pipeline to process larger datasets:

```python
import time
from rich.progress import track

def get_batch_list(li, n=10):
    """Split the provided list into batches of size `n`."""
    batch_list = []
    for i in range(0, len(li), n):
        batch_list.append(li[i : i + n])
    return batch_list

def classify_batches(name_list, batch_size=10, wait=2):
    """Split the provided list of names into batches and classify them one by one."""
    all_results = {}
    batch_list = get_batch_list(name_list, n=batch_size)

    for batch in track(batch_list):
        batch_results = classify_payees(batch)
        all_results.update(batch_results)
        time.sleep(wait)

    return pd.DataFrame(
        all_results.items(),
        columns=["payee", "category"]
    )
```

**Run it on a larger sample:**
```python
bigger_sample = list(df.sample(100).payee)
results_df = classify_batches(bigger_sample)
print(results_df.category.value_counts())
```

**PAUSE FOR ENGINEERING DECISIONS**

Now you're processing real volume:

1. **Rate limiting:** Why do we `time.sleep(wait)` between batches? What could happen if we don't? What wait time would you choose for 10,000 records?

2. **Error recovery:** If the process fails on batch 47 of 100, you lose all progress. How would you modify this code to handle failures gracefully and resume where it left off?

3. **Cost estimation:** Each API call costs money (though Groq is free for now). If you had to pay $0.10 per 1,000 tokens, estimate the cost to classify all ~16,000 payees in the dataset. Show your work.

*Share your answers and any code improvements...*

---

## Part 10: Scientific Evaluation

Now we need to know: **Is our classifier actually good?**

This is where traditional machine learning concepts help us.

**Create a supervised sample:**

You need to manually label some data. The tutorial provides a sample, but let's understand the process:

```python
# Create a sample to label manually
df.sample(250).to_csv("./my_sample.csv", index=False)
```

Open this in Excel/Google Sheets and add a `category` column with the correct labels.

For this tutorial, use the provided sample:
```python
sample_df = pd.read_csv("_notebooks/sample.csv")
sample_df.head()
```

**Split into training and test sets:**
```python
from sklearn.model_selection import train_test_split

training_input, test_input, training_output, test_output = train_test_split(
    sample_df[['payee']],
    sample_df['category'],
    test_size=0.33,
    random_state=42,
)
```

**PAUSE FOR METHODOLOGY**

This is a critical methodological moment:

1. **Training vs. Test:** In your own words, explain why we split the data. What would happen if we tested on the same data we trained on?

2. **The 67/33 split:** Why use 33% for testing? What are the tradeoffs of using more or less test data?

3. **Random seed (42):** Why set `random_state=42`? Why does reproducibility matter in ML evaluation?

4. **Sample size:** We're using 250 labeled examples. Is that enough? How would you decide how many examples to label manually?

*Explain your understanding of the evaluation methodology...*

---

## Part 11: Measuring Performance

Run the classifier on the test set:

```python
llm_results = classify_batches(list(test_input.payee))
```

**Evaluate the results:**
```python
from sklearn.metrics import classification_report, confusion_matrix
import seaborn as sns
import matplotlib.pyplot as plt

print(classification_report(test_output, llm_results.category))
```

You should see something like:
```
              precision    recall  f1-score   support
         Bar       1.00      1.00      1.00         2
       Hotel       0.89      0.80      0.84        10
       Other       0.96      0.96      0.96        57
  Restaurant       0.87      0.93      0.90        14
    accuracy                           0.94        83
```

**Visualize with a confusion matrix:**
```python
conf_mat = confusion_matrix(test_output, llm_results.category, labels=['Restaurant', 'Bar', 'Hotel', 'Other'])
fig, ax = plt.subplots(figsize=(5,5))
sns.heatmap(conf_mat, annot=True, fmt='d', xticklabels=['Restaurant', 'Bar', 'Hotel', 'Other'], yticklabels=['Restaurant', 'Bar', 'Hotel', 'Other'])
plt.ylabel('Actual')
plt.xlabel('Predicted')
plt.show()
```

**PAUSE FOR RESULTS INTERPRETATION**

This is where you demonstrate understanding:

1. **Precision vs. Recall:** Explain the difference in plain English. Use the Restaurant category as an example from your results.

2. **The 94% accuracy:** Is 94% accuracy good enough for journalism? What if it were 80%? What about 99%? Where would you draw the line and why?

3. **Confusion matrix analysis:** Look at your confusion matrix. What patterns do you see? Where does the model make mistakes? What do those mistakes tell you?

4. **Category imbalance:** Notice "Other" has 57 examples but "Bar" only has 2. How does this affect your evaluation? Should you weight the categories differently?

*Share your detailed analysis of the results...*

---

## Part 12: Comparison with Traditional ML

Now let's see how traditional machine learning performs on the same task:

```python
from sklearn.svm import LinearSVC
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.feature_extraction.text import TfidfVectorizer

vectorizer = TfidfVectorizer(
    sublinear_tf=True,
    min_df=5,
    norm='l2',
    encoding='latin-1',
    ngram_range=(1, 3),
)

preprocessor = ColumnTransformer(
    transformers=[('payee', vectorizer, 'payee')],
    sparse_threshold=0,
    remainder='drop'
)

pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', LinearSVC(dual="auto"))
])

model = pipeline.fit(training_input, training_output)
predictions = model.predict(test_input)

print(classification_report(test_output, predictions))
```

**PAUSE FOR COMPARISON**

This is the "wow" moment of the tutorial:

1. **LLM vs. Traditional ML:** Compare the two classification reports. What are the key differences in performance?

2. **Code complexity:** Compare the amount of code and complexity needed for the LLM approach vs. the traditional ML approach. Which is easier to understand and modify?

3. **When to use which:** Can you think of scenarios where traditional ML might still be preferable to LLMs for classification? Consider cost, speed, interpretability, and control.

4. **The "free knowledge":** The LLM performs better without being trained on our specific data. Why? What knowledge does it bring to the task that traditional ML doesn't have?

*Give me your detailed comparison and insights...*

---

## Part 13: Improvement with Few-Shot Training

We can use our manually labeled training data to improve the LLM:

```python
def get_fewshots(training_input, training_output, batch_size=10):
    """Convert training data into few-shot prompt examples"""
    input_batches = get_batch_list(list(training_input.payee), n=batch_size)
    output_batches = get_batch_list(list(training_output), n=batch_size)

    fewshot_list = []
    for i, input_list in enumerate(input_batches):
        fewshot_list.extend([
            {
                "role": "user",
                "content": "\n".join(input_list),
            },
            {
                "role": "assistant",
                "content": json.dumps(output_batches[i])
            }
        ])
    return fewshot_list

fewshot_list = get_fewshots(training_input, training_output)
```

**Modify your classifier to use the few-shots:**
```python
@retry(ValueError, tries=2, delay=2)
def classify_payees(name_list):
    prompt = """..."""  # Same prompt as before

    messages = [{"role": "system", "content": prompt}]
    messages += fewshot_list  # Add the training examples!
    messages += [{"role": "user", "content": "\n".join(name_list)}]

    response = client.chat.completions.create(
        messages=messages,
        model="llama-3.3-70b-versatile",
        temperature=0,
    )

    # ... rest of the validation code ...
    return dict(zip(name_list, answer_list))
```

**Re-run the evaluation:**
```python
llm_results = classify_batches(list(test_input.payee))
print(classification_report(test_output, llm_results.category))
```

**PAUSE FOR ANALYSIS**

Now you're combining approaches:

1. **Did it improve?** Compare the new results to your original LLM results. Did the few-shot training help? By how much?

2. **Diminishing returns:** You're now using supervised data to train the LLM, just like traditional ML. At what point does the LLM approach lose its advantage?

3. **The hybrid approach:** Is this the best of both worlds or an awkward compromise? Explain your thinking.

*Share your results and analysis...*

---

## Part 14: Error Analysis and Iteration

Let's look at where the classifier makes mistakes:

```python
comparison_df = llm_results.merge(
    sample_df,
    on="payee",
    how="inner",
    suffixes=["_llm", "_human"]
)

# Show the errors
errors_df = comparison_df[comparison_df.category_llm != comparison_df.category_human]
print(errors_df)
```

**PAUSE FOR ITERATIVE IMPROVEMENT**

This is your final design challenge:

1. **Pattern analysis:** Look at the errors. Do you see any patterns? Are certain types of names consistently misclassified?

2. **Prompt refinement:** Based on the errors, how would you modify the prompt to fix them? Write out your improved prompt.

3. **The iteration cycle:** In a real project, you would:
   - Analyze errors
   - Refine the prompt
   - Re-test
   - Repeat

   How many iterations would you expect to need? When would you stop?

4. **Edge case handling:** Look at a specific error (like "ANGELE RESTAURANT & BAR" classified as Bar instead of Restaurant). Should you add a rule to the prompt like "If a business name contains both Restaurant and Bar, classify it as Restaurant"? Is this good practice or are we overfitting?

*Share your error analysis and your refined prompt...*

---

## Part 15: Final Reflection and Next Steps

Congratulations! You've built a working LLM classifier for investigative journalism.

**FINAL REFLECTION QUESTIONS:**

1. **The George Santos question:** Go back to the original mission. Could this classifier have helped catch George Santos's suspicious expenses? Why or why not?

2. **Limitations:** What are the key limitations of this approach? What kinds of errors or problems could still slip through?

3. **Scaling up:** If you wanted to run this on ALL California campaign finance data (millions of records), what would you need to change? Consider cost, time, error handling, and data storage.

4. **Your own project:** Describe a specific dataset or problem where you could apply this technique. What would you classify? What categories would you use? What unique challenges would you face?

5. **Ethics and transparency:** If you published a news story based on this classifier's findings, how would you explain your methodology to readers? What would you disclose about the AI's role?

6. **The future:** Where do you think LLM classifiers will be in 5 years? What will be better? What concerns might emerge?

*Take your time with these reflections. This is the most important part.*

---

## Completion

You've completed the interactive tutorial! Your responses have been saved in `Student.md`.

**Optional challenges if you want to go further:**

1. Add new categories (e.g., "Travel", "Consulting", "Media")
2. Try a different LLM model and compare results
3. Build a web interface for the classifier
4. Analyze the full dataset and find potentially newsworthy expenses
5. Compare different prompting strategies quantitatively

**Resources for continued learning:**
- Original tutorial docs in the `docs/` folder
- The working notebook at `_notebooks/classify.ipynb`
- [Groq Documentation](https://console.groq.com/docs)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)

Thank you for your thoughtful engagement with this material!
