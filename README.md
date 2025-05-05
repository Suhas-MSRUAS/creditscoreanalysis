# 🏦 Decentralized Credit Scoring on Compound V2

This project implements a self-driven, end-to-end decentralized credit scoring system using raw transaction-level data (specifically `deposits` and `liquidates`) from the Compound V2 protocol.

## 📌 Objective

To design a behavioral credit scoring system that:
- Evaluates wallet activity based on protocol participation.
- Classifies wallets as "good" or "bad" using engineered features.
- Promotes healthy interaction with the lending protocol.

## 📂 Dataset

Three selected JSON datasets containing:
- `deposits`: Records of users depositing assets into the Compound protocol.
- `liquidates`: Events where undercollateralized users were liquidated.

Other types like `borrows`, `repays`, and `withdraws` were intentionally excluded.

## ⚙️ Methodology

### 1. **Data Loading**
All transactions were parsed from the three JSON files and separated into `deposits` and `liquidates`.

### 2. **Feature Engineering**
For each wallet, the following features were extracted:
- **Total Deposit (USD)**: Sum of all deposit values.
- **Number of Deposits**: Total count of deposit transactions.
- **Deposit Duration**: Days between first and last deposit.
- **Unique Assets Deposited**: Number of different assets deposited.
- **Times Liquidated**: Number of times the wallet was liquidated.
- **Total Liquidated Value (USD)**: Sum of amounts liquidated.

### 3. **Scoring Logic**

Raw score (unnormalized) is calculated using a weighted formula:
```python
score = (
    0.4 * log1p(total_deposit_usd) +
    0.2 * deposit_duration_days +
    0.2 * unique_assets -
    0.2 * times_liquidated -
    0.2 * log1p(total_liquidated_usd)
)
```

- **Positive contributors**: More deposits, longer engagement, diversified assets.
- **Negative contributors**: Frequent or high-value liquidations.

### 4. **Normalization**
Raw scores were min-max normalized to a 0–100 scale for intuitive comparison.

### 5. **Output**
Top 1,000 wallets by credit score were saved as:
```
wallet_scores.csv
```

## 🧠 Behavioral Assumptions

- **Good Wallets**: Engage in sustained deposits across multiple assets, avoid liquidation.
- **Bad Wallets**: Exhibit risky behavior leading to frequent or large-scale liquidation.

## 🧪 Example Scores

| Wallet Address                               | Credit Score |
|----------------------------------------------|--------------|
| `0x0f3c2476fbf0ed09dff00ea7f4ef252dcc72e6f1` | 100.00       |
| `0x4654f56a64301b9b582f843f97332d96ead11ff8` | 99.08        |
| `0xe20ee1d250c8d3e0bd140306121001bfdc7a1201` | 94.55        |
| `0xf0b22f7e45a223a106c35eda11bd055a70a234dd` | 69.83        |
| `0x18ab13f62635bec69fcc1d2ce3dbe5e0bdef1b99` | 69.75        |

## 🚀 How to Run

1. Place your `file1.json`, `file2.json`, and `file3.json` in a `data/` directory.
2. Run the main script:
```bash
python credit_scoring.py
```
3. The output file `wallet_scores.csv` will be generated in the current directory.

## 📈 Extensions (Future Work)

- Integrate borrow/repay activity for richer scoring.
- Cluster wallets into behavioral segments.
- Build a real-time dashboard using Streamlit or Dash.
