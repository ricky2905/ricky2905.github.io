# Datasets in Statistics and Cybersecurity

Datasets are fundamental for analyzing and modeling behaviors in cybersecurity. They form the basis for detecting anomalies, training predictive models, and evaluating intrusion detection systems [1].

---

## Types of Datasets

### 1. **Structured**
Data organized in tables with rows and columns, such as relational databases.  
**Example:** access logs, user tables [2].

### 2. **Unstructured**
Data without a fixed schema, such as text, images, or video. They require advanced processing techniques, like NLP or computer vision [3].

### 3. **Semi-Structured**
Data with partial structure, such as JSON or XML files. They contain labels or metadata that facilitate analysis [2].

---

## Example: Tabular Dataset from KDD Cup '99

The **KDD Cup '99** dataset is widely used for intrusion detection. It contains simulated network traffic information with 41 variables and various attack labels [6].  
Here is a simplified tabular representation:

| Duration | Protocol | Service | SrcBytes | DstBytes | Label  |
| -------- | -------- | ------- | -------- | -------- | ------ |
| 0        | tcp      | http    | 181      | 5450     | normal |
| 0        | udp      | domain  | 105      | 146      | normal |
| 0        | tcp      | ftp     | 239      | 486      | attack |

This structure allows the application of statistical and machine learning techniques to identify anomalous behaviors.

---

## Dataset Management

Proper dataset management is crucial for reliable and replicable results. Main steps include:

* **Data cleaning**: removing missing, duplicate, or erroneous values.
* **Normalization and standardization**: aligning variable scales to avoid bias in models [4].
* **Anonymization and privacy**: protecting sensitive data, essential in cybersecurity [5].
* **Feature selection**: choosing the most relevant features to improve model performance and reduce computational complexity [2].

---

## Data Distribution Concepts

**Data distribution** describes how values of a variable or set of variables are spread. Understanding distribution is essential to:

* Choose appropriate statistical tests
* Identify outliers and anomalies
* Improve predictive modeling [4]

### Types of Distribution

* **Univariate** — examines one variable at a time, analyzing mean, median, mode, variance, skewness, and kurtosis [4].  
* **Bivariate** — examines the relationship between two variables, using scatter plots, contingency tables, and correlations [4].  
* **Multivariate** — involves multiple variables simultaneously, used in PCA, clustering, and complex predictive models [4].

### Other Relevant Concepts

* **Probability distributions**: Normal, Poisson, Binomial — useful for modeling events like access attempts or attacks [4].  
* **Outliers**: extreme values that may indicate anomalies or intrusions [1][5].  
* **Patterns and correlations**: identifying dependencies between variables can reveal abnormal behaviors or vulnerabilities [2][4].

---

## Final Considerations

Proper analysis of datasets and distributions is essential in cybersecurity for identifying threats and optimizing defense systems.  
The combined use of structured, unstructured, and semi-structured datasets, along with rigorous data management, allows the development of robust and reliable statistical and machine learning models [1][6].

---

## References

[1] Provost, F., & Fawcett, T. (2013). *Data Science for Business*. O'Reilly Media.  
[2] Han, J., Pei, J., & Kamber, M. (2011). *Data Mining: Concepts and Techniques*. Elsevier.  
[3] Jurafsky, D., & Martin, J. H. (2020). *Speech and Language Processing*. Pearson.  
[4] Montgomery, D. C., & Runger, G. C. (2018). *Applied Statistics and Probability for Engineers*. Wiley.  
[5] Goodfellow, I., Bengio, Y., & Courville, A. (2016). *Deep Learning*. MIT Press.  
[6] Tavallaee, M., et al. (2009). *A detailed analysis of the KDD CUP 99 data set*. In *IEEE Symposium on Computational Intelligence for Security and Defense Applications*.

---

## Univariate and Bivariate Distribution on a Dataset

Here is an example based on a simple database created with Access. Using two basic SQL queries, we can calculate both the univariate and bivariate distributions.  
For the univariate distribution, we consider the **Age** variable, while for the bivariate distribution we examine **Age** and **Height**.

