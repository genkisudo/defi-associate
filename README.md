# Algorithmic Portfolio Rebalancer

An on-chain automation that maintains a target allocation (30% USDC / 70% WETH) in Aave v3, rebalancing via Uniswap v3 when drift exceeds a 10% threshold.

Built as a learning project with [Moccasin](https://github.com/Cyfrin/moccasin), a Python/Vyper smart contract framework.

## Stack

- **Language**: Python + Vyper
- **Framework**: Moccasin (`titanoboa`)
- **Protocols**: Aave v3, Uniswap v3, Chainlink
- **Networks**: Ethereum mainnet (fork), zkSync Era

## How It Works

1. **Deposit** — supply USDC and WETH to Aave v3
2. **Monitor** — fetch aToken balances and Chainlink prices
3. **Rebalance** — if either asset drifts >10% from target, withdraw → swap → redeposit

## Commands

```bash
# Run full deposit + rebalance against a mainnet fork
mox run deposit_and_rebalance

# Run only deposit
mox run deposit

# Run only rebalance
mox run rebalance
```

## Setup

```bash
# 1. Configure .env
cp .env.example .env
# Set MAINNET_RPC_URL and ANVIL1_PASSWORD_FILE

# 2. Import anvil wallet (key: 0xac0974...4f2ff80)
echo -n "mypassword" > ~/.anvil1_password
uv run python -c "
from eth_account import Account
import json, os
from pathlib import Path
acct = Account.from_key('0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80')
keystore = Account.encrypt(acct.key, 'mypassword')
Path(os.environ['HOME'] + '/.moccasin/keystores').mkdir(parents=True, exist_ok=True)
with open(os.environ['HOME'] + '/.moccasin/keystores/anvil1', 'w') as f:
    json.dump(keystore, f)
"
```

## Key Parameters

| Parameter | Value |
|---|---|
| Target USDC | 30% |
| Target WETH | 70% |
| Rebalance threshold | 10% drift |
| Uniswap fee tier | 0.3% |
| Slippage tolerance | 5% |

## Project Structure

```
script/
  _setup_script.py       # Token minting, aToken resolution
  deposit.py             # Aave supply logic
  rebalance.py           # Drift check + swap logic
  deposit_and_rebalance.py
tests/
  test_full.py           # End-to-end integration test
abis/                    # Fetched from block explorers
```
