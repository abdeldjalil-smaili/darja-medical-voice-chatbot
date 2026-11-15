# Darja Medical Voice Chatbot

End-to-end voice chatbot for medical question answering in **Algerian Arabic (Darja)**, a low-resource dialect. The system takes a spoken question, retrieves relevant medical knowledge, generates an answer with a fine-tuned LLM, and speaks it back.

Master's thesis project (M.Sc. Bioinformatics, USTHB, 2026), supervised by Dr. Salima Harrat.

## Pipeline

Speech → **ASR** (fine-tuned Whisper small) → **RAG** (FAISS retrieval over medical Q&A) → **LLM** (fine-tuned LLaMA 3.2 3B, QLoRA) → **TTS** (Habibi-TTS, zero-shot voice cloning) → Speech

## Results

**ASR** (test set: 448 samples)

| Model | WER |
|---|---|
| Whisper small, zero-shot | ~80% |
| SeamlessM4T, zero-shot | 44.2% |
| Whisper large-v3, zero-shot | 38.2% |
| **Whisper small, fine-tuned (this project)** | **10.9%** |

**LLM** (all fine-tuned with QLoRA on the same dataset)

| Model | Parameters | Validation perplexity |
|---|---|---|
| **LLaMA 3.2 3B** | 3B | **4.50** |
| Atlas-Chat 9B | 9B | 7.14 |
| SILMA 9B | 9B | 9.14 |

**TTS** (5 native Algerian Arabic speakers): overall MOS 3.75/5. Intelligibility 4.60, voice consistency 4.80, dialectal fidelity 3.20, naturalness 2.40.

## Models and datasets (Hugging Face)

- Whisper (ASR): [whisper-algerian-medical-final](https://huggingface.co/abdeldjalil-smaili/whisper-algerian-medical-final)
- LLaMA 3.2 3B (LLM): [llama3b-algerian-medical](https://huggingface.co/abdeldjalil-smaili/llama3b-algerian-medical)
- Speech dataset: [algerian-medical-asr-v3](https://huggingface.co/datasets/abdeldjalil-smaili/algerian-medical-asr-v3) (2,237 samples: 87 real recordings + 2,150 synthetic)
- Q&A dataset: [algerian-medical-llm-v3](https://huggingface.co/datasets/abdeldjalil-smaili/algerian-medical-llm-v3) (101,265 pairs, built from the DZChatbot corpus and a Moroccan medical corpus adapted to Algerian vocabulary with PADIC and MADAR)

## Notebooks

| Notebook | Content |
|---|---|
| `01_asr_whisper_finetuning.ipynb` | Fine-tuning Whisper small on Algerian medical speech |
| `02_llm_llama32_3b_finetuning.ipynb` | QLoRA fine-tuning of LLaMA 3.2 3B |
| `03_tts_habibi.ipynb` | Text-to-speech with Habibi-TTS |
| `04_chatbot_pipeline.ipynb` | Full pipeline: ASR, RAG, LLM and TTS |

The notebooks were developed in Google Colab. API tokens are not included: when a notebook needs a Hugging Face token, it asks for it at runtime.

## Limitations

- Most of the training speech (2,150 of 2,237 samples) is synthetic, and the test set comes from the same mix. The WER does not prove generalisation to new speakers or regional accents.
- Perplexity is compared across models with different tokenizers, so it is indicative, not strictly comparable.
- TTS naturalness is low (2.40/5) and the evaluation is small (5 raters, 5 phrases).
- This is a research prototype. It was tested in a controlled setting only, not with real patients, and it is not a medical device or a substitute for professional advice.