![Dati](/assets/images/dati.png "Esempio di dati tabellari")

### Univariate Distribution on Age
![Eta](/assets/images/Dist_univariata.png "Distribuzione Univariata")

**SQL Code:**
![EtaSQL](/assets/images/Dist_univariata_sql.png "Distribuzione Univariata SQL")

### Bivariate Distribution on Age and Height
![EtaAltezza](/assets/images/Dist_bivariata.png "Distribuzione Bivariata")

**SQL Code:**
![EtaAltezzaSQL](/assets/images/Dist_bivariata_sql.png "Distribuzione Bivariata SQL")

---

## Using Distribution to Break a Caesar Cipher

This section introduces a simple web-based tool and an explanation for working with the **Caesar cipher (shift = 3)**.  
The tool allows users to:

* Encrypt text
* Attempt decryption by trying all 25 possible shifts (brute-force)
* Automatically estimate the correct shift using letter frequency analysis (chi-squared test)

The frequency-based approach provides a statistical guess for the most likely shift (for longer texts), while brute-force remains reliable for shorter ones.

---

## Main Goals of the Tool

### Caesar Cipher Generation
* Encrypts plaintext with a user-selected shift.
* Supports accent mapping before encryption.

### Attempted Decryption of All Shifts
* Performs brute-force over all 26 possible Caesar cipher shifts.

### Scoring and Ranking of Candidates
Calculates scores based on:
* Hamming distance (letter order)
* Chi-squared statistic (χ²) for frequencies
* Presence of common Italian words (wordScore)
* Presence of common bigrams (bigramScore)

### Results Display
* Shows all candidate decryptions in a sortable table.
* Highlights the top N candidates.
* Allows selection of a candidate to view a preview and detailed scores.

### User Utilities
* Copy ciphertext to clipboard.
* Automatically handle accents and capitalization.

---

