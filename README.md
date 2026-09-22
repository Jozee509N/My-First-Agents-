# 1st Agent — Simple Chatbot Agents in Python

This notebook (`1st_Agent.ipynb`) builds two small chatbot programs that illustrate a core idea from AI agent theory: the difference between an agent that reacts only to the current input, and an agent that also *remembers* things about the world (or the user) to inform its responses.

---

## Overview

| Cell | Name | Type of Agent | Key Idea |
|------|------|----------------|----------|
| 2 | `chatbot1` | Simple reflex agent | Responds using only the current input — no memory |
| 3 | `chatbot2` | Model-based (memory) agent | Responds using current input **and** stored past information |

---

## Cell 1: `chatbot1` — Simple Reflex Agent

```python
def chatbot1(user_input):
    user_input = user_input.lower()
    if "hello" in user_input:
        return "Hello!"
    elif "fees" in user_input:
        return "Fees balance is Ksh 20,000"
    elif "bye" in user_input:
        return "Goodbye!"
    else:
        return "I don't understand."
```

**What it does:**
- Takes one string, `user_input`.
- Converts it to lowercase with `.lower()` so the matching isn't case-sensitive (e.g. "HELLO" and "hello" behave the same).
- Checks the input against a fixed set of keyword rules using `if / elif / else`, and returns a matching canned response.
- If none of the keywords are found, it falls back to `"I don't understand."`

**Why it's called a "simple reflex agent":**
This matches the classic AI agent pattern: **condition → action**. The function has no concept of what was said before or after — each call is evaluated completely independently. Calling `chatbot1("greetings")` doesn't match any of the `if` conditions (since "greetings" doesn't contain "hello", "fees", or "bye"), so it correctly falls through to `"I don't understand."` — which is exactly what the output cell shows.

**Limitation:** It can't recall context. If you called it twice in a row, it would have no idea the two calls were related.

---

## Cell 2: `chatbot2` — Agent with Memory

```python
memory = {}

def chatbot2(user_input):
    user_input = user_input.lower()

    if "my name is" in user_input:
        name = user_input.replace("my name is", "").strip()
        memory["name"] = name
        return f"Nice to meet you,{name}!"

    elif "what is my name" in user_input:
        if "name" in memory:
            return f"Your name is {memory['name']}."
        else:
            return "I don't know your name yet."

    elif "hello" in user_input:
        if "name" in memory:
            return f"Hello again, {memory['name']}!"
        else:
            return "Hello!"

    elif "bye" in user_input:
        return "Goodbye!"

    else:
        return "I don't understand."
```

**What it does differently:**
- `memory = {}` is a dictionary defined **outside** the function, in the notebook's global scope. Because it's outside the function, it persists between calls — this is what gives the chatbot "memory."
- If the user says something containing `"my name is"`, the code:
  1. Strips out the phrase `"my name is"`, leaving just the name.
  2. Removes extra whitespace with `.strip()`.
  3. Saves it into `memory["name"]`.
  4. Greets the user using that name.
- On a *later* call, if the user asks `"what is my name"`, the function looks up `memory["name"]` and recalls it — even though that information wasn't part of the current input at all.
- Saying `"hello"` behaves differently depending on whether a name has already been stored: a first-time "Hello!" vs. a personalized "Hello again, joseph!"

**Why it's a step up from `chatbot1`:**
This is a basic example of a **model-based agent** — it keeps an internal *model* (here, just a dictionary) of relevant past information, and uses that model, alongside the current input, to decide how to respond. That's a foundational idea behind more advanced conversational agents (chatbots, assistants, etc.), which often maintain much richer memory (full conversation history, user profiles, session state) to produce more contextually relevant answers.

**How the two calls at the bottom work:**
```python
message1 = chatbot2(input("Let's Talk!"))
print(message1)

message2 = chatbot2(input())
print(message2)
```
- `input(...)` pauses execution and waits for the user to type something in the notebook, then passes that typed text into `chatbot2`.
- The first call is expected to be something like `"my name is joseph"`, which stores `"joseph"` in `memory`.
- The second call is expected to be something like `"what is my name"`, which then successfully recalls it — matching the notebook's saved output:
  ```
  Nice to meet you,joseph!
  Your name is joseph.
  ```

---

## A Small Technical Note

Inside `chatbot2`, you'll see lines like:
```python
return f"Your name is {memory["name"]}."
```
Using the **same** quote character (`"`) both to open the f-string and inside the embedded expression works in Python 3.12+ (which relaxed f-string quoting rules), but it will raise a `SyntaxError` on older Python versions. If you run into that error, the safer, more portable fix is to use different quote types, e.g.:
```python
return f"Your name is {memory['name']}."
```

---

## Concepts This Notebook Demonstrates

1. **Simple reflex agents** — decide actions based purely on the current perception (input), using condition-action rules.
2. **Memory / model-based agents** — maintain internal state across interactions, allowing responses to depend on history, not just the present input.
3. **String matching and normalization** — using `.lower()`, `in`, and `.replace()` for basic (rule-based) natural language handling.
4. **Interactive input** — using Python's built-in `input()` to simulate a live conversation inside a notebook.

---

## How to Run

1. Open `1st_Agent.ipynb` in Jupyter Notebook, JupyterLab, or VS Code.
2. Run the cells in order from top to bottom.
3. For the memory agent cell, you'll be prompted twice for input — try:
   - First prompt: `my name is <your name>`
   - Second prompt: `what is my name`
4. Observe how the second chatbot "remembers" what you told it, while the first chatbot has no such ability.

---

## Possible Extensions (Ideas for Next Steps)

- Store more than just a name in `memory` (e.g. preferences, past questions asked).
- Replace the `if/elif` keyword matching with more flexible logic (e.g. regular expressions or a simple intent classifier).
- Add a loop so the chatbot can keep chatting until the user says "bye," instead of only handling two fixed exchanges.
- Compare this rule-based approach to a machine-learning-based approach (e.g. training a text classifier) to illustrate the same "programmer vs. examples" theme from your class notes on traditional programming vs. machine learning.
