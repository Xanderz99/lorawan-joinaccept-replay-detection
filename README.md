# LoRaWAN Join-Accept Replay Detection

Code for the experiment in my MSc Cyber Security dissertation at the University of
Plymouth, *Lightweight Machine Learning-Based Anomaly Detection for LoRaWAN Networks*.

The notebook detects Join-Accept replay attacks at a LoRaWAN gateway. It parses a
Semtech UDP packet-forwarder capture, simulates benign join sessions and two kinds of
replay attacker (naive and timing-aware), computes seven gateway-level behavioural
features, and evaluates a Random Forest, Logistic Regression, Isolation Forest and a
payload-repeat baseline using a time-blocked, purged train/test split. A final step
computes the extra checks quoted in Chapters 3 to 5 of the dissertation.

## Contents

| File | What it is |
| --- | --- |
| `JoinAccept_Replay_Detection.ipynb` | The full pipeline, Steps 1 to 11, with explanations above each step and saved outputs |
| `normaltraffic.pcap` | The real background capture the notebook reads (about 15 minutes of traffic) |
| `requirements.txt` | Python packages needed to run it |

## How to run

```bash
git clone https://github.com/Xanderz99/lorawan-joinaccept-replay-detection.git
cd lorawan-joinaccept-replay-detection
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter lab JoinAccept_Replay_Detection.ipynb
```

Then run all cells in order. A full run takes well under a minute on a laptop.

To use a different capture, change `PCAP_PATH` in the first code cell. The parser reads
classic PCAP files with no extra dependencies; pcapng files need `scapy` installed.

## Reproducibility

All randomness comes from `SEED = 42` in the first code cell, so a run reproduces the
same dataset, split and scores. Step 10 repeats the experiment over seeds 42 to 51 and
reports the mean and standard deviation of each metric.

The main settings, all in the first code cell:

- `N_CAPTURE_LOOPS = 10`: copies of the real capture laid end to end (set to 1 for the
  original single-capture run)
- `REPLAY_SESSION_FRACTION = 0.18`: share of eligible victims that are attacked
- `N_MULTI_SEED_RUNS = 10`: repeats in Step 10

The timing figures in the notebook were measured on a 13-inch MacBook Air (Apple M4,
16 GB RAM) with Python 3.14.5 and scikit-learn 1.9.0. They show the relative cost of
the models, not the cost on gateway hardware.

## Outputs

Running the figures cell saves two PNG files next to the notebook:

- `fig4_4_confusion_matrices.png (Figure 4.4)`
- `fig4_5_feature_importance.png (Figure 4.5)`

These are ignored by git, so they are regenerated rather than committed.

## Limitations

This is a controlled, partly simulated proof of concept. The capture holds only one real
Join-Accept, so most benign Join-Accepts and all replay attacks are simulated on top of
real background traffic. See the *Interpretation and limitations* section at the end of
the notebook for the full discussion.
