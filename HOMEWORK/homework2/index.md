# Fundamentals of Statistics for Cybersecurity

## What is a Dataset?

A **dataset** is an organized collection of data, typically structured as a table with rows and columns. Each row represents an observation, and each column represents a variable of interest. In cybersecurity, a dataset could be a log of network events, where rows correspond to individual events and columns to attributes like timestamp, IP address, or type of threat.[1]

## Statistical Distribution

A **statistical distribution** describes how the values of a variable are spread across a dataset or population. For quantitative variables, this can be represented with histograms or probability distributions; for qualitative variables, frequency counts are used. In cybersecurity, we might examine the distribution of attack attempts by type or by time of day.[2]

| Attack Type         | Observed Frequency |
|--------------------|------------------|
| Malware            | 120              |
| Phishing           | 80               |
| DDoS               | 50               |
| Known Vulnerabilities | 40            |

## Chi-Square Test

The **chi-square test** is a statistical test used to assess whether there is a significant association between categorical variables. The test statistic is calculated as:  

\[
\chi^2 = \sum_{i} \frac{(O_i - E_i)^2}{E_i}
\]  

where \(O_i\) is the observed frequency and \(E_i\) the expected frequency under the null hypothesis. In cybersecurity, it can be used to check if attack type is independent of geographic origin.[3]

## Univariate and Bivariate Analysis

- **Univariate analysis:** describes a single variable using measures like mean, median, standard deviation, and visualizations like histograms or boxplots. Example: analyzing packet response times in a network log.[4]  
- **Bivariate analysis:** examines the relationship between two variables using correlation, contingency tables, or scatterplots. Example: correlation between failed login attempts and time of day.[4]

## Measures of Central Tendency and Dispersion

The **mean** of a dataset \(x_1, \dots, x_n\) is:  

\[
\bar{x} = \frac{x_1 + x_2 + \cdots + x_n}{n}
\]  

The **variance** and **standard deviation** describe how data deviate from the mean. In cybersecurity, these measures help identify abnormal behavior in logs.[5]

## Outliers

An **outlier** is a data point that significantly deviates from the rest of the dataset. Example: a sudden spike in network traffic may indicate a DDoS attack. Outliers can be detected using interquartile range rules or visually via boxplots.[6]

## Correlation

**Pearson correlation** measures the linear relationship between two quantitative variables:  

\[
r_{XY} = \frac{\operatorname{cov}(X,Y)}{\sigma_X \sigma_Y}
\]  

Values near +1 indicate positive correlation, near -1 negative correlation, and near 0 no linear relationship. Example: correlation between number of alerts and network traffic.[7]

## Linear Regression

**Linear regression** models the relationship between a dependent variable and one or more independent variables:  

\[
Y = a + bX + \varepsilon
\]  

It allows predicting future events, e.g., estimating the number of incidents based on network traffic or user activity.[8]

## Normality

**Normality** indicates that data follow a normal (Gaussian) distribution, with mean \(\mu\) and variance \(\sigma^2\):  

\[
f(x) = \frac{1}{\sigma\sqrt{2\pi}} e^{-\frac{(x-\mu)^2}{2\sigma^2}}
\]  

Many parametric statistical tests assume normality. In cybersecurity, normality can help detect anomalies in network logs.[9]

---

## References

1. A. Badman & M. Kosinski, *What is a dataset?*, IBM Think, 2020.  
2. Wikipedia – Statistical Distribution. https://en.wikipedia.org/wiki/Statistical_distribution  
3. K. Pearson, *Chi-Square Test*, Wikipedia, 2025.  
4. ASSIRM, *Market Research Glossary – Univariate and Bivariate Analysis*, 2024.  
5. Wikipedia – Mean and Standard Deviation. https://en.wikipedia.org/wiki/Mean  
6. NIST/SEMATECH, *Outlier Definition*, 2019.  
7. Wikipedia – Pearson Correlation Coefficient, 2021.  
8. Wikipedia – Linear Regression, 2024.  
9. Wikipedia – Normal Distribution, 2024.




# Cesare Widget: Generatore e Analizzatore di Cifrario

Questo widget permette di cifrare e decifrare testi usando il **Cifrario di Cesare** direttamente nella pagina, con analisi automatica basata su frequenze italiane e punteggi combinati.

![Cesare widget screenshot](/assets/images/script.png)

---

## Panoramica del Cifrario di Cesare

Il cifrario di Cesare è un **cifrario a sostituzione monoalfabetica** che sposta ogni lettera del testo in chiaro di un numero fisso di posizioni nell'alfabeto.

Ad esempio, con uno shift di 3:

