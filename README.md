# Protein LLM Distillation and GRPO

This project investigates the compression and alignment of Protein Language Models (PLMs) for protein-function question answering.

It distils the instruction-tuned **InstructProtein** teacher into a compact **OPT-125M** student, then explores **Group Relative Policy Optimization (GRPO)** on verifiable binary protein-function questions.

## Objectives

- Prepare protein-instruction data with protein-disjoint train/validation/test splits.
- Perform response distillation without access to teacher logits.
- Fine-tune a compact OPT-125M student using LoRA.
- Apply GRPO to binary `yes/no` protein-function questions.
- Compare the teacher, SFT/KD student, and SFT-GRPO student for quality and efficiency.
- Deploy all three models through FastAPI, ngrok, and a local web interface.

## Pipeline

```text
InstructProtein (teacher)
        |
        | response distillation without logits
        v
OPT-125M + LoRA (SFT/KD student)
        |
        | GRPO on binary protein-function QA
        v
OPT-125M + LoRA (SFT-GRPO student)
```

## Notebooks

| Notebook | Description |
|---|---|
| `01_response_distillation_instructprotein.ipynb` | Protein-disjoint preprocessing and LoRA response distillation. |
| `02_grpo_binary_instructprotein.ipynb` | GRPO optimisation on binary protein-function questions. |
| `03_compare_teacher_kd_grpo.ipynb` | Common-protocol comparison of teacher, KD and KD+GRPO models. |
| `04_serve_three_models_ngrok.ipynb` | FastAPI service for the three models, exposed using ngrok. |

## Results

The models were evaluated on a frozen protein-disjoint binary test set (200 examples).

| Model | Accuracy | Macro-F1 | MCC | Parameters | Peak VRAM | Mean latency |
|---|---:|---:|---:|---:|---:|---:|
| InstructProtein teacher | 80.5% | 86.7% | 0.681 | 1.316B | 2,813 MB | 332 ms |
| OPT-125M + KD | 46.0% | 49.6% | 0.064 | 127.9M | 400 MB | 88 ms |
| OPT-125M + KD + GRPO | 46.0% | 33.2% | 0.019 | 127.9M | 400 MB | 89 ms |

The student is about 10.3 times smaller, uses about 7 times less GPU memory, and is about 3.8 times faster than the teacher. The experiment also shows that the simple GRPO configuration did not yield a robust quality gain under the common evaluation protocol.

## Published models

- SFT/KD student: [karouiFares/instructprotein-opt125m-response-kd](https://huggingface.co/karouiFares/instructprotein-opt125m-response-kd)
- SFT-GRPO student: [karouiFares/instructprotein-opt125m-grpo](https://huggingface.co/karouiFares/instructprotein-opt125m-grpo)

## Running the notebooks

Run the notebooks on Kaggle with a Tesla T4 GPU, Internet enabled, and the `instructions.txt` dataset attached. To publish models, add a Kaggle secret named `HF_TOKEN`. The API notebook additionally needs an `NGROK_AUTHTOKEN` secret.

## References

- Wang et al. (2024), *InstructProtein: Aligning Human and Protein Language via Knowledge Instruction*.
- Hu et al. (2022), *LoRA: Low-Rank Adaptation of Large Language Models*.
- Hinton et al. (2015), *Distilling the Knowledge in a Neural Network*.
- Shao et al. (2024), *DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models*.

## License

This repository is intended for academic and educational use. Review the licenses of the upstream models and datasets before any other use.
