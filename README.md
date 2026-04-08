## Strategy Definition — Chaikin Oscillator (CHO) Long/Short Rules

This strategy uses the Chaikin Oscillator (CHO) to capture shifts in underlying buying and selling pressure *before* they appear in price trends.  
CHO measures the momentum of the Accumulation/Distribution Line (ADL), which incorporates both price location within the daily range and trading volume.

### Intuition
We use a single threshold value:

- When CHO is above the threshold, it indicates stronger buying pressure and bullish momentum.
- When CHO is below the threshold, it indicates weaker or negative buying pressure and potential bearish conditions.

The strategy reacts to whether CHO is above or below this threshold, rather than just zero.

## How CHO Is Constructed

### 1. Money Flow Multiplier (MFM)

$$
\text{MFM}_t =
\frac{(Close_t - Low_t) - (High_t - Close_t)}
     {High_t - Low_t}
$$

Interpretation:
- MFM close to +1 → close near high → buying pressure  
- MFM close to -1 → close near low → selling pressure  
- MFM ≈ 0 → neutral day  

If High_t = Low_t, we set MFM = 0 to avoid division-by-zero.


### 2. Money Flow Volume (MFV)

$$
\text{MFV}_t = \text{MFM}_t \times Volume_t
$$

This weights the MFM by trading volume so high-volume days have a larger impact.


### 3. Accumulation/Distribution Line (ADL)

$$
ADL_t = \sum_{i=1}^{t} MFV_i
$$

- ADL trending upward → accumulation (buying)
- ADL trending downward → distribution (selling)


### 4. Chaikin Oscillator (CHO)

We compute two EMAs of ADL:

- Fast EMA (e.g. 5-period)  
- Slow EMA (e.g. 25-period)

Then:

$$
CHO_t = EMA_{\text{fast}}(ADL_t) - EMA_{\text{slow}}(ADL_t)
$$

- CHO > 0 → fast EMA above slow EMA → upward momentum  
- CHO < 0 → downward momentum  

CHO behaves like a *MACD of the ADL*, combining price location, volume, and momentum.


## Single Threshold Logic

We define a single scalar threshold:

- If CHO_t > threshold → bullish regime  
- If CHO_t < threshold → bearish/weak regime  

## Trading Rules

For each stock and at each time \( t \):

### Long (buy) rules

- Enter long when CHO moves from ≤ threshold to > threshold  
  (i.e. CHO crosses above the threshold).
- Exit long when CHO moves from ≥ threshold to < threshold.

### Short (sell) rules (if allow_short = True)

- Enter short when CHO moves from ≥ threshold to < threshold  
  (i.e. CHO falls below the threshold).
- Exit short when CHO moves from ≤ threshold to > threshold.

In other words:
- Above threshold → prefer long exposure.  
- Below threshold → prefer short exposure (if enabled), or stay flat in a long-only setup.
