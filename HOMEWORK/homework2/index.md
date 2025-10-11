What is a dataset

A dataset is a structured collection of data, usually organized so that each record (row) corresponds to a single observation and each column corresponds to a variable or attribute describing the observation. In research and analysis contexts a dataset provides the empirical basis for inference, modeling and visualization [1].

Formal (concise) definition:

“A collection of data, usually represented in tabular form, where rows correspond to individual entities or observations and columns correspond to variables or attributes describing those entities.” [1]

Key components

Observations / records / instances — each row is one case (e.g., one log entry or one patient).

Variables / features — columns that can be quantitative (numeric) or qualitative (categorical).

Values — the individual cells in the table.

Metadata — data about the data (field descriptions, units, coding schemes, provenance).

When documenting a dataset include a short data dictionary listing each field, its type and allowed values — this is essential for reproducible analysis and downstream statistical tests [6].

Dataset types & data quality

By structure

Structured — relational tables, CSVs. (Example: firewall logs).

Semi-structured — JSON, XML, log files with fields (common in telemetry).

Unstructured — free text, images, audio.

By measurement

Quantitative — numeric variables (continuous or discrete).

Qualitative — categorical variables (nominal or ordinal).

Quality checklist (recommended)

total number of records n;

percent missing per field;

data formats (timestamps in ISO 8601 recommended);

known filters / sampling used during collection;

unit tests or validation scripts for field ranges/regexes.
Quality metadata is required to validate assumptions for many statistical tests (for example, contingency-table χ² requires adequate counts per cell) [6].

CSV example & aggregation

Example logs_sample.csv (put a file like this in your repo to test):
timestamp,src_ip,dst_ip,src_port,dst_port,protocol,action,threat_type,bytes,response_time
2025-10-01T08:12:23Z,192.0.2.10,198.51.100.5,52344,443,TCP,ACCEPT,none,1240,23
2025-10-01T08:12:27Z,203.0.113.9,198.51.100.5,33421,22,TCP,DROP,ssh-bruteforce,0,-
2025-10-01T08:13:01Z,198.51.100.20,198.51.100.5,445,49152,TCP,DROP,ddos,0,-

Aggregated view (example frequency table, useful for χ² and trend charts):
|           Attack Type | Observed Frequency |
| --------------------: | -----------------: |
|               Malware |                120 |
|              Phishing |                 80 |
|                  DDoS |                 50 |
| Known Vulnerabilities |                 40 |

Use these aggregates to compute proportions, compare groups or run independence tests.

Distributions: core concepts

A distribution describes how values of a variable (or set of variables) are spread through the data. Understanding distributional shape is essential for inference, anomaly detection and model selection.

Classification by dimensionality

Univariate distribution — distribution of a single variable (histogram, PMF/PDF) (example: distribution of response times).

Bivariate distribution — joint distribution of two variables (contingency table for categorical pair; scatter/density for continuous pair).

Multivariate distribution — joint distribution of three or more variables (used in regression, PCA, etc.).

Common representations

Frequency table / histogram — counts or counts per bin (empirical distribution).

Probability distribution — assignment of probabilities (theoretical or estimated).

Cumulative distribution (CDF) — probability below a threshold.

Important quantities

Frequency / Count and Relative frequency (empirical probability).

Moments (mean, variance), skewness, kurtosis — describe central tendency and shape.

Joint frequencies and marginals — for bivariate/categorical analysis.

Many statistical decisions in security (thresholding for alerts, anomaly detection) depend on accurate estimation of these distributions and on correct pre-processing (cleaning, normalization) [1][6].

SQL examples: univariate & bivariate distributions

A simple relational workflow can compute distributions efficiently.

Univariate distribution (SQL)
SELECT
  age,
  COUNT(*) AS frequency
FROM
  People
GROUP BY
  age
ORDER BY
  age;

Bivariate distribution (SQL)
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


Use these results to build histograms, contingency tables, or heatmaps and then apply statistical tests (for example χ² for independence on the contingency table) [2][6].

Using distributions to attack the Caesar cipher (statistical approach)

This section adapts the demo you provided to the project: explanation + ready-to-copy JavaScript functions. The goal is educational: show Caesar encryption, brute-force decryption, and an automatic estimator that uses distribution comparison (χ²) against a language reference frequency table.

Important caveat: frequency-based automatic decoding works reliably on sufficiently long plaintexts written in the same language used for the reference frequencies; for very short ciphertexts two approaches are common: (a) brute-force + manual inspection, (b) combine frequency metrics with word / n-gram scoring.

Core JavaScript functions (demo)

Below is a compact, well-documented JavaScript implementation you can reuse in your HTML widget. It includes: Caesar shift, brute-force listing, letter counting, χ² computation and automatic decode by χ².

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
  if (total === 0) return { text: '', shift: 0, chi: null }; // nothing to analyze

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



Use bruteForceDecode to present all candidates, and autoDecodeByChi2 to suggest the most likely key (statistical guess).

Chi-squared automatic decoding — algorithm explained (step-by-step)

Preprocess: remove non-letter characters and compute raw counts for A..Z; convert counts into percentages O_i (observed percents).

Reference: pick a language reference frequency E_i (for example freqEn for English). The quality of the reference matters: if the plaintext is Italian but you use English frequencies the method will fail [5].

Rotation / candidate shifts: for each candidate shift s rotate the observed distribution O by s positions — rotation simulates in-place decryption with that shift.

Compute χ² for each rotation:

χ2=∑i=126(Oi−Ei)2Ei.
χ
2
=
i=1
∑
26
	​

E
i
	​

(O
i
	​

−E
i
	​

)
2
	​

.

Lower χ² means the rotated observed distribution matches the reference more closely. [2]

Select the shift with minimum χ² as the statistical estimate.

Fallback: on short texts or texts with heavy punctuation/structured tokens, prefer brute-force + human inspection, or use additional heuristics (word lists, bigrams, language models) to disambiguate.

This is a classical frequency-attack approach: brute-force always enumerates all possibilities; χ² gives a principled way to rank them statistically [2][3][4].



References (thesis style — numbered)

Dataset (Wikipedia) — general definition and examples. https://en.wikipedia.org/wiki/Dataset
