<!-- Cesare widget aggiornato: mostra solo Top N e bottone per estendere la tabella -->
<div id="cesare-widget">
  <style>
/* stile aggiornato: testo nero su sfondo verde (migliore contrasto) */
#cesare-widget {
  font-family: system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial;
  padding: 12px;
  border: 1px solid #cfe6cfe0;
  border-radius: 8px;
  background-color: #e6f7e6; /* verde chiaro di sfondo */
  color: #000;               /* testo in nero */
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
  background-color: #f7fff7; /* leggermente chiaro rispetto al fondo */
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
  background: #eaf9ea; /* tabella su verde chiaro */
}

#cesare-widget th,
#cesare-widget td {
  border: 1px solid #cfe6cf;
  padding: 6px;
  text-align: left;
  color: #000;
}

#cesare-widget th {
  background: #d0f0d0; /* intestazione leggermente più scura */
  color: #000;
  font-weight: 600;
}

#cesare-widget .result {
  white-space: pre-wrap;
  background: #ffffff; /* area testo selezionato bianca per leggibilità */
  border: 1px solid #cfe6cf;
  padding: 10px;
  margin-top: 8px;
  max-height: 280px;
  overflow: auto;
  color: #000;
}

#cesare-widget .top {
  background: #bfe6bf !important; /* evidenzia riga top con verde più scuro */
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

  <h3>Cesare: genera cifrato → analizza</h3>
  <div class="small">Inserisci un testo in chiaro qui sotto, scegli lo shift e premi <strong>Genera</strong>. Puoi usare <strong>Genera e Analizza</strong> per generare il cifrato e avviare subito l'analisi.</div>

  <div class="generator" aria-label="Generatore Cesare">
    <div class="small"><strong>Generatore</strong></div>
    <textarea id="plaintext">Questo è un messaggio di prova: prova a cambiare shift e vedere il risultato.</textarea>
    <div class="controls">
      <label>Shift <input id="genShift" type="number" min="0" max="25" value="7"></label>
      <label><input id="genMapAcc" type="checkbox" checked> Mappa accenti prima di cifrare</label>
      <button id="genBtn" class="small">Genera</button>
      <button id="genAnalyzeBtn" class="small">Genera e Analizza</button>
      <button id="copyCipher" class="small">Copia cifrato</button>
    </div>
    <textarea id="generated" readonly style="height:72px"></textarea>
  </div>

  <label><strong>Testo cifrato (per analisi)</strong></label>
  <textarea id="cipher">Xblzav l bu tlzzhnnpv kp wyvch. Jvuaplul whyvsl jvtbup l xbhsjol ipnyhtth apwpjv.</textarea>

  <div class="controls">
    <label><input type="checkbox" id="mapAccents" checked> Mappa accenti (è→e)</label>
    <label><input type="checkbox" id="ignoreCase" checked> Ignora maiuscole</label>
    <button id="run" class="small">Prova tutti gli shift</button>
    <button id="sortHam" class="small">Ordina per Hamming</button>
    <button id="sortChi" class="small">Ordina per Chi²</button>
    <button id="sortComb" class="small">Ordina per Combined</button>
    <label style="margin-left:8px" class="small">Top N: <input id="topN" type="number" value="5" min="1" max="26"/></label>
    <!-- bottone per estendere / comprimere la tabella -->
    <button id="toggleExpand" class="small expandBtn" aria-pressed="false">Mostra tutto</button>
  </div>

  <details style="margin-top:8px">
    <summary class="small">Pesi per il punteggio combinato</summary>
    <div style="margin-top:8px">
      <label class="small">peso wordScore: <input id="w_word" type="number" step="0.05" value="0.45" /></label>
      <label class="small" style="margin-left:8px">peso hamScore: <input id="w_ham" type="number" step="0.05" value="0.25" /></label>
      <label class="small" style="margin-left:8px">peso chiScore: <input id="w_chi" type="number" step="0.05" value="0.15" /></label>
      <label class="small" style="margin-left:8px">peso bigramScore: <input id="w_big" type="number" step="0.05" value="0.15" /></label>
    </div>
  </details>

  <div id="summary" class="small" style="margin-top:8px"></div>

  <table id="results" aria-live="polite">
    <thead>
      <tr>
        <th>Shift</th><th>combined</th><th>word%</th><th>ham</th><th>chi²</th><th>bigram%</th><th>anteprima</th><th></th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>

  <h4>Testo selezionato</h4>
  <div id="chosen" class="result">---</div>

  <script>
  /* --- Frequenze di riferimento (italiano) --- */
  const referenceFreq = {'A':10.85,'B':1.05,'C':4.30,'D':3.39,'E':11.49,'F':1.01,'G':1.65,'H':1.43,'I':10.18,'J':0.01,'K':0.01,'L':5.70,'M':2.87,'N':7.02,'O':9.97,'P':2.96,'Q':0.45,'R':6.19,'S':5.48,'T':6.97,'U':3.16,'V':1.75,'W':0.01,'X':0.01,'Y':0.06,'Z':0.85};

  /* mappa accenti */
  const accentMap = {'à':'a','á':'a','â':'a','ã':'a','ä':'a','å':'a','è':'e','é':'e','ê':'e','ë':'e','ì':'i','í':'i','î':'i','ï':'i','ò':'o','ó':'o','ô':'o','õ':'o','ö':'o','ù':'u','ú':'u','û':'u','ü':'u','À':'A','Á':'A','Â':'A','Ã':'A','Ä':'A','Å':'A','È':'E','É':'E','Ê':'E','Ë':'E','Ì':'I','Í':'I','Î':'I','Ï':'I','Ò':'O','Ó':'O','Ô':'O','Õ':'O','Ö':'O','Ù':'U','Ú':'U','Û':'U','Ü':'U','ç':'c','Ç':'C','ñ':'n','Ñ':'N'};
  function mapAccents(s){ return s.split('').map(ch => accentMap[ch] || ch).join(''); }

  /* mini dizionario e bigrammi */
  const commonWords = new Set(['di','e','il','la','che','a','in','un','per','con','non','una','sono','si','da','al','lo','ma','più','come','su','o','se','anche','perché','quando','dove','questo','quella','queste','noi','voi','loro','mia','mio','tua','tuo','suo','nostro','l','ha','hai','ho','era','stato','stata','cioè','ora','bene','male','molto','poco','grande','piccolo','anno','giorno','giorni']);
  const commonBigrams = ['CH','GL','QU','SC','GN','TR','PR','BR','SP','RA','RE','RI','IN','AN','TO','TE','IO','IA','CO'];

  /* caesar shift */
  function caesarShift(text, shift){
    return text.split('').map(ch => {
      const c = ch.charCodeAt(0);
      if (c>=65 && c<=90) return String.fromCharCode(((c-65+shift)%26)+65);
      if (c>=97 && c<=122) return String.fromCharCode(((c-97+shift)%26)+97);
      return ch;
    }).join('');
  }

  /* utilities: freqs, rank, hamming, chi2, wordscore, bigrams */
  function letterFrequencies(text){
    const counts = {}; for (let i=65;i<=90;i++) counts[String.fromCharCode(i)] = 0;
    let total = 0;
    for (const ch of text){ const C = ch.toUpperCase(); if (C>='A'&&C<='Z'){ counts[C] = (counts[C]||0)+1; total++; } }
    const freqs = {}; for (const L in counts) freqs[L] = total ? (counts[L]/total*100) : 0;
    return {counts, freqs, total};
  }
  function rankByFrequency(freqs){ return Object.keys(freqs).sort((a,b)=> freqSort(freqs,b,a)); }
  function freqSort(freqs,b,a){ if (freqs[b]===freqs[a]) return a.localeCompare(b); return freqs[b]-freqs[a]; }
  function hammingRankDistance(orderA,orderB){ let diff=0; for (let i=0;i<26;i++) if (orderA[i]!==orderB[i]) diff++; return diff; }
  function chiSquared(obsFreqs){ let chi2=0; for (const L in referenceFreq){ const O=obsFreqs[L]||0; const E=referenceFreq[L]; if (E>0) chi2 += ((O-E)*(O-E))/E; } return chi2; }
  function wordScore(text){ const tokens = text.toLowerCase().split(/[^a-zA-Z]+/).filter(Boolean); if (!tokens.length) return 0; let hits=0; for (const t of tokens) if (commonWords.has(t)) hits++; return hits/tokens.length; }
  function bigramScore(text){ const s = text.toUpperCase().replace(/[^A-Z]/g,''); if (s.length<2) return 0; let found=0; for (const bg of commonBigrams) if (s.indexOf(bg)!==-1) found++; return found/commonBigrams.length; }

  const referenceOrder = Object.keys(referenceFreq).sort((a,b)=>{ if (referenceFreq[b]===referenceFreq[a]) return a.localeCompare(b); return referenceFreq[b]-referenceFreq[a]; });

  /* UI hooks */
  document.getElementById('genBtn').addEventListener('click', ()=> {
    const pt = document.getElementById('plaintext').value || '';
    let s = Math.max(0, Math.min(25, parseInt(document.getElementById('genShift').value || '0',10)));
    const map = document.getElementById('genMapAcc').checked;
    const input = map ? mapAccents(pt) : pt;
    document.getElementById('generated').value = caesarShift(input, s);
  });

  document.getElementById('genAnalyzeBtn').addEventListener('click', ()=> {
    document.getElementById('genBtn').click();
    document.getElementById('cipher').value = document.getElementById('generated').value || '';
    tryAll();
  });

  document.getElementById('copyCipher').addEventListener('click', async ()=> {
    try { const t = document.getElementById('generated').value || document.getElementById('cipher').value || ''; await navigator.clipboard.writeText(t); alert('Cifrato copiato.'); } catch(e){ alert('Copia non permessa dal browser.'); }
  });

  document.getElementById('run').addEventListener('click', tryAll);
  document.getElementById('sortHam').addEventListener('click', ()=> renderResults(window._lastResults||[], 'ham'));
  document.getElementById('sortChi').addEventListener('click', ()=> renderResults(window._lastResults||[], 'chi'));
  document.getElementById('sortComb').addEventListener('click', ()=> renderResults(window._lastResults||[], 'combined'));

  // toggle expand button
  const toggleBtn = document.getElementById('toggleExpand');
  toggleBtn.addEventListener('click', ()=>{
    window._showAll = !window._showAll;
    toggleBtn.textContent = window._showAll ? 'Mostra solo Top' : 'Mostra tutto';
    toggleBtn.setAttribute('aria-pressed', String(window._showAll));
    renderResults(window._lastResults||[], window._lastSortMode||'combined');
  });

  function readWeights(){
    const w_word = parseFloat(document.getElementById('w_word').value) || 0.45;
    const w_ham  = parseFloat(document.getElementById('w_ham').value)  || 0.25;
    const w_chi  = parseFloat(document.getElementById('w_chi').value)  || 0.15;
    const w_big  = parseFloat(document.getElementById('w_big').value)  || 0.15;
    const sum = w_word + w_ham + w_chi + w_big;
    return {w_word: w_word/sum, w_ham: w_ham/sum, w_chi: w_chi/sum, w_big: w_big/sum};
  }

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
        const hamScore = (26 - ham)/26;
        const chiScore = 1 / (1 + chi2);
        results.push({shift:s, shifted, ham, chi2, freqs, total, wordScore: ws, bigramScore: bg, hamScore, chiScore});
      }
      const chiVals = results.map(r=>r.chiScore);
      const chiMin = Math.min(...chiVals), chiMax = Math.max(...chiVals);
      results.forEach(r => r.chiScoreNorm = (chiMax===chiMin)?0:((r.chiScore-chiMin)/(chiMax-chiMin)));
      const weights = readWeights();
      results.forEach(r => r.combined = weights.w_word*r.wordScore + weights.w_ham*r.hamScore + weights.w_chi*r.chiScoreNorm + weights.w_big*r.bigramScore);
      window._lastResults = results;
      // salva modalità di ordinamento per ri-render dopo toggle
      window._lastSortMode = 'combined';
      renderResults(results, 'combined');
      document.getElementById('summary').textContent = `Generati 26 candidati. Ordinati per "combined".`;
    } catch(e){ console.error(e); alert('Errore: vedi console.'); }
  }

  function renderResults(results, mode){
    if (!results || !results.length) return alert('Esegui prima "Prova tutti gli shift".');
    window._lastSortMode = mode;
    const tbody = document.querySelector('#cesare-widget table tbody');
    tbody.innerHTML = '';
    const topN = Math.max(1, Math.min(26, parseInt(document.getElementById('topN').value || '5',10)));
    let sorted = [...results];
    if (mode === 'ham') sorted.sort((a,b)=>a.ham - b.ham || b.combined - a.combined);
    else if (mode === 'chi') sorted.sort((a,b)=>a.chi2 - b.chi2 || b.combined - a.combined);
    else sorted.sort((a,b)=>b.combined - a.combined || a.ham - b.ham);

    // se showAll è falso, mostriamo solo topN; altrimenti tutti
    const showAll = !!window._showAll;
    const toRender = showAll ? sorted : sorted.slice(0, topN);

    // aggiungiamo righe effettive
    toRender.forEach((r, idx) => {
      const tr = document.createElement('tr');
      // manteniamo l'evidenziazione delle righe top (relative alla vista completa)
      if (idx < topN && !showAll) tr.classList.add('top');
      // se stiamo mostrando tutto, evidenziamo le prime topN globali
      if (showAll){
        const globalIndex = sorted.indexOf(r);
        if (globalIndex < topN) tr.classList.add('top');
      }
      tr.innerHTML = `<td>${r.shift}</td><td>${r.combined.toFixed(4)}</td><td>${(r.wordScore*100).toFixed(1)}%</td><td>${r.ham} (${r.hamScore.toFixed(3)})</td><td>${r.chi2.toFixed(2)}</td><td>${(r.bigramScore*100).toFixed(1)}%</td><td><code>${escapeHtml(r.shifted.slice(0,140))}${r.shifted.length>140?'…':''}</code></td><td><button data-shift="${r.shift}">Seleziona</button></td>`;
      tbody.appendChild(tr);
    });

    // se non stiamo mostrando tutto e ci sono altre righe, aggiungiamo una riga di 'espandi'
    if (!showAll && sorted.length > toRender.length){
      const remaining = sorted.length - toRender.length;
      const tr = document.createElement('tr');
      tr.innerHTML = `<td colspan="8" style="text-align:center"><button id="expandInline" class="small">Mostra altri ${remaining} risultati</button></td>`;
      tbody.appendChild(tr);
      document.getElementById('expandInline').addEventListener('click', ()=>{
        // attiva il toggle (mostra tutto) e ri-render
        window._showAll = true; toggleBtn.textContent = 'Mostra solo Top'; toggleBtn.setAttribute('aria-pressed','true');
        renderResults(results, mode);
      });
    }

    // bind bottoni selezione
    tbody.querySelectorAll('button[data-shift]').forEach(btn=>btn.addEventListener('click', e=> {
      const s = parseInt(e.currentTarget.getAttribute('data-shift'),10);
      const obj = results.find(r=>r.shift===s);
      showSelected(obj);
    }));
  }

  function showSelected(obj){
    if (!obj) return;
    const out = document.getElementById('chosen');
    out.textContent = `${obj.shift}: ${obj.shifted}\n\nTot lettere A–Z considerate: ${obj.total}\n\nScores:\n  combined: ${obj.combined.toFixed(4)}\n  wordScore: ${(obj.wordScore*100).toFixed(1)}%\n  ham: ${obj.ham} (hamScore ${(obj.hamScore).toFixed(3)})\n  chi²: ${obj.chi2.toFixed(2)} (chiScoreNorm ${(obj.chiScoreNorm||0).toFixed(3)})\n  bigramScore: ${(obj.bigramScore*100).toFixed(1)}%\n\nAnteprima:\n${obj.shifted}`;
    out.scrollIntoView({behavior:'smooth'});
  }

  function escapeHtml(str){ return str.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

  /* inizializza stato */
  window._showAll = false;
  window._lastResults = null;
  window._lastSortMode = 'combined';

  /* run iniziale */
  tryAll();
  </script>
</div>
