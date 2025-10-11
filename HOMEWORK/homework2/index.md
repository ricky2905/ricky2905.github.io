<!doctype html>
<html lang="it">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Cesare: generate cipher → analyze</title>
<style>
/* updated style: black text on green background (better contrast) */
#cesare-widget { font-family: system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial; padding: 12px; border: 1px solid #cfe6cfe0; border-radius: 8px; background-color: #e6f7e6; /* light green background */ color: #000; /* black text */ }
#cesare-widget h3, #cesare-widget h4 { color: #000; margin-top: 0; }
#cesare-widget textarea { width: 100%; font-family: monospace; font-size: 13px; margin-bottom: 8px; background-color: #f7fff7; /* slightly lighter than background */ color: #000; border: 1px solid #cfe6cf; padding: 8px; box-sizing: border-box; }
#cesare-widget .controls { display: flex; gap: 8px; flex-wrap: wrap; align-items: center; margin: 8px 0; }
#cesare-widget table { border-collapse: collapse; width: 100%; margin-top: 8px; font-size: 13px; background: #eaf9ea; /* light green for table */ }
#cesare-widget th, #cesare-widget td { border: 1px solid #cfe6cf; padding: 6px; text-align: left; color: #000; }
#cesare-widget th { background: #d0f0d0; /* slightly darker header */ color: #000; font-weight: 600; }
#cesare-widget .result { white-space: pre-wrap; background: #ffffff; /* white background for clarity */ border: 1px solid #cfe6cf; padding: 10px; margin-top: 8px; max-height: 280px; overflow: auto; color: #000; }
#cesare-widget .top { background: #bfe6bf !important; /* darker green highlight for top rows */ color: #000 !important; }
#cesare-widget .generator { border: 1px dashed #bfe6bf; padding: 8px; margin: 8px 0; border-radius: 6px; background: #eaf9ea; color: #000; }
#cesare-widget .small { font-size: 13px; color: #000; }
#cesare-widget input[type="number"] { width: 72px; color: #000; }
#cesare-widget button.small { padding: 6px 8px; font-size: 13px; background: #d8f0d8; border: 1px solid #bfe6bf; color: #000; cursor: pointer; border-radius: 4px; }
#cesare-widget button.small:hover { background: #c6e8c6; }
#cesare-widget .expandBtn { margin-left: 8px; background: #fff9d9; border: 1px solid #f0e6b8; }
@media (max-width:640px){
  #cesare-widget .controls{flex-direction:column;align-items:flex-start;}
}
</style>
</head>
<body>
<div id="cesare-widget" role="region" aria-label="Cesare widget">
  <h3>Cesare: generate cipher → analyze</h3>
  <div class="small">Enter plaintext below, choose the shift, and click <strong>Generate</strong>. You can also use <strong>Generate and Analyze</strong> to immediately generate and analyze the cipher.</div>

  <div class="generator" aria-label="Caesar generator">
    <div class="small"><strong>Generator</strong></div>
    <textarea id="plaintext">My password is Hello.123!</textarea>
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
</div>

