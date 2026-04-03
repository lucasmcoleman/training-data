# training-data

Training data generators for [Foundry](https://github.com/lucasmcoleman/Foundry) fine-tuning pipelines.

## Workflows

### ZeroClaw (datagen/)

Synthetic tool-call training data generator. Produces JSONL conversations where an assistant uses structured tool calls (search, code execution, file operations, etc.) to solve user requests.

```bash
# Generate training data
python datagen/generate.py --count 500

# Bulk generation with parallelism
python datagen/generate_bulk.py --count 5000 --workers 4
```

Key files:
- `datagen/generate.py` — Single-threaded generator
- `datagen/generate_bulk.py` — Parallel bulk generator
- `datagen/scenarios.py` — Base scenario definitions
- `datagen/scenarios_extended.py` — Extended scenario library
- `datagen/scenarios_generated.py` — Auto-generated scenarios
- `datagen/zeroclaw_tools.py` — Tool definitions and formatting

### Carolina Ground Truth (gardener/)

NC Master Gardener training data generator. Uses Claude API as a teacher model to generate expert gardening conversations covering soil science, pest management, permaculture, and seasonal planning for North Carolina.

```bash
# Set your API key
export ANTHROPIC_API_KEY=sk-ant-...

# Generate training data (~3000 examples, ~$15-25 with Sonnet)
python gardener/generate.py --count 3000

# With ebook reference material for higher quality
python gardener/extract_references.py /path/to/ebooks/ gardener/references/
python gardener/generate.py --count 3000 --references gardener/references/

# Generate from ebooks directly
python gardener/generate_from_books.py /path/to/ebooks/
```

Key files:
- `gardener/generate.py` — Claude API teacher-model generator
- `gardener/generate_from_books.py` — Ebook-to-training-data pipeline
- `gardener/extract_references.py` — Ebook text extraction
- `gardener/system_prompt.py` — Carolina Ground Truth persona
- `gardener/topics.py` — 39 gardening subcategories

### Dataset Upload

```bash
# Upload generated dataset to HuggingFace
python upload_dataset.py --repo user/dataset-name --file datagen/output.jsonl
```

## Output Format

Standard HuggingFace chat template JSONL:
```json
{"messages": [{"role": "system", "content": "..."}, {"role": "user", "content": "..."}, {"role": "assistant", "content": "..."}]}
```

Compatible with Foundry's training pipeline and any HuggingFace SFTTrainer workflow.

## License

MIT
