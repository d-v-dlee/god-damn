---
title: "Autonomous LLM Agents"
date: 2023-08-10
description: A short review and history of autonomous LLM agents
tags: [LLMs, autogpt, automation, autonomous agents]
---

## About

This post provides an overview of emerging research on LLM-powered autonomous agents, which combine Large Language Model reasoning with complementary components like planning and memory to effectively perform complex real-world tasks, serving as AI assistants that augment human capabilities.

## Background

* The goal of autonomous LLM agents is to create AI systems that can effectively perform complex, real-world tasks.
* In LLM-powered autonomous agents, the LLM acts as the agent's "brain" complemented by other key components like planning, memory and tools.
* Planning involves task decomposition into manageable subgoals and reflection to refine actions.
* Memory includes short-term (in-context learning aka the prompt) and long-term (text retrieved from vector stores) to retain information (eg. what actions have I taken, what subgoals do I have left, what information do i have access to)
* Tool use allows accessing external information missing from the LLM's weights like APIs, code execution, and proprietary data.
* While this is still an exploratory space, and the hype of past projects like AutoGPT have already come-and-gone, projects like MetaGPT and ChemCrow demonstrate real potential for autonomous agents to take on complex, real-world problems.
* Projecting into the future, imagine:
    * Autonomous agents helping analysts build financial reports by retrieving relevant data, generating visualizations, and drafting narratives
    * Agents assisting customer service teams by quickly accessing account information, determining solutions, and directly interacting with customers
    * Agents performing audits by gathering data from multiple systems, identifying anomalies, and compiling results into reports
    * Agents acting as research assistants that rapidly synthesize findings from papers, datasets, and experiments into summary briefings

## Examples

### HuggingGPT (May 2023)

* HuggingGPT uses a central LLM to coordinate and connect specialized deep learning models together to solve complex AI tasks. The LLM serves as the "brain" that plans how to solve problems.
* It leverages few-shot learning and instruction prompting (aka prompts with examples), to get the LLM to decompose problems, select relevant models, execute models, and generate responses.
* To select models, it ranks them by downloads on HuggingFace then provides the top options to the LLM. The LLM decomposes the problem into executable steps for the models.
* Models with dependencies wait for required outputs from other models before executing. Independent models execute in parallel for faster performance.
* Success largely depends on problem decomposition and generated DAG plan.

<!-- Using HTML Including Repository Name -->
<img src="/god-damn/assets/images/auto-llm/hugginggpt.jpg" alt="hugginggpt" width="500" style="display: block; margin-left: auto; margin-right: auto;">


### Gorilla (May 2023)

* Gorilla is an LLM fine-tuned to leverage 1,600+ model APIs.
* Gorilla was fine-tuned on a dataset of valid API calls generated for PyTorch, TensorFlow, and HuggingFace hubs.
* During fine-tuning, the LLM learns to use the latest up-to-date API documents to make proper calls.
* When tasked with instructions, it will retrieve the most relevant API documentation (via a vector database) before making the call. 
* Combined, this allows Gorilla to be effective even when APIs change + reduces hallucinated / incorrect API calls.

<img src="/god-damn/assets/images/auto-llm/gorilla.jpg" alt="gorilla" width="500" style="display: block; margin-left: auto; margin-right: auto;">

[Image: image.png]
### ChemCrow (June 2023)

* ChemCrow is an LLM with access to 17 expert chemistry tools such as web search, literature search, a python REPL, and molecule specific tools.
* It uses a workflow prompting the LLM to reason, request tools, provide inputs, and observe outputs.
* ChemCrow was able to successfuly plan the synthesis of an insect repellent, three organocatalysts, and other relevant molecules.

<img src="/god-damn/assets/images/auto-llm/chemcrow.jpg" alt="chemcrow" width="500" style="display: block; margin-left: auto; margin-right: auto;">

### MetaGPT (Aug 2023)

* MetaGPT is a framework for solving complex problems by assigning different LLM agents specific roles (Product Manager, Architect, Engineer, etc). 
* Assigning agents roles 1) guides the behavior of each agent, improving its outputs and 2) diversifies the expertise
* The MetaGPT framework uses Standard Operating Procedures (SOPs) to govern the behavior of agents and structure the coordination of agents; agents can share information amongst each other
* MetaGPT produces state of the art results on coding tasks; it also creates intermediate artifacts like BRDs and flowcharts (try the [demo](https://github.com/geekan/MetaGPT) its brazy, bruh.)
* Integrating human domain knowledge into multi-agent systems with SOPs reduces hallucination and creates opportunities to solve complex real-world problems.

<img src="/god-damn/assets/images/auto-llm/metagpt1.jpg" alt="metagpt1" width="500" style="display: block; margin-left: auto; margin-right: auto;">
<img src="/god-damn/assets/images/auto-llm/metagpt2.jpg" alt="metagpt2" width="500" style="display: block; margin-left: auto; margin-right: auto;">

## Appendix

Attached are links to the papers or projects + writing of way smarter people like Cameron R Wolfe and Lilian Weng who I'm always eternally grateful for.

- [HuggingGPT](https://arxiv.org/pdf/2303.17580.pdf)
- [Gorilla](https://gorilla.cs.berkeley.edu/)
- [ChemCrow](https://arxiv.org/pdf/2304.05376.pdf)
- [MetaGPT](https://arxiv.org/pdf/2308.00352.pdf)
- [LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/)
- [Language Model and Friends](https://cameronrwolfe.substack.com/p/language-models-and-friends-gorilla)