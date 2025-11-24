<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DNA Sequence Analyzer</title>
    <style>
        :root {
            --color-adenine: #FF6B6B;
            --color-thymine: #4ECDC4;
            --color-guanine: #FFE66D;
            --color-cytosine: #95E1D3;
            --color-background: var(--color-cream-50);
            --color-surface: var(--color-cream-100);
            --color-text: var(--color-slate-900);
            --color-primary: var(--color-teal-500);
            --color-border: rgba(94, 82, 64, 0.2);
        }

        @media (prefers-color-scheme: dark) {
            :root {
                --color-background: var(--color-charcoal-700);
                --color-surface: var(--color-charcoal-800);
                --color-text: var(--color-gray-200);
                --color-border: rgba(119, 124, 124, 0.3);
            }
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
            color: var(--color-text);
        }

        .container {
            max-width: 900px;
            margin: 0 auto;
            background: var(--color-surface);
            border-radius: 16px;
            padding: 32px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
        }

        h1 {
            text-align: center;
            color: var(--color-primary);
            margin-bottom: 8px;
            font-size: 2.5rem;
        }

        .subtitle {
            text-align: center;
            color: var(--color-text-secondary);
            margin-bottom: 32px;
            font-size: 1rem;
        }

        .input-group {
            margin-bottom: 24px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: var(--color-text);
        }

        textarea {
            width: 100%;
            padding: 16px;
            border: 2px solid var(--color-border);
            border-radius: 12px;
            font-family: 'Courier New', monospace;
            font-size: 16px;
            resize: vertical;
            min-height: 120px;
            background: var(--color-background);
            color: var(--color-text);
            transition: border-color 0.3s;
        }

        textarea:focus {
            outline: none;
            border-color: var(--color-primary);
        }

        .btn-group {
            display: flex;
            gap: 12px;
            margin-bottom: 32px;
        }

        button {
            flex: 1;
            padding: 14px 24px;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 20px rgba(102, 126, 234, 0.4);
        }

        .btn-secondary {
            background: var(--color-secondary);
            color: var(--color-text);
        }

        .btn-secondary:hover {
            background: var(--color-secondary-hover);
        }

        .results {
            display: none;
            animation: fadeIn 0.5s;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .result-card {
            background: var(--color-background);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            border: 1px solid var(--color-border);
        }

        .result-card h3 {
            margin-bottom: 16px;
            color: var(--color-primary);
            font-size: 1.3rem;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 16px;
            margin-bottom: 20px;
        }

        .stat-box {
            background: var(--color-surface);
            padding: 16px;
            border-radius: 10px;
            text-align: center;
            border: 2px solid var(--color-border);
        }

        .stat-value {
            font-size: 2rem;
            font-weight: bold;
            margin-bottom: 4px;
        }

        .stat-label {
            font-size: 0.9rem;
            color: var(--color-text-secondary);
        }

        .nucleotide-a { color: var(--color-adenine); }
        .nucleotide-t { color: var(--color-thymine); }
        .nucleotide-g { color: var(--color-guanine); }
        .nucleotide-c { color: var(--color-cytosine); }

        .sequence-display {
            font-family: 'Courier New', monospace;
            font-size: 18px;
            line-height: 1.8;
            word-break: break-all;
            padding: 16px;
            background: var(--color-surface);
            border-radius: 8px;
            margin-top: 12px;
        }

        .gc-bar {
            height: 30px;
            background: linear-gradient(90deg, var(--color-guanine) 0%, var(--color-cytosine) 100%);
            border-radius: 15px;
            margin-top: 12px;
            position: relative;
            overflow: hidden;
        }

        .gc-bar-fill {
            height: 100%;
            background: linear-gradient(90deg, #FFE66D 0%, #95E1D3 100%);
            border-radius: 15px;
            transition: width 0.6s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #333;
            font-weight: bold;
        }

        .error {
            background: rgba(255, 107, 107, 0.1);
            border: 2px solid #FF6B6B;
            color: #FF6B6B;
            padding: 16px;
            border-radius: 10px;
            margin-bottom: 20px;
            display: none;
        }

        .info-box {
            background: rgba(78, 205, 196, 0.1);
            border-left: 4px solid #4ECDC4;
            padding: 16px;
            border-radius: 8px;
            margin-bottom: 20px;
            font-size: 0.9rem;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🧬 DNA Sequence Analyzer</h1>
        <p class="subtitle">Analyze nucleotide composition and properties of DNA sequences</p>

        <div class="info-box">
            💡 <strong>Tip:</strong> Enter a DNA sequence using letters A, T, G, C. Example: ATGCGATACGTAGCTA
        </div>

        <div class="input-group">
            <label for="dnaInput">Enter DNA Sequence:</label>
            <textarea id="dnaInput" placeholder="ATGCGATACGTAGCTAATGCCCGTATCGATGC"></textarea>
        </div>

        <div class="btn-group">
            <button class="btn-primary" onclick="analyzeDNA()">🔬 Analyze Sequence</button>
            <button class="btn-secondary" onclick="generateRandom()">🎲 Generate Random</button>
            <button class="btn-secondary" onclick="clearAll()">🗑️ Clear</button>
        </div>

        <div class="error" id="error"></div>

        <div class="results" id="results">
            <div class="result-card">
                <h3>📊 Nucleotide Composition</h3>
                <div class="stats-grid">
                    <div class="stat-box">
                        <div class="stat-value nucleotide-a" id="countA">0</div>
                        <div class="stat-label">Adenine (A)</div>
                    </div>
                    <div class="stat-box">
                        <div class="stat-value nucleotide-t" id="countT">0</div>
                        <div class="stat-label">Thymine (T)</div>
                    </div>
                    <div class="stat-box">
                        <div class="stat-value nucleotide-g" id="countG">0</div>
                        <div class="stat-label">Guanine (G)</div>
                    </div>
                    <div class="stat-box">
                        <div class="stat-value nucleotide-c" id="countC">0</div>
                        <div class="stat-label">Cytosine (C)</div>
                    </div>
                </div>
            </div>

            <div class="result-card">
                <h3>📈 Sequence Properties</h3>
                <div class="stats-grid">
                    <div class="stat-box">
                        <div class="stat-value" id="totalLength">0</div>
                        <div class="stat-label">Total Length</div>
                    </div>
                    <div class="stat-box">
                        <div class="stat-value" id="gcContent">0%</div>
                        <div class="stat-label">GC Content</div>
                    </div>
                    <div class="stat-box">
                        <div class="stat-value" id="atContent">0%</div>
                        <div class="stat-label">AT Content</div>
                    </div>
                </div>
                <div class="gc-bar">
                    <div class="gc-bar-fill" id="gcBar" style="width: 0%">
                        <span id="gcBarText"></span>
                    </div>
                </div>
            </div>

            <div class="result-card">
                <h3>🧵 Complementary Strand</h3>
                <div class="sequence-display" id="complement"></div>
            </div>

            <div class="result-card">
                <h3>🔄 Reverse Complement</h3>
                <div class="sequence-display" id="reverseComplement"></div>
            </div>
        </div>
    </div>

    <script>
        function analyzeDNA() {
            const input = document.getElementById('dnaInput').value.toUpperCase().trim();
            const errorDiv = document.getElementById('error');
            const resultsDiv = document.getElementById('results');

            errorDiv.style.display = 'none';

            if (!input) {
                showError('Please enter a DNA sequence');
                return;
            }

            const validNucleotides = /^[ATGC]+$/;
            if (!validNucleotides.test(input)) {
                showError('Invalid DNA sequence. Only A, T, G, C nucleotides are allowed.');
                return;
            }

            const counts = {
                A: (input.match(/A/g) || []).length,
                T: (input.match(/T/g) || []).length,
                G: (input.match(/G/g) || []).length,
                C: (input.match(/C/g) || []).length
            };

            const totalLength = input.length;
            const gcContent = ((counts.G + counts.C) / totalLength * 100).toFixed(1);
            const atContent = ((counts.A + counts.T) / totalLength * 100).toFixed(1);

            document.getElementById('countA').textContent = counts.A;
            document.getElementById('countT').textContent = counts.T;
            document.getElementById('countG').textContent = counts.G;
            document.getElementById('countC').textContent = counts.C;
            document.getElementById('totalLength').textContent = totalLength;
            document.getElementById('gcContent').textContent = gcContent + '%';
            document.getElementById('atContent').textContent = atContent + '%';

            const gcBar = document.getElementById('gcBar');
            gcBar.style.width = gcContent + '%';
            document.getElementById('gcBarText').textContent = gcContent + '%';

            const complement = getComplement(input);
            const reverseComplement = getReverseComplement(input);

            document.getElementById('complement').innerHTML = colorSequence(complement);
            document.getElementById('reverseComplement').innerHTML = colorSequence(reverseComplement);

            resultsDiv.style.display = 'block';
        }

        function getComplement(sequence) {
            const complementMap = { 'A': 'T', 'T': 'A', 'G': 'C', 'C': 'G' };
            return sequence.split('').map(base => complementMap[base]).join('');
        }

        function getReverseComplement(sequence) {
            return getComplement(sequence).split('').reverse().join('');
        }

        function colorSequence(sequence) {
            return sequence.split('').map(base => {
                const colorClass = `nucleotide-${base.toLowerCase()}`;
                return `<span class="${colorClass}">${base}</span>`;
            }).join('');
        }

        function generateRandom() {
            const nucleotides = ['A', 'T', 'G', 'C'];
            const length = Math.floor(Math.random() * 50) + 30;
            let sequence = '';
            
            for (let i = 0; i < length; i++) {
                sequence += nucleotides[Math.floor(Math.random() * 4)];
            }
            
            document.getElementById('dnaInput').value = sequence;
            analyzeDNA();
        }

        function clearAll() {
            document.getElementById('dnaInput').value = '';
            document.getElementById('results').style.display = 'none';
            document.getElementById('error').style.display = 'none';
        }

        function showError(message) {
            const errorDiv = document.getElementById('error');
            errorDiv.textContent = '⚠️ ' + message;
            errorDiv.style.display = 'block';
            document.getElementById('results').style.display = 'none';
        }
    </script>
</body>
</html>
