<!DOCTYPE html>
<html lang="ja" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>西条農業 - スマート農業営農支援ダッシュボード</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Noto+Sans+JP:wght@300;400;500;700&display=swap" rel="stylesheet">
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Custom configuration for Tailwind -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'Noto Sans JP', 'sans-serif'],
                    },
                    colors: {
                        agri: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            200: '#bbf7d0',
                            300: '#86efac',
                            400: '#4ade80',
                            500: '#22c55e',
                            600: '#16a34a',
                            700: '#15803d',
                            800: '#166534',
                            900: '#14532d',
                            950: '#022c16',
                        },
                        earth: {
                            100: '#f5efe6',
                            200: '#e8dccl',
                            300: '#ddc7a0',
                            800: '#4a3b32',
                            900: '#2e251f'
                        }
                    },
                    animation: {
                        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                        'float': 'float 5s ease-in-out infinite',
                        'spin-slow': 'spin 12s linear infinite',
                    },
                    keyframes: {
                        float: {
                            '0%, 100%': { transform: 'translateY(0px)' },
                            '50%': { transform: 'translateY(-6px)' },
                        }
                    }
                }
            }
        }
    </script>
    
    <style>
        body {
            font-family: 'Inter', 'Noto Sans JP', sans-serif;
            background: radial-gradient(circle at top left, #0f2d1e, #07170f, #020704);
            min-height: 100vh;
            color: #f1f5f9;
            overflow-x: hidden;
        }

        /* Glassmorphism Panel styles with a natural green accent */
        .glass-panel {
            background: rgba(15, 32, 23, 0.55);
            backdrop-filter: blur(14px);
            -webkit-backdrop-filter: blur(14px);
            border: 1px solid rgba(74, 222, 128, 0.08);
            box-shadow: 0 10px 35px 0 rgba(0, 0, 0, 0.4);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .glass-panel:hover {
            border-color: rgba(74, 222, 128, 0.22);
            box-shadow: 0 15px 40px 0 rgba(34, 197, 94, 0.12);
        }

        /* Progress ring transitions */
        .progress-ring-circle {
            transition: stroke-dashoffset 0.4s ease;
        }

        /* Custom scrollbars */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.2);
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(34, 197, 94, 0.25);
            border-radius: 8px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(34, 197, 94, 0.5);
        }
    </style>
