# Context Rot
Original source: [Context Rot, Chroma Research](https://www.trychroma.com/research/context-rot)

Context Rot refers to the phenomenon where LLM performance degrades as input (context) length increases, even on simple tasks — contradicting the assumption that long-context capability is a solved problem.

## Background

Standard long-context benchmarks like **Needle in a Haystack (NIAH)** report near-perfect scores, leading many to believe models handle long contexts uniformly. Chroma's research challenges this assumption by revealing that performance degradation is real, non-linear, and highly sensitive to how information is presented.

## Key Findings

- Performance degrades non-uniformly as input length grows
- Semantic-matching tasks suffer more than lexical retrieval tasks
- Lower needle-question similarity accelerates degradation with longer contexts
- Even a single distractor reduces accuracy; multiple distractors compound the effect
- Paradoxically, shuffled (incoherent) haystacks outperform logically structured ones
- Models fail to accurately replicate repeated word sequences at extreme lengths

## Experiments

### NIAH Extensions

Standard NIAH was extended across four dimensions:

| Dimension | Description |
|---|---|
| Needle-question similarity | 8 needles tested with cosine similarity 0.445–0.829 |
| Distractor injection | Baseline / single distractor / four distractors |
| Needle-haystack similarity | Topically related vs. unrelated haystack |
| Haystack structure | Original vs. sentence-shuffled variants |

### LongMemEval

Conversational QA benchmark with ~113k token contexts. Models show significantly higher performance on focused prompts vs. full prompts — revealing that even when the relevant information exists in context, models fail to use it reliably.

### Repeated Words Task

Models replicate sequences of repeated words with one unique word inserted at varying positions. Context length ranges from 25 to 10,000 words (1,090 variations). Common failure modes:
- Generating incorrect words
- Undercounting output length
- Misplacing the unique word

## Model-Specific Patterns

| Model Family | Behavior |
|---|---|
| Claude | Lowest hallucination rate; tends toward abstention when uncertain |
| GPT | Highest hallucination rate with distractors; variable output in repeated-word tasks |
| Gemini | Generates random non-input words starting around 500–750 words |
| Qwen | More stable but shows refusal patterns at extreme lengths |

## Implications

- **Context engineering** — how information is arranged, not just whether it exists — is critical for reliability
- Structural properties, information placement, and semantic matching all significantly affect outcomes
- Real-world applications, which are more complex than these minimal benchmarks, likely suffer even more severe degradation
- Current long-context benchmarks conflate input length with task difficulty, making them insufficient proxies for real performance

## Reference

<a id="Chroma_Context_Rot"></a>
[1] [Context Rot, Chroma Research](https://www.trychroma.com/research/context-rot)  
[2] [chroma-core/context-rot (GitHub)](https://github.com/chroma-core/context-rot)  
