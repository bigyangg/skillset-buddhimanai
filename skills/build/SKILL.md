---
name: Web3 & Wallet Frontend Integration Guidelines
description: Best practices and standard protocols for wallet connection, address presentation, token number rendering, transaction confirmation safety, and RPC error management.
---

# Web3 & Wallet Frontend Integration: Senior Lead Guidelines

## Role & Framing
Approach every interface like a Lead Web3 Product Engineer or Design Partner at a top product studio producing user-facing crypto apps (e.g., Phantom, Solflare, or Rainbow wallet style). Every balance, address, transaction confirmation, and RPC interaction is a critical trust vector. An interface that jitters, displays scientific notations, leaks raw transaction errors, or connects clunkily destroys product confidence instantly. Build interface systems that are responsive, predictable, and visually robust.

---

## 1. Wallet Connect & Adapter Rules
The connection flow is the gateway to the application. Ensure it is frictionless and handles boundary states gracefully:

*   **Explicit State Tracking:** Keep the wallet connection button clean. Design exactly three visual states:
    1.  *Disconnected:* "Connect wallet" button (prominent action).
    2.  *Connecting:* Muted button with a locking spinner / skeleton text.
    3.  *Connected:* Secondary button displaying a green connectivity indicator, the truncated address string (e.g., `7xKX...p2aB`), and a dropdown arrow.
*   **Persistent Session:** Enable configurations (like `autoConnect` in React adapters) to automatically restore established sessions on page refreshes.
*   **The Locked State Exception:** If the browser extension is installed but locked, show a dedicated "Unlock wallet" action rather than executing a standard connect trigger.
*   **Failure Fallback Link:** If the requested browser wallet is not detected, redirect the click to the wallet's official installation landing page rather than failing silently.

---

## 2. Address Representation & Interaction
On-chain addresses are long and mathematically intimidating. Format them to match user readability:

*   **Standard Truncation Formula:** Always format public keys and addresses using a `FirstN...LastN` string wrapper.
    *   *Default Case:* Display the first 4 characters and the last 4 characters (`First4...Last4`, e.g., `7xKX...p2aB`).
    *   *Compact Case (Mobile):* Display first 3 and last 3 characters.
*   **Monospace Font Enforced:** Render all wallet addresses using monospace typography (`font-mono`). Proportional fonts cause address characters to misalign and look disjointed.
*   **Single-Click Copy Utility:** Place a copy icon or action directly adjacent to any displayed address. On click:
    1.  Copy the raw, full-length base58 address to the clipboard.
    2.  Trigger a transient visual confirmation (e.g. tooltip or pill stating "Copied!") for 1.5 seconds.
*   **Explorer Link Separation:** Place links to block explorers (e.g., Solscan, Solana Explorer) on a distinct external link icon (`ExternalLink`) next to the address. Do not link the address text itself, otherwise clicking to copy will navigate the user away.

---

## 3. Token Balances & Amounts
Token quantities span massive scales ($100k BTC to $0.000000000000000001 altcoins). Format numbers dynamically to prevent broken UI layouts:

*   **Use Tabular Numerals:** Always set numeric displays to `font-mono tabular-nums` to prevent visual layout shifts (jitter) when values update.
*   **Dynamic Decimal Precision:** Do not hardcode a static `.toFixed(2)` on token amounts.
    *   Compute decimals based on token value:
        $$\text{Decimals} = \max\left(2, \min\left(6, \lceil -\log_{10}(\text{Threshold} / \text{Price}_{\text{USD}}) \rceil\right)\right)$$
    *   Convert raw units (lamports/satoshis) into human-readable quantities using the mint decimals: $\text{Human Amount} = \text{Raw Quantity} / 10^{\text{Decimals}}$.
