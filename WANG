<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>肺癌CT影像智能分析系统</title>
<style>
  :root {
    --bg: #0d1117;
    --surface: #161b22;
    --surface2: #1c2330;
    --border: #30363d;
    --primary: #58a6ff;
    --primary-dim: rgba(88,166,255,0.12);
    --danger: #f85149;
    --danger-dim: rgba(248,81,73,0.12);
    --warning: #e3b341;
    --warning-dim: rgba(227,179,65,0.1);
    --success: #3fb950;
    --success-dim: rgba(63,185,80,0.1);
    --text: #e6edf3;
    --text-muted: #8b949e;
    --text-dim: #484f58;
    --radius: 12px;
    --radius-sm: 8px;
    --mono: 'SF Mono', 'Consolas', 'Monaco', monospace;
  }

  * { margin:0; padding:0; box-sizing:border-box; }

  body {
    font-family: -apple-system, BlinkMacSystemFont, 'PingFang SC', 'Microsoft YaHei', 'Segoe UI', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    -webkit-font-smoothing: antialiased;
  }

  .topbar {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 14px 28px;
    border-bottom: 1px solid var(--border);
    background: var(--surface);
    position: sticky;
    top: 0;
    z-index: 100;
  }
  .topbar-logo {
    width: 34px; height: 34px;
    background: linear-gradient(135deg, #1f6feb, #388bfd);
    border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 18px;
    flex-shrink: 0;
    box-shadow: 0 0 16px rgba(56,139,253,0.35);
  }
  .topbar-title { font-size: 16px; font-weight: 700; letter-spacing: -0.3px; }
  .topbar-sub { font-size: 12px; color: var(--text-muted); margin-top: 1px; }
  .topbar-badge {
    margin-left: auto;
    font-size: 11px; font-weight: 600;
    padding: 3px 10px;
    border-radius: 20px;
    background: var(--primary-dim);
    color: var(--primary);
    border: 1px solid rgba(88,166,255,0.25);
    white-space: nowrap;
  }

  .layout {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 0;
    height: calc(100vh - 63px);
    overflow: hidden;
  }
  @media (max-width: 900px) {
    .layout { grid-template-columns: 1fr; height: auto; overflow: visible; }
    .panel-right { border-top: 1px solid var(--border); border-left: none !important; }
  }

  .panel { display: flex; flex-direction: column; overflow: hidden; }
  .panel-right { border-left: 1px solid var(--border); }
  .panel-header {
    padding: 16px 24px;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; gap: 10px;
    flex-shrink: 0;
    background: var(--surface);
  }
  .panel-header-icon {
    width: 28px; height: 28px;
    border-radius: 6px;
    display: flex; align-items: center; justify-content: center;
    font-size: 14px;
    flex-shrink: 0;
  }
  .icon-blue { background: var(--primary-dim); }
  .icon-red  { background: var(--danger-dim); }
  .panel-header-text { font-size: 14px; font-weight: 600; }
  .panel-body { flex: 1; overflow-y: auto; padding: 20px 24px; }

  .upload-zone {
    border: 2px dashed var(--border);
    border-radius: var(--radius);
    padding: 44px 20px;
    text-align: center;
    cursor: pointer;
    transition: all 0.2s;
    background: var(--surface2);
    position: relative;
  }
  .upload-zone:hover, .upload-zone.drag-over {
    border-color: var(--primary);
    background: var(--primary-dim);
  }
  .upload-zone input { position: absolute; inset: 0; opacity: 0; cursor: pointer; }
  .upload-icon-big { font-size: 48px; margin-bottom: 12px; opacity: 0.6; }
  .upload-zone:hover .upload-icon-big { opacity: 1; }
  .upload-label { font-size: 15px; font-weight: 600; color: var(--text); }
  .upload-hint { font-size: 12px; color: var(--text-muted); margin-top: 6px; }

  .image-frame {
    margin-top: 16px;
    border-radius: var(--radius);
    overflow: hidden;
    background: #000;
    min-height: 260px;
    display: flex; align-items: center; justify-content: center;
    position: relative;
  }
  .image-frame img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    display: block;
    border-radius: var(--radius);
  }
  .image-placeholder {
    text-align: center;
    color: var(--text-dim);
    padding: 60px 20px;
  }
  .image-placeholder span { font-size: 52px; display: block; margin-bottom: 10px; }
  .image-placeholder p { font-size: 13px; }

  .btn-row { display: flex; gap: 10px; flex-wrap: wrap; margin-top: 16px; }
  .btn {
    padding: 9px 20px;
    border-radius: 20px;
    border: none;
    cursor: pointer;
    font-size: 13px;
    font-weight: 600;
    font-family: inherit;
    transition: all 0.15s;
    display: flex; align-items: center; gap: 6px;
    white-space: nowrap;
  }
  .btn:active { transform: scale(0.96); }
  .btn-primary { background: var(--primary); color: #0d1117; box-shadow: 0 0 16px rgba(88,166,255,0.3); }
  .btn-primary:hover { background: #79b8ff; box-shadow: 0 0 24px rgba(88,166,255,0.45); }
  .btn-primary:disabled { opacity: 0.45; cursor: not-allowed; transform: none; }
  .btn-ghost { background: var(--surface2); color: var(--text-muted); border: 1px solid var(--border); }
  .btn-ghost:hover { color: var(--text); border-color: #555; }

  .analyzing-bar {
    display: none;
    align-items: center;
    gap: 12px;
    padding: 14px 18px;
    background: var(--primary-dim);
    border: 1px solid rgba(88,166,255,0.25);
    border-radius: var(--radius-sm);
    margin-top: 16px;
    font-size: 13px;
    color: var(--primary);
    font-weight: 500;
  }
  .analyzing-bar.active { display: flex; }
  .spinner {
    width: 16px; height: 16px;
    border: 2px solid rgba(88,166,255,0.3);
    border-top-color: var(--primary);
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
    flex-shrink: 0;
  }
  @keyframes spin { to { transform: rotate(360deg); } }

  .result-empty {
    text-align: center;
    padding: 80px 20px;
    color: var(--text-dim);
  }
  .result-empty span { font-size: 52px; display: block; margin-bottom: 12px; }
  .result-empty p { font-size: 13px; line-height: 1.7; }

  .ai-output { display: none; flex-direction: column; gap: 0; }
  .ai-output.visible { display: flex; }

  .risk-banner {
    border-radius: var(--radius);
    padding: 18px 20px;
    margin-bottom: 20px;
    border: 1px solid;
  }
  .risk-banner.high { background: var(--danger-dim); border-color: rgba(248,81,73,0.3); }
  .risk-banner.medium { background: var(--warning-dim); border-color: rgba(227,179,65,0.25); }
  .risk-banner.low { background: var(--success-dim); border-color: rgba(63,185,80,0.2); }
  .risk-banner.unknown { background: var(--surface2); border-color: var(--border); }
  .risk-level { font-size: 11px; font-weight: 700; letter-spacing: 1px; text-transform: uppercase; margin-bottom: 4px; }
  .risk-banner.high .risk-level { color: var(--danger); }
  .risk-banner.medium .risk-level { color: var(--warning); }
  .risk-banner.low .risk-level { color: var(--success); }
  .risk-banner.unknown .risk-level { color: var(--text-muted); }
  .risk-summary { font-size: 14px; font-weight: 600; line-height: 1.5; }

  .analysis-section { margin-bottom: 20px; }
  .section-label {
    font-size: 11px; font-weight: 700; letter-spacing: 0.8px;
    text-transform: uppercase; color: var(--text-muted);
    margin-bottom: 10px;
    display: flex; align-items: center; gap: 8px;
  }
  .section-label::after { content: ''; flex: 1; height: 1px; background: var(--border); }

  .finding-card {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 14px 16px;
    margin-bottom: 10px;
    border-left: 3px solid;
    font-size: 13px;
    line-height: 1.65;
    color: var(--text);
  }
  .finding-card.high { border-left-color: var(--danger); }
  .finding-card.medium { border-left-color: var(--warning); }
  .finding-card.low { border-left-color: var(--success); }
  .finding-card.info { border-left-color: var(--primary); }
  .finding-title {
    font-weight: 700; font-size: 13px; margin-bottom: 5px;
    display: flex; align-items: center; justify-content: space-between;
  }
  .finding-tag {
    font-size: 10px; font-weight: 700; letter-spacing: 0.5px;
    padding: 2px 8px; border-radius: 10px;
  }
  .finding-card.high .finding-tag { background: var(--danger-dim); color: var(--danger); }
  .finding-card.medium .finding-tag { background: var(--warning-dim); color: var(--warning); }
  .finding-card.low .finding-tag { background: var(--success-dim); color: var(--success); }
  .finding-card.info .finding-tag { background: var(--primary-dim); color: var(--primary); }

  .rec-list { list-style: none; display: flex; flex-direction: column; gap: 8px; }
  .rec-list li {
    display: flex; gap: 10px; align-items: flex-start;
    font-size: 13px; line-height: 1.55; color: var(--text);
  }
  .rec-list li::before { content: '→'; color: var(--primary); font-weight: 700; flex-shrink: 0; margin-top: 1px; }

  .raw-text-block {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 16px;
    font-size: 13px;
    line-height: 1.75;
    color: var(--text);
    white-space: pre-wrap;
    word-break: break-word;
  }

  .disclaimer {
    margin-top: 20px;
    padding: 12px 16px;
    background: rgba(227,179,65,0.07);
    border: 1px solid rgba(227,179,65,0.2);
    border-radius: var(--radius-sm);
    font-size: 11px;
    color: #9e8035;
    line-height: 1.6;
  }
  .disclaimer strong { color: var(--warning); }

  .progress-track {
    height: 2px; background: var(--border); border-radius: 2px;
    margin-top: 8px; overflow: hidden; display: none;
  }
  .progress-track.visible { display: block; }
  .progress-bar {
    height: 100%;
    background: linear-gradient(90deg, var(--primary), #79b8ff);
    border-radius: 2px; width: 0%; transition: width 0.3s ease;
  }

  .panel-body::-webkit-scrollbar { width: 5px; }
  .panel-body::-webkit-scrollbar-track { background: transparent; }
  .panel-body::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }
</style>
</head>
<body>

<div class="topbar">
  <div class="topbar-logo">🫁</div>
  <div>
    <div class="topbar-title">肺癌 CT 影像智能分析系统</div>
    <div class="topbar-sub">真实 AI · 上传即分析 · 结果因图而异</div>
  </div>
  <div class="topbar-badge">🤖 Claude AI 驱动</div>
</div>

<div class="layout">
  <div class="panel">
    <div class="panel-header">
      <div class="panel-header-icon icon-blue">📋</div>
      <div class="panel-header-text">上传 CT 影像</div>
    </div>
    <div class="panel-body">
      <div class="upload-zone" id="uploadZone">
        <div class="upload-icon-big">🖼️</div>
        <div class="upload-label">点击或拖拽上传 CT 影像</div>
        <div class="upload-hint">支持 JPG · PNG · DICOM截图 · WEBP</div>
        <input type="file" id="fileInput" accept="image/*">
      </div>
      <div class="image-frame" id="imageFrame">
        <div class="image-placeholder" id="imgPlaceholder">
          <span>🫁</span>
          <p>上传 CT 图片后在此预览</p>
        </div>
        <img id="previewImg" style="display:none;" alt="CT影像预览">
      </div>
      <div class="progress-track" id="progressTrack">
        <div class="progress-bar" id="progressBar"></div>
      </div>
      <div class="analyzing-bar" id="analyzingBar">
        <div class="spinner"></div>
        <span id="analyzingText">Claude AI 正在读取影像，请稍候…</span>
      </div>
      <div class="btn-row">
        <button class="btn btn-primary" id="btnAnalyze" disabled onclick="startAnalysis()">
          🔍 开始 AI 分析
        </button>
        <button class="btn btn-ghost" id="btnReset" style="display:none" onclick="resetAll()">
          🔄 重新上传
        </button>
      </div>
    </div>
  </div>

  <div class="panel panel-right">
    <div class="panel-header">
      <div class="panel-header-icon icon-red">📊</div>
      <div class="panel-header-text">AI 分析报告</div>
    </div>
    <div class="panel-body">
      <div class="result-empty" id="resultEmpty">
        <span>🔬</span>
        <p>上传 CT 影像并点击「开始 AI 分析」<br>Claude 将真实读取您的图片并生成报告</p>
      </div>
      <div class="ai-output" id="aiOutput">
        <div class="risk-banner unknown" id="riskBanner">
          <div class="risk-level" id="riskLevel">分析中…</div>
          <div class="risk-summary" id="riskSummary">正在读取影像内容…</div>
        </div>
        <div class="analysis-section" id="findingsSection" style="display:none">
          <div class="section-label">影像发现</div>
          <div id="findingsList"></div>
        </div>
        <div class="analysis-section">
          <div class="section-label">完整分析内容</div>
          <div class="raw-text-block" id="rawText"></div>
        </div>
        <div class="analysis-section" id="recSection" style="display:none">
          <div class="section-label">临床建议</div>
          <ul class="rec-list" id="recList"></ul>
        </div>
        <div class="disclaimer">
          ⚠️ <strong>重要说明：</strong>本报告由 Claude AI 基于您上传的图片内容生成，分析结果因图片而异，非固定模板。但 AI 不能替代持牌放射科医师出具的正式 CT 报告。如有健康疑虑，请务必前往医院就诊。
        </div>
      </div>
    </div>
  </div>
</div>

<script>
const fileInput = document.getElementById('fileInput');
const uploadZone = document.getElementById('uploadZone');
const previewImg = document.getElementById('previewImg');
const imgPlaceholder = document.getElementById('imgPlaceholder');
const btnAnalyze = document.getElementById('btnAnalyze');
const btnReset = document.getElementById('btnReset');
const analyzingBar = document.getElementById('analyzingBar');
const analyzingText = document.getElementById('analyzingText');
const progressTrack = document.getElementById('progressTrack');
const progressBar = document.getElementById('progressBar');
const resultEmpty = document.getElementById('resultEmpty');
const aiOutput = document.getElementById('aiOutput');
const riskBanner = document.getElementById('riskBanner');
const riskLevel = document.getElementById('riskLevel');
const riskSummary = document.getElementById('riskSummary');
const rawText = document.getElementById('rawText');
const findingsSection = document.getElementById('findingsSection');
const findingsList = document.getElementById('findingsList');
const recSection = document.getElementById('recSection');
const recList = document.getElementById('recList');

let currentBase64 = null;
let currentMimeType = 'image/jpeg';

uploadZone.addEventListener('click', e => { if (e.target !== fileInput) fileInput.click(); });
uploadZone.addEventListener('dragover', e => { e.preventDefault(); uploadZone.classList.add('drag-over'); });
uploadZone.addEventListener('dragleave', () => uploadZone.classList.remove('drag-over'));
uploadZone.addEventListener('drop', e => {
  e.preventDefault(); uploadZone.classList.remove('drag-over');
  if (e.dataTransfer.files[0]) handleFile(e.dataTransfer.files[0]);
});
fileInput.addEventListener('change', () => { if (fileInput.files[0]) handleFile(fileInput.files[0]); });

function handleFile(file) {
  if (!file.type.match(/image\//)) return alert('请上传图片文件');
  currentMimeType = file.type || 'image/jpeg';
  const reader = new FileReader();
  reader.onload = e => {
    const dataUrl = e.target.result;
    currentBase64 = dataUrl.split(',')[1];
    previewImg.src = dataUrl;
    previewImg.style.display = 'block';
    imgPlaceholder.style.display = 'none';
    btnAnalyze.disabled = false;
    btnReset.style.display = 'inline-flex';
    resetResult();
  };
  reader.readAsDataURL(file);
}

function resetAll() {
  currentBase64 = null;
  previewImg.style.display = 'none';
  previewImg.src = '';
  imgPlaceholder.style.display = 'block';
  btnAnalyze.disabled = true;
  btnReset.style.display = 'none';
  fileInput.value = '';
  resetResult();
}
function resetResult() {
  analyzingBar.classList.remove('active');
  progressTrack.classList.remove('visible');
  progressBar.style.width = '0%';
  resultEmpty.style.display = 'block';
  aiOutput.classList.remove('visible');
  rawText.textContent = '';
  findingsList.innerHTML = '';
  recList.innerHTML = '';
  findingsSection.style.display = 'none';
  recSection.style.display = 'none';
}

let progressInterval = null;
function startProgress() {
  progressTrack.classList.add('visible');
  let pct = 0;
  progressInterval = setInterval(() => {
    pct += Math.random() * 4;
    if (pct > 88) pct = 88;
    progressBar.style.width = pct + '%';
  }, 300);
}
function finishProgress() {
  clearInterval(progressInterval);
  progressBar.style.width = '100%';
  setTimeout(() => { progressTrack.classList.remove('visible'); progressBar.style.width = '0%'; }, 800);
}

async function startAnalysis() {
  if (!currentBase64) return;
  btnAnalyze.disabled = true;
  analyzingBar.classList.add('active');
  analyzingText.textContent = 'Claude AI 正在读取影像内容，请稍候…';
  startProgress();
  resultEmpty.style.display = 'none';
  aiOutput.classList.add('visible');
  riskBanner.className = 'risk-banner unknown';
  riskLevel.textContent = '分析中…';
  riskSummary.textContent = 'AI 正在解读您的 CT 影像…';
  rawText.textContent = '';

  const systemPrompt = `你是一位经验丰富的放射科医师 AI 助手，专门分析肺部 CT 影像。
用户会上传一张 CT 图片，请你：
1. 认真观察图片实际内容（肺部结构、密度、结节、阴影等）
2. 根据你真实看到的内容进行描述和分析，不要套用固定模板
3. 如果图片不是 CT 影像或图片模糊无法判断，请明确说明
4. 如果是正常的胸部 CT，也要如实说明正常
5. 用中文输出，结构清晰

输出格式（JSON）：
{
  "riskLevel": "high" | "medium" | "low" | "normal" | "unclear",
  "riskSummary": "一句话总结（20字以内）",
  "findings": [
    {
      "level": "high" | "medium" | "low" | "info",
      "title": "发现名称",
      "detail": "具体描述（位置、大小、特征等）"
    }
  ],
  "fullReport": "完整的放射科风格报告（中文，3-8句话，描述你实际看到的内容）",
  "recommendations": ["建议1", "建议2", "建议3"]
}

重要：你的分析必须基于你实际看到的图片内容，不同的图片应给出不同的结果。`;

  try {
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        system: systemPrompt,
        messages: [{
          role: 'user',
          content: [
            {
              type: 'image',
              source: { type: 'base64', media_type: currentMimeType, data: currentBase64 }
            },
            {
              type: 'text',
              text: '请分析这张CT影像，根据你实际看到的图像内容给出报告。只返回JSON，不要加任何说明文字或代码块标记。'
            }
          ]
        }]
      })
    });

    if (!response.ok) {
      const err = await response.json().catch(() => ({}));
      throw new Error(err.error?.message || `HTTP ${response.status}`);
    }

    const data = await response.json();
    const rawContent = data.content?.find(b => b.type === 'text')?.text || '';

    let cleaned = rawContent.trim();
    cleaned = cleaned.replace(/^```json\s*/i, '').replace(/^```\s*/i, '').replace(/```\s*$/i, '').trim();

    let result;
    try {
      result = JSON.parse(cleaned);
    } catch {
      result = {
        riskLevel: 'unclear',
        riskSummary: '结果已生成，请查看完整报告',
        findings: [],
        fullReport: rawContent,
        recommendations: ['建议将本报告带给专业医师进一步解读']
      };
    }

    renderResult(result);
  } catch (err) {
    renderError(err.message);
  } finally {
    finishProgress();
    analyzingBar.classList.remove('active');
    btnAnalyze.disabled = false;
  }
}

const riskMap = {
  high:    { cls: 'high',    label: '⚠️ 高风险 — 需立即就医' },
  medium:  { cls: 'medium',  label: '🔶 中风险 — 建议尽快随访' },
  low:     { cls: 'low',     label: '🟡 低风险 — 建议定期随访' },
  normal:  { cls: 'low',     label: '✅ 未见明显异常' },
  unclear: { cls: 'unknown', label: '❓ 影像不明确' },
};

function renderResult(r) {
  const risk = riskMap[r.riskLevel] || riskMap['unclear'];
  riskBanner.className = 'risk-banner ' + risk.cls;
  riskLevel.textContent = risk.label;
  riskSummary.textContent = r.riskSummary || '';
  rawText.textContent = r.fullReport || '（无详细报告）';

  if (r.findings && r.findings.length > 0) {
    findingsSection.style.display = 'block';
    findingsList.innerHTML = '';
    r.findings.forEach(f => {
      const tagLabel = { high:'高风险', medium:'中风险', low:'低风险', info:'信息' }[f.level] || f.level;
      findingsList.innerHTML += `
        <div class="finding-card ${f.level || 'info'}">
          <div class="finding-title">
            ${f.title}
            <span class="finding-tag">${tagLabel}</span>
          </div>
          <div>${f.detail}</div>
        </div>`;
    });
  }

  if (r.recommendations && r.recommendations.length > 0) {
    recSection.style.display = 'block';
    recList.innerHTML = r.recommendations.map(rec => `<li>${rec}</li>`).join('');
  }
}

function renderError(msg) {
  riskBanner.className = 'risk-banner unknown';
  riskLevel.textContent = '❌ 分析失败';
  riskSummary.textContent = '请检查网络连接后重试';
  rawText.textContent = '错误详情：' + msg;
}
</script>
</body>
</html>
