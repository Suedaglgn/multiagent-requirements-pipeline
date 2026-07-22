# Examples: Inputs & Ground Truths

The exact experiment inputs and reference (ground-truth) outputs.

## Inputs (`test_inputs/`)

Four use cases. Each file is the raw project description fed to the Requirement Extractor.

| File | Use case |
|------|----------|
| `test_inputs/use_case_1.txt` | HR & payroll mobile app |
| `test_inputs/use_case_2.txt` | AI study assistant |
| `test_inputs/use_case_3.txt` | Plant identification app |
| `test_inputs/use_case_4.txt` | Movie seat booking app |

Note: these descriptions are drawn from public app-store style product descriptions and may
reference third-party product names/trademarks owned by their respective vendors. They contain
no secrets or personal data. They are included for reproducibility of the experiment inputs.

## Ground truths (`ground_truths/`)

Reference RA and DE documents used to evaluate generated outputs.

Layout and mapping to use cases (N = 1..4):

```
ground_truths/requirement_output/RA_<N>.md        # RA reference for use_case_<N>
ground_truths/design_estimation_output/DE_<N>.md  # DE reference for use_case_<N>
```
