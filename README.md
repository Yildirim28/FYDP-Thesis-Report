# Bangla Speech-to-Text Homophone Ambiguity Resolution

This repository contains the final-year design project thesis report for **SHUDDHO**, a lightweight post-processing framework that resolves homophone ambiguity in Bangla automatic speech recognition (ASR).

**Full report:** [Bangla-Speech-to-Text-Homophone-Ambiguity-Resolution.pdf](./Bangla-Speech-to-Text-Homophone-Ambiguity-Resolution.pdf)

## Project Summary

Bangla contains many words that sound identical but have different spellings and meanings. Because the acoustic signal alone cannot distinguish a true homophone, speech recognizers often choose the spelling that appeared most frequently in their training data. The resulting word is validly spelled, so ordinary spell-checking cannot detect the semantic error.

The thesis introduces **SHUDDHO**, a modular correction layer that runs after an existing ASR engine. It detects possible homophones, evaluates each spelling using acoustic, contextual, fluency, and frequency evidence, and rewrites the transcript only when the alternative is sufficiently more likely. This conservative decision rule improves semantic accuracy while limiting damage to words that were already transcribed correctly.

## SHUDDHO Pipeline

1. **Transcribe:** A frozen Whisper or Wav2Vec 2.0 model produces the initial Bangla transcript.
2. **Detect:** A phonological confusion dictionary identifies homophones and safely handles common noun suffixes.
3. **Generate candidates:** The system retrieves all verified spellings associated with the detected pronunciation.
4. **Score evidence:** Each candidate receives scores from the ASR model, a frequency-balanced BanglaBERT context model, a standard BanglaBERT fluency model, and corpus-derived word-frequency priors.
5. **Fuse evidence:** Class-specific regularized linear models combine the evidence into calibrated candidate probabilities.
6. **Apply a conservative gate:** A word is replaced only when the best alternative exceeds the original ASR output by a calibrated margin.

## Dataset and Evaluation

| Item | Scale |
| --- | ---: |
| Homophone groups | 208 |
| Prompt sentences | 425 |
| Speech recordings | 1,028 |
| Native speakers | 5 |
| External corpus transcripts mined | 218,703 |
| Balanced contrastive sentence pairs | 25,237 |

The evaluation covers true homophones, post-alveolar flap variations, nasalized pairs, and phonetic control groups. Experiments use group-disjoint five-fold cross-validation, an additional speaker-disjoint robustness check, and paired group bootstrap significance testing with 1,000 resamples.

## Key Results

The final scaled SHUDDHO configuration reported in the thesis achieved:

| Metric | Whisper baseline | Whisper + SHUDDHO |
| --- | ---: | ---: |
| Homophone Error Rate | 26.2% | **21.0%** |
| Rare-word error rate | 68.6% | **50.0%** |
| Word Error Rate | 16.3% | **15.9%** |
| Balanced accuracy | 58.3% | **68.1%** |
| Correction precision | - | **78.0%** |

Additional findings include:

- Whisper's homophone error rate was 15.3% for frequent target words but 69.5% for rare target words, demonstrating a strong frequency bias.
- Frequency-balanced context training increased rare-candidate accuracy from 38.3% to 63.2%.
- The final configuration reduced homophone errors by 19.8% relative to the Whisper baseline.
- Improvements remained stable when both homophone groups and speakers were held out.
- Text-only SHUDDHO also improved a Wav2Vec 2.0 CTC recognizer, demonstrating cross-architecture portability.
- Post-processing added approximately 13.7% inference overhead while remaining faster than real time.

## Work Completed

- Reviewed Bangla ASR, contextual biasing, and homophone-correction research.
- Constructed and annotated a multi-speaker Bangla homophone speech benchmark.
- Developed a phonological normalizer and suffix-aware candidate lookup process.
- Mined OpenSLR-53 and Bengali.AI text for lexical-frequency analysis and context-model training.
- Diagnosed frequency bias, tokenizer fragmentation, and evaluation-data contamination risks.
- Trained a frequency-balanced BanglaBERT context scorer.
- Built class-specific multi-source evidence fusion and conservative rewrite gating.
- Evaluated Whisper and Wav2Vec 2.0 using disjoint cross-validation, ablation studies, and statistical significance tests.
- Profiled runtime, over-correction, phonological error classes, limitations, and deployment constraints.

## Main Contributions

- A dedicated Bangla homophone speech benchmark with phonological class labels.
- Empirical evidence that ASR systems strongly favor frequent spellings for acoustically identical words.
- A decoupled correction architecture that does not require retraining the upstream ASR model.
- A frequency-balanced contextual training strategy that reduces majority-word shortcuts.
- An evaluation protocol that measures homophone error, rare-word performance, over-correction, and statistical significance.

## Limitations and Future Work

The current corpus contains read speech from five speakers using standard colloquial Bangladeshi Bangla. Performance on spontaneous speech, code-switching, regional dialects, and broader speaker populations remains to be evaluated. Nasalized homophone pairs remain particularly difficult, and dictionary-only detection cannot recover every out-of-vocabulary ASR error.

Future work proposed in the report includes neural grapheme-to-phoneme matching, targeted acoustic analysis for nasalization, wider discourse context, ASR lattice rescoring, hard-negative mining, and evaluation on spontaneous multi-dialect speech.

## Authors

- Md. Tahmid Hasan
- Md. Shamim Osman Chowdhury
- Monika Akter
- Md. Minhajul Kabir Nishat
- Md. Monirul Islam

Department of Computer Science and Engineering, United International University, 2026.
