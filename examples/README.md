# Examples: Inputs & Ground Truths

The exact experiment inputs and reference (ground-truth) outputs.

Each use case has its own folder containing everything for that case:


| Folder                         | Use case                 |
| ------------------------------ | ------------------------ |
| `1_HR_and_Payroll_Mobile_App/` | HR & payroll mobile app  |
| `2_AI_Study_Assistant/`        | AI study assistant       |
| `3_Plant_Identification_App/`  | Plant identification app |
| `4_Movie_Seat_Booking/`        | Movie seat booking app   |


## Folder contents

Every use-case folder has the same layout:

```
<use_case>/
  input.txt          # Raw project description fed to the Requirement Extractor
  RA/                # Requirements Analysis documents
    ground_truth.md  # Reference RA document
    balanced_first.md    balanced_second.md    balanced_third.md
    quality_focused_first.md  quality_focused_second.md  quality_focused_third.md
    cost_focused_first.md  cost_focused_second.md  cost_focused_third.md
  DE/                # Design & Estimation documents (same 10-file layout as RA/)
    ground_truth.md
    balanced_first.md ... cost_focused_third.md
```

`ground_truth.md` is the human reference document used to evaluate generated outputs. The
`*_first.md` / `*_second.md` / `*_third.md` files are generated outputs selected as described below.

## How the top files are selected

Each experiment configuration is a **combination** of `(framework, workflow, llm, prompt_level)`.
Every combination was executed **5 times** (5 runs) on all use cases. Outputs are scored with a
**Composite Score (CS)** that blends two normalized sub-metrics:

- **SS** — Semantic Similarity to the ground truth (ROUGE-1, ROUGE-L, METEOR, BERTScore, BLEU-4).
- **CR** — Cost Rate (latency and token usage, inverted so that cheaper/faster scores higher).

Three CS weighting schemes drive the three file families:

| File prefix    | Weighting scheme | Weights (SS / CR) |
| -------------- | ---------------- | ----------------- |
| `balanced_*`   | CS equal         | 0.5 / 0.5         |
| `quality_focused_*` | CS SS-weighted   | 0.8 / 0.2         |
| `cost_focused_*` | CS CR-weighted   | 0.2 / 0.8         |

Selection is done per use case, in two steps:

1. **Pick the top-3 combinations** for each scheme, ranked by the scheme's CS averaged over the
   5 runs for that use case (`first` = best, `third` = third best).
2. **Pick the best run** for each selected combination: among the 5 runs, take the run whose CS
   (for that scheme and use case) is highest, and use that run's `RA.md` / `DE.md`. The RA and DE
   files in a given slot come from the same winning run.

Note: the input descriptions are drawn from public app-store style product descriptions and may
reference third-party product names/trademarks owned by their respective vendors. They contain
no secrets or personal data. They are included for reproducibility of the experiment inputs.

## Sources


| Use case                 | Source                                                                                                                                                                                                                                                                          |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HR & payroll mobile app  | Paylocity, "Paylocity mobile application," App Store / Google Play product listing, 2025. [App Store](https://apps.apple.com/us/app/paylocity/id652438572) · [Google Play](https://play.google.com/store/apps/details?id=com.paylocity.paylocitymobile)                         |
| AI study assistant       | Codeway Studios, "Nerd AI – AI study assistant," App Store / Google Play product listing, 2025. [App Store](https://apps.apple.com/us/app/nerd-ai-math-problem-solver/id6449704549) · [Google Play](https://play.google.com/store/apps/details?id=com.codeway.homework)         |
| Plant identification app | Glority Global Group, "PictureThis – Plant identifier," App Store / Google Play product listing, 2025. [App Store](https://apps.apple.com/us/app/picturethis-plant-identifier/id1252497129) · [Google Play](https://play.google.com/store/apps/details?id=cn.danatech.xingseus) |
| Movie seat booking app   | Internal use case description                                                                                                                                                                                                                                                   |