</head>
<body class="p-3 sm:p-6 lg:p-8 antialiased selection:bg-emerald-800/50 selection:text-emerald-200">

    <div class="max-w-7xl mx-auto flex flex-col gap-6">
        
        <!-- HEADER SECTION -->
        <header class="glass-panel rounded-2xl p-6 flex flex-col md:flex-row justify-between items-center gap-6">
            <div class="flex items-center gap-4">
                <div class="w-14 h-14 rounded-xl bg-gradient-to-tr from-emerald-500 to-green-600 flex items-center justify-center shadow-lg shadow-emerald-500/20 animate-float">
                    <i class="fa-solid fa-leaf text-white text-2xl"></i>
                </div>
                <div>
                    <h1 class="text-2xl font-bold tracking-tight bg-gradient-to-r from-white via-emerald-100 to-emerald-300 bg-clip-text text-transparent">西条農業</h1>
                    <p class="text-xs text-emerald-400/80 font-medium">西条市スマート農業管理 ＆ インタラクティブ営農シミュレーター</p>
                </div>
            </div>
            
            <!-- Realtime Clock & Simulation Parameters -->
            <div class="flex flex-col md:flex-row items-center gap-6">
                <div class="flex items-center gap-4 bg-slate-900/40 px-4 py-2 rounded-xl border border-emerald-900/30">
                    <div class="text-center">
                        <span class="text-[10px] text-slate-400 block uppercase">現在の純利益</span>
                        <span id="farm-revenue" class="text-lg font-mono font-bold text-amber-300">¥12,500</span>
                    </div>
                    <div class="w-px h-8 bg-slate-800"></div>
                    <div class="text-center">
                        <span class="text-[10px] text-slate-400 block uppercase">累計収穫量</span>
                        <span id="total-harvests" class="text-lg font-mono font-bold text-emerald-400">0個</span>
                    </div>
                </div>

                <div class="flex flex-col items-center md:items-end">
                    <div class="text-2xl font-mono font-semibold tracking-widest text-emerald-100" id="live-clock">00:00:00</div>
                    <div class="text-xs text-slate-400 mt-1 flex items-center gap-2">
                        <span>気象サーバー: オンライン</span>
                        <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
                    </div>
                </div>
            </div>
        </header>

        <!-- ADVISORY NOTIFICATION BAR -->
        <div class="glass-panel rounded-2xl p-4 flex flex-col sm:flex-row items-center justify-between gap-4 border-l-4 border-l-emerald-500">
            <div class="flex items-center gap-3">
                <span class="text-emerald-400 text-lg animate-pulse-slow">
                    <i class="fa-solid fa-microchip-ai"></i>
                </span>
                <div>
                    <span class="text-[10px] text-emerald-400 font-bold uppercase tracking-wider">AI営農アドバイス</span>
                    <p class="text-sm text-slate-200" id="ai-advisor-msg">農場は安定稼働しています。作物の状況を見守りましょう。</p>
                </div>
            </div>
            <div class="flex items-center gap-2">
                <span class="text-xs text-slate-400">現在地: 愛媛県西条市 晴天</span>
                <button onclick="triggerAIConsult()" class="text-xs bg-emerald-500/10 hover:bg-emerald-500/25 text-emerald-300 hover:text-white px-3.5 py-1.5 rounded-lg transition-all border border-emerald-500/20 active:scale-95 flex items-center gap-1.5">
                    <i class="fa-solid fa-wand-magic-sparkles"></i>
                    AI診断を再実行
                </button>
            </div>
        </div>

        <!-- MAIN GRID LAYOUT -->
        <main class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
            
            <!-- LEFT COLUMN: Crop Fields Map & Interactive Farming Grid -->
            <section class="lg:col-span-5 flex flex-col gap-6">
                <div class="glass-panel rounded-2xl p-6 flex flex-col">
                    <div class="flex justify-between items-center mb-4">
                        <div>
                            <h3 class="text-xs font-semibold tracking-widest text-emerald-400 uppercase">Farm Fields Map</h3>
                            <h4 class="text-lg font-bold text-white mt-1">インタラクティブ圃場（ほじょう）マップ</h4>
                        </div>
                        <span class="text-[10px] bg-amber-500/10 text-amber-300 border border-amber-500/20 px-2 py-0.5 rounded-md">クリックで栽培管理</span>
                    </div>
                    
                    <p class="text-xs text-slate-400 mb-4">
                        農場の各区画を管理できます。作物が成長して100%になると<strong class="text-amber-300">「収穫」</strong>が可能になり、資金を獲得できます！
                    </p>

                    <!-- Interactive Plot Grid (6 slots) -->
                    <div class="grid grid-cols-2 sm:grid-cols-3 gap-3.5" id="farm-plots-container">
                        <!-- Generates via JS dynamically -->
                    </div>

                    <!-- Quick Legend & Farming Tools Guide -->
                    <div class="mt-4 pt-4 border-t border-emerald-950 flex flex-wrap gap-x-4 gap-y-2 justify-between items-center text-xs text-slate-400">
                        <div class="flex items-center gap-1.5">
                            <span class="w-2.5 h-2.5 rounded-full bg-emerald-500"></span>
                            <span>良好</span>
                            <span class="w-2.5 h-2.5 rounded-full bg-blue-500 ml-1.5"></span>
                            <span>要水やり</span>
                            <span class="w-2.5 h-2.5 rounded-full bg-amber-500 ml-1.5"></span>
                            <span>肥料不足</span>
                        </div>
                        <button onclick="boostAllCrops()" class="hover:text-emerald-300 transition-colors flex items-center gap-1">
                            <i class="fa-solid fa-gauge-high"></i>
                            一括液肥投入 (成長ブースト)
                        </button>
                    </div>
                </div>

                <!-- GREENHOUSE CONTROLLER -->
                <div class="glass-panel rounded-2xl p-6 flex flex-col">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="text-xs font-semibold tracking-widest text-emerald-400 uppercase">Automation Control</h3>
                        <span class="text-[10px] bg-indigo-500/10 text-indigo-300 border border-indigo-500/20 px-2 py-0.5 rounded-md">ハウス自動制御</span>
                    </div>
                    
                    <div class="flex flex-col gap-4">
                        <!-- Sprinkler Switch -->
                        <div class="flex items-center justify-between p-3 bg-slate-950/40 rounded-xl border border-emerald-950/40">
                            <div class="flex items-center gap-3">
                                <div class="w-9 h-9 rounded-lg bg-blue-500/10 flex items-center justify-center text-blue-400" id="sprinkler-icon-box">
                                    <i class="fa-solid fa-shower"></i>
                                </div>
                                <div>
                                    <h5 class="text-xs font-semibold text-slate-200">自動スプリンクラー (自動灌水)</h5>
                                    <p class="text-[10px] text-slate-400">土壌水分を自動で適正レベル(70%以上)に維持</p>
                                </div>
                            </div>
                            <button onclick="toggleSystem('sprinkler')" id="btn-sprinkler" class="w-12 h-6 rounded-full bg-slate-700 p-0.5 transition-all duration-300 focus:outline-none relative">
                                <span class="block w-5 h-5 rounded-full bg-white shadow-md transform translate-x-0 transition-transform duration-300" id="toggle-dot-sprinkler"></span>
                            </button>
                        </div>

                        <!-- Ventilation Switch -->
                        <div class="flex items-center justify-between p-3 bg-slate-950/40 rounded-xl border border-emerald-950/40">
                            <div class="flex items-center gap-3">
                                <div class="w-9 h-9 rounded-lg bg-teal-500/10 flex items-center justify-center text-teal-400" id="vent-icon-box">
                                    <i class="fa-solid fa-fan"></i>
                                </div>
                                <div>
                                    <h5 class="text-xs font-semibold text-slate-200">ハウス換気ファン</h5>
                                    <p class="text-[10px] text-slate-400">ハウス内温度上昇を防ぎ、CO2濃度を外気と同等に保つ</p>
                                </div>
                            </div>
                            <button onclick="toggleSystem('ventilation')" id="btn-ventilation" class="w-12 h-6 rounded-full bg-slate-700 p-0.5 transition-all duration-300 focus:outline-none relative">
                                <span class="block w-5 h-5 rounded-full bg-white shadow-md transform translate-x-0 transition-transform duration-300" id="toggle-dot-ventilation"></span>
                            </button>
                        </div>

                        <!-- Shading Screen Switch -->
                        <div class="flex items-center justify-between p-3 bg-slate-950/40 rounded-xl border border-emerald-950/40">
                            <div class="flex items-center gap-3">
                                <div class="w-9 h-9 rounded-lg bg-amber-500/10 flex items-center justify-center text-amber-400" id="shade-icon-box">
                                    <i class="fa-solid fa-cloud-sun"></i>
                                </div>
                                <div>
                                    <h5 class="text-xs font-semibold text-slate-200">自動遮光カーテン</h5>
                                    <p class="text-[10px] text-slate-400">強すぎる日光をカットし、作物の日焼け・萎れを防ぐ</p>
                                </div>
                            </div>
                            <button onclick="toggleSystem('shading')" id="btn-shading" class="w-12 h-6 rounded-full bg-slate-700 p-0.5 transition-all duration-300 focus:outline-none relative">
                                <span class="block w-5 h-5 rounded-full bg-white shadow-md transform translate-x-0 transition-transform duration-300" id="toggle-dot-shading"></span>
                            </button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- MIDDLE COLUMN: Sensors telemetry and real-time visualization -->
            <section class="lg:col-span-4 flex flex-col gap-6">
                
                <!-- TELEMETRY SENSORS PANEL -->
                <div class="glass-panel rounded-2xl p-6 relative overflow-hidden flex flex-col">
                    <h3 class="text-xs font-semibold tracking-widest text-emerald-400 uppercase mb-4">IoT Telemetry Sensors</h3>
                    
                    <!-- Major telemetry visualizers -->
                    <div class="grid grid-cols-2 gap-4 mb-4">
                        <!-- Soil Moisture (Circular SVG) -->
                        <div class="bg-slate-950/50 p-4 rounded-xl border border-emerald-950/40 flex flex-col items-center text-center">
                            <span class="text-[10px] text-slate-400 font-medium">平均土壌水分量</span>
                            <div class="relative w-24 h-24 flex items-center justify-center my-2">
                                <svg class="w-full h-full transform -rotate-90">
                                    <circle cx="48" cy="48" r="38" stroke="rgba(255,255,255,0.03)" stroke-width="5" fill="transparent" />
                                    <circle id="moisture-ring" cx="48" cy="48" r="38" stroke="#3b82f6" stroke-width="5" stroke-dasharray="239" stroke-dashoffset="60" stroke-linecap="round" fill="transparent" class="progress-ring-circle" />
                                </svg>
                                <span id="telemetry-moisture" class="absolute text-sm font-mono font-bold text-blue-400">75%</span>
                            </div>
                            <span class="text-[10px] text-emerald-400 px-2 py-0.5 bg-emerald-500/10 rounded-full" id="moisture-status">適正水分</span>
                        </div>

                        <!-- Greenhouse Temp (Circular SVG) -->
                        <div class="bg-slate-950/50 p-4 rounded-xl border border-emerald-950/40 flex flex-col items-center text-center">
                            <span class="text-[10px] text-slate-400 font-medium">ハウス内平均温度</span>
                            <div class="relative w-24 h-24 flex items-center justify-center my-2">
                                <svg class="w-full h-full transform -rotate-90">
                                    <circle cx="48" cy="48" r="38" stroke="rgba(255,255,255,0.03)" stroke-width="5" fill="transparent" />
                                    <circle id="temp-ring" cx="48" cy="48" r="38" stroke="#f59e0b" stroke-width="5" stroke-dasharray="239" stroke-dashoffset="120" stroke-linecap="round" fill="transparent" class="progress-ring-circle" />
                                </svg>
                                <span id="telemetry-temp" class="absolute text-sm font-mono font-bold text-amber-400">24.5°C</span>
                            </div>
                            <span class="text-[10px] text-emerald-400 px-2 py-0.5 bg-emerald-500/10 rounded-full" id="temp-status">快適温</span>
                        </div>
                    </div>

                    <!-- Linear Metrics Sliders -->
                    <div class="flex flex-col gap-3">
                        <!-- CO2 concentration -->
                        <div class="bg-slate-950/30 p-3 rounded-xl border border-emerald-950/30">
                            <div class="flex justify-between items-center text-xs mb-1">
                                <span class="text-slate-300 flex items-center gap-1.5">
                                    <i class="fa-solid fa-leaf text-emerald-400"></i> CO2濃度 (二酸化炭素)
                                </span>
                                <span id="val-co2" class="font-mono text-emerald-400 font-semibold">415 ppm</span>
                            </div>
                            <div class="w-full bg-slate-900 h-1.5 rounded-full overflow-hidden">
                                <div id="bar-co2" class="bg-emerald-500 h-full w-[41%] transition-all duration-500"></div>
                            </div>
                        </div>

                        <!-- Solar Irradiation -->
                        <div class="bg-slate-950/30 p-3 rounded-xl border border-emerald-950/30">
                            <div class="flex justify-between items-center text-xs mb-1">
                                <span class="text-slate-300 flex items-center gap-1.5">
                                    <i class="fa-solid fa-sun text-yellow-400"></i> 日照強度
                                </span>
                                <span id="val-solar" class="font-mono text-yellow-400 font-semibold">4.8 MJ/㎡</span>
                            </div>
                            <div class="w-full bg-slate-900 h-1.5 rounded-full overflow-hidden">
                                <div id="bar-solar" class="bg-yellow-400 h-full w-[65%] transition-all duration-500"></div>
                            </div>
                        </div>
                    </div>

                    <div class="mt-4 pt-4 border-t border-emerald-950/50">
                        <span class="text-xs text-slate-400">テストコントロール: 簡易日照手動設定</span>
                        <input type="range" id="slider-solar" min="0" max="100" value="65" class="w-full accent-emerald-500 h-1 bg-slate-900 rounded-lg appearance-none cursor-pointer mt-2" oninput="manualSolarOverride(this.value)">
                    </div>
                </div>

                <!-- ENVIRONMENT WEATHER CONTROLLER -->
                <div class="glass-panel rounded-2xl p-6 flex flex-col">
                    <h3 class="text-xs font-semibold tracking-widest text-emerald-400 uppercase mb-2">Weather Environment Simulator</h3>
                    <h4 class="text-base font-bold text-white mb-3">気象環境の疑似コントロール</h4>
                    <p class="text-xs text-slate-400 mb-4">
                        天候を変更して、IoTセンサーやスプリンクラーの自動制御システムがどのように動的追従するか確認しましょう。
                    </p>

                    <div class="grid grid-cols-3 gap-2">
                        <button onclick="setSimulationWeather('sunny')" id="weather-sunny" class="py-2.5 rounded-xl text-xs font-medium border border-amber-500/30 bg-amber-500/10 text-amber-300 flex flex-col items-center gap-1.5 transition-all">
                            <i class="fa-solid fa-sun text-lg animate-spin-slow"></i>
                            晴天・夏日
                        </button>
                        <button onclick="setSimulationWeather('rainy')" id="weather-rainy" class="py-2.5 rounded-xl text-xs font-medium border border-slate-700 bg-slate-800/40 text-slate-400 flex flex-col items-center gap-1.5 transition-all hover:text-white">
                            <i class="fa-solid fa-cloud-showers-water text-lg text-blue-400"></i>
                            恵みの長雨
                        </button>
                        <button onclick="setSimulationWeather('scorching')" id="weather-scorching" class="py-2.5 rounded-xl text-xs font-medium border border-slate-700 bg-slate-800/40 text-slate-400 flex flex-col items-center gap-1.5 transition-all hover:text-white">
                            <i class="fa-solid fa-temperature-arrow-up text-lg text-red-400"></i>
                            記録的猛暑
                        </button>
                    </div>

                    <div class="mt-4 p-3 bg-emerald-950/20 border border-emerald-900/30 rounded-xl text-[11px] text-slate-300">
                        <span class="font-bold text-emerald-400 block mb-1">💡 シミュレーション情報</span>
                        <span>晴天時は水分が緩やかに減少します。「記録的猛暑」では急速に乾燥するため、自動灌水システムまたは手動の散水が極めて重要になります。</span>
                    </div>
                </div>

            </section>

            <!-- RIGHT COLUMN: Market Prices and Crop Planner -->
            <section class="lg:col-span-3 flex flex-col gap-6">
                
                <!-- MARKET PRICE REAL-TIME TRACKER -->
                <div class="glass-panel rounded-2xl p-6 flex flex-col">
                    <div class="flex justify-between items-center mb-1">
                        <h3 class="text-xs font-semibold tracking-widest text-emerald-400 uppercase">Realtime Market Prices</h3>
                        <span class="text-[9px] bg-amber-500/10 text-amber-300 border border-amber-500/20 px-1.5 py-0.5 rounded-md">10秒毎に変動</span>
                    </div>
                    <h4 class="text-base font-bold text-white mb-2">市場取引相場 (kgあたり)</h4>
                    
                    <div class="flex justify-between items-baseline mb-3">
                        <span class="text-[11px] text-slate-400">トマト(ブランド特選)</span>
                        <div>
                            <span class="text-lg font-mono font-bold text-amber-300" id="current-tomato-price">¥1,250</span>
                            <span class="text-[10px] text-emerald-400 font-semibold ml-1" id="tomato-price-trend">
                                <i class="fa-solid fa-arrow-trend-up"></i> +5.4%
                            </span>
                        </div>
                    </div>

                    <!-- Dynamic SVG mini line chart -->
                    <div class="bg-slate-950/50 p-2 rounded-xl border border-emerald-950/50 h-28 flex items-center justify-center relative">
                        <svg class="w-full h-full" id="price-svg-chart" viewBox="0 0 200 100" preserveAspectRatio="none">
                            <defs>
                                <linearGradient id="chart-gradient" x1="0" y1="0" x2="0" y2="1">
                                    <stop offset="0%" stop-color="rgba(245, 158, 11, 0.2)"></stop>
                                    <stop offset="100%" stop-color="rgba(245, 158, 11, 0)"></stop>
                                </linearGradient>
                            </defs>
                            <path id="chart-area-path" d="M 0,100 L 200,100 Z" fill="url(#chart-gradient)"></path>
                            <path id="chart-line-path" d="" fill="none" stroke="#f59e0b" stroke-width="2.5" stroke-linecap="round"></path>
                        </svg>
                        <span class="absolute top-2 left-2 text-[8px] text-slate-500 font-mono">1,500円</span>
                        <span class="absolute bottom-2 left-2 text-[8px] text-slate-500 font-mono">800円</span>
                    </div>

                    <div class="mt-3 text-[11px] text-slate-400 flex justify-between items-center">
                        <span>最高相場のタイミングで収穫が吉！</span>
                        <button onclick="simulateMarketFluctuation()" class="hover:text-emerald-300 text-[10px] text-slate-500 transition-colors">
                            <i class="fa-solid fa-rotate-right"></i> 相場変動
                        </button>
                    </div>
                </div>

                <!-- CROP WORK TRACKER (DAILY TODO) -->
                <div class="glass-panel rounded-2xl p-6 flex flex-col">
                    <h3 class="text-xs font-semibold tracking-widest text-emerald-400 uppercase mb-2">Farming Task Board</h3>
                    <h4 class="text-lg font-bold text-white mb-4">今日の農作業チェック</h4>
                    
                    <div class="flex flex-col gap-3" id="todo-tasks-list">
                        <!-- Dynamic items -->
                    </div>

                    <!-- Simple task entry -->
                    <form onsubmit="addFarmingTask(event)" class="mt-4 flex gap-1.5">
                        <input type="text" id="task-input-field" placeholder="水やり、草引きなど..." required class="flex-1 bg-slate-900/50 border border-emerald-950/50 text-slate-200 text-xs px-3 py-2 rounded-lg focus:outline-none focus:border-emerald-500/60 placeholder:text-slate-600 transition-all">
                        <button type="submit" class="bg-emerald-600 hover:bg-emerald-500 text-white px-3 rounded-lg text-xs transition-all active:scale-95 flex items-center justify-center">
                            <i class="fa-solid fa-plus"></i>
                        </button>
                    </form>
                </div>

                <!-- SYSTEM AUDIO & RESET PANEL -->
                <div class="glass-panel rounded-2xl p-4 flex justify-between items-center text-xs">
                    <span class="text-slate-400">農場シミュレータ初期化</span>
                    <button onclick="resetAgriDashboard()" class="text-red-400/80 hover:text-red-400 flex items-center gap-1.5 transition-colors">
                        <i class="fa-solid fa-trash-can"></i>
                        データをすべてリセット
                    </button>
                </div>

            </section>
        </main>
        
        <!-- FOOTER -->
        <footer class="text-center py-6 text-xs text-emerald-700 tracking-wider">
            <p>© 2026 西条農業 スマートファーム農業アシスト. 大地とテクノロジーを繋ぐ。</p>
        </footer>
    </div>

    <!-- TOAST NOTIFICATION STACK -->
    <div id="toast-container" class="fixed bottom-6 right-6 z-50 flex flex-col gap-2 pointer-events-none">
        <!-- Toast notifications inject here -->
    </div>

    <script>
        // Simulation State
        let state = {
            farm: {
                revenue: 12500,
                totalHarvests: 0
            },
            weather: 'sunny', // 'sunny', 'rainy', 'scorching'
            telemetry: {
                moisture: 75,
                temperature: 24.5,
                co2: 415,
                solar: 4.8
            },
            systems: {
                sprinkler: false,
                ventilation: false,
                shading: false
            },
            // 6 Plots model
            plots: [
                { id: 1, type: 'トマト', emoji: '🍅', stage: '苗木', progress: 30, water: 80, health: 90, lastUpdated: Date.now() },
                { id: 2, type: 'イチゴ', emoji: '🍓', stage: '開花中', progress: 65, water: 70, health: 95, lastUpdated: Date.now() },
                { id: 3, type: '青ネギ', emoji: '🌱', stage: '苗木', progress: 12, water: 50, health: 80, lastUpdated: Date.now() },
                { id: 4, type: 'レタス', emoji: '🥬', stage: '結球期', progress: 85, water: 75, health: 100, lastUpdated: Date.now() },
                { id: 5, type: 'パプリカ', emoji: '🫑', stage: '苗木', progress: 40, water: 65, health: 85, lastUpdated: Date.now() },
                { id: 6, type: 'ナス', emoji: '🍆', stage: '収穫適期', progress: 100, water: 85, health: 98, lastUpdated: Date.now() }
            ],
            // Simulated historic crop prices (10 periods)
            tomatoPrices: [1050, 1100, 1080, 1150, 1200, 1180, 1220, 1250],
            tasks: [
                { id: 1, text: "トマト苗木の水分補給を確認", completed: true },
                { id: 2, text: "イチゴハウスの遮光カーテン適正確認", completed: false },
                { id: 3, text: "収穫作物の仕分け作業", completed: false }
            ]
        };

        const cropTypeConfig = {
            'トマト': { basePrice: 1200, icon: '🍅', growthSpeed: 1.2 },
            'イチゴ': { basePrice: 2000, icon: '🍓', growthSpeed: 0.9 },
            '青ネギ': { basePrice: 400, icon: '🌱', growthSpeed: 1.8 },
            'レタス': { basePrice: 600, icon: '🥬', growthSpeed: 1.5 },
            'パプリカ': { basePrice: 1500, icon: '🫑', growthSpeed: 1.0 },
            'ナス': { basePrice: 800, icon: '🍆', growthSpeed: 1.1 }
        };

        window.onload = function() {
            // Restore from localstorage if available
            const savedState = localStorage.getItem('saijo_agri_state') || localStorage.getItem('agriflow_state');
            if (savedState) {
                try {
                    const parsed = JSON.parse(savedState);
                    if (parsed.farm) state.farm = parsed.farm;
                    if (parsed.plots) state.plots = parsed.plots;
                    if (parsed.tasks) state.tasks = parsed.tasks;
                    if (parsed.systems) state.systems = parsed.systems;
                    if (parsed.weather) state.weather = parsed.weather;
                } catch (e) {
                    console.error("Localstorage recovery failed:", e);
                }
            }

            // Sync visual states for systems
            updateSystemToggleVisuals('sprinkler');
            updateSystemToggleVisuals('ventilation');
            updateSystemToggleVisuals('shading');

            // Render components
            renderPlots();
            renderTasks();
            updateTelemetryDisplay();
            updateFinancials();
            renderPriceChart();
            startWeatherClock();

            // Run main simulator step every 3 seconds
            setInterval(simulateEnvironmentalTicks, 3000);
            
            // Fluctuating market prices every 10 seconds
            setInterval(simulateMarketFluctuation, 10000);

            // Execute first AI diagnosis
            triggerAIConsult();

            // Setup audio engine on single click interaction
            document.addEventListener('click', initAudioOnFirstInteraction, { once: true });
        };

        function saveAgriState() {
            localStorage.setItem('saijo_agri_state', JSON.stringify({
                farm: state.farm,
                plots: state.plots,
                tasks: state.tasks,
                systems: state.systems,
                weather: state.weather
            }));
        }

        // Web Audio Synthesizer sound generator
        let audioCtx = null;
        function initAudioOnFirstInteraction() {
            if (audioCtx) return;
            try {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            } catch (err) {
                console.error("Audio Context initialization failed:", err);
            }
        }

        // Generate dynamic organic synthesizer alerts and chimes
        function playAgriSound(type) {
            if (!audioCtx) return;
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }

            const now = audioCtx.currentTime;
            
            if (type === 'harvest') {
                // Happy high pitch arpeggio chime
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'triangle';
                
                // Note sequence: E5 -> G5 -> C6
                osc.frequency.setValueAtTime(659.25, now); // E5
                osc.frequency.setValueAtTime(783.99, now + 0.1); // G5
                osc.frequency.setValueAtTime(1046.50, now + 0.2); // C6
                
                gain.gain.setValueAtTime(0, now);
                gain.gain.linearRampToValueAtTime(0.25, now + 0.05);
                gain.gain.exponentialRampToValueAtTime(0.0001, now + 0.6);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.6);

            } else if (type === 'water') {
                // Soft liquid pop sound
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                
                osc.frequency.setValueAtTime(150, now);
                osc.frequency.exponentialRampToValueAtTime(800, now + 0.15);
                
                gain.gain.setValueAtTime(0, now);
                gain.gain.linearRampToValueAtTime(0.15, now + 0.02);
                gain.gain.exponentialRampToValueAtTime(0.0001, now + 0.2);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.25);

            } else if (type === 'fertilize') {
                // Sparkly frequency shift chime
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                
                osc.frequency.setValueAtTime(880, now);
                osc.frequency.linearRampToValueAtTime(1760, now + 0.3);
                
                gain.gain.setValueAtTime(0, now);
                gain.gain.linearRampToValueAtTime(0.1, now + 0.05);
                gain.gain.exponentialRampToValueAtTime(0.0001, now + 0.35);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.4);

            } else if (type === 'warning') {
                // Gentle low pulse caution chime
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(220, now);
                
                gain.gain.setValueAtTime(0, now);
                gain.gain.linearRampToValueAtTime(0.2, now + 0.1);
                gain.gain.linearRampToValueAtTime(0, now + 0.4);
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.5);
            }
        }

        // Weather Clock ticker
        function startWeatherClock() {
            const updateClock = () => {
                const now = new Date();
                const pad = (n) => String(n).padStart(2, '0');
                document.getElementById('live-clock').textContent = 
                    `${pad(now.getHours())}:${pad(now.getMinutes())}:${pad(now.getSeconds())}`;
            };
            updateClock();
            setInterval(updateClock, 1000);
        }

        function showAgriToast(message, type = 'emerald') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            
            toast.className = `glass-panel text-white text-xs px-4 py-3 rounded-xl flex items-center gap-2.5 shadow-2xl transition-all duration-300 transform translate-y-3 opacity-0 pointer-events-auto border-l-4 border-l-emerald-500`;
            
            let icon = 'fa-circle-info';
            if (type === 'success') {
                toast.className = toast.className.replace('border-l-emerald-500', 'border-l-amber-500');
                icon = 'fa-basket-shopping';
            } else if (type === 'warning') {
                toast.className = toast.className.replace('border-l-emerald-500', 'border-l-red-500');
                icon = 'fa-triangle-exclamation';
                playAgriSound('warning');
            } else if (type === 'blue') {
                toast.className = toast.className.replace('border-l-emerald-500', 'border-l-blue-400');
                icon = 'fa-shower';
            }
            
            toast.innerHTML = `
                <i class="fa-solid ${icon} text-base"></i>
                <div class="font-medium">${message}</div>
            `;
            
            container.appendChild(toast);
            
            setTimeout(() => {
                toast.classList.remove('translate-y-3', 'opacity-0');
            }, 50);
            
            setTimeout(() => {
                toast.classList.add('translate-y-3', 'opacity-0');
                setTimeout(() => { toast.remove(); }, 300);
            }, 4000);
        }

        // Render Farming Interactive Plots Grid
        function renderPlots() {
            const container = document.getElementById('farm-plots-container');
            container.innerHTML = '';

            state.plots.forEach(plot => {
                const isReady = plot.progress >= 100;
                const progressPercent = Math.min(plot.progress, 100);
                
                // Color status logic based on water/health
                let statusColor = 'border-emerald-900/40 bg-emerald-950/20';
                if (plot.water < 40) {
                    statusColor = 'border-blue-900/60 bg-blue-950/20 shadow-inner shadow-blue-500/10';
                } else if (plot.health < 60) {
                    statusColor = 'border-amber-900/60 bg-amber-950/20';
                }

                const card = document.createElement('div');
                card.className = `glass-panel rounded-xl p-3 flex flex-col justify-between border ${statusColor} relative group overflow-hidden`;

                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <span class="text-xs font-semibold text-slate-300 flex items-center gap-1">
                            <span class="text-lg">${plot.emoji}</span>
                            ${plot.type}
                        </span>
                        <span class="text-[9px] text-slate-500 font-mono">区画 #${plot.id}</span>
                    </div>

                    <div class="my-3 text-center">
                        <div class="text-xs text-slate-400">${plot.stage}</div>
                        <div class="text-xl font-bold font-mono tracking-tight text-emerald-300 mt-0.5">${progressPercent}%</div>
                        <div class="w-full bg-slate-900/80 h-1.5 rounded-full overflow-hidden mt-1.5">
                            <div class="bg-gradient-to-r from-emerald-500 to-amber-400 h-full transition-all duration-500" style="width: ${progressPercent}%"></div>
                        </div>
                    </div>

                    <!-- Water & Nutrition micro stats -->
                    <div class="grid grid-cols-2 gap-1.5 text-[9px] text-slate-400 mb-3.5 border-t border-slate-900/50 pt-2">
                        <span class="flex items-center gap-1">
                            <i class="fa-solid fa-droplet text-blue-400"></i> ${plot.water}%
                        </span>
                        <span class="flex items-center gap-1">
                            <i class="fa-solid fa-heart text-rose-400"></i> ${plot.health}%
                        </span>
                    </div>

                    <!-- Tool actions overlay -->
                    <div class="flex gap-1.5 justify-between">
                        ${isReady ? 
                            `<button onclick="harvestPlot(${plot.id})" class="flex-1 text-[10px] bg-amber-500 hover:bg-amber-600 text-slate-950 font-bold py-1 px-2 rounded-md transition-all active:scale-95 flex items-center justify-center gap-1 animate-pulse">
                                <i class="fa-solid fa-basket-shopping"></i> 収穫
                            </button>` : 
                            `<button onclick="waterPlot(${plot.id})" class="flex-1 text-[9px] bg-blue-500/15 hover:bg-blue-500/35 text-blue-300 border border-blue-500/25 py-1 px-1 rounded-md transition-all active:scale-95 flex items-center justify-center gap-1">
                                <i class="fa-solid fa-faucet-drip"></i> 水
                            </button>
                            <button onclick="fertilizePlot(${plot.id})" class="flex-1 text-[9px] bg-emerald-500/15 hover:bg-emerald-500/35 text-emerald-300 border border-emerald-500/25 py-1 px-1 rounded-md transition-all active:scale-95 flex items-center justify-center gap-1">
                                <i class="fa-solid fa-vial"></i> 液肥
                            </button>`
                        }
                    </div>
                `;

                container.appendChild(card);
            });
        }

        // Action: Water a single plot
        function waterPlot(id) {
            state.plots = state.plots.map(p => {
                if (p.id === id) {
                    p.water = Math.min(p.water + 30, 100);
                    p.health = Math.min(p.health + 5, 100);
                    showAgriToast(`区画#${id} (${p.type}) に水やりを行いました。`, 'blue');
                    playAgriSound('water');
                }
                return p;
            });
            saveAgriState();
            renderPlots();
        }

        // Action: Fertilize a single plot
        function fertilizePlot(id) {
            state.plots = state.plots.map(p => {
                if (p.id === id) {
                    p.health = Math.min(p.health + 25, 100);
                    // Boost progress slightly too
                    p.progress = Math.min(p.progress + 10, 100);
                    showAgriToast(`区画#${id} (${p.type}) に良質な有機液肥を散布しました。🌱`, 'emerald');
                    playAgriSound('fertilize');
                }
                return p;
            });
            saveAgriState();
            renderPlots();
        }

        // Action: Harvest a matured crop
        function harvestPlot(id) {
            let harvestedCrop = null;
            let revenueGain = 0;

            state.plots = state.plots.map(p => {
                if (p.id === id && p.progress >= 100) {
                    harvestedCrop = p;
                    // Tomato dynamic price vs generic base
                    let basePrice = cropTypeConfig[p.type]?.basePrice || 1000;
                    if (p.type === 'トマト') {
                        // Tie directly to live ticker
                        basePrice = state.tomatoPrices[state.tomatoPrices.length - 1];
                    }
                    
                    // Modify payout based on health factor
                    revenueGain = Math.round(basePrice * (p.health / 100));
                    
                    // Reset to initial planting state
                    p.progress = 0;
                    p.water = 60;
                    p.health = 80;
                    p.stage = '苗木';
                }
                return p;
            });

            if (harvestedCrop) {
                state.farm.revenue += revenueGain;
                state.farm.totalHarvests++;
                saveAgriState();
                
                updateFinancials();
                renderPlots();
                playAgriSound('harvest');

                showAgriToast(`${harvestedCrop.type} を無事収穫しました！ +¥${revenueGain.toLocaleString()}`, 'success');
                triggerAIConsult();
            }
        }

        // Boost all crops in farm
        function boostAllCrops() {
            state.plots = state.plots.map(p => {
                p.progress = Math.min(p.progress + 20, 100);
                p.health = Math.min(p.health + 10, 100);
                return p;
            });
            saveAgriState();
            renderPlots();
            playAgriSound('fertilize');
            showAgriToast("全区画へ一斉に成長促進液肥を投入しました！🌟", "success");
        }

        // Set simulated Weather environment
        function setSimulationWeather(weather) {
            state.weather = weather;
            
            // Highlight Buttons CSS
            ['sunny', 'rainy', 'scorching'].forEach(w => {
                const btn = document.getElementById(`weather-${w}`);
                btn.className = "py-2.5 rounded-xl text-xs font-medium border border-slate-700 bg-slate-800/40 text-slate-400 flex flex-col items-center gap-1.5 transition-all hover:text-white";
            });

            const activeBtn = document.getElementById(`weather-${weather}`);
            if (weather === 'sunny') {
                activeBtn.className = "py-2.5 rounded-xl text-xs font-medium border border-amber-500/30 bg-amber-500/10 text-amber-300 flex flex-col items-center gap-1.5 transition-all";
                state.telemetry.solar = 5.2;
                state.telemetry.co2 = 410;
            } else if (weather === 'rainy') {
                activeBtn.className = "py-2.5 rounded-xl text-xs font-medium border border-blue-500/30 bg-blue-500/10 text-blue-300 flex flex-col items-center gap-1.5 transition-all";
                state.telemetry.solar = 1.1;
                state.telemetry.co2 = 425;
            } else if (weather === 'scorching') {
                activeBtn.className = "py-2.5 rounded-xl text-xs font-medium border border-red-500/30 bg-red-500/10 text-red-300 flex flex-col items-center gap-1.5 transition-all";
                state.telemetry.solar = 7.8;
                state.telemetry.co2 = 390;
            }

            saveAgriState();
            triggerAIConsult();
            showAgriToast(`気象環境を「${getWeatherNameJa(weather)}」に切り替えました。`);
        }

        function getWeatherNameJa(w) {
            if (w === 'sunny') return '晴天・夏日';
            if (w === 'rainy') return '恵みの長雨';
            return '記録的猛暑';
        }

        // Toggle automated systems
        function toggleSystem(name) {
            state.systems[name] = !state.systems[name];
            updateSystemToggleVisuals(name);
            saveAgriState();

            const statusStr = state.systems[name] ? "ON (自動稼働)" : "OFF";
            showAgriToast(`${getSystemNameJa(name)}を ${statusStr} に設定しました。`);
            triggerAIConsult();
        }

        function getSystemNameJa(name) {
            if (name === 'sprinkler') return '自動スプリンクラー';
            if (name === 'ventilation') return '換気ファン';
            return '自動遮光カーテン';
        }

        function updateSystemToggleVisuals(name) {
            const btn = document.getElementById(`btn-${name}`);
            const dot = document.getElementById(`toggle-dot-${name}`);
            const iconBox = document.getElementById(`${name}-icon-box`);

            if (state.systems[name]) {
                btn.classList.replace('bg-slate-700', 'bg-emerald-500');
                dot.style.transform = 'translateX(24px)';
                if (iconBox) {
                    iconBox.classList.add('animate-pulse');
                }
            } else {
                btn.classList.replace('bg-emerald-500', 'bg-slate-700');
                dot.style.transform = 'translateX(0px)';
                if (iconBox) {
                    iconBox.classList.remove('animate-pulse');
                }
            }
        }

        // Manual Solar slide override
        function manualSolarOverride(val) {
            const converted = (parseFloat(val) / 10).toFixed(1);
            state.telemetry.solar = parseFloat(converted);
            document.getElementById('val-solar').textContent = `${converted} MJ/㎡`;
            document.getElementById('bar-solar').style.width = `${val}%`;
        }

        // Update financial headers
        function updateFinancials() {
            document.getElementById('farm-revenue').textContent = `¥${state.farm.revenue.toLocaleString()}`;
            document.getElementById('total-harvests').textContent = `${state.farm.totalHarvests}個`;
        }

        // Live Environmental Simulation Engine Ticks
        function simulateEnvironmentalTicks() {
            // Adjust telemetry parameters based on weather & system automation
            let tempTarget = 24.0;
            let humidityDropRate = 2; // general moisture evaporation

            // Weather base profiles
            if (state.weather === 'sunny') {
                tempTarget = 27.5;
                humidityDropRate = 3;
            } else if (state.weather === 'rainy') {
                tempTarget = 19.0;
                humidityDropRate = -5; // ambient rain replenishes
            } else if (state.weather === 'scorching') {
                tempTarget = 36.8;
                humidityDropRate = 7; // severe heat dries out
            }

            // Adjust by automation systems
            if (state.systems.sprinkler) {
                humidityDropRate = -8; // powerful water spraying keeps moisture high
            }
            if (state.systems.ventilation) {
                // keeps temp closer to ambient natural, brings back healthy CO2
                tempTarget = Math.max(tempTarget - 3.5, 22.0);
                state.telemetry.co2 = Math.min(state.telemetry.co2 + 10, 420);
            } else {
                // sealed greenhouse keeps CO2 dropping as plants consume it
                state.telemetry.co2 = Math.max(state.telemetry.co2 - 4, 340);
            }
            if (state.systems.shading && state.weather === 'scorching') {
                // curtain cuts intense UV
                tempTarget = Math.max(tempTarget - 4.5, 28.0);
                state.telemetry.solar = Math.max(state.telemetry.solar - 2.5, 1.5);
            }

            // Smooth approach to targets
            state.telemetry.temperature += (tempTarget - state.telemetry.temperature) * 0.25;
            state.telemetry.temperature = parseFloat(state.telemetry.temperature.toFixed(1));

            // Simulating crop specific growth & soil water depletion
            state.plots = state.plots.map(p => {
                // decrease water based on evaporation rate + unique crop uptake
                let cropWaterUptake = p.type === 'トマト' ? 1.5 : 1.0;
                p.water = Math.max(p.water - Math.round(humidityDropRate * cropWaterUptake), 0);
                p.water = Math.min(p.water, 100);

                // Health behavior based on limits
                if (p.water < 30 || p.water > 95) {
                    p.health = Math.max(p.health - 6, 10);
                } else {
                    p.health = Math.min(p.health + 2, 100);
                }

                // Growth progress if healthy
                if (p.progress < 100 && p.health > 50) {
                    let speedModifier = cropTypeConfig[p.type]?.growthSpeed || 1.0;
                    // faster under decent solar
                    let solarFactor = state.telemetry.solar > 3.0 ? 1.3 : 0.8;
                    p.progress = Math.min(p.progress + Math.round(3 * speedModifier * solarFactor), 100);
                }

                // Stage upgrades
                if (p.progress >= 100) p.stage = '収穫適期';
                else if (p.progress >= 70) p.stage = '結球期 / 着果';
                else if (p.progress >= 40) p.stage = '開花期';
                else p.stage = '苗木';

                return p;
            });

            // Average moisture calculation
            let totalMoist = 0;
            state.plots.forEach(p => totalMoist += p.water);
            state.telemetry.moisture = Math.round(totalMoist / state.plots.length);

            // Redraw
            updateTelemetryDisplay();
            renderPlots();
            saveAgriState();
        }

        function updateTelemetryDisplay() {
            // Soil Moisture Arc
            document.getElementById('telemetry-moisture').textContent = `${state.telemetry.moisture}%`;
            const mCircle = document.getElementById('moisture-ring');
            // Full perimeter is 239. Calc offset
            let mOffset = 239 - (239 * (state.telemetry.moisture / 100));
            mCircle.setAttribute('stroke-dashoffset', mOffset);

            // Soil Moisture Status label
            const mLabel = document.getElementById('moisture-status');
            if (state.telemetry.moisture < 45) {
                mLabel.className = "text-[10px] text-red-400 px-2 py-0.5 bg-red-500/10 rounded-full animate-pulse-slow";
                mLabel.innerHTML = `<i class="fa-solid fa-droplet-slash"></i> 極度乾燥`;
            } else if (state.telemetry.moisture > 85) {
                mLabel.className = "text-[10px] text-blue-400 px-2 py-0.5 bg-blue-500/10 rounded-full";
                mLabel.innerHTML = `多湿ぎみ`;
            } else {
                mLabel.className = "text-[10px] text-emerald-400 px-2 py-0.5 bg-emerald-500/10 rounded-full";
                mLabel.innerHTML = `最適水分`;
            }

            // Temp Arc
            document.getElementById('telemetry-temp').textContent = `${state.telemetry.temperature}°C`;
            const tCircle = document.getElementById('temp-ring');
            // scale 0 to 45 degrees
            let tempPct = Math.min(Math.max(state.telemetry.temperature / 45, 0), 1);
            let tOffset = 239 - (239 * tempPct);
            tCircle.setAttribute('stroke-dashoffset', tOffset);

            const tLabel = document.getElementById('temp-status');
            if (state.telemetry.temperature > 32) {
                tLabel.className = "text-[10px] text-red-400 px-2 py-0.5 bg-red-500/10 rounded-full";
                tLabel.textContent = "高温警戒";
            } else if (state.telemetry.temperature < 15) {
                tLabel.className = "text-[10px] text-sky-300 px-2 py-0.5 bg-sky-500/10 rounded-full";
                tLabel.textContent = "低温管理";
            } else {
                tLabel.className = "text-[10px] text-emerald-400 px-2 py-0.5 bg-emerald-500/10 rounded-full";
                tLabel.textContent = "適正温度";
            }

            // CO2
            document.getElementById('val-co2').textContent = `${state.telemetry.co2} ppm`;
            document.getElementById('bar-co2').style.width = `${(state.telemetry.co2 / 1000) * 100}%`;

            // Solar
            document.getElementById('val-solar').textContent = `${state.telemetry.solar.toFixed(1)} MJ/㎡`;
            document.getElementById('bar-solar').style.width = `${Math.min((state.telemetry.solar / 10) * 100, 100)}%`;
            document.getElementById('slider-solar').value = Math.min((state.telemetry.solar / 10) * 100, 100);
        }

        // Market Price Fluctuations Sim
        function simulateMarketFluctuation() {
            const currentPrice = state.tomatoPrices[state.tomatoPrices.length - 1];
            // random jump between -120 to +140
            const delta = Math.floor(Math.random() * 260) - 120;
            let nextPrice = currentPrice + delta;
            
            // clip price extremes
            nextPrice = Math.max(Math.min(nextPrice, 1500), 800);
            
            state.tomatoPrices.push(nextPrice);
            if (state.tomatoPrices.length > 10) {
                state.tomatoPrices.shift(); // keep 10 nodes
            }

            // Update UI Ticker
            document.getElementById('current-tomato-price').textContent = `¥${nextPrice.toLocaleString()}`;
            
            const pctDelta = (((nextPrice - currentPrice) / currentPrice) * 100).toFixed(1);
            const trendEl = document.getElementById('tomato-price-trend');
            if (delta >= 0) {
                trendEl.className = "text-[10px] text-emerald-400 font-semibold ml-1";
                trendEl.innerHTML = `<i class="fa-solid fa-arrow-trend-up"></i> +${pctDelta}%`;
            } else {
                trendEl.className = "text-[10px] text-red-400 font-semibold ml-1";
                trendEl.innerHTML = `<i class="fa-solid fa-arrow-trend-down"></i> ${pctDelta}%`;
            }

            renderPriceChart();
        }

        // Draw pricing line chart
        function renderPriceChart() {
            const svg = document.getElementById('price-svg-chart');
            const polyline = document.getElementById('chart-line-path');
            const area = document.getElementById('chart-area-path');
            
            const width = 200;
            const height = 100;
            const len = state.tomatoPrices.length;

            if (len === 0) return;

            const minVal = 800;
            const maxVal = 1500;
            const range = maxVal - minVal;

            let points = [];
            for (let i = 0; i < len; i++) {
                let x = (i / (len - 1)) * width;
                // invert y because svg 0 is at top
                let y = height - (((state.tomatoPrices[i] - minVal) / range) * (height - 20)) - 10;
                points.push(`${x.toFixed(1)},${y.toFixed(1)}`);
            }

            const pathString = "M " + points.join(" L ");
            polyline.setAttribute('d', pathString);

            // complete area path below line to bottom
            const areaString = `${pathString} L ${width},${height} L 0,${height} Z`;
            area.setAttribute('d', areaString);
        }

        // AI Advisor logic
        function triggerAIConsult() {
            const advisorBox = document.getElementById('ai-advisor-msg');
            
            let dryPlots = state.plots.filter(p => p.water < 35);
            let unharvestedCount = state.plots.filter(p => p.progress >= 100).length;

            if (dryPlots.length > 0) {
                advisorBox.innerHTML = `警告: <strong class="text-amber-300">区画#${dryPlots[0].id} (${dryPlots[0].type})</strong> が乾燥しています！スプリンクラーの自動制御をONにするか、今すぐ散水ツールを実行してください。`;
                advisorBox.parentElement.parentElement.className = advisorBox.parentElement.parentElement.className.replace('border-l-emerald-500', 'border-l-red-500 animate-pulse');
                return;
            }

            if (unharvestedCount > 0) {
                advisorBox.innerHTML = `市場市況をチェック：現在、<strong class="text-amber-300">${unharvestedCount}個の作物</strong>が収穫の最盛期を迎えています。高値相場に合わせて市場出荷しましょう！`;
                advisorBox.parentElement.parentElement.className = advisorBox.parentElement.parentElement.className.replace('border-l-red-500', 'border-l-amber-500').replace('animate-pulse', '');
                return;
            }

            if (state.weather === 'scorching' && !state.systems.shading) {
                advisorBox.innerHTML = `猛暑警告：強い直射日光は作物の育成に悪影響を及ぼします。<strong>自動遮光カーテン</strong>をオンにすることを推奨します。`;
                advisorBox.parentElement.parentElement.className = advisorBox.parentElement.parentElement.className.replace('border-l-red-500', 'border-l-amber-500');
                return;
            }

            // Normal optimal states
            const tips = [
                "センサー監視稼働中：非常に健康な土壌状態が維持されています。AI灌水と相まって高品質な作物が実っています。",
                "定期報告：ハウス内のCO2濃度を最適に保つため、換気ファンを定期的に自動稼働しています。",
                "アドバイス：市場価格は10秒ごとにリアルタイム変動します。価格変動グラフを意識した計画出荷を推奨します。"
            ];
            advisorBox.textContent = tips[Math.floor(Math.random() * tips.length)];
            advisorBox.parentElement.parentElement.className = "glass-panel rounded-2xl p-4 flex flex-col sm:flex-row items-center justify-between gap-4 border-l-4 border-l-emerald-500 transition-all";
        }

        // Tasks checklist logic
        function renderTasks() {
            const container = document.getElementById('todo-tasks-list');
            container.innerHTML = '';

            if (state.tasks.length === 0) {
                container.innerHTML = `<div class="text-slate-500 text-center py-4 text-xs">今日の作業は完了しました！</div>`;
                return;
            }

            state.tasks.forEach(t => {
                const item = document.createElement('div');
                item.className = `flex items-center justify-between p-2.5 rounded-xl border transition-all ${t.completed ? 'bg-slate-900/30 border-slate-900/40 opacity-50' : 'bg-slate-950/40 border-emerald-950/40 hover:bg-emerald-950/10'}`;

                item.innerHTML = `
                    <div class="flex items-center gap-2.5 cursor-pointer flex-1" onclick="toggleTaskCompletion(${t.id})">
                        <div class="w-4 h-4 rounded flex items-center justify-center border transition-all ${t.completed ? 'bg-emerald-500/20 border-emerald-400 text-emerald-400' : 'border-slate-600 hover:border-emerald-400 text-transparent'}">
                            <i class="fa-solid fa-check text-[9px] ${t.completed ? '' : 'hidden'}"></i>
                        </div>
                        <span class="text-xs text-slate-200 ${t.completed ? 'line-through text-slate-500' : ''}">${escapeHTML(t.text)}</span>
                    </div>
                    <button onclick="deleteFarmingTask(${t.id})" class="text-slate-600 hover:text-red-400 text-xs px-2 transition-colors">
                        <i class="fa-solid fa-trash-can"></i>
                    </button>
                `;

                container.appendChild(item);
            });
        }

        function addFarmingTask(event) {
            event.preventDefault();
            const input = document.getElementById('task-input-field');
            const text = input.value.trim();
            if (!text) return;

            const task = {
                id: Date.now(),
                text: text,
                completed: false
            };

            state.tasks.push(task);
            input.value = '';
            saveAgriState();
            renderTasks();
            showAgriToast("タスクを追加しました。");
        }

        function toggleTaskCompletion(id) {
            state.tasks = state.tasks.map(t => {
                if (t.id === id) {
                    t.completed = !t.completed;
                }
                return t;
            });
            saveAgriState();
            renderTasks();
        }

        function deleteFarmingTask(id) {
            state.tasks = state.tasks.filter(t => t.id !== id);
            saveAgriState();
            renderTasks();
        }

        // Reset Agriflow Simulator dashboard completely
        function resetAgriDashboard() {
            state.farm = {
                revenue: 12500,
                totalHarvests: 0
            };
            state.weather = 'sunny';
            state.systems = {
                sprinkler: false,
                ventilation: false,
                shading: false
            };
            state.plots = [
                { id: 1, type: 'トマト', emoji: '🍅', stage: '苗木', progress: 30, water: 80, health: 90, lastUpdated: Date.now() },
                { id: 2, type: 'イチゴ', emoji: '🍓', stage: '開花中', progress: 65, water: 70, health: 95, lastUpdated: Date.now() },
                { id: 3, type: '青ネギ', emoji: '🌱', stage: '苗木', progress: 12, water: 50, health: 80, lastUpdated: Date.now() },
                { id: 4, type: 'レタス', emoji: '🥬', stage: '結球期', progress: 85, water: 75, health: 100, lastUpdated: Date.now() },
                { id: 5, type: 'パプリカ', emoji: '🫑', stage: '苗木', progress: 40, water: 65, health: 85, lastUpdated: Date.now() },
                { id: 6, type: 'ナス', emoji: '🍆', stage: '収穫適期', progress: 100, water: 85, health: 98, lastUpdated: Date.now() }
            ];
            state.tasks = [
                { id: 1, text: "トマト苗木の水分補給を確認", completed: true },
                { id: 2, text: "イチゴハウスの遮光カーテン適正確認", completed: false },
                { id: 3, text: "収穫作物の仕分け作業", completed: false }
            ];

            updateSystemToggleVisuals('sprinkler');
            updateSystemToggleVisuals('ventilation');
            updateSystemToggleVisuals('shading');

            saveAgriState();
            renderPlots();
            renderTasks();
            updateFinancials();
            triggerAIConsult();
            
            showAgriToast("シミュレーション環境を完全に初期化しました。", "warning");
        }

        // Security Utility
        function escapeHTML(str) {
            return str.replace(/[&<>'"]/g, 
                tag => ({
                    '&': '&amp;',
                    '<': '&lt;',
                    '>': '&gt;',
                    "'": '&#39;',
                    '"': '&quot;'
                }[tag] || tag)
            );
        }
    </script>
</body>
</html>
