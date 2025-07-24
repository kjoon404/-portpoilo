<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>제조 공정 AI 자동화 분석 시스템</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/plotly.js/2.26.0/plotly.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            color: #333;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            text-align: center;
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
        }

        .header h1 {
            font-size: 2.5rem;
            color: #2c3e50;
            margin-bottom: 10px;
            background: linear-gradient(45deg, #667eea, #764ba2);
            background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .header p {
            font-size: 1.1rem;
            color: #555;
            margin-bottom: 20px;
        }

        .controls {
            display: flex;
            gap: 15px;
            justify-content: center;
            flex-wrap: wrap;
        }

        .btn {
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 14px;
            font-weight: 600;
            transition: all 0.3s ease;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .stat-card {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 25px;
            text-align: center;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease;
        }

        .stat-card:hover {
            transform: translateY(-5px);
        }

        .stat-value {
            font-size: 2.5rem;
            font-weight: bold;
            margin-bottom: 10px;
        }

        .stat-label {
            color: #666;
            font-size: 0.9rem;
            text-transform: uppercase;
            letter-spacing: 1px;
        }

        .normal { color: #27ae60; }
        .warning { color: #f39c12; }
        .danger { color: #e74c3c; }
        .info { color: #3498db; }

        .charts-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }

        .chart-container {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
        }

        .chart-title {
            font-size: 1.2rem;
            font-weight: 600;
            margin-bottom: 20px;
            color: #2c3e50;
            text-align: center;
        }

        .data-table {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
        }

        th, td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }

        th {
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            font-weight: 600;
        }

        tr:hover {
            background-color: #f5f5f5;
        }

        .anomaly-row {
            background-color: #ffe6e6;
        }

        .loading {
            text-align: center;
            padding: 50px;
            font-size: 1.2rem;
            color: #666;
        }

        .spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #667eea;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 20px auto;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .alert {
            background: #fff3cd;
            border: 1px solid #ffeaa7;
            border-radius: 10px;
            padding: 15px;
            margin: 20px 0;
            border-left: 4px solid #f39c12;
        }

        @media (max-width: 768px) {
            .charts-grid {
                grid-template-columns: 1fr;
            }
            
            .header h1 {
                font-size: 2rem;
            }
            
            .controls {
                flex-direction: column;
                align-items: center;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🏭 제조 공정 AI 자동화 분석 시스템</h1>
            <p>반도체 제조 공정 데이터 품질 분석 및 이상 탐지</p>
            <div class="controls">
                <button class="btn" onclick="generateData()">📊 데이터 생성</button>
                <button class="btn" onclick="analyzeQuality()">🔍 품질 분석</button>
                <button class="btn" onclick="detectAnomalies()">⚠️ 이상 탐지</button>
                <button class="btn" onclick="optimizeProcess()">⚙️ 공정 최적화</button>
                <button class="btn" onclick="exportReport()">📋 보고서 내보내기</button>
            </div>
        </div>

        <div class="stats-grid" id="statsGrid" style="display: none;">
            <div class="stat-card">
                <div class="stat-value normal" id="totalSamples">2000</div>
                <div class="stat-label">총 데이터 포인트</div>
            </div>
            <div class="stat-card">
                <div class="stat-value danger" id="anomalyCount">3000</div>
                <div class="stat-label">이상 데이터</div>
            </div>
            <div class="stat-card">
                <div class="stat-value info" id="avgYield">86%</div>
                <div class="stat-label">평균 수율</div>
            </div>
            <div class="stat-card">
                <div class="stat-value warning" id="avgDefect">3%</div>
                <div class="stat-label">평균 불량률</div>
            </div>
        </div>

        <div class="charts-grid" id="chartsGrid" style="display: none;">
            <div class="chart-container">
                <div class="chart-title">온도 vs 수율 분석</div>
                <div id="tempYieldChart" style="height: 400px;"></div>
            </div>
            <div class="chart-container">
                <div class="chart-title">압력 vs 불량률 분석</div>
                <div id="pressureDefectChart" style="height: 400px;"></div>
            </div>
            <div class="chart-container">
                <div class="chart-title">시간별 수율 추이</div>
                <canvas id="yieldTrendChart" width="400" height="300"></canvas>
            </div>
            <div class="chart-container">
                <div class="chart-title">공정 파라미터 상관관계</div>
                <div id="correlationHeatmap" style="height: 400px;"></div>
            </div>
        </div>

        <div class="data-table" id="dataTable" style="display: none;">
            <h3>📈 실시간 공정 데이터</h3>
            <div class="alert">
                <strong>알림:</strong> 빨간색으로 표시된 행은 이상이 탐지된 데이터입니다.
            </div>
            <table id="processTable">
                <thead>
                    <tr>
                        <th>시간</th>
                        <th>온도(°C)</th>
                        <th>압력(atm)</th>
                        <th>습도(%)</th>
                        <th>진동(mm/s)</th>
                        <th>수율(%)</th>
                        <th>불량률(%)</th>
                        <th>상태</th>
                    </tr>
                </thead>
                <tbody id="processTableBody">
                </tbody>
            </table>
        </div>

        <div class="loading" id="loading" style="display: none;">
            <div class="spinner"></div>
            분석 중입니다...
        </div>
    </div>

    <script>
        class ManufacturingAnalyzer {
            constructor() {
                this.data = [];
                this.analysisResults = {};
                this.charts = {};
            }

            // 가우시안 난수 생성
            gaussianRandom(mean = 0, std = 1) {
                let u = 0, v = 0;
                while(u === 0) u = Math.random();
                while(v === 0) v = Math.random();
                return mean + std * Math.sqrt(-2.0 * Math.log(u)) * Math.cos(2.0 * Math.PI * v);
            }

            // 시뮬레이션 데이터 생성
            generateSyntheticData(samples = 1000) {
                this.data = [];
                const anomalyIndices = new Set();
                
                // 이상 데이터 인덱스 생성 (10%)
                const anomalyCount = Math.floor(samples * 0.1);
                while(anomalyIndices.size < anomalyCount) {
                    anomalyIndices.add(Math.floor(Math.random() * samples));
                }

                for(let i = 0; i < samples; i++) {
                    const isAnomaly = anomalyIndices.has(i);
                    
                    // 기본 공정 파라미터
                    let temperature = this.gaussianRandom(250, 10);
                    let pressure = this.gaussianRandom(1.2, 0.1);
                    let humidity = this.gaussianRandom(45, 5);
                    let vibration = this.gaussianRandom(0.5, 0.1);
                    let yieldRate = this.gaussianRandom(95, 3);
                    let defectRate = Math.max(0, this.gaussianRandom(2, 1));

                    // 이상 상황 시뮬레이션
                    if(isAnomaly) {
                        const anomalyType = Math.random();
                        if(anomalyType < 0.33) {
                            // 온도 이상
                            temperature += this.gaussianRandom(50, 20);
                        } else if(anomalyType < 0.66) {
                            // 압력 이상
                            pressure *= (1.5 + Math.random() * 0.5);
                        } else {
                            // 복합 이상
                            temperature += this.gaussianRandom(30, 10);
                            pressure *= (0.5 + Math.random() * 0.3);
                            yieldRate -= (20 + Math.random() * 20);
                        }
                    }

                    const timestamp = new Date(Date.now() - (samples - i) * 60000);
                    
                    this.data.push({
                        timestamp: timestamp,
                        temperature: Math.round(temperature * 100) / 100,
                        pressure: Math.round(pressure * 1000) / 1000,
                        humidity: Math.round(humidity * 100) / 100,
                        vibration: Math.round(vibration * 1000) / 1000,
                        yieldRate: Math.max(0, Math.min(100, Math.round(yieldRate * 100) / 100)),
                        defectRate: Math.max(0, Math.min(50, Math.round(defectRate * 100) / 100)),
                        isAnomaly: isAnomaly,
                        detectedAnomaly: false
                    });
                }
            }

            // 이상 탐지 (단순화된 버전)
            detectAnomalies() {
                // 통계적 기준으로 이상 탐지
                const stats = this.calculateStatistics();
                
                this.data.forEach(point => {
                    let anomalyScore = 0;
                    
                    // Z-score 기반 이상 탐지
                    const tempZ = Math.abs((point.temperature - stats.temperature.mean) / stats.temperature.std);
                    const pressureZ = Math.abs((point.pressure - stats.pressure.mean) / stats.pressure.std);
                    const yieldZ = Math.abs((point.yieldRate - stats.yieldRate.mean) / stats.yieldRate.std);
                    
                    if(tempZ > 2.5) anomalyScore++;
                    if(pressureZ > 2.5) anomalyScore++;
                    if(yieldZ > 2.5) anomalyScore++;
                    
                    point.detectedAnomaly = anomalyScore >= 2;
                });
            }

            // 통계 계산
            calculateStatistics() {
                const stats = {};
                const fields = ['temperature', 'pressure', 'humidity', 'vibration', 'yieldRate', 'defectRate'];
                
                fields.forEach(field => {
                    const values = this.data.map(d => d[field]);
                    const mean = values.reduce((a, b) => a + b, 0) / values.length;
                    const variance = values.reduce((a, b) => a + (b - mean) ** 2, 0) / values.length;
                    const std = Math.sqrt(variance);
                    
                    stats[field] = {
                        mean: mean,
                        std: std,
                        min: Math.min(...values),
                        max: Math.max(...values)
                    };
                });
                
                return stats;
            }

            // 공정 최적화
            optimizeProcess() {
                const normalData = this.data.filter(d => !d.detectedAnomaly);
                const highYieldData = normalData.filter(d => d.yieldRate >= 98);
                const targetData = highYieldData.length > 0 ? highYieldData : 
                                  normalData.sort((a, b) => b.yieldRate - a.yieldRate).slice(0, Math.floor(normalData.length * 0.1));
                
                if(targetData.length === 0) return null;
                
                const optimization = {};
                ['temperature', 'pressure', 'humidity'].forEach(param => {
                    const values = targetData.map(d => d[param]);
                    optimization[param] = {
                        optimal: values.reduce((a, b) => a + b, 0) / values.length,
                        range: [Math.min(...values), Math.max(...values)]
                    };
                });
                
                return optimization;
            }

            // 시각화 업데이트
            updateVisualizations() {
                this.updateStats();
                this.createScatterPlots();
                this.createTrendChart();
                this.createCorrelationHeatmap();
                this.updateDataTable();
            }

            updateStats() {
                const totalSamples = this.data.length;
                const anomalyCount = this.data.filter(d => d.detectedAnomaly).length;
                const avgYield = this.data.reduce((a, b) => a + b.yieldRate, 0) / totalSamples;
                const avgDefect = this.data.reduce((a, b) => a + b.defectRate, 0) / totalSamples;

                document.getElementById('totalSamples').textContent = totalSamples.toLocaleString();
                document.getElementById('anomalyCount').textContent = anomalyCount.toLocaleString();
                document.getElementById('avgYield').textContent = avgYield.toFixed(1) + '%';
                document.getElementById('avgDefect').textContent = avgDefect.toFixed(1) + '%';
            }

            createScatterPlots() {
                // 온도 vs 수율
                const normalPoints = this.data.filter(d => !d.detectedAnomaly);
                const anomalyPoints = this.data.filter(d => d.detectedAnomaly);

                const trace1 = {
                    x: normalPoints.map(d => d.temperature),
                    y: normalPoints.map(d => d.yieldRate),
                    mode: 'markers',
                    type: 'scatter',
                    name: '정상',
                    marker: { color: '#27ae60', size: 6, opacity: 0.7 }
                };

                const trace2 = {
                    x: anomalyPoints.map(d => d.temperature),
                    y: anomalyPoints.map(d => d.yieldRate),
                    mode: 'markers',
                    type: 'scatter',
                    name: '이상',
                    marker: { color: '#e74c3c', size: 8, opacity: 0.8 }
                };

                Plotly.newPlot('tempYieldChart', [trace1, trace2], {
                    xaxis: { title: '온도 (°C)' },
                    yaxis: { title: '수율 (%)' },
                    showlegend: true,
                    margin: { t: 20, r: 20, b: 60, l: 60 }
                });

                // 압력 vs 불량률
                const trace3 = {
                    x: normalPoints.map(d => d.pressure),
                    y: normalPoints.map(d => d.defectRate),
                    mode: 'markers',
                    type: 'scatter',
                    name: '정상',
                    marker: { color: '#3498db', size: 6, opacity: 0.7 }
                };

                const trace4 = {
                    x: anomalyPoints.map(d => d.pressure),
                    y: anomalyPoints.map(d => d.defectRate),
                    mode: 'markers',
                    type: 'scatter',
                    name: '이상',
                    marker: { color: '#e74c3c', size: 8, opacity: 0.8 }
                };

                Plotly.newPlot('pressureDefectChart', [trace3, trace4], {
                    xaxis: { title: '압력 (atm)' },
                    yaxis: { title: '불량률 (%)' },
                    showlegend: true,
                    margin: { t: 20, r: 20, b: 60, l: 60 }
                });
            }

            createTrendChart() {
                const ctx = document.getElementById('yieldTrendChart').getContext('2d');
                
                if(this.charts.yieldTrend) {
                    this.charts.yieldTrend.destroy();
                }

                const sampleData = this.data.filter((_, i) => i % 10 === 0); // 샘플링
                
                this.charts.yieldTrend = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: sampleData.map(d => d.timestamp.toLocaleTimeString()),
                        datasets: [{
                            label: '수율',
                            data: sampleData.map(d => d.yieldRate),
                            borderColor: '#667eea',
                            backgroundColor: 'rgba(102, 126, 234, 0.1)',
                            borderWidth: 2,
                            pointBackgroundColor: sampleData.map(d => d.detectedAnomaly ? '#e74c3c' : '#667eea'),
                            pointRadius: sampleData.map(d => d.detectedAnomaly ? 6 : 3),
                            tension: 0.4
                        }]
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        scales: {
                            y: {
                                beginAtZero: false,
                                title: {
                                    display: true,
                                    text: '수율 (%)'
                                }
                            },
                            x: {
                                title: {
                                    display: true,
                                    text: '시간'
                                }
                            }
                        },
                        plugins: {
                            legend: {
                                display: true
                            }
                        }
                    }
                });
            }

            createCorrelationHeatmap() {
                const fields = ['temperature', 'pressure', 'humidity', 'vibration', 'yieldRate', 'defectRate'];
                const correlationMatrix = [];
                
                fields.forEach(field1 => {
                    const row = [];
                    fields.forEach(field2 => {
                        row.push(this.calculateCorrelation(field1, field2));
                    });
                    correlationMatrix.push(row);
                });

                const trace = {
                    z: correlationMatrix,
                    x: fields,
                    y: fields,
                    type: 'heatmap',
                    colorscale: 'RdBu',
                    zmid: 0,
                    zmin: -1,
                    zmax: 1,
                    showscale: true
                };

                Plotly.newPlot('correlationHeatmap', [trace], {
                    title: '',
                    margin: { t: 20, r: 20, b: 60, l: 80 }
                });
            }

            calculateCorrelation(field1, field2) {
                const x = this.data.map(d => d[field1]);
                const y = this.data.map(d => d[field2]);
                
                const meanX = x.reduce((a, b) => a + b, 0) / x.length;
                const meanY = y.reduce((a, b) => a + b, 0) / y.length;
                
                let numerator = 0;
                let denomX = 0;
                let denomY = 0;
                
                for(let i = 0; i < x.length; i++) {
                    const dx = x[i] - meanX;
                    const dy = y[i] - meanY;
                    numerator += dx * dy;
                    denomX += dx * dx;
                    denomY += dy * dy;
                }
                
                return numerator / Math.sqrt(denomX * denomY);
            }

            updateDataTable() {
                const tbody = document.getElementById('processTableBody');
                tbody.innerHTML = '';
                
                // 최근 50개 데이터만 표시
                const recentData = this.data.slice(-50);
                
                recentData.forEach(point => {
                    const row = tbody.insertRow();
                    if(point.detectedAnomaly) {
                        row.className = 'anomaly-row';
                    }
                    
                    row.innerHTML = `
                        <td>${point.timestamp.toLocaleTimeString()}</td>
                        <td>${point.temperature}</td>
                        <td>${point.pressure}</td>
                        <td>${point.humidity}</td>
                        <td>${point.vibration}</td>
                        <td>${point.yieldRate}</td>
                        <td>${point.defectRate}</td>
                        <td>${point.detectedAnomaly ? '⚠️ 이상' : '✅ 정상'}</td>
                    `;
                });
            }
        }

        // 전역 인스턴스
        const analyzer = new ManufacturingAnalyzer();

        // 이벤트 핸들러
        function showLoading() {
            document.getElementById('loading').style.display = 'block';
            document.getElementById('statsGrid').style.display = 'none';
            document.getElementById('chartsGrid').style.display = 'none';
            document.getElementById('dataTable').style.display = 'none';
        }

        function hideLoading() {
            document.getElementById('loading').style.display = 'none';
            document.getElementById('statsGrid').style.display = 'grid';
            document.getElementById('chartsGrid').style.display = 'grid';
            document.getElementById('dataTable').style.display = 'block';
        }

        async function generateData() {
            showLoading();
            
            setTimeout(() => {
                analyzer.generateSyntheticData(2000);
                hideLoading();
                alert('✅ 2,000개의 시뮬레이션 데이터가 생성되었습니다.');
            }, 1000);
        }

        async function analyzeQuality() {
            if(analyzer.data.length === 0) {
                alert('먼저 데이터를 생성해주세요.');
                return;
            }
            
            showLoading();
            
            setTimeout(() => {
                analyzer.updateStats();
                hideLoading();
                alert('✅ 데이터 품질 분석이 완료되었습니다.');
            }, 500);
        }

        async function detectAnomalies() {
            if(analyzer.data.length === 0) {
                alert('먼저 데이터를 생성해주세요.');
                return;
            }
            
            showLoading();
            
            setTimeout(() => {
                analyzer.detectAnomalies();
                analyzer.updateVisualizations();
                hideLoading();
                
                const anomalyCount = analyzer.data.filter(d => d.detectedAnomaly).length;
                alert(`✅ 이상 탐지 완료! ${anomalyCount}개의 이상 데이터가 발견되었습니다.`);
            }, 1500);
        }

        async function optimizeProcess() {
            if(analyzer.data.length === 0) {
                alert('먼저 데이터를 생성하고 이상 탐지를 수행해주세요.');
                return;
            }
            
            showLoading();
            
            setTimeout(() => {
                const optimization = analyzer.optimizeProcess();
                hideLoading();
                
                if(optimization) {
                    let message = '🎯 최적 공정 조건:\n\n';
                    Object.entries(optimization).forEach(([param, data]) => {
                        message += `${param}: ${data.optimal.toFixed(2)} (범위: ${data.range[0].toFixed(2)} ~ ${data.range[1].toFixed(2)})\n`;
                    });
                    alert(message);
                } else {
                    alert('최적화할 수 있는 데이터가 부족합니다.');
                }
            }, 800);
        }

        function exportReport() {
            if(analyzer.data.length === 0) {
                alert('먼저 분석을 수행해주세요.');
                return;
            }
            
            const report = {
                timestamp: new Date().toISOString(),
                totalSamples: analyzer.data.length,
                anomalies: analyzer.data.filter(d => d.detectedAnomaly).length,
                avgYield: analyzer.data.reduce((a, b) => a + b.yieldRate, 0) / analyzer.data.length,
                avgDefect: analyzer.data.reduce((a, b) => a + b.defectRate, 0) / analyzer.data.length,
                data: analyzer.data
            };
            
            const blob = new Blob([JSON.stringify(report, null, 2)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `manufacturing_analysis_${new Date().toISOString().slice(0, 10)}.json`;
            a.click();
            URL.revokeObjectURL(url);
            
            alert('📋 분석 보고서가 다운로드되었습니다.');
        }

        // 실시간 데이터 시뮬레이션
        function startRealTimeSimulation() {
            if(analyzer.data.length === 0) {
                alert('먼저 기본 데이터를 생성해주세요.');
                return;
            }

            const interval = setInterval(() => {
                // 새로운 데이터 포인트 생성
                const lastPoint = analyzer.data[analyzer.data.length - 1];
                const newPoint = {
                    timestamp: new Date(),
                    temperature: lastPoint.temperature + analyzer.gaussianRandom(0, 2),
                    pressure: lastPoint.pressure + analyzer.gaussianRandom(0, 0.02),
                    humidity: lastPoint.humidity + analyzer.gaussianRandom(0, 1),
                    vibration: lastPoint.vibration + analyzer.gaussianRandom(0, 0.05),
                    yieldRate: Math.max(0, Math.min(100, lastPoint.yieldRate + analyzer.gaussianRandom(0, 1))),
                    defectRate: Math.max(0, lastPoint.defectRate + analyzer.gaussianRandom(0, 0.5)),
                    isAnomaly: Math.random() < 0.05, // 5% 확률로 이상
                    detectedAnomaly: false
                };

                // 간단한 이상 탐지
                if(Math.abs(newPoint.temperature - 250) > 30 || 
                   Math.abs(newPoint.pressure - 1.2) > 0.3 || 
                   newPoint.yieldRate < 90) {
                    newPoint.detectedAnomaly = true;
                }

                analyzer.data.push(newPoint);
                
                // 데이터 크기 제한 (최대 5000개)
                if(analyzer.data.length > 5000) {
                    analyzer.data.shift();
                }

                // 주기적으로 차트 업데이트 (10초마다)
                if(analyzer.data.length % 10 === 0) {
                    analyzer.updateVisualizations();
                }
            }, 1000);

            // 1분 후 자동 중지
            setTimeout(() => {
                clearInterval(interval);
                alert('실시간 시뮬레이션이 완료되었습니다.');
            }, 60000);

            alert('실시간 데이터 시뮬레이션이 시작되었습니다. (1분간 진행)');
        }

        // 키보드 단축키
        document.addEventListener('keydown', function(e) {
            if(e.ctrlKey) {
                switch(e.key) {
                    case '1':
                        e.preventDefault();
                        generateData();
                        break;
                    case '2':
                        e.preventDefault();
                        analyzeQuality();
                        break;
                    case '3':
                        e.preventDefault();
                        detectAnomalies();
                        break;
                    case '4':
                        e.preventDefault();
                        optimizeProcess();
                        break;
                    case 's':
                        e.preventDefault();
                        exportReport();
                        break;
                }
            }
        });

        // 페이지 로드 시 안내 메시지
        window.addEventListener('load', function() {
            setTimeout(() => {
                alert(`🚀 제조 공정 AI 자동화 분석 시스템에 오신 것을 환영합니다!

사용 방법:
1. "데이터 생성" 버튼을 클릭하여 시뮬레이션 데이터를 생성하세요
2. "품질 분석" 버튼으로 기본 통계를 확인하세요  
3. "이상 탐지" 버튼으로 AI 분석을 수행하세요
4. "공정 최적화" 버튼으로 최적 조건을 찾으세요

💡 팁: Ctrl+1~4, Ctrl+S 단축키를 사용할 수 있습니다.`);
            }, 1000);
        });

        // 추가 버튼을 헤더에 추가
        document.addEventListener('DOMContentLoaded', function() {
            const controls = document.querySelector('.controls');
            const realtimeBtn = document.createElement('button');
            realtimeBtn.className = 'btn';
            realtimeBtn.innerHTML = '📡 실시간 모니터링';
            realtimeBtn.onclick = startRealTimeSimulation;
            controls.appendChild(realtimeBtn);
        });
    </script>
</body>
</html>
