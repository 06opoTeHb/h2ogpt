## h2oGPT - The world's best open source GPT

Our goal is to make the world's best open source GPT!

### Try h2oGPT now 

Live hosted instances:
- [![img-small.png](img-small.png) h2oGPT 20B](https://gpt.h2o.ai/)
- [🤗 h2oGPT 12B #1](https://huggingface.co/spaces/h2oai/h2ogpt-chatbot)
- [🤗 h2oGPT 12B #2](https://huggingface.co/spaces/h2oai/h2ogpt-chatbot2)
- [![img-small.png](img-small.png) h2oGPT (research) 30B](http://gpu.hopto.org)

For questions, discussing, or just hanging out, come and join our <a href="https://discord.gg/WKhYMWcVbq"><b>Discord</b></a>!

https://user-images.githubusercontent.com/6147661/232924684-6c0e2dfb-2f24-4098-848a-c3e4396f29f6.mov

<a href="https://user-images.githubusercontent.com/6147661/233239878-de3b0fce-5425-4189-8095-5313c7817d58.png"><img width="70%" alt="home" src="https://user-images.githubusercontent.com/6147661/233239878-de3b0fce-5425-4189-8095-5313c7817d58.png"></a><a href="https://user-images.githubusercontent.com/6147661/233239861-e99f238c-dd5d-4dd7-ac17-6367f91f86ac.png"><img width="28.5%" alt="home" src="https://user-images.githubusercontent.com/6147661/233239861-e99f238c-dd5d-4dd7-ac17-6367f91f86ac.png"></a>

### Current state

- Open-source repository with **fully permissive, commercially usable code, data and models**
- Code for preparing **large open-source datasets** as instruction datasets for fine-tuning of large language models (LLMs), including prompt engineering
- Code for **fine-tuning large language models** (currently up to 20B parameters) on commodity hardware and enterprise GPU servers (single or multi node)
- Code for enabling **LoRA (low-rank approximation) and 8-bit quantization** for memory-efficient fine-tuning and generation.
- Code to **run a chatbot** on a GPU server, with shareable end-point with Python client API
- Code to evaluate and compare the **performance** of fine-tuned LLMs

All open-source datasets and models are posted on [🤗 H2O.ai's Hugging Face page](https://huggingface.co/h2oai/).

Also check out [H2O LLM Studio](https://github.com/h2oai/h2o-llmstudio) for our no-code LLM fine-tuning framework!

<a href="https://user-images.githubusercontent.com/1069138/233859311-32aa1f8c-4d68-47ac-8cd9-9313171ff9f9.png"><img width="50%" alt="home" src="https://user-images.githubusercontent.com/1069138/233859311-32aa1f8c-4d68-47ac-8cd9-9313171ff9f9.png"></a><a href="https://user-images.githubusercontent.com/1069138/233859315-e6928aa7-28d2-420b-8366-bc7323c368ca.png"><img width="50%" alt="logs" src="https://user-images.githubusercontent.com/1069138/233859315-e6928aa7-28d2-420b-8366-bc7323c368ca.png"></a>

### Roadmap items

- Integration of code and resulting LLMs with downstream applications and low/no-code platforms
- Complement h2oGPT chatbot with search and other APIs
- High-performance distributed training of larger models on trillion tokens
- Enhance the model's code completion, reasoning, and mathematical capabilities, ensure factual correctness, minimize hallucinations, and avoid repetitive output

### Chat with h2oGPT

```bash
git clone https://github.com/h2oai/h2ogpt.git
cd h2ogpt
pip install -r requirements.txt
python generate.py --base_model=h2oai/h2ogpt-oig-oasst1-512-6.9b
```
and then use browser at http://0.0.0.0:7860 or the public live URL printed by the server (can disable public URL by passing `--share=False`).

For help installing a Python 3.10 environment or CUDA toolkit or installing flash attention support, see the [installation instructions](INSTALL.md)

You can also use [Docker](INSTALL-DOCKER.md#containerized-installation-for-inference-on-linux-gpu-servers) for inference.

#### Larger models require more GPU memory

Depending on available GPU memory, you can load differently sized models. For multiple GPUs, automatic sharding can be enabled with `--infer_devices=False`, but this is disabled by default since cuda:x cuda:y mismatches can occur.

For GPUs with at least 24GB of memory, we recommend:
```bash
python generate.py --base_model=h2oai/h2ogpt-oasst1-512-12b
```
For GPUs with at least 48GB of memory, we recommend:
```bash
python generate.py --base_model=h2oai/h2ogpt-oasst1-512-20b
```
The number `512` in the model names indicates the cutoff lengths (in tokens) used for fine-tuning. Shorter values generally result in faster training and more focus on the last part of the provided input text (consisting of prompt and answer).

More information about the models can be found on [H2O.ai's Hugging Face page](https://huggingface.co/h2oai/).

### Development

- To create a development environment for training and generation, follow the [installation instructions](INSTALL.md).
- To fine-tune any LLM models on your data, follow the [fine-tuning instructions](FINETUNE.md).
- To create a container for deployment, follow the [Docker instructions](INSTALL-DOCKER.md).

6. Compile Apex (for Flash attention, needs CUDA 11.7 above) [howto src](https://github.com/NVIDIA/apex/#linux)

```bash
git clone https://github.com/NVIDIA/apex
cd apex
pip install -v --disable-pip-version-check --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./
```

Fine-tune on single GPU on single node:
```
torchrun finetune.py --base_model='EleutherAI/gpt-j-6B' --data_path=alpaca_data_cleaned.json 
```
this will download the model, load the data, and generate an output directory lora-alpaca.

Fine-tune using 2 nodes with 2 GPUs each:
```
WORLD_SIZE=4 CUDA_VISIBLE_DEVICES="0,1" torchrun --nnodes=2 --master_addr="10.10.10.2" --node_rank=0 --nproc_per_node=2 --master_port=1234 finetune.py --data_path=alpaca_data_cleaned.json --run_id=0 --base_model='EleutherAI/gpt-j-6B'

WORLD_SIZE=4 CUDA_VISIBLE_DEVICES="0,1" torchrun --nnodes=2 --master_addr="10.10.10.2" --node_rank=1 --nproc_per_node=2 --master_port=1234 finetune.py --data_path=alpaca_data_cleaned.json --run_id=0 --base_model='EleutherAI/gpt-j-6B'
```

Fine-tune using 2 24GB GPUs to split up a 30B model:
```
WORLD_SIZE=2 python finetune.py --data_path=alpaca_data_cleaned.json --base_model="decapoda-research/llama-30b-hf" --ddp=False
```

Fine-tune previously saved model (running `export_hf_checkpoint.py`):
```
WORLD_SIZE=4 CUDA_VISIBLE_DEVICES="0,1" torchrun --nnodes=2 --master_addr="10.10.10.2" --node_rank=0 --nproc_per_node=2 --master_port=1234 finetune.py --num_epochs=2 --micro_batch_size=8 --data_path=alpaca_data_cleaned.json --run_id=3 --base_model='gpt-j-6B.DAIdocs' --tokenizer_base_model='EleutherAI/gpt-j-6B' --output_dir=lora_6B.DAIdocs &> 3.node0.log

WORLD_SIZE=4 CUDA_VISIBLE_DEVICES="0,1" torchrun --nnodes=2 --master_addr="10.10.10.2" --node_rank=1 --nproc_per_node=2 --master_port=1234 finetune.py --num_epochs=2 --micro_batch_size=8 --data_path=alpaca_data_cleaned.json --run_id=3 --base_model='gpt-j-6B.DAIdocs' --tokenizer_base_model='EleutherAI/gpt-j-6B' --output_dir=lora_6B.DAIdocs &> 3.node1.log
```

Generate on single GPU on single node:
```
torchrun generate.py --base_model='EleutherAI/gpt-j-6B' --lora_weights=lora-alpaca
```
this will download the foundation model, our fine-tuned lora_weights, and open up a GUI with text generation input/output.


In case you get peer to peer related errors, set this env var:
```
export NCCL_P2P_LEVEL=LOC
```


### Docker Setup & Inference

1. Build the container image:

```bash
docker build -t h2o-llm .
```

2. Run the container (you can also use `finetune.py` and all of its parameters as shown above for training):

```bash
docker run --runtime=nvidia --shm-size=64g -p 7860:7860 -v ${HOME}/.cache:/root/.cache --rm h2o-llm -it generate.py \
    --load_8bit=True --base_model='EleutherAI/gpt-neox-20b' --prompt_type=human_bot
```

3. Open `https://localhost:7860` in the browser

### Docker Compose Setup & Inference

1. (optional) Change desired model and weights under `environment` in the `docker-compose.yml`

2. Build and run the container

```bash
docker-compose up -d --build
```

3. Open `https://localhost:7860` in the browser

4. See logs:

```bash
docker-compose logs -f
```

5. Clean everything up:

```bash
docker-compose down --volumes --rmi all
```


### Tensorboard

```bash
tensorboard --logdir=runs/
```

### Plan
Open source instruct model for demoable usecases.
1. Base: Start with fully open source apache 2.0 models EleutherAI--gpt-j-6B, EleutherAI--gpt-neox-20b, 
GPT-NeoXT-Chat-Base-20B, etc. 
2. Construct Prompt: Setup prompt engineering on 6B-20B as-is to convert a sentence into question/answer or command/response format 
3. Open-Source Instruct Data: Convert wiki data into instruct form
4. Fine-tune: LORA fine-tune 6B and 20B using DAI docs
5. Open Data & Model: Submit DAI docs model huggingface
6. Use toolformer approach for external APIs

### Goals
1. Demonstrate fine-tuning working on some existing corpus
2. Demonstrate efficiency of LORA for fast and low-memory fine-tuning


### Code to consider including
[flan-alpaca](https://github.com/declare-lab/flan-alpaca)<br />
[text-generation-webui](https://github.com/oobabooga/text-generation-webui)<br />
[minimal-llama](https://github.com/zphang/minimal-llama/)<br />
[finetune GPT-NeoX](https://nn.labml.ai/neox/samples/finetune.html)<br />
[GPTQ-for_LLaMa](https://github.com/qwopqwop200/GPTQ-for-LLaMa/compare/cuda...Digitous:GPTQ-for-GPT-NeoX:main)<br />
[OpenChatKit on multi-GPU](https://github.com/togethercomputer/OpenChatKit/issues/20)<br />
[Non-Causal LLM](https://huggingface.co/docs/transformers/main/en/model_doc/gptj#transformers.GPTJForSequenceClassification)<br />
[OpenChatKit_Offload](https://github.com/togethercomputer/OpenChatKit/commit/148b5745a57a6059231178c41859ecb09164c157)<br />
[Flan-alpaca](https://github.com/declare-lab/flan-alpaca/blob/main/training.py)<br />

### Help

[FAQs](FAQ.md)

### More links, context, competitors, models, datasets

[Links](LINKS.md)

### Acknowledgements

* Some training code was based upon March 24 version of [Alpaca-LoRA](https://github.com/tloen/alpaca-lora/).
* Used high-quality created data by [OpenAssistant](https://open-assistant.io/).
* Used base models by [EleutherAI](https://www.eleuther.ai/).
* Used OIG data created by [LAION](https://laion.ai/blog/oig-dataset/).

### Why H2O.ai?

Our [Makers](https://h2o.ai/company/team/) at [H2O.ai](https://h2o.ai) have built several world-class Machine Learning, Deep Learning and AI platforms:
- #1 open-source machine learning platform for the enterprise [H2O-3](https://github.com/h2oai/h2o-3)
- The world's best AutoML (Automatic Machine Learning) with [H2O Driverless AI](https://h2o.ai/platform/ai-cloud/make/h2o-driverless-ai/)
- No-Code Deep Learning with [H2O Hydrogen Torch](https://h2o.ai/platform/ai-cloud/make/hydrogen-torch/)
- Document Processing with Deep Learning in [Document AI](https://h2o.ai/platform/ai-cloud/make/document-ai/)

We also built platforms for deployment and monitoring, and for data wrangling and governance:
- [H2O MLOps](https://h2o.ai/platform/ai-cloud/operate/h2o-mlops/) to deploy and monitor models at scale
- [H2O Feature Store](https://h2o.ai/platform/ai-cloud/make/feature-store/) in collaboration with AT&T
- Open-source Low-Code AI App Development Frameworks [Wave](https://wave.h2o.ai/) and [Nitro](https://nitro.h2o.ai/)
- Open-source Python [datatable](https://github.com/h2oai/datatable/) (the engine for H2O Driverless AI feature engineering)

Many of our customers are creating models and deploying them enterprise-wide and at scale in the [H2O AI Cloud](https://h2o.ai/platform/ai-cloud/):
- Multi-Cloud or on Premises
- [Managed Cloud (SaaS)](https://h2o.ai/platform/ai-cloud/managed)
- [Hybrid Cloud](https://h2o.ai/platform/ai-cloud/hybrid)
- [AI Appstore](https://docs.h2o.ai/h2o-ai-cloud/)

We are proud to have over 25 (of the world's 280) [Kaggle Grandmasters](https://h2o.ai/company/team/kaggle-grandmasters/) call H2O home, including three Kaggle Grandmasters who have made it to world #1.

### Disclaimer

Please read this disclaimer carefully before using the large language model provided in this repository. Your use of the model signifies your agreement to the following terms and conditions.

- Biases and Offensiveness: The large language model is trained on a diverse range of internet text data, which may contain biased, racist, offensive, or otherwise inappropriate content. By using this model, you acknowledge and accept that the generated content may sometimes exhibit biases or produce content that is offensive or inappropriate. The developers of this repository do not endorse, support, or promote any such content or viewpoints.
- Limitations: The large language model is an AI-based tool and not a human. It may produce incorrect, nonsensical, or irrelevant responses. It is the user's responsibility to critically evaluate the generated content and use it at their discretion.
- Use at Your Own Risk: Users of this large language model must assume full responsibility for any consequences that may arise from their use of the tool. The developers and contributors of this repository shall not be held liable for any damages, losses, or harm resulting from the use or misuse of the provided model.
- Ethical Considerations: Users are encouraged to use the large language model responsibly and ethically. By using this model, you agree not to use it for purposes that promote hate speech, discrimination, harassment, or any form of illegal or harmful activities.
- Reporting Issues: If you encounter any biased, offensive, or otherwise inappropriate content generated by the large language model, please report it to the repository maintainers through the provided channels. Your feedback will help improve the model and mitigate potential issues.
- Changes to this Disclaimer: The developers of this repository reserve the right to modify or update this disclaimer at any time without prior notice. It is the user's responsibility to periodically review the disclaimer to stay informed about any changes.

By using the large language model provided in this repository, you agree to accept and comply with the terms and conditions outlined in this disclaimer. If you do not agree with any part of this disclaimer, you should refrain from using the model and any content generated by it.
