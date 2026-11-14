# VLM Edge Profiling Results

Raw inference-profiling measurements for five compact vision-language models on two edge platforms: an **NVIDIA Jetson Orin Nano 8GB** (FP16, GPU) and a **Raspberry Pi 5 16GB** (FP32, CPU-only).

Each model was evaluated on 50 VQAv2 samples with per-stage timing instrumentation.

## Results — Jetson Orin Nano (FP16)

| Model | Total (ms) | Throughput (tok/s) | Peak mem (MB) | Vision stage (ms) | VQAv2 EM |
|---|---|---|---|---|---|
| LFM2-VL-450M | 267.97 | 14.06 | 901.8 | 35.18 | 0.807 |
| Florence-2-base-ft | 332.22 | 29.91 | 527.2 | 133.05 | 0.753 |
| SmolVLM-256M | 1623.90 | 7.47 | 886.7 | 393.89 | 0.473 |
| SmolVLM-500M | 1630.29 | 6.97 | 1473.4 | 390.29 | 0.613 |
| FastVLM-0.5B | 3986.52 | 8.50 | 1345.9 | 338.35 | 0.193 |

## Results — Raspberry Pi 5 (FP32, CPU)

| Model | Total (ms) | Throughput (tok/s) | Peak mem (MB) | VQAv2 EM |
|---|---|---|---|---|
| LFM2-VL-450M | 7,444.0 | 1.22 | 2901.1 | 0.720 |
| Florence-2-base-ft | 12,261.2 | 2.92 | 2286.4 | 0.640 |
| FastVLM-0.5B | 59,698.1 | 0.81 | 4565.3 | 0.160 |
| SmolVLM-500M | 62,338.8 | 1.17 | 4588.7 | 0.460 |
| SmolVLM-256M | 76,022.5 | 2.38 | 3437.7 | 0.440 |

## Findings

- **Parameter count does not predict latency.** LFM2-VL-450M is both larger and ~6x faster than SmolVLM-256M on Jetson, and scores higher on VQAv2.
- **The vision encoder dominates on Jetson**, accounting for 24–35% of total inference time across models.
- **CPU-only inference is 20–47x slower.** The same models on Raspberry Pi 5 fall to roughly 1–3 tok/s, below any interactive response budget.
- **Accuracy and speed are not correlated** across this model set — the fastest model (LFM2-VL-450M) also scores highest.

## Layout

```
jetson/   FP16 results, NVIDIA Jetson Orin Nano 8GB
rpi5/     FP32 results, Raspberry Pi 5 16GB (CPU-only)
```

Each JSON records `timing_ms` (prefill / decode / total), `components_ms` (vision, attention, feedforward, normalization), `throughput_tok_s`, `memory_peak_mb`, and VQAv2 exact-match accuracy.
