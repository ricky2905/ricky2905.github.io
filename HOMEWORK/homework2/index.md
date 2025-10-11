# Cesare: Caesar Cipher Widget

Questo widget permette di **generare e analizzare un cifrario di Cesare** direttamente nella pagina Markdown.

<!-- Cesare widget -->
<div id="cesare-widget">
  <style>
    #cesare-widget {
      font-family: system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial;
      padding: 12px;
      border: 1px solid #cfe6cfe0;
      border-radius: 8px;
      background-color: #e6f7e6;
      color: #000;
    }
    #cesare-widget h3, #cesare-widget h4 {
      color: #000;
      margin-top: 0;
    }
    #cesare-widget textarea {
      width: 100%;
      font-family: monospace;
      font-size: 13px;
      margin-bottom: 8px;
      background-color: #f7fff7;
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
      background: #eaf9ea;
    }
    #cesare-widget th, #cesare-widget td {
      border: 1px solid #cfe6cf;
      padding: 6px;
      text-align: left;
      color: #000;
    }
    #cesare-widget th {
      background: #d0f0d0;
      font-weight: 600;
    }
    #cesare-widget .result {
      white-space: pre-wrap;
      background: #ffffff;
      border: 1px solid #cfe6cf;
      padding: 10px;
      margin-top: 8px;
      max-height: 280px;
      overflow: auto;
      color: #000;
    }
    #cesare-widget .top {
      background: #bfe6bf !important;
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

  <script>
    function caesarEncrypt(text, shift, mapAccents = true) {
      if(mapAccents) text = text.normalize('NFD').replace(/[\u0300-\u036f]/g, "");
      return text.replace(/[a-zA-Z]/g, c => {
        let base = c >= 'a' ? 97 : 65;
        return String.fromCharCode((c.charCodeAt(0) - base + shift) % 26 + base);
      });
    }

    const plaintext = document.getElementById('plaintext');
    const genShift = document.getElementById('genShift');
    const genMapAcc = document.getElementById('genMapAcc');
    const generated = document.getElementById('generated');
    const genBtn = document.getElementById('genBtn');
    const genAnalyzeBtn = document.getElementById('genAnalyzeBtn');
    const copyCipher = document.getElementById('copyCipher');

    genBtn.addEventListener('click', () => {
      generated.value = caesarEncrypt(plaintext.value, parseInt(genShift.value), genMapAcc.checked);
    });

    genAnalyzeBtn.addEventListener('click', () => {
      generated.value = caesarEncrypt(plaintext.value, parseInt(genShift.value), genMapAcc.checked);
      document.getElementById('cipher').value = generated.value;
      document.getElementById('run').click();
    });

    copyCipher.addEventListener('click', () => {
      generated.select();
      document.execCommand('copy');
      alert('Cipher copied!');
    });
  </script>
</div>

![Cesare widget screenshot](/assets/images/script.png)
