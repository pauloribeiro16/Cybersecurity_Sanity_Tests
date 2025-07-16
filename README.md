# Cybersecurity Knowledge & Reasoning Assessor for LLMs

## Overview

This project provides a systematic framework for evaluating and benchmarking the capabilities of local Large Language Models (LLMs), accessed via [Ollama](https://ollama.com/), within the specific domain of cybersecurity.

The primary motivation is to move beyond simple Q&A and develop a robust methodology to assess how reliable, consistent, and deep the "knowledge" and "reasoning" abilities of these models are for professional, real-world use cases.

---

## The Evaluation Methodology

The assessment is designed in progressive phases, with each phase increasing in complexity to test different facets of a model's capabilities.

### Phase 1: Factual Knowledge Test (The "Normal" Test)

This initial phase benchmarked a wide range of models against a set of direct questions covering fundamental cybersecurity concepts.

*   **Objective:** To measure the model's ability to recall and state factual knowledge.
*   **Example Prompt:** `What are the five core functions of the NIST Cybersecurity Framework (CSF)?`
*   **Script:** `cybersecurity_sanity_tester.py` (initial version)

### Phase 2: Concept Identification Test (The "Reverse" Test)

The approach was then inverted. Instead of asking for a definition, a practical scenario was provided, and the model had to identify the underlying concept.

*   **Objective:** To measure the model's ability to apply knowledge to a specific context, moving from rote memorization to application.
*   **Example Prompt:** `A web application has a comments section where user input is directly rendered back to the page without being sanitized... What is the common name for this vulnerability and what is its corresponding CWE identifier?`

### Phase 3: Multi-Step Inference & Reasoning Test

This phase narrows the focus to the top-performing models from the previous tests. It uses a set of 9 multi-faceted questions designed to force models to connect concepts from different cybersecurity domains.

*   **Objective:** To measure the model's ability to perform multi-step reasoning, analyze trade-offs, and plan sequences of action.
*   **Key Feature:** Uses a "Chain-of-Thought" system prompt (`"First, think step-by-step..."`) to encourage models to show their reasoning process.
*   **Example Prompt:** `Your company discovers it's vulnerable to Log4Shell (CVE-2021-44228)... Using the NIST CSF: 1) What is one immediate 'Respond' action? 2) What is one immediate 'Protect' action? 3) What fundamental principle of the CIA triad is most at risk?`
*   **Script:** `cybersecurity_inference_tester.py`

---

## Detailed Evaluation Process

A core component of this project is the rigorous and multi-layered evaluation process applied to the model outputs.

### 1. Output Classification & Scoring

Each response from a model is manually reviewed and classified using a simple but effective scoring system:

*   **2 Points (Correct):** The response is factually accurate, complete, and directly addresses all parts of the user prompt.
*   **1 Point (Incomplete / Partially Correct):** The response captures the main idea but is missing key elements, contains minor inaccuracies, or fails to address a sub-question.
*   **0 Points (Incorrect):** The response is factually wrong, irrelevant to the question, or contains a dangerous hallucination.

<img width="1851" height="835" alt="image" src="https://github.com/user-attachments/assets/1b7f4ce2-6c5a-498d-8f64-4733d2d8cb80" />
<img width="1843" height="926" alt="image" src="https://github.com/user-attachments/assets/0fb04dc4-7f94-4d10-a2f7-440c210580f7" />
<img width="1846" height="890" alt="image" src="https://github.com/user-attachments/assets/f13ea925-7d97-4eab-a2a9-ac53904c15a2" />


### 2. Consistency Analysis: Consistency Acrros the 3 Tests Table

Beyond raw accuracy scores, a crucial part of this project is the **consistency analysis**. The same set of tests were run multiple times to answer a vital question: *Can a model be trusted to provide the same correct answer every time?*

The following comparative analysis chart, which visualizes the evaluation of answers between two identical test runs, reveals profound differences in model behavior. A green "Equal" cell means the model's response received the same evaluation in both tests, indicating consistency. A red "Different" cell signifies that the model's response changed enough between runs to alter its score (e.g., from "Correct" to "Incomplete"), exposing a lack of coherence.

<img width="1620" height="869" alt="image" src="https://github.com/user-attachments/assets/8feba078-73ce-447f-8f3e-245dd4e4fcce" />

This analysis allows us to classify the models into distinct behavioral groups:

#### 1. The Consistent Leaders (The "Green Zone")

These models are defined by their reliability.
*   **Prime Examples:** `qwen:8b`, `granite3:8b`, `granite3-dense:8b`.
*   **Behavior:** As the chart shows, these models are overwhelmingly green. They don't just provide correct answers; they provide the *same* high-quality, correct answers reliably across multiple runs. Their responses are structurally similar and contain the necessary information every time.
*   **Implication:** These models exhibit robust and predictable knowledge. They are the only candidates suitable for dependable, professional use where the same query must yield the same correct outcome.

#### 2. The Inconsistent "Turncoats" (The "Red Zone")

This group contains models that might achieve a high score in one run but fail to replicate it. They are unpredictable and therefore untrustworthy.
*   **Prime Examples:** `llama3:8b`, `mistral:7b`, `phi3:3.8b`.
*   **Behavior:** These models have a high number of red cells. They are the "turncoats" (`vira casacas`) of the evaluation. Their inconsistency stems from a phenomenon we can call the **"Keyword Lottery."**
    *   In one run, a model's probabilistic text generation might "get lucky" and happen to include specific keywords or phrases that satisfy the evaluation criteria, earning it a "Correct" or "Incomplete" score or simply performce drops.
    *   In the next run, a slight, almost random change in sentence structure or word choice can cause those same keywords to be omitted, dropping the evaluation to "Incomplete."
*   **Implication:** A correct answer from these models cannot be fully trusted as a sign of genuine understanding. It might be a one-time fluke. For any serious application, this level of semantic instability is a critical risk.

#### 3. The Consistently Underperforming

This is a subtle but important category of models that are consistent, but for the wrong reason.
*   **Prime Examples:** `deepseek-r1:1.5b`, `qwen2.5:0.5b`.
*   **Behavior:** Some of these smaller models may show green cells, but their overall scores remain low. This indicates that they are *consistently wrong* or *consistently incomplete*. They reliably provide the same poor-quality answer every time.
*   **Implication:** This highlights that consistency is a necessary, but not sufficient, condition for a good model. Correctness and consistency must exist together.

### Final Conclusion on Consistency

For a high-stakes domain like cybersecurity, **consistency is arguably more important than peak performance**. An inconsistent model that gives a brilliant answer one time and a dangerously wrong one the next is a liability. This multi-run evaluation methodology is therefore central to the project, as it effectively filters out unreliable models and identifies those that are truly robust.

---

## Repository Structure & Evaluation Artifacts

The raw data and analysis for each test phase are stored in dedicated folders.

*   **`Teste_1/`,  `Teste_2/` and `Teste_3/`:** These folders contain the raw log files from the initial "Normal" and "Reverse" test runs. They also include the Excel spreadsheets where the detailed scoring (0, 1, or 2 points) for each model's response is recorded, along with the final consistency comparison table.

---

## Initial Findings & Analysis (Phases 1 & 2)

The initial tests on over 20 models produced several key insights. The evaluation was not just about getting the right answer, but also about the consistency of the answers across multiple runs.

*(You can insert your results charts here in the README for visual reference)*

#### Key Takeaways:
1.  **Top Performers:** `qwen3:8b`, `granite3-dense:8b` and  `granite3.2:8b` consistently achieved the highest scores, demonstrating both accurate and reliable knowledge. `granite3-dense:8b` also performed exceptionally well.
2.  **Consistency is a Critical Metric:** Some models, like `llama3:8b`, achieved a good score but were highly inconsistent, providing correct answers in one run and failing in the next. This highlights the risk of relying on models that are not robustly predictable.
3.  **Critical Factual Errors in Mid-Tier Models:** Some popular models showed surprising knowledge gaps. For instance, `mistral:7b` incorrectly identified CWE-79 (Cross-Site Scripting) as SQL Injection, a fundamental error that would be dangerous in a production setting.
4.  **Model Size vs. Architecture:** While there is a general trend of larger models (7B/8B parameters) outperforming smaller ones, the results clearly show that model architecture and training are just as important. A well-tuned 7B model often outperformed a less-focused 8B model.

---

## Scripts in this Repository

*   **`cybersecurity_sanity_tester.py`**: The initial script used for broad-based factual and concept-identification testing. It is interactive and allows for testing any available Ollama model.
*   **`cybersecurity_inference_tester.py`**: The advanced script for deep-diving into the reasoning capabilities of the top-performing models (`qwen:8b`, `granite3.2:8b`, `granite3-dense:8b`). It runs a fixed set of 9 complex inference questions.

## How to Run the Tests

1.  **Prerequisites:**
    *   Python 3
    *   An running instance of [Ollama](https://ollama.com/) with the desired models downloaded (e.g., `ollama pull qwen:8b`).
    *   Python dependencies: `pip install requests beaupy` (`beaupy` is only required for the interactive `cybersecurity_sanity_tester.py`).

2.  **Execute a Test:**
    Run the desired script from your terminal. For example:
    ```bash
    python cybersecurity_inference_tester.py
    ```

3.  **Check the Results:**
    A log file containing the full prompts and LLM responses will be created in a corresponding log directory (e.g., `cybersecurity_inference_logs/`).
