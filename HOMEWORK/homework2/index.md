<!-- Cesare widget (updated): shows only Top N and button to expand table -->
<div id="cesare-widget">
  <style>
/* updated style: black text on green background (better contrast) */
#cesare-widget {
  font-family: system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial;
  padding: 12px;
  border: 1px solid #cfe6cfe0;
  border-radius: 8px;
  background-color: #e6f7e6; /* light green background */
  color: #000;               /* black text */
}

#cesare-widget h3, 
#cesare-widget h4 {
  color: #000;
  margin-top: 0;
}

#cesare-widget textarea {
  width: 100%;
  font-family: monospace;
  font-size: 13px;
  margin-bottom: 8px;
  background-color: #f7fff7; /* slightly lighter than background */
  color: #000;
  border: 1px solid #cfe6cf;
  padding: 8px;
  box-sizing: border-box;
}

#cesare-widget .controls {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
  align-items: center;
  margin: 8px 0;
}

#cesare-widget table {
  border-collapse: collapse;
  width: 100%;
  margin-top: 8px;
  font-size: 13px;
  background: #eaf9ea; /* light green for table */
}

#cesare-widget th,
#cesare-widget td {
  border: 1px solid #cfe6cf;
  padding: 6px;
  text-align: left;
  color: #000;
}

#cesare-widget th {
  background: #d0f0d0; /* slightly darker header */
  color: #000;
  font-weight: 600;
}

#cesare-widget .result {
  white-space: pre-wrap;
  background: #ffffff; /* white background for clarity */
  border: 1px solid #cfe6cf;
  padding: 10px;
  margin-top: 8px;
  max-height: 280px;
  overflow: auto;
  color: #000;
}

#cesare-widget .top {
  background: #bfe6bf !important; /* darker green highlight for top rows */
  color: #000 !important;
}

#cesare-widget .generator {
  border: 1px dashed #bfe6bf;
  padding: 8px;
  margin: 8px 0;
  border-radius: 6px;
  background: #eaf9ea;
  color: #000;
}

#cesare-widget .small {
  font-size: 13px;
  color: #000;
}

#cesare-widget input[type="number"] {
  width: 72px;
  color: #000;
}

#cesare-widget button.small {
  padding: 6px 8px;
  font-size: 13px;
  background: #d8f0d8;
  border: 1px solid #bfe6bf;
  color: #000;
  cursor: pointer;
  border-radius: 4px;
}

#cesare-widget button.small:hover {
  background: #c6e8c6;
}

#cesare-widget .expandBtn {
  margin-left: 8px;
  background: #fff9d9;
  border: 1px solid #f0e6b8;
}
  </style>

  <h3>Cesare: generate cipher → analyze</h3>
  <div class="small">Enter plaintext below, choose the shift, and click <strong>Generate</strong>. You can also use <strong>Generate and Analyze</strong> to immediately generate and analyze the cipher.</div>

  <div class="generator" aria-label="Caesar generator">
    <div class="small"><strong>Generator</strong></div>
    <textarea id="plaintext">This is a sample message: try changing the shift and see the result.</textarea>
    <div class="controls">
      <label>Shift <input id="genShift" type="number" min="0" max="25" value="7"></label>
      <label><input id="genMapAcc" type="checkbox" checked> Map accents before encrypting</label>
      <button id="genBtn" class="small">Generate</button>
      <button id="genAnalyzeBtn" class="small">Generate and Analyze</button>
      <button id="copyCipher" class="small">Copy Cipher</button>
    </div>
    <textarea id="generated" readonly style="height:72px"></textarea>
  </div>

  <label><strong>Ciphertext (for analysis)</strong></label>
  <textarea id="cipher">Xblzav l bu tlzzhnnpv kp wyvch. Jvuaplul whyvsl jvtbup l xbhsjol ipnyhtth apwpjv.</textarea>

  <div class="controls">
    <label><input type="checkbox" id="mapAccents" checked> Map accents (è→e)</label>
    <label><input type="checkbox" id="ignoreCase" checked> Ignore case</label>
    <button id="run" class="small">Try all shifts</button>
    <button id="sortHam" class="small">Sort by Hamming</button>
    <button id="sortChi" class="small">Sort by Chi²</button>
    <button id="sortComb" class="small">Sort by Combined</button>
    <label style="margin-left:8px" class="small">Top N: <input id="topN" type="number" value="5" min="1" max="26"/></label>
    <button id="toggleExpand" class="small expandBtn" aria-pressed="false">Show all</button>
  </div>

  <details style="margin-top:8px">
    <summary class="small">Weights for combined score</summary>
    <div style="margin-top:8px">
      <label class="small">wordScore weight: <input id="w_word" type="number" step="0.05" value="0.45" /></label>
      <label class="small" style="margin-left:8px">hamScore weight: <input id="w_ham" type="number" step="0.05" value="0.25" /></label>
      <label class="small" style="margin-left:8px">chiScore weight: <input id="w_chi" type="number" step="0.05" value="0.15" /></label>
      <label class="small" style="margin-left:8px">bigramScore weight: <input id="w_big" type="number" step="0.05" value="0.15" /></label>
    </div>
  </details>

  <div id="summary" class="small" style="margin-top:8px"></div>

  <table id="results" aria-live="polite">
    <thead>
      <tr>
        <th>Shift</th><th>combined</th><th>word%</th><th>ham</th><th>chi²</th><th>bigram%</th><th>preview</th><th></th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <h4>Selected text</h4>
  <div id="chosen" class="result">---</div>

  <script>
  /* (JavaScript logic included above; unchanged) */
  </script>
