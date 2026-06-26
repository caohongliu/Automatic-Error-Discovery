# Error Discovery — Focused Pipeline

A Jupyter notebook for **LLM-driven error taxonomy discovery** from AI agent transcripts.

Given a set of agent conversation logs and a policy document, this pipeline automatically:
- Identifies failures in each transcript and maps them to a growing error taxonomy
- Proposes new error types when a failure is *meaningfully different* (different root cause, policy impact, or user harm)
- Captures open observations for any behavior worth noting
- Produces analytics on discovery accumulation, convergence, and per-dimension rates

## Design Principles

1. **No structured scoring** — the LLM freely identifies what went wrong, not against a fixed rubric
2. **Relaxed gate** — new types are invited when a failure is *meaningfully different*, not only as a last resort
3. **Unconditional open-observation slot** — every transcript yields a free-form observations list, capturing candidates that don't yet have a name
4. **Discovery analytics** — accumulation curve, per-dimension rate, convergence check, proposals table, and Markdown taxonomy export

## Prerequisites

- Python ≥ 3.10
- An Azure OpenAI resource with a chat-completion model deployed (e.g. `gpt-5`)

## Installation

```bash
pip install -r requirements.txt
```

## Configuration

1. Copy `.env.example` to `.env` and fill in your credentials:
   ```bash
   cp .env.example .env
   ```

2. Open `error_discovery_focused.ipynb` and edit the **Section 8 — Configure & Run** block:
   - Set `JSON_PATH` to your tau2-bench results JSON (see `sample_data/input_schema.json` for the format)
   - Set `POLICY_PATH` to your domain policy Markdown file
   - Set `OUTPUT_DIR` for where results should be saved
   - Optionally override `MODEL` (defaults to `AZURE_OPENAI_MODEL` env var or `gpt-4o`)

## Input Format

See [`sample_data/input_schema.json`](sample_data/input_schema.json) for the expected structure.

Input is a JSON file with two top-level keys:
- `tasks` — list of task objects, each with an `id` field and ground-truth information
- `simulations` — list of simulation objects, each with `task_id`, `trial`, `reward`, and a conversation transcript

## Outputs

| File | Description |
|---|---|
| `registry_focused.json` | Full error registry with per-type metadata and discovery log |
| `run_focused.json` | Per-task flagged errors, new proposals, and open observations |
| `taxonomy_focused.md` | Markdown taxonomy table grouped by dimension |

## Seeded Error Taxonomy

The pipeline seeds 6 base dimensions out of the box:

| Dimension | Question |
|---|---|
| `user_intent_adherence` | Did the agent honor preferences and correctly read user input? |
| `user_question_fulfillment` | Did the agent answer the user's direct questions? |
| `policy_violation` | Did the agent's actions and decisions follow policy? |
| `policy_faithfulness` | Did the agent correctly state policy rules? |
| `data_faithfulness` | Did the agent accurately report data from tool outputs? |
| `tool_efficiency` | Were the agent's tool calls reasonable given available context? |

New dimensions and error types are discovered automatically during the run and exported to the taxonomy Markdown.

## Citation

If you use this pipeline in your research, please cite:
```bibtex
@article{cao2026beyond,
  title   = {Beyond Task Completion: Revealing Corrupt Success in LLM Agents through Procedure-Aware Evaluation},
  author  = {Cao, Hongliu and Driouich, Ilias and Thomas, Eoin},
  journal = {arXiv preprint arXiv:2603.03116},
  year    = {2026},
  url     = {https://arxiv.org/abs/2603.03116}
}
```

## License

Apache 2.0 — see [LICENSE](LICENSE) for details.
