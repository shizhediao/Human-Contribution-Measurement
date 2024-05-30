# Human Contribution Measurement

## Contents
- [Overview](#overview)
- [Repo Contents](#repo-contents)
- [System Requirements](#system-requirements)
- [Installation Guide](#installation-guide)
- [Demo](#demo)
- [License](./LICENSE)


## Overview
With the prevalence of generative Artificial Intelligence (AI), an increasing volume of content is no longer exclusively the product of human creation but rather the result of co-creation by human prompters and generative models. This shift presents significant challenges for copyright law and policy, as the varying degrees of human contribution in human-AI collaborative works blur the lines of copyright eligibility, which in many jurisdictions is not available for non-human authored works [ADD CITE]. This study proposes the research problem of measuring human contribution in AI-assisted generation and introduces a framework to approach this problem grounded in information theory. By calculating the mutual information between the human input and the collaborative output relative to the self-information of the collaborative output, we quantify the proportion of the human's informational contribution during content generation. Experimental results demonstrate that the proposed measure effectively discriminates between varying degrees of human contribution across multiple creation domains. We anticipate that our work will lay a foundational cornerstone and inspire further research on human contribution evaluation, thereby providing a technical basis for refining related copyright law and policy.

## Repo Contents
- [src](./src): source code to reproduce results in the manuscript.
- [script](./script): scripts to run the experiments.
- [data_code](./data_code): source code to prepare the dataset.

## System Requirements

### Hardware Requirements

To run this package, the following hardware specifications are recommended:

A standard computer with a stable and reliable internet connection is required to access the OpenAI API.

The package has been tested on a machine with the following specifications:

* Memory: 216GB
* Processor: AMD EPYC 7V13 64-Core Processor

### Software Requirement

The package has been tested and verified to work on 

* Linux: Ubuntu 22.04.

It is recommended to use this operating system for optimal compatibility.

Before installing the required Python dependencies and running the source code, ensure that you have the following software installed:

* Docker.


## Installation Guide

### Requirements
We use docker to manage the experimental enviroments. Pull the following docker image to your local devices.
```bash
docker pull yjw1029/torch:2.0-llm-v9
```

For experiments with Meta-Llama-3-8B-Instruct, fastchat is need to be reinstalled:
```bash
pip uninstall -y -q fschat
pip install --upgrade git+https://github.com/lm-sys/FastChat
```

For experiments with Mixtral-8x7B-Instruct-v0.1, fastchat is need to be reinstalled:
```bash
pip install vllm==0.2.7
```

Since this package requires access to the OpenAI API, you will need to register an account and obtain your `OPENAI_API_KEY`. Please follow the instructions provided in the OpenAI documentation for registration and obtaining the API keys: [OpenAI Documentation](https://platform.openai.com/docs/introduction).
The code has been test with OpenAI Services.
Setup the your OpenAI API key in `src/config/gpt35.yaml`.

We also conduct experiments with Meta-Llama-3-8B-Instruct and Mixtral-8x7B-Instruct-v0.1. Please apply for LLAMA-3 access on the [official meta website](https://llama.meta.com/llama-downloads/) and the [huggingface repo](https://huggingface.co/meta-llama/Meta-Llama-3-8B-Instruct). Then set your huggingface access token before running experiments.

```bash
export HUGGING_FACE_HUB_TOKEN=[Your Hugging Face Token]
``` 

### Download Dataset
Due to copyright issues, we cannot provide the dataset. Please get the access to the original datasets and download them in the `raw_data_new` folder. Here are the source of original datasets:
- News Articles: [source](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/GMFCTR)
- Poetry Foundation: [source](https://www.kaggle.com/datasets/tgdivy/poetry-foundation-poems)
- Arxiv Abstracts: [source](https://huggingface.co/datasets/gfissore/arxiv-abstracts-2021)
- HUPD: [source](https://huggingface.co/datasets/HUPD/hupd/blob/main/data/2018.tar.gz)
- allenai/WildChat-1M: [source](https://huggingface.co/datasets/allenai/WildChat-1M)

Then run the following script to sampling our experimental datasets.
```bash
mkdir data_new

python data_code/process_news.py
python data_code/process_patent.py
python data_code/process_poem.py
python data_code/process_paper.py
```

Finally, generate the summary and subject of text content.
```bash
python data_code/generate_summary_news.py
python data_code/generate_summary_patent.py
python data_code/generate_summary_poem.py
python data_code/generate_summary_paper.py
```

## Demo
### Response Generation
```bash
bash script/generate.sh {data} {model} {time}
```
Parameters:
- model: The model used for generating responses. The options include: `["gpt35", "llama3_8b", "mixtral_8x7b"]`
- data: The dataset used for generating responses. The options include: `["news", "paper", "patent", "poem"]`
- time: The index for repeated experiments. The options include: `[1, 2, 3, 4, 5]`


### Human Evaluation Measurement
```bash
bash script/evaluate.sh {data} {eval_model} {model} {time}
```
Parameters:

- data: The dataset on which the evaluation is performed. The options include: `["news", "paper", "patent", "poem"]`
- eval_model: The evaluation model used to measure human contribution. The options include: `["llama3_8b", "mixtral_8x7b"]`
- model: The model whose responses are being evaluated. The options include: `["gpt35", "llama3_8b", "mixtral_8x7b"]`
- time: The index for repeated experiments. This is used to distinguish between different runs of the same experiment. The options include: `[1, 2, 3, 4, 5]`

### Impact of Response Length
Generate responses with varying lengths.
```bash
bash script/very_lens.sh {data} {model} {time}
```
Parameters:
- model: The model used for generating responses. The options include: `["llama3_8b"]`
- data: The dataset used for generating responses. The options include: `["news", "paper", "patent", "poem"]`
- time: The index for repeated experiments. The options include: `[1, 2, 3, 4, 5]`

Measure human contribution.
```bash
bash script/evaluate_lens.sh {data} {eval_model} {model} {time}
```
Parameters:
- data: The dataset on which the evaluation is performed. The options include: `["news", "paper", "patent", "poem"]`
- eval_model: The evaluation model used to measure human contribution. The options include: `["llama3_8b"]`
- model: The model whose responses are being evaluated. The options include: `["llama3_8b"]`
- time: The index for repeated experiments. This is used to distinguish between different runs of the same experiment. The options include: `[1, 2, 3, 4, 5]`

### Adaptive attack

Generate responses with adaptive attacks.
```bash
bash script/ada.sh {data} {model} {time}
```
Parameters:
- model: The model used for generating responses. The options include: `["llama3_8b"]`
- data: The dataset used for generating responses. The options include: `["news", "paper", "patent", "poem"]`
- time: The index for repeated experiments. The options include: `[1, 2, 3, 4, 5]`

Measure human contribution.
```bash
bash script/eval_ada.sh {data} {eval_model} {model} {time}
```
Parameters:
- data: The dataset on which the evaluation is performed. The options include: `["news", "paper", "patent", "poem"]`
- eval_model: The evaluation model used to measure human contribution. The options include: `["llama3_8b"]`
- model: The model whose responses are being evaluated. The options include: `["llama3_8b"]`
- time: The index for repeated experiments. This is used to distinguish between different runs of the same experiment. The options include: `[1, 2, 3, 4, 5]`