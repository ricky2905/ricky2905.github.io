# What is a Dataset

A **dataset** is a structured collection of data, usually organized so that each record (row) corresponds to a single observation and each column corresponds to a variable or attribute describing the observation.  
In research and analysis contexts, a dataset provides the empirical basis for inference, modeling, and visualization [1].

### Formal (concise) definition
> “A collection of data, usually represented in tabular form, where rows correspond to individual entities or observations and columns correspond to variables or attributes describing those entities.” [1]

---

## Key Components

- **Observations / records / instances** — each row is one case (e.g., one log entry or one patient).  
- **Variables / features** — columns that can be quantitative (numeric) or qualitative (categorical).  
- **Values** — the individual cells in the table.  
- **Metadata** — data about the data (field descriptions, units, coding schemes, provenance).  

When documenting a dataset, include a short **data dictionary** listing each field, its type, and allowed values — this is essential for reproducible analysis and downstream statistical tests [6].

---

## Dataset Types & Data Quality

### By Structure
- **Structured** — relational tables, CSVs. *(Example: firewall logs)*  
- **Semi-structured** — JSON, XML, log files with fields *(common in telemetry)*.  
- **Unstructured** — free text, images, audio.

### By Measurement
- **Quantitative** — numeric variables (continuous or discrete).  
- **Qualitative** — categorical variables (nominal or ordinal).

### Quality Checklist (recommended)
- Total number of records `n`  
- Percent missing per field  
- Data formats *(timestamps in ISO 8601 recommended)*  
- Known filters / sampling used during collection  
- Unit tests or validation scripts for field ranges / regexes  

Quality metadata is required to validate assumptions for many statistical tests (for example, contingency-table χ² requires adequate counts per cell) [6].

---

## CSV Example & Aggregation

### Example: `logs_sample.csv`

```csv
timestamp,src_ip,dst_ip,src_port,dst_port,protocol,action,threat_type,bytes,response_time
2025-10-01T08:12:23Z,192.0.2.10,198.51.100.5,52344,443,TCP,ACCEPT,none,1240,23
2025-10-01T08:12:27Z,203.0.113.9,198.51.100.5,33421,22,TCP,DROP,ssh-bruteforce,0,-
2025-10-01T08:13:01Z,198.51.100.20,198.51.100.5,445,49152,TCP,DROP,ddos,0,-
```

### Aggregated View (example frequency table)
| Attack Type           | Observed Frequency |
| ---------------------: | -----------------: |
| Malware                | 120 |
| Phishing               | 80 |
| DDoS                   | 50 |
| Known Vulnerabilities  | 40 |

Use these aggregates to compute proportions, compare groups, or run independence tests.

---

## Distributions: Core Concepts

A **distribution** describes how values of a variable (or set of variables) are spread through the data.  
Understanding distributional shape is essential for inference, anomaly detection, and model selection.

### Classification by Dimensionality
- **Univariate distribution** — distribution of a single variable (histogram, PMF/PDF).  
  *Example: distribution of response times.*  
- **Bivariate distribution** — joint distribution of two variables (contingency table for categorical pairs; scatter/density for continuous pairs).  
- **Multivariate distribution** — joint distribution of three or more variables (used in regression, PCA, etc.).

### Common Representations
- Frequency table / histogram — counts or counts per bin (empirical distribution).  
- Probability distribution — assignment of probabilities (theoretical or estimated).  
- Cumulative distribution (CDF) — probability below a threshold.

### Important Quantities
- Frequency / Count and Relative frequency (empirical probability)  
- Moments (mean, variance), skewness, kurtosis — describe central tendency and shape  
- Joint frequencies and marginals — for bivariate/categorical analysis  

Many statistical decisions in security (thresholding for alerts, anomaly detection) depend on accurate estimation of these distributions and on correct pre-processing (cleaning, normalization) [1][6].

---

## SQL Examples: Univariate & Bivariate Distributions

A simple relational workflow can compute distributions efficiently.

### Univariate Distribution (SQL)

```sql
SELECT
  age,
  COUNT(*) AS frequency
FROM
  People
GROUP BY
  age
ORDER BY
  age;
```

### Bivariate Distribution (SQL)

```sql
SELECT
  age,
  gender,
  COUNT(*) AS frequency
FROM
  People
GROUP BY
  age, gender
ORDER BY
  age, gender;
```

Use these results to build histograms, contingency tables, or heatmaps, and then apply statistical tests  
(for example χ² for independence on the contingency table) [2][6].

---

## Using Distributions to Attack the Caesar Cipher (Statistical Approach)

This section adapts the demo you provided to the project: explanation + ready-to-copy JavaScript functions.  
The goal is educational: show Caesar encryption, brute-force decryption, and an automatic estimator that uses distribution comparison (χ²) against a language reference frequency table.

> **Important caveat:** Frequency-based automatic decoding works reliably on sufficiently long plaintexts written in the same language used for the reference frequencies.  
> For very short ciphertexts, two approaches are common:
> 1. Brute-force + manual inspection  
> 2. Combine frequency metrics with word / n-gram scoring

