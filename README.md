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