<script>
/* Cesar widget — fully functional JS */
(function(){
  // Utilities
  const el = id => document.getElementById(id);
  const textareaCipher = el('cipher');
  const textareaGenerated = el('generated');
  const plaintextEl = el('plaintext');

  // small Italian word list (extendable)
  const DICT = new Set([
    "il","lo","la","i","gli","le","e","di","che","per","non","si","un","una","con",
    "sono","sono","ciao","salve","come","ciao","ma","se","mi","ti","ha","hai","ha","anche",
    "password","is","hello","my","the","sono"
  ]);

  const italianFreq = {
    A:11.745,B:0.927,C:4.501,D:3.736,E:11.792,F:1.153,G:1.644,H:0.636,I:10.143,L:6.510,M:2.512,N:6.883,O:9.832,P:3.056,Q:0.505,R:6.367,S:4.981,T:5.623,U:3.011,V:2.097,Z:1.181,
    J:0.001,K:0.001,W:0.001,X:0.001,Y:0.001
  };

  const COMMON_BIGRAMS = ["CH","GL","GH","GN","SC","QU","TR","PR","BR","ST","TI","AR","RE","EN","AL"];

  // accent mapping
  const ACC_MAP = {'à':'a','á':'a','â':'a','ä':'a','ã':'a','å':'a','è':'e','é':'e','ê':'e','ë':'e','ì':'i','í':'i','î':'i','ï':'i','ò':'o','ó':'o','ô':'o','ö':'o','õ':'o','ù':'u','ú':'u','û':'u','ü':'u','ý':'y','ÿ':'y','ç':'c','ñ':'n'};

  function mapAccents(s){
    return s.split('').map(ch => ACC_MAP[ch] || ACC_MAP[ch?.toLowerCase()]?.toUpperCase?.() || ch).join('');
  }

  function caesarShift(text, shift, opts={ignoreCase:true, mapAcc:false}){
    if(opts.mapAcc) text = mapAccents(text);
    const out = [];
    for(let i=0;i<text.length;i++){
      const ch = text[i];
      if(/[A-Za-z]/.test(ch)){
        const base = (ch === ch.toUpperCase()) ? 'A'.charCodeAt(0) : 'a'.charCodeAt(0);
        // if ignoreCase is set, we will produce lowercase shifted output for readability
        if(opts.ignoreCase){
          const lower = ch.toLowerCase();
          const code = (lower.charCodeAt(0) - 'a'.charCodeAt(0) + shift) % 26;
          out.push(String.fromCharCode('a'.charCodeAt(0) + code));
        } else {
          const code = (ch.charCodeAt(0) - base + shift) % 26;
          out.push(String.fromCharCode(base + code));
        }
      } else {
        out.push(ch);
      }
    }
    return out.join('');
  }

  // frequency analysis
  function letterFrequencies(text){
    const counts = {};
    let total = 0;
    for(const ch of text.toUpperCase()){
      if(ch >= 'A' && ch <= 'Z'){
        counts[ch] = (counts[ch]||0) + 1; total++;
      }
    }
    const freqs = {};
    for(let c='A'.charCodeAt(0); c <= 'Z'.charCodeAt(0); c++){
      const ch = String.fromCharCode(c);
      freqs[ch] = (counts[ch]||0) / (total || 1) * 100;
    }
    return {freqs, counts, total};
  }

  function chiSquared(obsFreq){
    // obsFreq: {A:%, B:%, ...}
    let chi = 0;
    for(const L in italianFreq){
      const expected = italianFreq[L];
      const observed = obsFreq[L] || 0;
      const diff = observed - expected;
      chi += (diff*diff)/ (expected || 0.0001);
    }
    return chi;
  }

  function hamScoreFromFreqs(obsFreq){
    // compute rank arrays and measure rank differences
    const obsSorted = Object.keys(obsFreq).sort((a,b)=>obsFreq[b]-obsFreq[a]);
    const expSorted = Object.keys(italianFreq).sort((a,b)=>italianFreq[b]-italianFreq[a]);
    const rankObs = {};
    obsSorted.forEach((ch,i)=>rankObs[ch]=i);
    const rankExp = {};
    expSorted.forEach((ch,i)=>rankExp[ch]=i);
    let sumAbs = 0;
    for(const ch in rankObs){
      sumAbs += Math.abs(rankObs[ch]-rankExp[ch]);
    }
    // maximum possible sumAbs ~ 26 * 25
    const max = 26 * 25;
    const normalized = 1 - (sumAbs / max); // closer to 1 better
    return Math.max(0, Math.min(1, normalized));
  }

  function wordScoreFromText(text){
    const words = text.toLowerCase().match(/[a-zàèéìíîòóùúçñ]+/gi) || [];
    if(words.length === 0) return 0;
    let matches = 0;
    for(const w of words){
      if(DICT.has(w)) matches++;
      else {
        // also try remove trailing s (plural) and common punctuation
        const w2 = w.replace(/s$/,'');
        if(DICT.has(w2)) matches++;
      }
    }
    return matches / words.length;
  }

  function bigramScoreFromText(text){
    const up = text.toUpperCase();
    let found = 0;
    for(const b of COMMON_BIGRAMS){
      if(up.indexOf(b) >= 0) found++;
    }
    return found / COMMON_BIGRAMS.length; // 0..1
  }

  // create result object for a shift
  function analyzeShift(cipherText, shift, opts){
    const dec = caesarShift(cipherText, (26 - shift)%26, opts); // decrypt by shifting back
    const proc = opts.mapAcc ? mapAccents(dec) : dec;
    const procForAnalysis = opts.ignoreCase ? proc.toLowerCase() : proc;
    const freqs = letterFrequencies(procForAnalysis);
    const chi = chiSquared(freqs.freqs);
    const ham = hamScoreFromFreqs(freqs.freqs); // 0..1 (higher better)
    const word = wordScoreFromText(procForAnalysis); // 0..1
    const big = bigramScoreFromText(procForAnalysis); // 0..1
    // normalize chi to a 0..1 where higher is better (smaller chi -> closer to 1)
    const chiNorm = 1 / (1 + chi);
    return {
      shift,
      decrypt: dec,
      chi,
      chiNorm,
      ham,
      word,
      big
    };
  }

  // DOM helpers for table
  const tbody = document.querySelector('#results tbody');
  const topNInput = el('topN');
  const toggleExpandBtn = el('toggleExpand');
  let lastResults = []; // array of result objects
  let expanded = false;

  function renderResults(results, sortKey='combined'){
    // results must have combined property
    // sort
    const rows = [...results].sort((a,b)=>{
      if(sortKey === 'ham') return (b.ham - a.ham);
      if(sortKey === 'chi') return (b.chiNorm - a.chiNorm);
      return (b.combined - a.combined);
    });
    lastResults = rows;
    const topN = Math.max(1, Math.min(26, parseInt(topNInput.value)||5));
    tbody.innerHTML = '';
    const toShow = expanded ? rows : rows.slice(0, topN);
    toShow.forEach((r, idx)=>{
      const tr = document.createElement('tr');
      if(idx < topN && !expanded) tr.classList.add('top');
      tr.innerHTML = `
        <td>${r.shift}</td>
        <td>${(r.combined*100).toFixed(1)}%</td>
        <td>${(r.word*100).toFixed(1)}%</td>
        <td>${(r.ham*100).toFixed(1)}%</td>
        <td>${r.chi.toFixed(2)}</td>
        <td>${(r.big*100).toFixed(1)}%</td>
        <td style="max-width:240px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">${escapeHtml(r.decrypt)}</td>
        <td><button class="small selectBtn" data-shift="${r.shift}">Select</button></td>
      `;
      tbody.appendChild(tr);
    });

    // add event listeners to select buttons
    tbody.querySelectorAll('.selectBtn').forEach(btn=>{
      btn.addEventListener('click', (ev)=>{
        const shift = parseInt(btn.dataset.shift,10);
        const sel = rows.find(x=>x.shift===shift);
        if(sel) showChosen(sel);
      });
    });
  }

  function showChosen(result){
    const chosen = el('chosen');
    const html = [
      `Shift: ${result.shift}`,
      `Combined: ${(result.combined*100).toFixed(2)}%`,
      `Word match: ${(result.word*100).toFixed(1)}%`,
      `Hamming score: ${(result.ham*100).toFixed(1)}%`,
      `Chi²: ${result.chi.toFixed(2)} (chiNorm:${result.chiNorm.toFixed(4)})`,
      `Bigram: ${(result.big*100).toFixed(1)}%`,
      '',
      result.decrypt
    ].join('\n');
    chosen.textContent = html;
  }

  function escapeHtml(s){
    return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
  }

  // main analyze function
  function runAnalysis(){
    const cipher = textareaCipher.value || '';
    const opts = {
      mapAcc: el('mapAccents').checked,
      ignoreCase: el('ignoreCase').checked
    };
    const w_word = parseFloat(el('w_word').value) || 0;
    const w_ham  = parseFloat(el('w_ham').value) || 0;
    const w_chi  = parseFloat(el('w_chi').value) || 0;
    const w_big  = parseFloat(el('w_big').value) || 0;
    // normalize weights sum to 1 (if not zero)
    const sumW = w_word + w_ham + w_chi + w_big;
    const weights = sumW ? {w_word:w_word/sumW, w_ham:w_ham/sumW, w_chi:w_chi/sumW, w_big:w_big/sumW} : {w_word:0.4,w_ham:0.3,w_chi:0.15,w_big:0.15};

    const results = [];
    for(let s=0;s<26;s++){
      const r = analyzeShift(cipher, s, opts);
      // combined uses chiNorm (higher better), ham, word, big
      r.combined = (weights.w_word * r.word) + (weights.w_ham * r.ham) + (weights.w_chi * r.chiNorm) + (weights.w_big * r.big);
      results.push(r);
    }

    // default sort by combined
    renderResults(results, 'combined');

    // update summary
    const summary = el('summary');
    summary.textContent = `Analyzed ${results.length} shifts — showing Top ${topNInput.value}. Weights (normalized): word ${weights.w_word.toFixed(2)}, ham ${weights.w_ham.toFixed(2)}, chi ${weights.w_chi.toFixed(2)}, bigram ${weights.w_big.toFixed(2)}.`;
  }

  // wire up buttons
  el('run').addEventListener('click', runAnalysis);
  el('sortHam').addEventListener('click', ()=>renderResults(lastResults,'ham'));
  el('sortChi').addEventListener('click', ()=>renderResults(lastResults,'chi'));
  el('sortComb').addEventListener('click', ()=>renderResults(lastResults,'combined'));

  toggleExpandBtn.addEventListener('click', ()=>{
    expanded = !expanded;
    toggleExpandBtn.textContent = expanded ? 'Show top' : 'Show all';
    toggleExpandBtn.setAttribute('aria-pressed', expanded ? 'true' : 'false');
    // re-render with same sort (use combined)
    renderResults(lastResults, 'combined');
  });

  // Generate cipher from plaintext
  el('genBtn').addEventListener('click', ()=>{
    const shift = parseInt(el('genShift').value || 0,10) % 26;
    const opts = {mapAcc: el('genMapAcc').checked, ignoreCase: true};
    const plain = plaintextEl.value || '';
    const gen = caesarShift(plain, shift, opts);
    textareaGenerated.value = gen;
    // do not overwrite analysis field unless user clicks copy
  });

  el('genAnalyzeBtn').addEventListener('click', ()=>{
    const shift = parseInt(el('genShift').value || 0,10) % 26;
    const opts = {mapAcc: el('genMapAcc').checked, ignoreCase: true};
    const plain = plaintextEl.value || '';
    const gen = caesarShift(plain, shift, opts);
    textareaGenerated.value = gen;
    textareaCipher.value = gen;
    runAnalysis();
  });

  el('copyCipher').addEventListener('click', ()=>{
    const t = textareaGenerated.value || '';
    if(!t) return;
    textareaCipher.value = t;
    // attempt to copy to clipboard
    navigator.clipboard?.writeText(t).catch(()=>{/* ignore */});
  });

  // initial auto-run
  runAnalysis();

})();
</script>
</body>
</html>