---

## Core JavaScript Functions (Demo)

Below is a compact, well-documented JavaScript implementation you can reuse in your HTML widget.

```js
// --- Reference frequencies (example: English A..Z) ---
const freqEn = {
  A:8.17,B:1.49,C:2.78,D:4.25,E:12.70,F:2.23,
  G:2.02,H:6.09,I:6.97,J:0.15,K:0.77,L:4.03,
  M:2.41,N:6.75,O:7.51,P:1.93,Q:0.10,R:5.99,
  S:6.33,T:9.06,U:2.76,V:0.98,W:2.36,X:0.15,Y:1.97,Z:0.07
};

// Caesar shift: shift can be positive or negative
function caesarShift(str, shift) {
  let out = '';
  for (let i = 0; i < str.length; i++) {
    const ch = str[i];
    if (ch >= 'A' && ch <= 'Z') {
      const base = 65;
      out += String.fromCharCode(((ch.charCodeAt(0) - base + shift + 26) % 26) + base);
    } else if (ch >= 'a' && ch <= 'z') {
      const base = 97;
      out += String.fromCharCode(((ch.charCodeAt(0) - base + shift + 26) % 26) + base);
    } else {
      out += ch;
    }
  }
  return out;
}

// Brute-force: try all shifts 1..25
function bruteForceDecode(ciphertext) {
  const results = [];
  for (let shift = 1; shift < 26; shift++) {
    results.push({ shift, plaintext: caesarShift(ciphertext, -shift) });
  }
  return results;
}

// Helpers for distribution analysis: A..Z only
function onlyLettersUpper(s) {
  return s.toUpperCase().replace(/[^A-Z]/g, '');
}
function letterCounts(s) {
  const counts = Array(26).fill(0);
  const clean = onlyLettersUpper(s);
  for (const ch of clean) counts[ch.charCodeAt(0) - 65]++;
  return { counts, total: clean.length };
}
function freqPercent(counts, total) {
  return counts.map(c => total ? (c * 100 / total) : 0);
}

// Chi-squared between observed percentage array and expected percentage array
function chiSquared(obsPerc, expectedPerc) {
  let chi = 0;
  for (let i = 0; i < 26; i++) {
    const O = obsPerc[i], E = expectedPerc[i];
    if (E > 0) chi += ((O - E) * (O - E)) / E;
  }
  return chi;
}

// Automatic decode by chi-squared (returns best shift and decoded text)
function autoDecodeByChi2(ciphertext, freqReference = freqEn) {
  const { counts, total } = letterCounts(ciphertext);
  if (total === 0) return { text: '', shift: 0, chi: null };

  const obsPerc = freqPercent(counts, total);
  const expectedArr = Object.keys(freqReference).map(k => freqReference[k]);

  let bestShift = 0, bestChi = Infinity;
  for (let shift = 0; shift < 26; shift++) {
    const rotated = obsPerc.map((v, i) => obsPerc[(i + shift) % 26]);
    const chi = chiSquared(rotated, expectedArr);
    if (chi < bestChi) { bestChi = chi; bestShift = shift; }
  }
  return { text: caesarShift(ciphertext, -bestShift), shift: bestShift, chi: bestChi };
}
```

Use `bruteForceDecode()` to present all candidates, and `autoDecodeByChi2()` to suggest the most likely key (statistical guess).

---

## Chi-Squared Automatic Decoding — Algorithm Explained

1. **Preprocess:** remove non-letter characters and compute raw counts for A–Z; convert counts into percentages \( O_i \) (observed percents).  
2. **Reference:** pick a language reference frequency \( E_i \) (e.g., `freqEn` for English).  
   - The quality of the reference matters — if the plaintext is Italian but you use English frequencies, the method will fail [5].  
3. **Rotation / candidate shifts:** for each candidate shift *s*, rotate the observed distribution \( O \) by *s* positions (simulating decryption).  
4. **Compute χ² for each rotation:**

   \[
   \chi^2 = \sum_{i=1}^{26} \frac{(O_i - E_i)^2}{E_i}
   \]

   Lower χ² means the rotated observed distribution matches the reference more closely [2].  
5. **Select** the shift with minimum χ² as the statistical estimate.  
6. **Fallback:** on short texts or texts with heavy punctuation, prefer brute-force + manual inspection, or use additional heuristics (word lists, bigrams, language models).

This is a **classical frequency-attack approach**:  
- Brute-force enumerates all possibilities.  
- χ² provides a principled way to rank them statistically [2][3][4].

---

## References

1. [Dataset (Wikipedia)](https://en.wikipedia.org/wiki/Dataset) — general definition and examples.  
2. Statistical analysis and χ² test fundamentals.  
3. Classical cipher cryptanalysis references.  
4. Frequency analysis methods in cryptography.  
5. Cross-language effects in statistical decoding.  
6. Data quality and reproducibility guidelines.  
