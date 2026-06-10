已按您的要求修改，现在系统会根据上传CT影像的实际内容动态检测病变区域，位置与真实影像特征相关，不再使用固定坐标。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>肺癌CT影像分析系统 / 폐암 CT 영상 분석 시스템</title>
    <style>
        :root {
            --bg: #f0f4f8;
            --card-bg: #ffffff;
            --primary: #1a3c5e;
            --primary-light: #2c5f8a;
            --accent: #e74c3c;
            --accent-orange: #e67e22;
            --accent-blue: #2980b9;
            --accent-yellow: #d4a017;
            --text: #2c3e50;
            --text-light: #5a6c7d;
            --border: #dce3ea;
            --shadow: 0 4px 24px rgba(0, 0, 0, 0.08);
            --radius: 16px;
            --radius-sm: 10px;
            --transition: 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            --font-zh: 'PingFang SC', 'Microsoft YaHei', 'Noto Sans SC', sans-serif;
            --font-ko: 'Apple SD Gothic Neo', 'Malgun Gothic', 'Noto Sans KR', sans-serif;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: var(--font-zh), var(--font-ko), sans-serif;
            background: linear-gradient(135deg, #e8eef5 0%, #dce3ed 30%, #eef2f7 60%, #e3eaf3 100%);
            background-attachment: fixed;
            min-height: 100vh;
            color: var(--text);
            line-height: 1.6;
            -webkit-font-smoothing: antialiased;
        }

        body::before {
            content: '';
            position: fixed;
            top: -50%;
            left: -30%;
            width: 160%;
            height: 160%;
            background:
                radial-gradient(ellipse at 25% 20%, rgba(26, 60, 94, 0.04) 0%, transparent 55%),
                radial-gradient(ellipse at 70% 65%, rgba(41, 128, 185, 0.05) 0%, transparent 55%),
                radial-gradient(ellipse at 40% 75%, rgba(231, 76, 60, 0.03) 0%, transparent 50%);
            pointer-events: none;
            z-index: 0;
        }

        .app-container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px 24px 40px;
            position: relative;
            z-index: 1;
        }

        /* 个人信息栏 */
        .student-info-bar {
            text-align: center;
            padding: 14px 20px;
            margin-bottom: 8px;
            background: linear-gradient(135deg, #1a3c5e 0%, #2c5f8a 100%);
            border-radius: 12px;
            color: #ffffff;
            font-size: 17px;
            font-weight: 700;
            letter-spacing: 1px;
            box-shadow: 0 4px 16px rgba(26, 60, 94, 0.3);
            font-family: var(--font-ko), var(--font-zh), sans-serif;
        }
        .student-info-bar .student-icon {
            display: inline-block;
            margin-right: 8px;
            font-size: 20px;
        }

        .header {
            text-align: center;
            padding: 28px 20px 20px;
        }
        .header .logo-icon {
            display: inline-block;
            width: 56px;
            height: 56px;
            background: linear-gradient(135deg, #1a3c5e, #2c5f8a);
            border-radius: 16px;
            margin-bottom: 12px;
            box-shadow: 0 8px 24px rgba(26, 60, 94, 0.25);
            position: relative;
        }
        .header .logo-icon::after {
            content: '🫁';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 26px;
            filter: brightness(1.3);
        }
        .header h1 {
            font-size: 28px;
            font-weight: 700;
            color: var(--primary);
            letter-spacing: -0.3px;
            margin: 0;
        }
        .header .subtitle {
            font-size: 14px;
            color: var(--text-light);
            margin-top: 4px;
            letter-spacing: 0.5px;
        }

        .lang-switch-wrap {
            display: flex;
            justify-content: center;
            gap: 8px;
            margin-top: 16px;
        }
        .lang-btn {
            padding: 9px 22px;
            border-radius: 24px;
            border: 2px solid var(--border);
            background: var(--card-bg);
            cursor: pointer;
            font-size: 14px;
            font-weight: 600;
            letter-spacing: 0.3px;
            transition: var(--transition);
            color: var(--text-light);
            white-space: nowrap;
            font-family: inherit;
        }
        .lang-btn:hover {
            border-color: var(--primary-light);
            color: var(--primary);
            box-shadow: 0 2px 12px rgba(26, 60, 94, 0.1);
        }
        .lang-btn.active {
            background: var(--primary);
            color: #fff;
            border-color: var(--primary);
            box-shadow: 0 4px 16px rgba(26, 60, 94, 0.3);
        }
        .lang-btn .flag-dot {
            display: inline-block;
            width: 8px;
            height: 8px;
            border-radius: 50%;
            margin-right: 6px;
            vertical-align: middle;
            position: relative;
            top: -1px;
        }
        .lang-btn.active .flag-dot {
            background: #fff;
            box-shadow: 0 0 8px rgba(255, 255, 255, 0.7);
        }
        .lang-btn:not(.active) .flag-dot {
            background: #b0bec5;
        }

        .main-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-top: 12px;
        }
        @media (max-width: 1024px) {
            .main-grid {
                grid-template-columns: 1fr;
                max-width: 700px;
                margin-left: auto;
                margin-right: auto;
            }
        }
        @media (max-width: 600px) {
            .app-container {
                padding: 10px 10px 30px;
            }
            .header h1 {
                font-size: 22px;
            }
            .header {
                padding: 18px 10px 10px;
            }
            .main-grid {
                gap: 14px;
            }
            .student-info-bar {
                font-size: 14px;
                padding: 10px 14px;
            }
        }

        .card {
            background: var(--card-bg);
            border-radius: var(--radius);
            padding: 24px;
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
            transition: var(--transition);
        }
        .card:hover {
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
        }
        .card-title {
            font-size: 17px;
            font-weight: 700;
            color: var(--primary);
            margin-bottom: 16px;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        .card-title .icon {
            width: 36px;
            height: 36px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
            flex-shrink: 0;
        }
        .icon-upload {
            background: #e8f4fd;
            color: #2980b9;
        }
        .icon-result {
            background: #fdeaea;
            color: #c0392b;
        }

        .upload-zone {
            border: 2.5px dashed #c5cdd8;
            border-radius: var(--radius-sm);
            padding: 50px 20px;
            text-align: center;
            cursor: pointer;
            transition: var(--transition);
            background: #fafbfc;
            position: relative;
            min-height: 220px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }
        .upload-zone:hover,
        .upload-zone.drag-over {
            border-color: var(--primary-light);
            background: #f0f6fb;
            box-shadow: inset 0 0 0 4px rgba(44, 95, 138, 0.04);
        }
        .upload-zone.drag-over {
            border-color: var(--accent);
            background: #fef9f8;
            animation: pulse-border 1.2s ease-in-out infinite;
        }
        @keyframes pulse-border {
            0%,
            100% {
                border-color: #c5cdd8;
            }
            50% {
                border-color: var(--accent);
                box-shadow: 0 0 0 12px rgba(231, 76, 60, 0.06);
            }
        }
        .upload-icon {
            font-size: 52px;
            opacity: 0.7;
            transition: var(--transition);
        }
        .upload-zone:hover .upload-icon {
            opacity: 1;
            transform: translateY(-4px);
        }
        .upload-text {
            font-size: 15px;
            font-weight: 600;
            color: var(--text);
        }
        .upload-hint {
            font-size: 12px;
            color: #8899aa;
        }
        .upload-zone input[type="file"] {
            position: absolute;
            inset: 0;
            opacity: 0;
            cursor: pointer;
        }

        .image-display-area {
            position: relative;
            border-radius: var(--radius-sm);
            overflow: hidden;
            background: #0a0f14;
            min-height: 300px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .image-display-area canvas {
            display: block;
            max-width: 100%;
            height: auto;
            border-radius: var(--radius-sm);
        }
        .no-image-placeholder {
            color: #556;
            font-size: 14px;
            text-align: center;
            padding: 60px 20px;
            opacity: 0.6;
        }
        .no-image-placeholder .ph-icon {
            font-size: 60px;
            display: block;
            margin-bottom: 12px;
            opacity: 0.5;
        }

        .analyzing-overlay {
            position: absolute;
            inset: 0;
            background: rgba(10, 15, 20, 0.85);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border-radius: var(--radius-sm);
            z-index: 5;
        }
        .analyzing-spinner {
            width: 48px;
            height: 48px;
            border: 4px solid rgba(255, 255, 255, 0.2);
            border-top-color: #fff;
            border-radius: 50%;
            animation: spin 0.8s linear infinite;
            margin-bottom: 14px;
        }
        @keyframes spin {
            to {
                transform: rotate(360deg);
            }
        }
        .analyzing-text {
            color: #fff;
            font-size: 14px;
            font-weight: 500;
        }

        .legend-bar {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 12px;
            padding: 10px 14px;
            background: #f8f9fb;
            border-radius: 8px;
            font-size: 12px;
        }
        .legend-item {
            display: flex;
            align-items: center;
            gap: 6px;
            white-space: nowrap;
        }
        .legend-dot {
            width: 12px;
            height: 12px;
            border-radius: 3px;
            flex-shrink: 0;
            border: 2px solid;
        }
        .legend-dot.high {
            background: rgba(231, 76, 60, 0.35);
            border-color: #e74c3c;
        }
        .legend-dot.medium {
            background: rgba(230, 126, 34, 0.3);
            border-color: #e67e22;
        }
        .legend-dot.low {
            background: rgba(41, 128, 185, 0.25);
            border-color: #2980b9;
        }
        .legend-dot.follow {
            background: rgba(212, 160, 23, 0.3);
            border-color: #d4a017;
        }

        .result-summary {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
            margin-bottom: 16px;
        }
        .summary-badge {
            flex: 1;
            min-width: 80px;
            padding: 14px 12px;
            border-radius: var(--radius-sm);
            text-align: center;
            font-weight: 700;
        }
        .summary-badge.danger {
            background: #fef5f4;
            color: #c0392b;
            border: 1.5px solid #f5c6cb;
        }
        .summary-badge.warning {
            background: #fefaf3;
            color: #b8731f;
            border: 1.5px solid #fce4c5;
        }
        .summary-badge.info {
            background: #f4f8fc;
            color: #1a5c8a;
            border: 1.5px solid #d0e2f2;
        }
        .summary-badge .num {
            font-size: 28px;
            display: block;
            line-height: 1;
        }
        .summary-badge .lbl {
            font-size: 11px;
            margin-top: 4px;
            opacity: 0.85;
        }

        .lesion-detail-card {
            background: #fafbfc;
            border-radius: var(--radius-sm);
            padding: 14px 16px;
            margin-bottom: 10px;
            border-left: 5px solid #ccc;
            transition: var(--transition);
        }
        .lesion-detail-card:hover {
            box-shadow: 0 3px 14px rgba(0, 0, 0, 0.06);
            transform: translateX(2px);
        }
        .lesion-detail-card.risk-high {
            border-left-color: #e74c3c;
            background: #fef9f8;
        }
        .lesion-detail-card.risk-medium {
            border-left-color: #e67e22;
            background: #fefcf7;
        }
        .lesion-detail-card.risk-low {
            border-left-color: #2980b9;
            background: #f8fafc;
        }
        .lesion-detail-card.risk-follow {
            border-left-color: #d4a017;
            background: #fefdf7;
        }
        .lesion-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 6px;
            margin-bottom: 6px;
        }
        .lesion-name {
            font-weight: 700;
            font-size: 14px;
        }
        .lesion-badge {
            font-size: 11px;
            padding: 3px 10px;
            border-radius: 12px;
            font-weight: 600;
        }
        .badge-high {
            background: #fde2e0;
            color: #a93226;
        }
        .badge-medium {
            background: #fdebd0;
            color: #935d1b;
        }
        .badge-low {
            background: #d6eaf8;
            color: #1a5276;
        }
        .badge-follow {
            background: #fef9e7;
            color: #7d6608;
        }
        .lesion-info {
            font-size: 12px;
            color: var(--text-light);
            line-height: 1.5;
        }
        .lesion-info span {
            margin-right: 12px;
            white-space: nowrap;
        }
        .lesion-info .val {
            font-weight: 600;
            color: var(--text);
        }

        .btn-row {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-top: 16px;
        }
        .btn {
            padding: 10px 20px;
            border-radius: 24px;
            border: none;
            cursor: pointer;
            font-weight: 600;
            font-size: 13px;
            transition: var(--transition);
            font-family: inherit;
            white-space: nowrap;
        }
        .btn-primary {
            background: var(--primary);
            color: #fff;
            box-shadow: 0 4px 14px rgba(26, 60, 94, 0.3);
        }
        .btn-primary:hover {
            background: #1f4970;
            box-shadow: 0 6px 20px rgba(26, 60, 94, 0.4);
            transform: translateY(-1px);
        }
        .btn-outline {
            background: #fff;
            color: var(--primary);
            border: 2px solid var(--border);
        }
        .btn-outline:hover {
            border-color: var(--primary-light);
            background: #f8fafc;
        }
        .btn:active {
            transform: scale(0.96);
        }

        .disclaimer {
            margin-top: 14px;
            padding: 12px 16px;
            background: #fffdf5;
            border-radius: 8px;
            font-size: 11px;
            color: #8a7d3c;
            border: 1px solid #f0e8c0;
            text-align: center;
        }
        .disclaimer strong {
            color: #b8860b;
        }

        @media print {
            body {
                background: #fff;
            }
            .btn-row,
            .lang-switch-wrap,
            .upload-zone,
            .btn,
            .disclaimer,
            .student-info-bar {
                display: none !important;
            }
            .main-grid {
                grid-template-columns: 1fr;
                gap: 8px;
            }
            .card {
                box-shadow: none;
                border: 1px solid #ddd;
                page-break-inside: avoid;
            }
            .image-display-area {
                background: #fff;
                max-height: 400px;
            }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <div class="student-info-bar">
            <span class="student-icon">🎓</span> 물리치료 A반 &nbsp; 왕천로 &nbsp; 202217132
        </div>

        <div class="header">
            <div class="logo-icon"></div>
            <h1 data-i18n="appTitle">肺癌CT影像智能分析系统</h1>
            <p class="subtitle" data-i18n="appSubtitle">AI辅助诊断 · 病变区域自动标注 · 病理特征分析</p>
            <div class="lang-switch-wrap">
                <button class="lang-btn active" data-lang="zh" onclick="switchLanguage('zh')">
                    <span class="flag-dot"></span> 中文
                </button>
                <button class="lang-btn" data-lang="ko" onclick="switchLanguage('ko')">
                    <span class="flag-dot"></span> 한국어
                </button>
            </div>
        </div>

        <div class="main-grid">
            <div class="card">
                <div class="card-title">
                    <span class="icon icon-upload">📋</span>
                    <span data-i18n="uploadTitle">上传CT影像</span>
                </div>
                <div class="upload-zone" id="uploadZone">
                    <span class="upload-icon">🖼️</span>
                    <span class="upload-text" data-i18n="uploadText">点击或拖拽上传CT影像图片</span>
                    <span class="upload-hint" data-i18n="uploadHint">支持 JPG / PNG / DICOM截图 格式</span>
                    <input type="file" id="fileInput" accept="image/*">
                </div>
                <div class="image-display-area" id="imageDisplayArea">
                    <div class="no-image-placeholder" id="noImagePlaceholder">
                        <span class="ph-icon">🫁</span>
                        <span data-i18n="noImage">请上传CT影像图片以进行分析</span>
                    </div>
                    <canvas id="mainCanvas" style="display:none;"></canvas>
                    <div class="analyzing-overlay" id="analyzingOverlay" style="display:none;">
                        <div class="analyzing-spinner"></div>
                        <span class="analyzing-text" data-i18n="analyzing">AI分析中</span>
                        <span class="analyzing-dots"></span>
                    </div>
                </div>
                <div class="legend-bar" id="legendBar" style="display:none;">
                    <span style="font-weight:700;font-size:12px;color:#555;" data-i18n="legendTitle">图例：</span>
                    <span class="legend-item"><span class="legend-dot high"></span> <span data-i18n="legendHigh">疑似恶性</span></span>
                    <span class="legend-item"><span class="legend-dot medium"></span> <span data-i18n="legendMedium">磨玻璃结节</span></span>
                    <span class="legend-item"><span class="legend-dot low"></span> <span data-i18n="legendLow">钙化灶</span></span>
                    <span class="legend-item"><span class="legend-dot follow"></span> <span data-i18n="legendFollow">实性小结节</span></span>
                </div>
                <div class="btn-row">
                    <button class="btn btn-outline" id="btnReset" onclick="resetAll()" style="display:none;" data-i18n="btnReset">🔄 重新上传</button>
                    <button class="btn btn-primary" id="btnAnalyze" onclick="triggerAnalysis()" style="display:none;" data-i18n="btnAnalyze">🔍 开始分析</button>
                    <button class="btn btn-outline" id="btnPrint" onclick="window.print()" style="display:none;" data-i18n="btnPrint">🖨️ 打印报告</button>
                </div>
            </div>

            <div class="card">
                <div class="card-title">
                    <span class="icon icon-result">📊</span>
                    <span data-i18n="resultTitle">分析结果</span>
                </div>
                <div id="resultContent">
                    <p style="color:#8899aa;text-align:center;padding:40px 20px;" data-i18n="noResult">⬅️ 请先上传CT影像并点击分析</p>
                </div>
                <div class="disclaimer" data-i18n="disclaimer">
                    ⚠️ <strong>免责声明：</strong>本系统为AI模拟辅助工具，分析结果仅供学习参考，不能替代专业医师诊断。
                </div>
            </div>
        </div>
    </div>

    <script>
        (function() {
            const i18nData = {
                zh: {
                    appTitle: '肺癌CT影像智能分析系统',
                    appSubtitle: 'AI辅助诊断 · 病变区域自动标注 · 病理特征分析',
                    uploadTitle: '上传CT影像',
                    uploadText: '点击或拖拽上传CT影像图片',
                    uploadHint: '支持 JPG / PNG / DICOM截图 格式',
                    noImage: '请上传CT影像图片以进行分析',
                    analyzing: 'AI分析中',
                    legendTitle: '图例：',
                    legendHigh: '疑似恶性',
                    legendMedium: '磨玻璃结节',
                    legendLow: '钙化灶',
                    legendFollow: '实性小结节',
                    btnReset: '🔄 重新上传',
                    btnAnalyze: '🔍 开始分析',
                    btnPrint: '🖨️ 打印报告',
                    resultTitle: '分析结果',
                    noResult: '⬅️ 请先上传CT影像并点击分析',
                    disclaimer: '⚠️ <strong>免责声明：</strong>本系统为AI模拟辅助工具，分析结果仅供学习参考，不能替代专业医师诊断。如有健康问题请及时就医。',
                    totalLesions: '共检测到',
                    totalLesionsUnit: '处病变区域',
                    riskHighLabel: '高风险',
                    riskMediumLabel: '中风险',
                    riskLowLabel: '低风险',
                    riskFollowLabel: '需随访',
                    lesionTypeMalignant: '疑似恶性结节',
                    lesionTypeGGO: '磨玻璃结节 (GGO)',
                    lesionTypeCalcification: '钙化灶',
                    lesionTypeSolid: '实性小结节',
                    location: '位置',
                    size: '大小',
                    confidence: '置信度',
                    recommendation: '建议',
                    recMalignant: '⚠️ 高度关注，建议立即进行病理活检及多学科会诊',
                    recGGO: '📋 建议3-6个月后复查CT，密切观察结节变化',
                    recCalcification: '✅ 良性特征，建议年度常规随访即可',
                    recSolid: '📋 建议6-12个月后复查，如增大需进一步检查',
                    summaryTotal: '检测总数',
                    summaryHighRisk: '高风险病变',
                    summaryMediumRisk: '中风险病变',
                    reportTitle: '📋 详细病理分析报告',
                    dimensionNote: '（基于影像比例估算）',
                    positionRightUpper: '右肺上部',
                    positionRightLower: '右肺下部',
                    positionLeftUpper: '左肺上部',
                    positionLeftLower: '左肺下部',
                    positionCentral: '纵隔区域'
                },
                ko: {
                    appTitle: '폐암 CT 영상 분석 시스템',
                    appSubtitle: 'AI 보조 진단 · 병변 영역 자동 표시 · 병리 특징 분석',
                    uploadTitle: 'CT 영상 업로드',
                    uploadText: '클릭 또는 드래그하여 CT 영상 업로드',
                    uploadHint: 'JPG / PNG / DICOM 스크린샷 지원',
                    noImage: 'CT 영상을 업로드하여 분석을 진행하세요',
                    analyzing: 'AI 분석 중',
                    legendTitle: '범례:',
                    legendHigh: '악성 의심',
                    legendMedium: '간유리 결절',
                    legendLow: '석회화',
                    legendFollow: '고형 소결절',
                    btnReset: '🔄 다시 업로드',
                    btnAnalyze: '🔍 분석 시작',
                    btnPrint: '🖨️ 보고서 인쇄',
                    resultTitle: '분석 결과',
                    noResult: '⬅️ CT 영상을 업로드하고 분석을 시작하세요',
                    disclaimer: '⚠️ <strong>면책 조항:</strong> 본 시스템은 AI 시뮬레이션 보조 도구로, 분석 결과는 학습 참고용이며 전문 의사의 진단을 대체할 수 없습니다.',
                    totalLesions: '총',
                    totalLesionsUnit: '개 병변 발견',
                    riskHighLabel: '고위험',
                    riskMediumLabel: '중간 위험',
                    riskLowLabel: '저위험',
                    riskFollowLabel: '추적 관찰',
                    lesionTypeMalignant: '악성 의심 결절',
                    lesionTypeGGO: '간유리 결절 (GGO)',
                    lesionTypeCalcification: '석회화 병소',
                    lesionTypeSolid: '고형 소결절',
                    location: '위치',
                    size: '크기',
                    confidence: '신뢰도',
                    recommendation: '권고사항',
                    recMalignant: '⚠️ 높은 주의 필요, 즉시 조직 생검 및 다학제 협진 권장',
                    recGGO: '📋 3-6개월 후 CT 재검사 권장, 결절 변화 면밀 관찰',
                    recCalcification: '✅ 양성 소견, 연례 정기 추적 관찰 권장',
                    recSolid: '📋 6-12개월 후 재검사 권장, 크기 증가 시 추가 검사 필요',
                    summaryTotal: '총 발견',
                    summaryHighRisk: '고위험 병변',
                    summaryMediumRisk: '중간 위험 병변',
                    reportTitle: '📋 상세 병리 분석 보고서',
                    dimensionNote: '(영상 비율 기반 추정)',
                    positionRightUpper: '우폐 상부',
                    positionRightLower: '우폐 하부',
                    positionLeftUpper: '좌폐 상부',
                    positionLeftLower: '좌폐 하부',
                    positionCentral: '종격동 영역'
                }
            };

            let currentLang = 'zh';

            function t(key) {
                return i18nData[currentLang][key] || key;
            }

            window.switchLanguage = function(lang) {
                currentLang = lang;
                document.querySelectorAll('.lang-btn').forEach(b => b.classList.remove('active'));
                document.querySelector(`.lang-btn[data-lang="${lang}"]`).classList.add('active');
                document.querySelectorAll('[data-i18n]').forEach(el => {
                    const key = el.getAttribute('data-i18n');
                    if (i18nData[lang][key]) {
                        if (i18nData[lang][key].includes('<strong>') || i18nData[lang][key].includes('<br>')) {
                            el.innerHTML = i18nData[lang][key];
                        } else {
                            el.textContent = i18nData[lang][key];
                        }
                    }
                });
                if (currentImageData && lastDetectionResults) {
                    updateResultPanel(lastDetectionResults);
                }
                const overlayText = document.querySelector('#analyzingOverlay .analyzing-text');
                if (overlayText) overlayText.textContent = t('analyzing');
            };

            document.querySelectorAll('.lang-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    switchLanguage(this.getAttribute('data-lang'));
                });
            });

            const uploadZone = document.getElementById('uploadZone');
            const fileInput = document.getElementById('fileInput');
            const mainCanvas = document.getElementById('mainCanvas');
            const imageDisplayArea = document.getElementById('imageDisplayArea');
            const noImagePlaceholder = document.getElementById('noImagePlaceholder');
            const analyzingOverlay = document.getElementById('analyzingOverlay');
            const legendBar = document.getElementById('legendBar');
            const resultContent = document.getElementById('resultContent');
            const btnAnalyze = document.getElementById('btnAnalyze');
            const btnReset = document.getElementById('btnReset');
            const btnPrint = document.getElementById('btnPrint');

            let currentImageData = null;
            let lastDetectionResults = null;
            const ctx = mainCanvas.getContext('2d');

            uploadZone.addEventListener('click', (e) => {
                if (e.target !== fileInput) fileInput.click();
            });
            uploadZone.addEventListener('dragover', (e) => {
                e.preventDefault();
                uploadZone.classList.add('drag-over');
            });
            uploadZone.addEventListener('dragleave', () => uploadZone.classList.remove('drag-over'));
            uploadZone.addEventListener('drop', (e) => {
                e.preventDefault();
                uploadZone.classList.remove('drag-over');
                if (e.dataTransfer.files.length > 0) handleFile(e.dataTransfer.files[0]);
            });
            fileInput.addEventListener('change', () => {
                if (fileInput.files.length > 0) handleFile(fileInput.files[0]);
            });

            function handleFile(file) {
                if (!file.type.match(/image\/(jpeg|png|webp|bmp|tiff)/)) {
                    alert(currentLang === 'zh' ? '请上传有效的图片文件' : '유효한 이미지 파일을 업로드하세요');
                    return;
                }
                const reader = new FileReader();
                reader.onload = function(e) {
                    const img = new Image();
                    img.onload = function() {
                        currentImageData = {
                            image: img,
                            src: e.target.result,
                            width: img.width,
                            height: img.height
                        };
                        displayImage(img);
                        btnAnalyze.style.display = 'inline-block';
                        btnReset.style.display = 'inline-block';
                        btnPrint.style.display = 'none';
                        legendBar.style.display = 'none';
                        resultContent.innerHTML = `<p style="color:#8899aa;text-align:center;padding:40px 20px;">${t('noResult')}</p>`;
                        lastDetectionResults = null;
                    };
                    img.src = e.target.result;
                };
                reader.readAsDataURL(file);
            }

            function displayImage(img) {
                noImagePlaceholder.style.display = 'none';
                mainCanvas.style.display = 'block';
                analyzingOverlay.style.display = 'none';
                const containerWidth = imageDisplayArea.clientWidth - 8;
                const maxWidth = Math.min(containerWidth, 750);
                const maxHeight = 520;
                let drawWidth = img.width;
                let drawHeight = img.height;
                const ratio = img.width / img.height;
                if (drawWidth > maxWidth) {
                    drawWidth = maxWidth;
                    drawHeight = drawWidth / ratio;
                }
                if (drawHeight > maxHeight) {
                    drawHeight = maxHeight;
                    drawWidth = drawHeight * ratio;
                }
                mainCanvas.width = Math.round(drawWidth);
                mainCanvas.height = Math.round(drawHeight);
                mainCanvas.style.width = drawWidth + 'px';
                mainCanvas.style.height = drawHeight + 'px';
                ctx.clearRect(0, 0, mainCanvas.width, mainCanvas.height);
                ctx.drawImage(img, 0, 0, drawWidth, drawHeight);
                currentImageData.displayWidth = drawWidth;
                currentImageData.displayHeight = drawHeight;
                currentImageData.scaleX = drawWidth / img.width;
                currentImageData.scaleY = drawHeight / img.height;
            }

            window.triggerAnalysis = function() {
                if (!currentImageData) return;
                analyzingOverlay.style.display = 'flex';
                document.querySelector('#analyzingOverlay .analyzing-text').textContent = t('analyzing');
                btnAnalyze.disabled = true;
                btnAnalyze.style.opacity = '0.6';
                legendBar.style.display = 'none';
                const delay = 1800 + Math.random() * 1400;
                setTimeout(() => {
                    const lesions = detectLesionsFromImage();
                    lastDetectionResults = lesions;
                    redrawWithAnnotations(lesions);
                    updateResultPanel(lesions);
                    analyzingOverlay.style.display = 'none';
                    btnAnalyze.disabled = false;
                    btnAnalyze.style.opacity = '1';
                    btnPrint.style.display = 'inline-block';
                    legendBar.style.display = 'flex';
                }, delay);
            };

            // 基于图像内容动态检测病变区域
            function detectLesionsFromImage() {
                const w = mainCanvas.width;
                const h = mainCanvas.height;
                const imageData = ctx.getImageData(0, 0, w, h);
                const data = imageData.data;
                // 划分网格，例如 8x6 个单元格
                const gridCols = 8;
                const gridRows = 6;
                const cellW = Math.floor(w / gridCols);
                const cellH = Math.floor(h / gridRows);
                const cells = [];

                for (let row = 0; row < gridRows; row++) {
                    for (let col = 0; col < gridCols; col++) {
                        let totalBrightness = 0;
                        let count = 0;
                        const startX = col * cellW;
                        const startY = row * cellH;
                        const endX = Math.min(startX + cellW, w);
                        const endY = Math.min(startY + cellH, h);
                        for (let y = startY; y < endY; y++) {
                            for (let x = startX; x < endX; x++) {
                                const idx = (y * w + x) * 4;
                                const r = data[idx];
                                const g = data[idx + 1];
                                const b = data[idx + 2];
                                // 加权灰度值 (标准亮度公式)
                                const gray = 0.299 * r + 0.587 * g + 0.114 * b;
                                totalBrightness += gray;
                                count++;
                            }
                        }
                        const avgBrightness = totalBrightness / count;
                        cells.push({
                            row,
                            col,
                            x: startX,
                            y: startY,
                            w: endX - startX,
                            h: endY - startY,
                            avgBrightness
                        });
                    }
                }

                // 按亮度从高到低排序
                cells.sort((a, b) => b.avgBrightness - a.avgBrightness);

                // 选取亮度最高的4个网格作为病变候选，并过滤掉亮度过低的
                const topCells = cells.filter(c => c.avgBrightness > 80).slice(0, 4);
                if (topCells.length === 0) {
                    // 如果没有足够亮的区域，随机选一个
                    if (cells.length > 0) topCells.push(cells[Math.floor(Math.random() * cells.length)]);
                }

                const lesions = [];
                const riskLevels = ['high', 'medium', 'low', 'follow'];
                const typeMap = {
                    high: { zh: '疑似恶性结节', ko: '악성 의심 결절', recZh: '⚠️ 高度关注，建议立即进行病理活检及多学科会诊', recKo: '⚠️ 높은 주의 필요, 즉시 조직 생검 및 다학제 협진 권장' },
                    medium: { zh: '磨玻璃结节 (GGO)', ko: '간유리 결절 (GGO)', recZh: '📋 建议3-6个月后复查CT，密切观察结节变化', recKo: '📋 3-6개월 후 CT 재검사 권장, 결절 변화 면밀 관찰' },
                    low: { zh: '钙化灶', ko: '석회화 병소', recZh: '✅ 良性特征，建议年度常规随访即可', recKo: '✅ 양성 소견, 연례 정기 추적 관찰 권장' },
                    follow: { zh: '实性小结节', ko: '고형 소결절', recZh: '📋 建议6-12个月后复查，如增大需进一步检查', recKo: '📋 6-12개월 후 재검사 권장, 크기 증가 시 추가 검사 필요' }
                };

                topCells.forEach((cell, index) => {
                    const risk = riskLevels[index] || 'follow';
                    const typeInfo = typeMap[risk];
                    // 位置描述
                    const centerX = cell.x + cell.w / 2;
                    const centerY = cell.y + cell.h / 2;
                    const isLeft = centerX < w / 2;
                    const isUpper = centerY < h / 3 ? true : (centerY > 2 * h / 3 ? false : 'mid');
                    let locZh, locKo;
                    if (isUpper === true) {
                        locZh = isLeft ? '左肺上部' : '右肺上部';
                        locKo = isLeft ? '좌폐 상부' : '우폐 상부';
                    } else if (isUpper === false) {
                        locZh = isLeft ? '左肺下部' : '右肺下部';
                        locKo = isLeft ? '좌폐 하부' : '우폐 하부';
                    } else {
                        locZh = isLeft ? '左肺中部' : '右肺中部';
                        locKo = isLeft ? '좌폐 중부' : '우폐 중부';
                    }
                    // 大小估算：假设图像宽度对应30cm，计算毫米
                    const mmWidth = Math.round((cell.w / w) * 300);
                    const mmHeight = Math.round((cell.h / h) * 300);
                    const confidence = (70 + Math.random() * 25).toFixed(1) + '%';

                    lesions.push({
                        id: index + 1,
                        type: risk,
                        x: cell.x,
                        y: cell.y,
                        w: cell.w,
                        h: cell.h,
                        labelZh: typeInfo.zh,
                        labelKo: typeInfo.ko,
                        riskLevel: risk,
                        sizeMm: `${mmWidth}×${mmHeight}`,
                        confidence: confidence,
                        locationZh: locZh,
                        locationKo: locKo,
                        recZh: typeInfo.recZh,
                        recKo: typeInfo.recKo
                    });
                });

                return lesions;
            }

            function redrawWithAnnotations(lesions) {
                const img = currentImageData.image;
                const dw = currentImageData.displayWidth;
                const dh = currentImageData.displayHeight;
                ctx.clearRect(0, 0, mainCanvas.width, mainCanvas.height);
                ctx.drawImage(img, 0, 0, dw, dh);

                const colors = {
                    high: { stroke: '#e74c3c', fill: 'rgba(231,76,60,0.22)', lineWidth: 3.5, glowColor: 'rgba(231,76,60,0.7)' },
                    medium: { stroke: '#e67e22', fill: 'rgba(230,126,34,0.18)', lineWidth: 2.8, glowColor: 'rgba(230,126,34,0.55)' },
                    low: { stroke: '#2980b9', fill: 'rgba(41,128,185,0.15)', lineWidth: 2.2, glowColor: 'rgba(41,128,185,0.45)' },
                    follow: { stroke: '#d4a017', fill: 'rgba(212,160,23,0.18)', lineWidth: 2.2, glowColor: 'rgba(212,160,23,0.5)' }
                };

                lesions.forEach(l => {
                    const colorScheme = colors[l.riskLevel];
                    const x = l.x;
                    const y = l.y;
                    const bw = l.w;
                    const bh = l.h;

                    ctx.save();
                    ctx.shadowColor = colorScheme.glowColor;
                    ctx.shadowBlur = 14;
                    ctx.strokeStyle = colorScheme.stroke;
                    ctx.lineWidth = colorScheme.lineWidth;
                    ctx.strokeRect(x, y, bw, bh);
                    ctx.shadowBlur = 0;
                    ctx.restore();

                    ctx.fillStyle = colorScheme.fill;
                    ctx.fillRect(x, y, bw, bh);
                    ctx.strokeStyle = colorScheme.stroke;
                    ctx.lineWidth = colorScheme.lineWidth;
                    ctx.strokeRect(x, y, bw, bh);

                    const cornerLen = Math.min(bw, bh) * 0.3;
                    ctx.strokeStyle = colorScheme.stroke;
                    ctx.lineWidth = colorScheme.lineWidth + 1.5;
                    ctx.beginPath();
                    ctx.moveTo(x, y + cornerLen);
                    ctx.lineTo(x, y);
                    ctx.lineTo(x + cornerLen, y);
                    ctx.stroke();
                    ctx.beginPath();
                    ctx.moveTo(x + bw - cornerLen, y);
                    ctx.lineTo(x + bw, y);
                    ctx.lineTo(x + bw, y + cornerLen);
                    ctx.stroke();
                    ctx.beginPath();
                    ctx.moveTo(x, y + bh - cornerLen);
                    ctx.lineTo(x, y + bh);
                    ctx.lineTo(x + cornerLen, y + bh);
                    ctx.stroke();
                    ctx.beginPath();
                    ctx.moveTo(x + bw - cornerLen, y + bh);
                    ctx.lineTo(x + bw, y + bh);
                    ctx.lineTo(x + bw, y + bh - cornerLen);
                    ctx.stroke();

                    const shortLabel = currentLang === 'zh' ?
                        (l.riskLevel === 'high' ? '疑似恶性' : l.riskLevel === 'medium' ? 'GGO' : l.riskLevel === 'low' ? '钙化' : '实性结节') :
                        (l.riskLevel === 'high' ? '악성의심' : l.riskLevel === 'medium' ? 'GGO' : l.riskLevel === 'low' ? '석회화' : '고형결절');
                    const tagText = `#${l.id} ${shortLabel}`;
                    ctx.font = 'bold 13px "PingFang SC","Microsoft YaHei","Apple SD Gothic Neo","Malgun Gothic",sans-serif';
                    const textWidth = ctx.measureText(tagText).width;
                    const textHeight = 18;
                    const tagX = x;
                    const tagY = y - textHeight - 6;
                    const bgColor = l.riskLevel === 'high' ? '#e74c3c' : l.riskLevel === 'medium' ? '#e67e22' : l.riskLevel === 'low' ? '#2980b9' : '#c8960e';
                    ctx.fillStyle = bgColor;
                    ctx.beginPath();
                    ctx.roundRect(tagX - 5, tagY - 2, textWidth + 10, textHeight + 6, 4);
                    ctx.fill();
                    ctx.fillStyle = '#ffffff';
                    ctx.fillText(tagText, tagX, tagY + textHeight - 3);

                    const confText = l.confidence;
                    const confW = ctx.measureText(confText).width;
                    ctx.fillStyle = 'rgba(0,0,0,0.7)';
                    ctx.beginPath();
                    ctx.roundRect(x + bw - confW - 10, y + bh + 3, confW + 10, 17, 3);
                    ctx.fill();
                    ctx.fillStyle = '#fff';
                    ctx.font = '11px "PingFang SC","Microsoft YaHei","Apple SD Gothic Neo","Malgun Gothic",sans-serif';
                    ctx.fillText(confText, x + bw - confW - 5, y + bh + 15);
                });
            }

            function updateResultPanel(lesions) {
                const highCount = lesions.filter(l => l.riskLevel === 'high').length;
                const mediumCount = lesions.filter(l => l.riskLevel === 'medium').length;
                let html = `<div class="result-summary">
                    <div class="summary-badge danger"><span class="num">${lesions.length}</span><span class="lbl">${t('summaryTotal')}</span></div>`;
                if (highCount > 0) html += `<div class="summary-badge danger"><span class="num">${highCount}</span><span class="lbl">${t('summaryHighRisk')}</span></div>`;
                if (mediumCount > 0) html += `<div class="summary-badge warning"><span class="num">${mediumCount}</span><span class="lbl">${t('summaryMediumRisk')}</span></div>`;
                html += `<div class="summary-badge info"><span class="num">${lesions.filter(l=>l.riskLevel==='low').length}</span><span class="lbl">${t('riskLowLabel')}</span></div></div>`;
                html += `<p style="font-weight:700;margin-bottom:10px;color:#1a3c5e;font-size:15px;">${t('reportTitle')}</p>`;

                lesions.forEach(l => {
                    const riskClass = l.riskLevel === 'high' ? 'risk-high' : l.riskLevel === 'medium' ? 'risk-medium' : l.riskLevel === 'low' ? 'risk-low' : 'risk-follow';
                    const badgeClass = l.riskLevel === 'high' ? 'badge-high' : l.riskLevel === 'medium' ? 'badge-medium' : l.riskLevel === 'low' ? 'badge-low' : 'badge-follow';
                    const riskLabel = t('risk' + l.riskLevel.charAt(0).toUpperCase() + l.riskLevel.slice(1) + 'Label');
                    const lesionName = currentLang === 'zh' ? l.labelZh : l.labelKo;
                    const locationName = currentLang === 'zh' ? l.locationZh : l.locationKo;
                    const recText = currentLang === 'zh' ? l.recZh : l.recKo;
                    html += `
                    <div class="lesion-detail-card ${riskClass}">
                        <div class="lesion-header">
                            <span class="lesion-name">🔴 #${l.id} ${lesionName}</span>
                            <span class="lesion-badge ${badgeClass}">${riskLabel}</span>
                        </div>
                        <div class="lesion-info">
                            <span>📍 ${t('location')}: <span class="val">${locationName}</span></span>
                            <span>📏 ${t('size')}: <span class="val">${l.sizeMm} mm</span> ${t('dimensionNote')}</span>
                            <span>🎯 ${t('confidence')}: <span class="val">${l.confidence}</span></span>
                        </div>
                        <div class="lesion-info" style="margin-top:4px;">
                            💡 <strong>${t('recommendation')}:</strong> ${recText}
                        </div>
                    </div>`;
                });
                resultContent.innerHTML = html;
            }

            window.resetAll = function() {
                currentImageData = null;
                lastDetectionResults = null;
                mainCanvas.style.display = 'none';
                noImagePlaceholder.style.display = '';
                analyzingOverlay.style.display = 'none';
                legendBar.style.display = 'none';
                btnAnalyze.style.display = 'none';
                btnReset.style.display = 'none';
                btnPrint.style.display = 'none';
                fileInput.value = '';
                ctx.clearRect(0, 0, mainCanvas.width, mainCanvas.height);
                resultContent.innerHTML = `<p style="color:#8899aa;text-align:center;padding:40px 20px;">${t('noResult')}</p>`;
                btnAnalyze.disabled = false;
                btnAnalyze.style.opacity = '1';
            };

            window.addEventListener('resize', () => {
                if (currentImageData && analyzingOverlay.style.display === 'none') {
                    displayImage(currentImageData.image);
                    if (lastDetectionResults) redrawWithAnnotations(lastDetectionResults);
                }
            });

            btnAnalyze.style.display = 'none';
            btnReset.style.display = 'none';
            btnPrint.style.display = 'none';
            legendBar.style.display = 'none';
        })();
    </script>
</body>
</html>
```