<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Caesar — generator + ranking (Hamming + words + bigrams + chi²)</title>
<style>
  body{font-family:system-ui,-apple-system,"Segoe UI",Roboto,Helvetica,Arial;padding:18px}
  textarea{width:100%;height:110px;font-family:monospace;font-size:14px;margin-bottom:8px}
  table{border-collapse:collapse;width:100%;margin-top:8px}
  th,td{border:1px solid #ddd;padding:6px;text-align:left;font-size:13px}
  th{background:#f4f4f4}
  .result{white-space:pre-wrap;background:#fff;border:1px solid #ddd;padding:10px;margin-top:8px;max-height:260px;overflow:auto}
  .controls{display:flex;gap:8px;flex-wrap:wrap;align-items:center;margin-top:8px}
  .small{font-size:13px;color:#444}
  input[type="number"]{width:72px}
  .top{background:#e8ffe8}
  .generator{border:1px dashed #ccc;padding:8px;margin:8px 0;border-radius:6px}
  label.inline{display:inline-flex;align-items:center;gap:6px}
  button.small{padding:4px 8px;font-size:13px}
  .hiddenCol{display:none;}
</style>
</head>
<body>
  <h2>Caesar Cipher: Generate → Analyze (Improved Ranking)</h2>
  <div class="small">Enter plaintext, choose a shift, and generate the cipher. Then you can analyze the cipher.</div>

  <!-- GENERATOR -->
  <div class="generator">
    <div class="small"><strong>Caesar Cipher Generator</strong></div>
    <textarea id="plaintext">This is a test message: try changing the shift and see the result.</textarea>
    <div class="controls">
      <label class="inline">Shift <input id="genShift" type="number" min="0" max="25" value="7"></label>
      <label class="inline"><input id="genMapAcc" type="checkbox" checked> Map accents before encrypting</label>
      <button id="genBtn" class="small">Generate</button>
      <button id="genAnalyzeBtn" class="small">Generate & Analyze</button>
      <button id="copyCipher" class="small">Copy Cipher</button>
      <div class="small" style="margin-left:8px">Output:</div>
    </div>
    <textarea id="generated" readonly style="height:80px"></textarea>
  </div>

  <!-- CIPHER / ANALYSIS CONTROLS -->
  <label><strong>Ciphertext (for analysis)</strong></label>
  <textarea id="cipher">Xblzav l bu tlzzhnnpv kp wyvch. Jvuaplul whyvsl jvtbup l xbhsjol ipnyhtth apwpjv.</textarea>

  <div class="controls">
    <label><input type="checkbox" id="mapAccents" checked> Map accents (è→e)</label>
    <label style="margin-left:6px"><input type="checkbox" id="ignoreCase" checked> Ignore case</label>
    <button id="run">Try all shifts</button>
    <button id="sortHam">Sort by Hamming</button>
    <button id="sortChi">Sort by Chi²</button>
    <button id="sortComb">Sort by Combined</button>
    <label style="margin-left:8px" class="small">Top N: <input id="topN" type="number" value="5" min="1" max="26"/></label>
  </div>

  <div class="controls" style="margin-top:6px">
    <label class="small inline"><input type="checkbox" id="toggleCols"> Show detailed columns</label>
  </div>

  <details style="margin-top:8px">
    <summary class="small">Weights for combined scoring</summary>
    <div style="margin-top:8px">
      <label class="small">wordScore weight: <input id="w_word" type="number" step="0.05" value="0.45" /></label>
      <label class="small" style="margin-left:12px">hamScore weight: <input id="w_ham" type="number" step="0.05" value="0.25" /></label>
      <label class="small" style="margin-left:12px">chiScore weight: <input id="w_chi" type="number" step="0.05" value="0.15" /></label>
      <label class="small" style="margin-left:12px">bigramScore weight: <input id="w_big" type="number" step="0.05" value="0.15" /></label>
    </div>
  </details>

  <div id="summary" class="small" style="margin-top:8px"></div>

  <table id="results">
    <thead>
      <tr>
        <th>Shift</th>
        <th>Combined</th>
        <th class="hiddenCol">Word%</th>
        <th class="hiddenCol">Hamming</th>
        <th class="hiddenCol">Chi²</th>
        <th class="hiddenCol">Bigram%</th>
        <th>Preview</th>
        <th></th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <h3>Selected Text</h3>
  <div id="chosen" class="result">---</div>

<script>
/* --- Reference frequencies (Italian) --- */
const referenceFreq = {
  'A':10.85,'B':1.05,'C':4.30,'D':3.39,'E':11.49,'F':1.01,'G':1.65,'H':1.43,
  'I':10.18,'J':0.01,'K':0.01,'L':5.70,'M':2.87,'N':7.02,'O':9.97,'P':2.96,
  'Q':0.45,'R':6.19,'S':5.48,'T':6.97,'U':3.16,'V':1.75,'W':0.01,'X':0.01,
  'Y':0.06,'Z':0.85
};

/* accent map */
const accentMap = {
  'à':'a','á':'a','â':'a','ã':'a','ä':'a','å':'a',
  'è':'e','é':'e','ê':'e','ë':'e','ì':'i','í':'i','î':'i','ï':'i',
  'ò':'o','ó':'o','ô':'o','õ':'o','ö':'o','ù':'u','ú':'u','û':'u','ü':'u',
  'À':'A','Á':'A','Â':'A','Ã':'A','Ä':'A','Å':'A','È':'E','É':'E','Ê':'E','Ë':'E',
  'Ì':'I','Í':'I','Î':'I','Ï':'I','Ò':'O','Ó':'O','Ô':'O','Õ':'O','Ö':'O','Ù':'U','Ú':'U','Û':'U','Ü':'U',
  'ç':'c','Ç':'C','ñ':'n','Ñ':'N'
};
function mapAccents(s){ return s.split('').map(ch => accentMap[ch] || ch).join(''); }

/* common words */
const commonWords = new Set([
  'di','e','il','la','che','a','in','un','per','con','non','una','sono','si','da','al','lo','ma','più',
  'come','su','o','se','anche','perché','quando','dove','questo','quella','queste','noi','voi','loro',
  'mia','mio','tua','tuo','suo','nostro','l','ha','hai','ho','era','stato','stata','cioè',
  'ora','bene','male','molto','poco','grande','piccolo','anno','giorno','giorni'
]);

/* common bigrams */
const commonBigrams = ['CH','GL','QU','SC','GN','TR','PR','BR','SP','RA','RE','RI','IN','AN','TO','TE','IO','IA','CO'];

/* Caesar shift helper */
function caesarShift(text, shift){
  return text.split('').map(ch => {
    const code = ch.charCodeAt(0);
    if (code >= 65 && code <= 90) return String.fromCharCode(((code - 65 + shift) % 26) + 65);
    if (code >= 97 && code <= 122) return String.fromCharCode(((code - 97 + shift) % 26) + 97);
    return ch;
  }).join('');
}

/* letter frequencies */
function letterFrequencies(text){
  const counts = {};
  for (let c=65;c<=90;c++) counts[String.fromCharCode(c)] = 0;
  let total=0;
  for (const ch of text){
    const C = ch.toUpperCase();
    if (C >= 'A' && C <= 'Z'){ counts[C] = (counts[C]||0)+1; total++; }
  }
  const freqs = {};
  for (const L in counts) freqs[L] = total ? (counts[L]/total*100) : 0;
  return {counts, freqs, total};
}

/* rank by frequency */
function rankByFrequency(freqs){
  return Object.keys(freqs).sort((a,b) => {
    if (freqs[b] === freqs[a]) return a.localeCompare(b);
    return freqs[b] - freqs[a];
  });
}

/* Hamming distance */
function hammingRankDistance(orderA, orderB){
  let diff=0;
  for (let i=0;i<26;i++) if (orderA[i] !== orderB[i]) diff++;
  return diff;
}

/* chi² */
function chiSquared(obsFreqs){
  let chi2=0;
  for (const L in referenceFreq){
    const O = obsFreqs[L] || 0;
    const E = referenceFreq[L];
    if (E>0) chi2 += ((O - E)*(O - E)) / E;
  }
  return chi2;
}

/* wordScore */
function wordScore(text){
  const tokens = text.toLowerCase().split(/[^a-zA-Z]+/).filter(Boolean);
  if (!tokens.length) return 0;
  let hits=0;
  for (const t of tokens) if (commonWords.has(t)) hits++;
  return hits / tokens.length;
}

/* bigramScore */
function bigramScore(text){
  const s = text.toUpperCase().replace(/[^A-Z]/g,'');
  if (s.length < 2) return 0;
  let found=0;
  for (const bg of commonBigrams) if (s.indexOf(bg)!==-1) found++;
  return found / commonBigrams.length;
}

/* reference order */
const referenceOrder = Object.keys(referenceFreq).sort((a,b) => {
  if (referenceFreq[b] === referenceFreq[a]) return a.localeCompare(b);
  return referenceFreq[b] - referenceFreq[a];
});

/* UI hooks */
document.getElementById('run').addEventListener('click', tryAll);
document.getElementById('sortHam').addEventListener('click', ()=>renderResults(window._lastResults || [], 'ham'));
document.getElementById('sortChi').addEventListener('click', ()=>renderResults(window._lastResults || [], 'chi'));
document.getElementById('sortComb').addEventListener('click', ()=>renderResults(window._lastResults || [], 'combined'));

document.getElementById('genBtn').addEventListener('click', () => {
  const pt = document.getElementById('plaintext').value || '';
  const s = Math.max(0, Math.min(25, parseInt(document.getElementById('genShift').value || '0',10)));
  const map = document.getElementById('genMapAcc').checked;
  const input = map ? mapAccents(pt) : pt;
  const c = caesarShift(input, s);
  document.getElementById('generated').value = c;
});

document.getElementById('genAnalyzeBtn').addEventListener('click', () => {
  document.getElementById('genBtn').click();
  const c = document.getElementById('generated').value || '';
  document.getElementById('cipher').value = c;
  tryAll();
});

document.getElementById('copyCipher').addEventListener('click', async () => {
  try {
    const text = document.getElementById('generated').value || document.getElementById('cipher').value || '';
    await navigator.clipboard.writeText(text);
    alert('Cipher copied to clipboard.');
  } catch (e) {
    alert('Cannot copy automatically. Copy manually.');
  }
});

/* read weights */
function readWeights(){
  const w_word = parseFloat(document.getElementById('w_word').value) || 0.45;
  const w_ham  = parseFloat(document.getElementById('w_ham').value)  || 0.25;
  const w_chi  = parseFloat(document.getElementById('w_chi').value)  || 0.15;
  const w_big  = parseFloat(document.getElementById('w_big').value)  || 0.15;
  const sum = w_word + w_ham + w_chi + w_big;
  return {w_word: w_word/sum, w_ham: w_ham/sum, w_chi: w_chi/sum, w_big: w_big/sum};
}

/* main: try all shifts */
function tryAll(){
  try {
    const raw = document.getElementById('cipher').value || '';
    const mapAcc = document.getElementById('mapAccents').checked;
    const ignoreCase = document.getElementById('ignoreCase').checked;
    const text = mapAcc ? mapAccents(raw) : raw;
    const results = [];

    for (let s=0;s<26;s++){
      const shifted = caesarShift(text, s);
      const source = ignoreCase ? shifted.toUpperCase() : shifted;
      const {freqs, total} = letterFrequencies(source);
      const order = rankByFrequency(freqs);
      const ham = hammingRankDistance(order, referenceOrder);
      const chi2 = chiSquared(freqs);
      const ws = wordScore(mapAcc ? mapAccents(shifted).toLowerCase() : shifted.toLowerCase());
      const bg = bigramScore(shifted);
      const hamScore = (26 - ham) / 26;
      const chiScore = 1 / (1 + chi2);
      results.push({shift:s, shifted, ham, chi2, freqs, total, wordScore: ws, bigramScore: bg, hamScore, chiScore});
    }

    const chiVals = results.map(r=>r.chiScore);
    const chiMin = Math.min(...chiVals), chiMax = Math.max(...chiVals);
    results.forEach(r => {
      r.chiScoreNorm = (chiMax === chiMin) ? 0 : ((r.chiScore - chiMin) / (chiMax - chiMin));
    });

    const weights = readWeights();
    results.forEach(r => {
      r.combined = weights.w_word * r.wordScore + weights.w_ham * r.hamScore + weights.w_chi * r.chiScoreNorm + weights.w_big * r.bigramScore;
    });

    window._lastResults = results;
    renderResults(results, 'combined');
    document.getElementById('summary').textContent = `Generated 26 candidates. Sorted by "Combined" (adjust weights if needed).`;
  } catch (err) {
    console.error(err);
    alert('Error: see console (F12) for details.');
  }
}

/* render table with top N and optional columns */
function renderResults(results, mode){
  if (!results || !results.length) { alert('Run "Try all shifts" first.'); return; }
  const tbody = document.querySelector('#results tbody');
  tbody.innerHTML = '';
  const topN = Math.max(1, Math.min(26, parseInt(document.getElementById('topN').value || '5',10)));
  let sorted = [...results];
  if (mode === 'ham') sorted.sort((a,b)=>a.ham - b.ham || b.combined - a.combined);
  else if (mode === 'chi') sorted.sort((a,b)=>a.chi2 - b.chi2 || b.combined - a.combined);
  else sorted.sort((a,b)=>b.combined - a.combined || a.ham - b.ham);

  const showExtra = document.getElementById('toggleCols').checked;

  sorted.forEach((r, idx) => {
    if (idx >= topN) return;
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td>${r.shift}</td>
      <td>${r.combined.toFixed(4)}</td>
      <td class="${showExtra?'':'hiddenCol'}">${(r.wordScore*100).toFixed(1)}%</td>
      <td class="${showExtra?'':'hiddenCol'}">${r.ham} (${r.hamScore.toFixed(3)})</td>
      <td class="${showExtra?'':'hiddenCol'}">${r.chi2.toFixed(2)}</td>
      <td class="${showExtra?'':'hiddenCol'}">${(r.bigramScore*100).toFixed(1)}%</td>
      <td><code>${escapeHtml(r.shifted.slice(0,160))}${r.shifted.length>160?'…':''}</code></td>
      <td><button data-shift="${r.shift}">Select</button></td>
    `;
    tbody.appendChild(tr);
  });

  tbody.querySelectorAll('button').forEach(btn => btn.addEventListener('click', e => {
    const s = parseInt(e.currentTarget.getAttribute('data-shift'),10);
    const obj = results.find(r => r.shift === s);
    showSelected(obj);
  }));
}

/* show selected */
function showSelected(obj){
  if (!obj) return;
  const out = document.getElementById('chosen');
  out.textContent = `${obj.shift}: ${obj.shifted}\n\nTotal letters A–Z considered: ${obj.total}\n\nScores:\n  combined: ${obj.combined.toFixed(4)}\n  wordScore: ${(obj.wordScore*100).toFixed(1)}%\n  ham: ${obj.ham} (hamScore ${(obj.hamScore).toFixed(3)})\n  chi²: ${obj.chi2.toFixed(2)} (chiScoreNorm ${(obj.chiScoreNorm||0).toFixed(3)})\n  bigramScore: ${(obj.bigramScore*100).toFixed(1)}%\n\nPreview:\n${obj.shifted}`;
  out.scrollIntoView({behavior:'smooth'});
}

function escapeHtml(str){ return str.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

document.getElementById('toggleCols').addEventListener('change', () => {
  renderResults(window._lastResults || [], 'combined');
});

/* initial run for demo */
tryAll();
</script>

</body>
</html>

---

# Overview of the Caesar Cipher with Statistical Analysis

The Caesar cipher is a **monoalphabetic substitution cipher** that shifts each letter of the plaintext by a fixed number of positions.  
For example, with a shift of 3:

```
Plaintext:  ABCDEFGHIJKLMNOPQRSTUVWXYZ
Ciphertext: DEFGHIJKLMNOPQRSTUVWXYZABC
```

### Encryption/Decryption Function

```javascript
function caesarShift(text, shift){
  return text.split('').map(ch => {
    const code = ch.charCodeAt(0);
    if(code >= 65 && code <= 90) return String.fromCharCode(((code - 65 + shift) % 26) + 65);
    if(code >= 97 && code <= 122) return String.fromCharCode(((code - 97 + shift) % 26) + 97);
    return ch;
  }).join('');
}
```

### Brute-force Decryption

Since there are only 25 possible shifts, the Caesar cipher can easily be broken:

```javascript
for(let shift = 1; shift < 26; shift++){
  results.push({shift, text: caesarShift(ciphertext, shift)});
}
```

### Statistical / Distribution-based Decryption

The advanced part of the code uses **letter and bigram frequency distributions** to estimate the most probable shift automatically.

---

## Metrics Explained

| Metric | Description | Weight |
| ------- | ------------ | ------- |
| **Word%** | Fraction of valid Italian words | 0.45 |
| **Hamming** | Letter rank similarity | 0.25 |
| **Chi²** | Statistical deviation from expected frequencies | 0.15 |
| **Bigram%** | Common bigram presence | 0.15 |

---

## Cryptographic Insights

The Caesar cipher is weak because of its **small keyspace** (25 shifts) and **predictable frequency patterns**.  
Statistical analysis quickly reveals the shift.

---

## Conclusion

This implementation bridges **cryptography** and **statistics**, showing how data distributions can reveal hidden information.  
The **combined score metric** elegantly integrates multiple statistical indicators to automatically prioritize the most probable plaintext.

![Script](/assets/images/script.png "Code")
