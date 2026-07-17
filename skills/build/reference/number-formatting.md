# Number Formatting Spec

Strict display rules for all numeric values in crypto and Web3 UIs. Adapted from `sendaifun/solana-new` number-formatting skill — reworked for this skillset without DeFi protocol-specific logic.

> **Core principle:** Internal math always uses raw precision. These rules govern display only.

---

## Non-Negotiables

1. **Never show scientific notation.** `1.52e12` must display as `$1.52T`. No exceptions.
2. **Never truncate numbers with ellipsis.** Numbers are not text strings. Reduce decimals → abbreviate → tiny marker first.
3. **Never show `-0.00` or `-$0.00`.** Catch and suppress signed zero explicitly.
4. **Never render `NaN`, `undefined`, `Infinity`, or crash on null.** All display as `--`.
5. **Never hardcode decimal places.** No `.toFixed(2)` on token amounts — use dynamic precision.
6. **Always apply `font-mono tabular-nums`** to all numeric displays. Non-tabular fonts jitter on live updates and misalign in tables.
7. **Copy gives raw precision.** User copies `$1.2K` → clipboard gets `1234.5`. User copies `0.0₄58` → clipboard gets `0.00005835`.
8. **Zero-subscript requires `aria-label`.** Screen readers cannot parse `0.0₄58` — always provide expanded decimal via `aria-label`.

---

## The Six Number Types

| Type | Prefix/Suffix | Abbreviate? | Zero-subscript? | Exact-zero display |
| :--- | :--- | :--- | :--- | :--- |
| `fiat_value` | `$` prefix | Yes (K/M/B/T) | No | `$0.00` |
| `stable_value` | `$` prefix | Yes (K/M/B/T) | No | `$0.00` |
| `token_amount` | none | Compact only | Yes | `0` |
| `token_price` | `$` prefix | **Never** | Yes | `$0.00` |
| `percent` | `%` suffix | Never | No | `0.00%` |
| `ratio` | `x` suffix | Never | No | `0x` |

---

## Decision Pipeline (run in order)

```
1. null / NaN / Infinity?          → display "--"
2. Exact zero?                     → display type zero (e.g. "$0.00", "0", "0.00%")
3. Rounds to zero but isn't zero?  → tiny marker ("<$0.01", "<0.01%", "<0.01x")
4. ≥ 3 leading zeros after decimal? → zero-subscript: 0.0ₙXYZ
5. Compact context + value ≥ 1K + type allows abbreviation?
                                   → abbreviate: K / M / B / T
6. Otherwise: normal format with computed decimals
7. Still too wide? → reduce decimals → abbreviate → tiny marker → scale font
   NEVER overflow. NEVER ellipsis-truncate.
```

---

## Dynamic Decimal Computation

For `token_amount` and `token_price`, compute display decimals based on the token's USD price:

```
Compact:  decimals = clamp(ceil(-log10(0.01   / token_price_usd)), 0, 6)
Detailed: decimals = clamp(ceil(-log10(0.0001 / token_price_usd)), 0, 12)
```

If `token_price_usd` is unknown, fall back to 4 decimals and mark the value as approximate.

---

## Zero-Subscript Notation

For values with ≥ 3 leading zeros after the decimal point:

```
0.00005835  →  compact:  0.0₄58
            →  detailed: 0.0₄5835
```

Use Unicode subscript digits: `₀₁₂₃₄₅₆₇₈₉`

```tsx
// The subscript count is the number of leading zeros
<span aria-label="0.00005835">
  0.0<sub>4</sub>58
</span>
```

---

## Abbreviations (K / M / B / T)

Applies to: `fiat_value`, `stable_value`, compact `token_amount` only.
**Never abbreviate `token_price`, `percent`, or `ratio`.**

```
1,234.50   → compact: $1.2K   detailed: $1,234.50
1,520,000  → compact: $1.5M   detailed: $1,520,000.00
```

---

## Quick Examples Table

| Raw Value | Type | Compact | Detailed |
| :--- | :--- | :--- | :--- |
| `null` | any | `--` | `--` |
| `0` | `fiat_value` | `$0.00` | `$0.00` |
| `0.004` | `fiat_value` | `<$0.01` | `$0.004` |
| `0.00005835` | `token_price` | `0.0₄58` | `0.0₄5835` |
| `1234.5` | `fiat_value` | `$1.2K` | `$1,234.50` |
| `1520000000` | `fiat_value` | `$1.5B` | `$1,520,000,000.00` |
| `0.8234` | `percent` | `0.82%` | `0.8234%` |
| `2.5` | `ratio` | `2.5x` | `2.50x` |

---

## CSS Requirements

```css
/* Required on every element rendering a live-updating or tabular number */
.number-display {
  font-variant-numeric: tabular-nums;
  font-feature-settings: "tnum";
  font-family: var(--font-mono); /* e.g. JetBrains Mono, Fira Code */
}
```

---

## Copy Behavior

When the user copies a formatted display value, the clipboard must receive the **raw full-precision number string**, not the display format:

```ts
function copyNumber(rawValue: number) {
  // rawValue = 0.00005835, display showed "0.0₄58"
  navigator.clipboard.writeText(rawValue.toString());
  // clipboard receives: "0.00005835"
}
```
