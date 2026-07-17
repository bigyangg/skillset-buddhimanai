# Solana UI Patterns Reference

Wallet and on-chain display patterns specific to Solana frontends, adapted from production-grade wallet products. No DeFi protocol patterns included.

---

## 1. Wallet Connect Button States

Implement exactly three states — no more, no less:

```
State 1 - Disconnected:
  <Button onClick={connect}>Connect wallet</Button>

State 2 - Connecting:
  <Button disabled loading>Connecting...</Button>

State 3 - Connected (Dropdown):
  <Button variant="secondary">
    <GreenDot /> 7xKX...p2aB  <ChevronDown />
  </Button>
  -> Dropdown: [Copy address | View on explorer | --- | Disconnect]
```

**Rules:**
*   Use `@solana/wallet-adapter-react-ui` standard components if already installed — do not reinvent.
*   Enable `autoConnect` to restore sessions on page refresh.
*   If wallet extension is installed but locked: show "Unlock wallet" — not "Connect wallet."
*   If wallet extension is not installed: link to the wallet's official install page.

---

## 2. Address Formatting

**Never** show a raw base58 public key unless the user explicitly expands it.

```ts
export function truncateAddress(address: string, chars = 4): string {
  if (address.length <= chars * 2 + 3) return address;
  return `${address.slice(0, chars)}...${address.slice(-chars)}`;
}
// Default → "7xKX...p2aB"
// Compact → "7xK...2aB" (chars = 3)
```

**Rules:**
*   Always use `font-mono` for all address displays — proportional fonts break readability.
*   Always place a copy icon inline next to the address. Clicking copies the full raw address.
*   Show a transient "Copied" toast for 1.5 seconds after copy.
*   Place a separate `ExternalLink` icon for the explorer — do not link the address text itself.

---

## 3. Token Amount Display

See `reference/number-formatting.md` for the complete rules. Quick summary:

*   Convert raw lamports using mint decimals: `humanAmount = rawAmount / 10 ** mintDecimals`
*   Apply `font-mono tabular-nums` on all rendered amounts — mandatory.
*   Zero-subscript for ≥ 3 leading zeros: `0.00005835` → `0.0₄58` with `aria-label="0.00005835"`.
*   Always show token symbol next to amount: `"12.34 SOL"` not `"12.34"`.
*   USD approximation: displayed small, muted, below the primary amount.
*   Source attribution: "Est. via Pyth" or "Est. via Jupiter" — visible on hover or footnote.

---

## 4. Transaction Confirmation Dialogs

Before the user signs anything:

1. Show a full breakdown: what's sent, to whom (named if possible), network fee estimate, total.
2. Simulate the transaction first — show expected outcome before signing.
3. Addresses in the dialog: truncated with copy access, full address on hover.
4. Two-step confirm for high-value actions (threshold configurable): require a typed confirmation or drag-to-confirm.
5. **Never auto-sign.** Every signature requires an explicit user click.
6. Post-sign status loop: `Submitting → Confirming → Confirmed` with an explorer link at each stage.
7. On failure: decode the error into plain language. Provide a retry action.

---

## 5. Explorer Links

*   Always open in a new tab: `target="_blank" rel="noreferrer"`.
*   Match the cluster — devnet links go to the devnet explorer, not mainnet.
*   Use a small `ExternalLink` icon from `lucide-react` next to any explorer link text.
*   Pick one explorer per product (Solscan **or** Solana Explorer) — never mix on the same screen.

---

## 6. Network Indicator

If the app supports more than one cluster:
*   Show the active cluster name prominently in the header or nav bar.
*   **Mainnet:** neutral style, no warning color.
*   **Devnet:** amber/orange label — user must visually know funds are not real.
*   **Testnet:** gray/muted label.
*   Cluster switcher: behind a click (not hover) to avoid accidental changes.

---

## 7. RPC Loading & Error States

*   Assume RPC can be slow or flaky. Set explicit timeouts on all calls.
*   Use **skeleton cards** for balance areas during load — not a spinner in the middle of a card.
*   For live-updating data (price feeds, tx status): subtle "updating" pulse indicator, not a full reload flash.

### Translated Solana Errors

| Raw Error | User-Facing Message |
| :--- | :--- |
| Insufficient funds for rent | "Your account needs a minimum balance to stay active on Solana. Keep at least 0.002 SOL." |
| Blockhash not found | Auto-retry once silently. If it fails again: "Network busy, please try again." |
| Transaction simulation failed | "Transaction preview failed. Slippage may be too tight — try adjusting your slippage setting." |
| Wallet rejected / User cancelled | **No error toast.** Quietly reset the flow to idle state. |
| Slippage exceeded | "Price moved beyond your slippage limit. Try increasing slippage in settings or retry." |

---

## 8. Numbers That Update in Real Time

*   `tabular-nums` required — non-negotiable for any live-updating value.
*   Do not animate number changes more than 300ms — a direction flash (green pulse up, red pulse down) is enough.
*   Color-blind rule: pair color with a directional arrow or `+` / `-` sign. Never rely on color alone.
*   Return to neutral text color after the flash — do not leave prices permanently green.
