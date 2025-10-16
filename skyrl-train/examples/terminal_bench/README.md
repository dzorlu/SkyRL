### Terminal-Bench integration

This example demonstrates how to use the `sandboxes` framework (the successor to Terminal-Bench) to generate training data for `SkyRL`. It supports running against the full suite of `terminal-bench` tasks.

This integration requires the `sandboxes` repo:
```bash
# From the SkyRL/skyrl-train directory
git clone https://github.com/laude-institute/sandboxes.git
```

There is an existing package conflict between `skyrl-train` and `sandboxes`. Resolve it by modifying `sandboxes/pyproject.toml` with the following:
* `rich==13.7.1`
* `requires-python = ">=3.12"`

### Training Workflow

**1. Convert the Terminal-Bench Tasks**

First, use the provided script to convert the original `terminal-bench` tasks to the new `sandboxes` format. Run this from the `sandboxes` repository root.

```bash
# Make sure you are in the 'sandboxes' directory
uv run python scripts/convert_tbench_tasks.py \
  --input-dir /path/to/your/terminal-bench/tasks \
  --output-dir ./converted_tasks
```

**2. Configure the Training Run**

Modify `skyrl-train/examples/terminal_bench/terminal_bench_config/terminal_bench.yaml` to point to your converted dataset.

```yaml
# ... existing config ...
tasks_dataset_path: "/path/to/your/sandboxes/converted_tasks"
environment_type: "docker" # "docker", "e2b", "modal", "daytona"
```

**3. Run the Training Pipeline**

The training pipeline requires a dummy dataset to kick off the process (this will be improved in the future).

```bash
# First, create the dummy dataset
uv run -- python examples/gsm8k/gsm8k_dataset.py

# Then, run the training script
bash examples/terminal_bench/run_tbench.sh
```

- **Generation only**: To launch the generator/serving process for rapid debugging without the trainer setup, you can run:
```bash
bash examples/terminal_bench/run_tbench_gen.sh
```