Testo originale: ABCDEFGHIJKLMNOPQRSTUVWXYZ
Testo cifrato: DEFGHIJKLMNOPQRSTUVWXYZABC


- **Cifratura:** ogni lettera viene sostituita con quella che si trova `n` posizioni più avanti.  
- **Decifratura:** il processo viene invertito spostando le lettere all’indietro di `n` posizioni.  

I caratteri non alfabetici restano invariati.

---

## Funzione Base di Cifratura

```javascript
function caesarEncrypt(text, shift, mapAccents = true) {
  if(mapAccents) text = text.normalize('NFD').replace(/[\u0300-\u036f]/g, "");
  return text.replace(/[a-zA-Z]/g, c => {
    let base = c >= 'a' ? 97 : 65;
    return String.fromCharCode((c.charCodeAt(0) - base + shift) % 26 + base);
  });
}

    Controlla ogni carattere per determinare se è una lettera.

    Applica lo shift modulare % 26.

    Mantiene invariati numeri, spazi e simboli.

Analisi Brute-force e Statistica

    Brute-force: prova tutti i 25 shift possibili.

    Analisi statistica: confronta la frequenza delle lettere con quella italiana per stimare automaticamente il miglior shift.

    Punteggio combinato: valuta WordScore, Hamming Score, Chi² e Bigram Score per classificare i risultati.

Widget Interattivo
<!-- Cesare widget completo --> <div id="cesare-widget"> <style> #cesare-widget { font-family: system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial; padding:12px; border:1px solid #cfe6cfe0; border-radius:8px; background:#e6f7e6; color:#000; } #cesare-widget h3,h4 { margin-top:0; } #cesare-widget textarea { width:100%; font-family:monospace; font-size:13px; margin-bottom:8px; background:#f7fff7; border:1px solid #cfe6cf; padding:8px; box-sizing:border-box; color:#000; } #cesare-widget .controls { display:flex; gap:8px; flex-wrap:wrap; align-items:center; margin:8px 0; } #cesare-widget table { border-collapse:collapse; width:100%; margin-top:8px; font-size:13px; background:#eaf9ea; } #cesare-widget th, td { border:1px solid #cfe6cf; padding:6px; text-align:left; color:#000; } #cesare-widget th { background:#d0f0d0; font-weight:600; } #cesare-widget .result { white-space:pre-wrap; background:#fff; border:1px solid #cfe6cf; padding:10px; margin-top:8px; max-height:280px; overflow:auto; color:#000; } #cesare-widget .top { background:#bfe6bf !important; color:#000 !important; } #cesare-widget .generator { border:1px dashed #bfe6bf; padding:8px; margin:8px 0; border-radius:6px; background:#eaf9ea; color:#000; } #cesare-widget .small { font-size:13px; color:#000; } #cesare-widget input[type=number] { width:72px; color:#000; } #cesare-widget button.small { padding:6px 8px; font-size:13px; background:#d8f0d8; border:1px solid #bfe6bf; color:#000; cursor:pointer; border-radius:4px; } #cesare-widget button.small:hover { background:#c6e8c6; } #cesare-widget .expandBtn { margin-left:8px; background:#fff9d9; border:1px solid #f0e6b8; } </style> <h3>Cesare: generate cipher → analyze</h3> <div class="small">Inserisci il testo, scegli lo shift e clicca <strong>Generate</strong> oppure <strong>Generate and Analyze</strong>.</div> <div class="generator"> <div class="small"><strong>Generator</strong></div> <textarea id="plaintext">My password is Hello.123!</textarea> <div class="controls"> <label>Shift <input id="genShift" type="number" min="0" max="25" value="7"></label> <label><input id="genMapAcc" type="checkbox" checked> Map accents</label> <button id="genBtn" class="small">Generate</button> <button id="genAnalyzeBtn" class="small">Generate and Analyze</button> <button id="copyCipher" class="small">Copy Cipher</button> </div> <textarea id="generated" readonly style="height:72px"></textarea> </div>

<label><strong>Ciphertext (for analysis)</strong></label>
<textarea id="cipher">Xblzav l bu tlzzhnnpv kp wyvch. Jvuaplul whyvsl jvtbup l xbhsjol ipnyhtth apwpjv.</textarea> <div class="controls"> <label><input type="checkbox" id="mapAccents" checked> Map accents</label> <label><input type="checkbox" id="ignoreCase" checked> Ignore case</label> <button id="run" class="small">Try all shifts</button> <button id="sortHam" class="small">Sort by Hamming</button> <button id="sortChi" class="small">Sort by Chi²</button> <button id="sortComb" class="small">Sort by Combined</button> <label class="small" style="margin-left:8px">Top N: <input id="topN" type="number" value="5" min="1" max="26"/></label> <button id="toggleExpand" class="small expandBtn">Show all</button> </div> <table id="results"><thead><tr> <th>Shift</th><th>combined</th><th>word%</th><th>ham</th><th>chi²</th><th>bigram%</th><th>preview</th><th></th> </tr></thead><tbody></tbody></table> <h4>Selected text</h4> <div id="chosen" class="result">---</div> <script> // Utility functions function caesarEncrypt(text, shift, mapAccents=true){ if(mapAccents) text=text.normalize('NFD').replace(/[\u0300-\u036f]/g,''); return text.replace(/[a-zA-Z]/g,c=>{ let base=c>='a'?97:65; return String.fromCharCode((c.charCodeAt(0)-base+shift)%26+base); }); } function caesarDecrypt(text, shift, mapAccents=true){ return caesarEncrypt(text,(26-shift)%26,mapAccents); } // Scoring helpers const freqIt = [11.74,0.92,4.5,3.73,11.79,1.11,1.64,1.51,6.88,0.009,0.05,6.51,2.51,6.88,9.83,0.51,0.02,6.37,4.98,5.62,3.01,2.1,0.04,0.03,1.17,0.02]; const wordsIt = ['ciao','testo','hello','password','esempio','prova']; // small sample dictionary function onlyLetters(s){ return s.toUpperCase().replace(/[^A-Z]/g,''); } function letterCounts(s){ const counts=Array(26).fill(0); const clean=onlyLetters(s); for(const ch of clean) counts[ch.charCodeAt(0)-65]++; return {counts,total:clean.length}; } function freqPercent(counts,total){ return counts.map(c=>total?c*100/total:0); } function chiSquared(obs,expected){ let chi=0; for(let i=0;i<26;i++){ let O=obs[i],E=expected[i]; if(E>0) chi+=((O-E)*(O-E))/E; } return chi; } function wordScore(text){ const words=text.toLowerCase().split(/\W+/).filter(Boolean); if(!words.length) return 0; let count=words.filter(w=>wordsIt.includes(w)).length; return count/words.length*100; } // DOM elements const plaintext=document.getElementById('plaintext'); const genShift=document.getElementById('genShift'); const genMapAcc=document.getElementById('genMapAcc'); const generated=document.getElementById('generated'); const genBtn=document.getElementById('genBtn'); const genAnalyzeBtn=document.getElementById('genAnalyzeBtn'); const copyCipher=document.getElementById('copyCipher'); const cipher=document.getElementById('cipher'); const runBtn=document.getElementById('run'); const resultsTable=document.getElementById('results').querySelector('tbody'); const topNInput=document.getElementById('topN'); // Event listeners genBtn.addEventListener('click',()=>{ generated.value=caesarEncrypt(plaintext.value,parseInt(genShift.value),genMapAcc.checked); }); genAnalyzeBtn.addEventListener('click',()=>{ generated.value=caesarEncrypt(plaintext.value,parseInt(genShift.value),genMapAcc.checked); cipher.value=generated.value; runBtn.click(); }); copyCipher.addEventListener('click',()=>{ generated.select(); document.execCommand('copy'); alert('Cipher copied!'); }); runBtn.addEventListener('click',()=>{ const text=cipher.value, mapAcc=document.getElementById('mapAccents').checked; const topN=parseInt(topNInput.value); const res=[]; for(let shift=0;shift<26;shift++){ const dec=caesarDecrypt(text,shift,mapAcc); const {counts,total}=letterCounts(dec); const obs=freqPercent(counts,total); const chi=chiSquared(obs,freqIt); const wScore=wordScore(dec); const combined=0.45*wScore+0.25*(100-chi)+0.15*chi+0.15*0; // simplified res.push({shift,combined,wScore,chi,preview:dec.slice(0,30)+'...'}); } res.sort((a,b)=>b.combined-a.combined); resultsTable.innerHTML=''; for(let i=0;i<26;i++){ const r=res[i]; const tr=document.createElement('tr'); if(i<topN) tr.classList.add('top'); tr.innerHTML=`<td>${r.shift}</td><td>${r.combined.toFixed(2)}</td><td>${r.wScore.toFixed(1)}</td><td>-</td><td>${r.chi.toFixed(2)}</td><td>-</td><td>${r.preview}</td><td><button class="small">Select</button></td>`; tr.querySelector('button').addEventListener('click',()=>{ document.getElementById('chosen').textContent=res[i].preview; }); resultsTable.appendChild(tr); } }); </script> </div> ```

![Cesare widget screenshot](/assets/images/script.png)
