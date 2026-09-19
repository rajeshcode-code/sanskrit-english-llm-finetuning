# Sanskrit-English LLM Fine-Tuning

A practical fine-tuning project for Sanskrit-to-English instruction following using an open-source language model, LoRA, and Google Colab.

## Overview

The goal of this project is to fine-tune a small language model to improve its ability to translate Sanskrit sentences into English.

The project covers dataset preparation, instruction formatting, LoRA fine-tuning, model evaluation, tokenizer analysis, and error analysis under limited GPU resources.

## Dataset Preparation

A small curated dataset of 20 Sanskrit-English translation examples was created from Sanskrit text related to Ayurveda, health, diet, daily routine, and diseases.

- Total examples: 20
- Training examples: 16
- Validation examples: 4
- Format: Instruction-response / chat format
- Task: Sanskrit → English translation

The dataset was formatted using the model's chat template before fine-tuning.

## Model Selection

The base model used was:

`Qwen/Qwen2.5-1.5B-Instruct`

It was selected because its relatively small size makes it practical to experiment with on a Google Colab T4 GPU while still providing an instruction-following capability suitable for the task.

## Fine-Tuning

LoRA was used instead of full fine-tuning to reduce memory and compute requirements.

Main configuration:

- LoRA rank: 8
- LoRA alpha: 16
- LoRA dropout: 0.05
- Target modules: q_proj, k_proj, v_proj, o_proj
- Epochs: 3
- Batch size: 1
- Gradient accumulation: 4
- Learning rate: 2e-4
- FP16 training

Only about 0.14% of the model parameters were trainable.

## Hardware

Training was performed in Google Colab using an NVIDIA Tesla T4 GPU with approximately 14.5 GB available memory.

LoRA, FP16 training, and gradient accumulation were used to make training practical within the available GPU memory.

## Evaluation

The fine-tuned model was compared with the original base model on four unseen Sanskrit sentences.

| Model | BLEU |
|---|---:|
| Base Model | 4.21 |
| Fine-Tuned Model | 4.28 |

The BLEU improvement was small (+0.07). Because the evaluation set is very small, this result should not be treated as a strong measure of general improvement.

Qualitative outputs were also reviewed to compare translation quality and response behavior before and after fine-tuning.

## Error Analysis

The models generally produced the main meaning of the Sanskrit sentences, but both sometimes generated additional explanatory or meta text instead of returning only the translation.

The fine-tuned model did not consistently remove this extra text. This suggests that the current dataset is too small to reliably control the output format.

## Tokenizer Observation

A tokenizer check was performed on Sanskrit and English text. The Sanskrit example required more tokens than the corresponding short English translation.

This was treated as an observation of tokenizer behavior rather than a linguistic conclusion. No tokenizer modification was performed in this experiment.

## Challenges

During implementation, a PEFT compatibility issue related to `torchao` was encountered in Google Colab. The issue was resolved by removing the conflicting package and restarting the runtime.

The notebook also required debugging around tokenizer outputs during inference. The generated tokens were separated from the input tokens before decoding the final response.

## Limitations

- The dataset contains only 20 examples.
- The evaluation set contains only 4 examples.
- The data is focused mainly on Ayurveda-related Sanskrit text.
- The BLEU improvement is small.
- The model can still generate unnecessary explanatory text.

These limitations make the current experiment more suitable as a fine-tuning prototype than a production-ready translation system.

## Future Improvements

With more time and compute, the project could be improved by:

- Increasing the size and diversity of the Sanskrit-English dataset.
- Using a larger and more representative evaluation set.
- Including additional Sanskrit tasks such as QA, explanation, and summarization.
- Evaluating with multiple translation metrics.
- Improving output formatting through better instruction data.
- Further investigating Sanskrit tokenization and subword fragmentation.
- Experimenting with QLoRA or larger multilingual models.

## How to Run

1. Open `Sanskrit_LLM_FineTuning.ipynb` in Google Colab.
2. Select a T4 GPU from `Runtime → Change runtime type`.
3. Run the notebook cells in order.
4. The notebook installs the required libraries, prepares the dataset, loads the base model, performs LoRA fine-tuning, and evaluates the results.

## Project Structure

```text
Sanskrit_LLM_FineTuning/
│
├── Sanskrit_LLM_FineTuning.ipynb
├── sanskrit_translation_evaluation.csv
└── README.md

Conclusion

This project demonstrates an end-to-end Sanskrit-English fine-tuning workflow using an open-source LLM and parameter-efficient fine-tuning. The experiment also highlights the importance of dataset size, evaluation design, and error analysis when working with multilingual NLP tasks under limited compute.
