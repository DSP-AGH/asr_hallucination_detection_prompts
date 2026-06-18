# Prompts for LLM-Based ASR Hallucination Detection

Prompts used for the LLM-based detection experiments (Section 3.2) of:

> **From Text Metrics to Model Internals: A Study of Whisper ASR Hallucination Detection**
> Jan Jasiński, Mateusz Barański, Julitta Bartolewska, Marcin Witkowski, Konrad Kowalczyk
> *Interspeech 2026*

The paper studies Whisper large-v3 hallucination detection on real-speech, human-annotated data (the HALAS dataset) across three paradigms: text-based, LLM-based, and decoder internal-state probing. This repository releases the four prompts behind the **LLM-based** paradigm so that the results in Figure 2 can be reproduced and the prompts reused.

## The prompts

The four prompts form a **cumulative chain** — each builds on the previous one. The first three configurations in the paper (GPT-4o mini, Gemini 2.0 Flash, Gemini 3.0 Flash) all use the *same* control prompt and differ only in the model. The prompt enhancements were then applied on top of Gemini 3.0 Flash.

| File | Configuration | Setting | Fig. 2 label |
|------|---------------|---------|--------------|
| `01_control.txt` | Baseline reproduced from Atwany et al.: defines hallucination vs. non-hallucination errors, includes five examples, returns a classification label | Oracle | GPT-4o mini / Flash 2.0 / Flash 3.0 |
| `02_hallucination_characteristics.txt` | Control plus the Whisper large-v3 non-speech hallucination phrase list (Barański et al.), injected as a domain-specific error characteristic | Oracle | + Hallu. Charac. |
| `03_few_shot_examples.txt` | Adds targeted few-shot examples drawn from the HALAS training split | Oracle | + Few-Shot Examples |
| `04_reference_free.txt` | Adapts the above to a strictly reference-free setting: the ground-truth transcript is removed and detection relies on intrinsic textual anomalies and error-pattern recognition | Reference-free | + Ref. Free |

Because the prompts are cumulative, `03` already contains the hallucination characteristics from `02`, and `04` contains everything in `03` minus the ground-truth reference. The control prompt is reproduced from:
> Atwany et al., *"Lost in Transcription, Found in Distribution Shift: Demystifying Hallucination in Speech Foundation Models"*, Findings of ACL 2025.
> https://aclanthology.org/2025.findings-acl.1190/

## Input / output format

Each prompt expects a single ASR prediction and returns a short rationale plus a label:

```
Input
-----
Generated Output: <text produced by the ASR system>

Output
------
Reasoning: <one-sentence explanation of the internal clues>
Classification: <Non-Hallucination Error | Hallucination Error | No Error>
```

For the binary hallucination-detection metric reported in the paper, the three-way label is collapsed to *hallucination* (`Hallucination Error`) vs. *not hallucination* (`Non-Hallucination Error` or `No Error`).

## Models and headline results

Results in the paper were obtained with GPT-4o mini, Gemini 2.0 Flash, and Gemini 3.0 Flash; the prompt enhancements (`02`–`04`) were evaluated on Gemini 3.0 Flash. F1 scores reported in the paper:

| Configuration | Model | F1 |
|---------------|-------|----|
| Control | GPT-4o mini / Flash 2.0 | ≈ 41% |
| Control | Flash 3.0 | 49.3% |
| + Hallucination characteristics | Flash 3.0 | 56.2% |
| + Few-shot examples | Flash 3.0 | **58.7%** (best LLM config; accuracy 88.4%) |
| Reference-free | Flash 3.0 | 32.8% |

For context, even the strongest LLM configuration did not surpass the lightweight XGBoost text-feature classifier (F1 62.8%) reported in the paper, and the reference-free setting suffered a sharp collapse.

## Data

The non-speech hallucination phrase list used in `02`–`04` comes from:

> M. Barański, J. Jasiński, J. Bartolewska, S. Kacprzak, et al. "Investigation of Whisper ASR Hallucinations Induced by Non-Speech Audio." *ICASSP 2025.*
> https://ieeexplore.ieee.org/abstract/document/10890105

The few-shot examples in `03` - `04` are drawn from the **HALAS** training split, and evaluation uses its test split:

> M. Barański, J. Jasiński, J. Bartolewska, M. Witkowski, K. Kowalczyk. "HALAS: A Human-Annotated Dataset of Hallucinations of Modern ASR Systems." *Interspeech 2026.*
>


## Citation

If you use these prompts, please cite the paper:

```bibtex
@inproceedings{jasinski2026textmetrics,
  title     = {From Text Metrics to Model Internals: A Study of Whisper ASR Hallucination Detection},
  author    = {Jasi{\'n}ski, Jan and Bara{\'n}ski, Mateusz and Bartolewska, Julitta and Witkowski, Marcin and Kowalczyk, Konrad},
  booktitle = {Interspeech 2026},
  year      = {2026}
  % TODO: add pages / DOI once available
}
```


## License

The prompt text is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/), consistent with the open-access licence applied to the paper.