*   **Zero-Subscript Annotation:** For micro-token values with 3 or more leading zeros after the decimal, transition displaying characters to Unicode subscript format:
    *   *Example:* `0.00005835` displays as `0.0₄58` or `0.0₄5835`.
    *   *A11y:* Apply `aria-label="0.00005835"` over the span wrapper so screen readers can parse it correctly.
*   **Sign & USD Muting:** Always place the token ticker adjacent to the quantity (e.g., `12.35 SOL`). Show USD approximations in smaller, muted colors below or next to the main balance.

---

## 4. Transaction Confirmation Flows
Signing a transaction is a high-stakes moment where users commit real assets. Make the verification process explicit and clear:

*   **Full Breakdown Table:** Prior to signing, render a descriptive panel showing:
    1.  *Type of transaction:* (e.g., "Transfer", "Swap", "Approve Contract").
    2.  *Assets departing:* (e.g., "-12.50 SOL").
    3.  *Assets arriving:* (e.g., "+350.00 USDC").
    4.  *Network fees:* Estimated base network fee + priority fee.
*   **Simulated Outcomes:** Display the pre-transaction simulation execution status. Show the expected asset balances before the transaction is sent to the network.
*   **High-Value Confirmation Thresholds:** Introduce a confirmation guard input (e.g., typing "SEND" or slider drag actions) for values exceeding set limits to prevent accidental clicks.
*   **Step-by-Step Processing Feedback:** Cycle through clear statuses sequentially:
    $$\text{Awaiting Signature} \rightarrow \text{Submitting to RPC} \rightarrow \text{Confirming Block} \rightarrow \text{Confirmed / Success}$$
    Include links to explorers for active tx check paths.

---

## 5. Explorer Links & Network Indicators

*   **Link Targets:** Open all block explorer URLs in a new browser tab using standard `target="_blank" rel="noreferrer"` security settings.
*   **Match Cluster Environments:** Keep explorer destinations synchronized with the active connection cluster (e.g. do not point at mainnet history if the client wallet resides on devnet/testnet).
*   **Cluster Banner Indicator:** If the application supports multiple networks (devnet, mainnet-beta, localnet), place a visible status label in the top header:
    *   *Mainnet:* Classic neutral styling or subtle brand coloring.
    *   *Devnet/Testnet:* High-contrast warning tones (e.g. amber or gray status pills) alerting the user that assets are test currency.

---

## 6. RPC Loading States & Error Handlers
On-chain interfaces must survive high-latency periods:

*   **Skeleton Bounds:** Use skeleton cards that match layout shapes for wallet balances during load sequences. Avoid blank cards or central loaders.
*   **Solana/Web3 Specific Error Parsing:** Re-write raw error codes into helpful, action-oriented instructions:
    *   *Insufficient Funds:* "Unable to complete transaction. Account balance too low to cover rent minimums. Maintain at least 0.002 SOL."
    *   *Simulation Failed:* "Transaction simulation failed. Price slippage may have exceeded your limit. Adjust slippage in settings."
    *   *User Cancelled:* Silence warning alerts if the user declines to sign the transaction. Revert the workflow cleanly back to the button trigger without showing scary error overlays.

---

## 7. Verification & Self-Critique Checklist
Execute these critical checks before shipping web3 frontend modifications:

*   [ ] **The Jitter Test:** When numbers or prices flash and update, does the text stay perfectly still? (Checked for `tabular-nums`).
*   [ ] **Address Readability:** Are all public keys truncated correctly, displaying in monospace type, with copy buttons immediately accessible?
*   [ ] **Rent & Simulation Warnings:** Does the error panel translate raw code strings into instructions for covering rent and slippage boundaries?
*   [ ] **Double Sign Prevention:** Are all buttons locked in a loading state once a transaction submission has been initialized to prevent multiple submissions?
*   [ ] **Devnet Alignment Check:** Are all explorer destination links correctly aligned with the user workspace's target network settings?
*   [ ] **Review standard:** Would this portfolio view or transaction module pass a security and design check by lead engineers at a top wallet company?