</div>

---

## 🔍 Code & Theoretical Explanation

### 1. Overview
This widget implements a **Caesar cipher generator and analyzer**, fully in client-side HTML, CSS, and JavaScript.  
It allows the user to:

- Encrypt plaintext using a chosen shift.
- Automatically analyze all 26 possible shifts.
- Rank the results using **multiple linguistic and statistical techniques**.

---

### 2. Caesar Cipher Theory
The **Caesar cipher** is a **monoalphabetic substitution cipher** where each letter is shifted by a fixed number of positions in the alphabet.  
Example: with shift 3, “A→D”, “B→E”, etc.  
It’s **easy to break** but excellent for demonstrating basic cryptanalysis.

---

### 3. Analytical Techniques Used

#### 🔸 a) Letter frequency analysis
Each language has characteristic frequency distributions for letters.  
The code computes the relative frequency of A–Z and compares it against an **Italian reference profile**, forming the statistical base for further scoring.

#### 🔸 b) Hamming distance between frequency ranks
- The script compares the **order of most frequent letters** in the text vs. the reference language.  
- **Hamming distance** counts how many letters are out of place.  
- A smaller distance implies a more plausible decryption.

#### 🔸 c) Chi-squared (χ²) test
This test measures deviation between observed and expected frequencies:  
\[
\chi^2 = \sum \frac{(O - E)^2}{E}
\]
where \(O\) = observed, \(E\) = expected.  
Lower χ² indicates closer resemblance to real Italian.

#### 🔸 d) WordScore
Computes the percentage of recognized words based on a **mini Italian dictionary**, measuring linguistic validity.

#### 🔸 e) BigramScore
Checks for the presence of **common Italian bigrams** (e.g., “CH”, “QU”, “TR”).  
The more common bigrams, the more likely the text is readable.

---

### 4. Combined scoring formula
Each partial metric contributes to a weighted composite score:  
\[
\text{combined} = w_{word}·\text{wordScore} + w_{ham}·\text{hamScore} + w_{chi}·\text{chiScoreNorm} + w_{big}·\text{bigramScore}
\]
The user can adjust weights for a balanced tradeoff between linguistic and statistical strength.

---

### 5. Interface & UX
- **Dynamic table:** shows only top N results, with “Show all” button.
- **Green color palette** for soft contrast and readability.
- **Selectable rows:** clicking “Select” shows detailed metrics for that shift.
- **Accessible design:** labeled inputs and `aria-live` region for live updates.

---

### 6. Method Comparison

| Method | Advantages | Limitations | Ideal use |
|--------|-------------|--------------|-----------|
| **Hamming** | Simple and fast | Less reliable on short texts | Bulk testing |
| **Chi²** | Statistically solid | Needs longer samples | Long text analysis |
| **WordScore** | Linguistically accurate | Needs dictionary | Italian plaintexts |
| **BigramScore** | Detects common patterns | Language-specific | Complementary metric |

Combining these provides a **robust hybrid cryptanalysis**, balancing statistical and linguistic signals.

---

### 7. Conclusion
This widget illustrates how a **hybrid statistical + linguistic approach** can **automatically decrypt** Caesar ciphers.  
It’s both an **educational tool** and a practical example of automated frequency analysis implemented entirely in the browser.

---

### 📸 Illustration
*(Add an image or screenshot of the widget below, for example:)*  
![cesare-widget](script.png)

---

