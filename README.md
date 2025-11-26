<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>忠義國小六十一週年校慶運動競賽</title>
    <!-- 載入 Tailwind CSS (無需安裝，直接透過 CDN 運作) -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 載入 Font Awesome 專業圖標庫 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;500;700&family=Oswald:wght@400;500;700&family=Playfair+Display:ital,wght@0,600;1,600&display=swap" rel="stylesheet">
    
    <style>
        /* 木質調 Muji 風格配色定義 */
        :root {
            --bg-color: #fdfbf7; /* 暖米白背景 */
            --card-color: #ffffff;
            --text-main: #4a4036; /* 深咖啡色 (取代純黑) */
            --text-sub: #8c7b70; /* 淺棕灰 */
            --wood-dark: #8d6e63; /* 木頭色 */
            --wood-light: #d7ccc8; /* 淺木色 */
            --accent-red: #a94442; /* 復古磚紅 */
        }

        body {
            font-family: 'Noto Sans TC', sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            line-height: 1.6;
            /* 加入細微的雜訊紋理，增加紙質感 */
            background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)' opacity='0.03'/%3E%3C/svg%3E");
        }

        /* 標題字體，帶有一點人文氣息 */
        .font-serif-title {
            font-family: 'Playfair Display', serif;
        }

        /* 運動風格數字字體 */
        .font-sport {
            font-family: 'Oswald', sans-serif;
            letter-spacing: 0.05em;
        }

        /* 柔和的陰影 */
        .shadow-wood {
            box-shadow: 0 10px 40px -10px rgba(74, 64, 54, 0.1);
        }

        /* 按鈕互動效果 */
        .btn-hover {
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .btn-hover:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(141, 110, 99, 0.15);
            filter: brightness(1.1); /* hover時稍微變亮 */
        }
        
        /* 裝飾線條 */
        .wood-divider {
            height: 1px;
            background: linear-gradient(to right, transparent, #d7ccc8, transparent);
        }

        /* 裝飾線條動畫 - 讓線條圖案看起來更生動 */
        .dash-draw {
            stroke-dasharray: 1000;
            stroke-dashoffset: 1000;
            animation: dash 5s linear forwards;
        }
        @keyframes dash {
            to {
                stroke-dashoffset: 0;
            }
        }
    </style>
    <!-- Tailwind Custom Configuration -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        wood: {
                            bg: '#fdfbf7',
                            main: '#4a4036', // 深咖啡
                            secondary: '#8c7b70', // 淺棕
                            accent: '#8d6e63', // 木色裝飾
                            light: '#efebe9', // 米色區塊
                            red: '#a94442', // 磚紅
                        }
                    }
                }
            }
        }
    </script>
</head>
<body class="p-4 md:p-12 min-h-screen flex justify-center items-start">

    <div class="w-full max-w-6xl bg-white shadow-wood rounded-sm overflow-hidden border border-[#eaddcf] relative">
        <!-- 頂部木紋裝飾條 -->
        <div class="h-2 w-full bg-[#8d6e63] opacity-80"></div>

        <!-- 裝飾：右上角跑步線條 -->
        <div class="absolute top-6 right-6 sm:top-10 sm:right-16 opacity-10 pointer-events-none">
             <svg width="120" height="120" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
                <!-- 抽象跑步人形線條 -->
                <path d="M60 160 C 50 140, 80 130, 90 100" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/>
                <path d="M90 100 C 95 80, 85 70, 100 50" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/>
                <path d="M100 50 C 110 55, 120 60, 130 50" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/> <!-- Head/Arm -->
                <circle cx="105" cy="40" r="8" stroke="#4a4036" stroke-width="3"/>
                <path d="M90 100 C 70 110, 60 100, 40 105" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/> <!-- Arm -->
                <path d="M90 100 C 110 120, 120 110, 140 130" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/> <!-- Leg -->
                <!-- 速度線 -->
                <path d="M20 120 L 50 120" stroke="#a94442" stroke-width="2" stroke-dasharray="5,5"/>
                <path d="M30 140 L 55 140" stroke="#a94442" stroke-width="2" stroke-dasharray="5,5"/>
            </svg>
        </div>

        <!-- Header Section -->
        <header class="pt-16 pb-10 px-8 sm:px-16 text-center relative z-10">
            <h1 class="text-4xl sm:text-5xl font-bold tracking-tight text-wood-main mb-3">
                忠義國小<span class="text-wood-red font-serif-title italic mx-2">61st</span>週年校慶運動競賽
            </h1>
            <div class="flex justify-center items-center gap-4 text-wood-secondary text-sm tracking-[0.2em] mt-4">
                <span class="w-8 h-[1px] bg-wood-accent"></span>
                SPORTS DAY GUIDE
                <span class="w-8 h-[1px] bg-wood-accent"></span>
            </div>
        </header>

        <!-- Content Container -->
        <div class="p-8 sm:p-16 pt-6 relative">
            
            <!-- Grid Layout -->
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-12 lg:gap-16">
                
                <!-- Left Column: Timeline (Information) -->
                <div class="lg:col-span-7">
                    <div class="flex items-center mb-8 border-b border-[#eaddcf] pb-4">
                        <i class="fa-regular fa-calendar text-wood-red mr-3 text-xl"></i>
                        <h2 class="text-2xl font-bold text-wood-main tracking-wide">賽程資訊</h2>
                    </div>

                    <!-- Timeline Design -->
                    <div class="relative ml-2 space-y-8 pl-6 border-l-2 border-[#efebe9]">
                        
                        <!-- Item 1 -->
                        <div class="relative group">
                            <span class="absolute -left-[31px] top-2 w-4 h-4 bg-[#fdfbf7] border-2 border-wood-main rounded-full group-hover:bg-wood-red group-hover:border-wood-red transition duration-300"></span>
                            <div class="flex flex-col sm:flex-row sm:items-baseline">
                                <span class="font-sport text-xl font-bold text-wood-main mr-4 tracking-wide">
                                    114/11/25(二) <span class="text-sm text-wood-accent ml-1 font-sans">08:00</span>
                                </span>
                                <span class="text-lg text-wood-main font-medium border-b border-transparent group-hover:border-wood-red transition-all">全員賽跑</span>
                            </div>
                        </div>

                        <!-- Item 2 -->
                        <div class="relative group">
                            <span class="absolute -left-[31px] top-2 w-4 h-4 bg-[#fdfbf7] border-2 border-[#d7ccc8] rounded-full group-hover:border-wood-main transition duration-300"></span>
                            <div class="flex flex-col sm:flex-row sm:items-baseline">
                                <span class="font-sport text-xl font-bold text-wood-secondary mr-4 tracking-wide group-hover:text-wood-main transition">
                                    114/11/28(五) <span class="text-sm text-wood-accent ml-1 font-sans">08:10</span>
                                </span>
                                <span class="text-lg text-wood-secondary font-medium group-hover:text-wood-main transition">忠義紀錄賽 - 預賽</span>
                            </div>
                        </div>

                        <!-- Item 3 -->
                        <div class="relative group">
                            <span class="absolute -left-[31px] top-2 w-4 h-4 bg-[#fdfbf7] border-2 border-[#d7ccc8] rounded-full group-hover:border-wood-main transition duration-300"></span>
                            <div class="flex flex-col sm:flex-row sm:items-baseline">
                                <span class="font-sport text-xl font-bold text-wood-secondary mr-4 tracking-wide group-hover:text-wood-main transition">
                                    114/12/02(二) <span class="text-sm text-wood-accent ml-1 font-sans">08:10</span>
                                </span>
                                <span class="text-lg text-wood-secondary font-medium group-hover:text-wood-main transition">忠義紀錄賽 - 決賽</span>
                            </div>
                        </div>

                        <!-- Item 4 -->
                        <div class="relative group">
                            <span class="absolute -left-[31px] top-2 w-4 h-4 bg-[#fdfbf7] border-2 border-[#d7ccc8] rounded-full group-hover:border-wood-main transition duration-300"></span>
                            <div class="flex flex-col sm:flex-row sm:items-baseline">
                                <span class="font-sport text-xl font-bold text-wood-secondary mr-4 tracking-wide group-hover:text-wood-main transition">
                                    114/12/05(五) <span class="text-sm text-wood-accent ml-1 font-sans">08:00</span>
                                </span>
                                <span class="text-lg text-wood-secondary font-medium group-hover:text-wood-main transition">忠義路跑</span>
                            </div>
                        </div>

                        <!-- Item 5 -->
                        <div class="relative group">
                            <span class="absolute -left-[31px] top-2 w-4 h-4 bg-[#fdfbf7] border-2 border-wood-red rounded-full group-hover:bg-wood-red transition duration-300"></span>
                            <div class="flex flex-col sm:flex-row sm:items-baseline">
                                <span class="font-sport text-xl font-bold text-wood-red mr-4 tracking-wide">
                                    114/12/09(二) <span class="text-sm text-wood-main ml-1 font-sans">08:00</span>
                                </span>
                                <span class="text-lg text-wood-main font-bold">中高年大隊接力</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Right Column: Links (Control Panel) -->
                <div class="lg:col-span-5">
                    <div class="bg-[#faf8f5] p-8 rounded-sm h-full border border-[#efebe9]">
                        <div class="flex items-center mb-6">
                            <i class="fa-solid fa-link text-wood-accent mr-3"></i>
                            <h3 class="text-lg font-bold text-wood-main tracking-wide uppercase">快速通道</h3>
                        </div>

                        <div class="space-y-3">
                             <!-- 1. Schedule Link: Darkest Brown -->
                             <a href="https://docs.google.com/spreadsheets/d/1PTv-vBQNmaz5Qv_iQ3MhMV2P_SBugSnFWBm2jSgRYqs/edit?usp=sharing" target="_blank" class="btn-hover block w-full bg-[#3e2723] text-white text-center py-3 px-6 rounded-sm">
                                <div class="flex items-center justify-center">
                                    <i class="fa-solid fa-calendar-days mr-3 text-wood-light opacity-80"></i>
                                    <span class="font-medium">忠義紀錄賽賽程表</span>
                                </div>
                            </a>

                            <!-- 2. Program Link: Dark Brown -->
                            <a href="https://drive.google.com/file/d/1566M9ibsLrzH_7nHFCkBQQYunDTBHCo_/view?usp=sharing" target="_blank" class="btn-hover block w-full bg-[#4e342e] text-white text-center py-3 px-6 rounded-sm">
                                <div class="flex items-center justify-center">
                                    <i class="fa-solid fa-list-ol mr-3 text-wood-light opacity-80"></i>
                                    <span class="font-medium">節目表及隊形</span>
                                </div>
                            </a>

                            <!-- 3. Gemini Link: Medium Dark Brown -->
                            <a href="https://gemini.google.com/share/287652567e44" target="_blank" class="btn-hover block w-full bg-[#5d4037] text-white text-center py-3 px-6 rounded-sm">
                                <div class="flex items-center justify-center">
                                    <i class="fa-solid fa-robot mr-3 text-wood-light opacity-80"></i>
                                    <span class="font-bold tracking-wide">忠義國小體育六十一週年校慶路跑</span>
                                </div>
                            </a>

                            <!-- 4. Results Link: Medium Brown -->
                            <a href="https://docs.google.com/spreadsheets/d/1EbBJzfaweRRdTrR9oKaV-33ckmUFz_p3F-tTpnifmK0/edit?usp=sharing" target="_blank" class="btn-hover block w-full bg-[#6d4c41] text-white text-center py-3 px-6 rounded-sm">
                                <div class="flex items-center justify-center">
                                    <i class="fa-solid fa-trophy mr-3 text-wood-light opacity-80"></i>
                                    <span class="font-bold">競賽成績專區 (最新資訊)</span>
                                </div>
                            </a>

                            <!-- 5. Presentation: Light Medium Brown -->
                            <a href="https://docs.google.com/presentation/d/1KsDhXPuPH0xcGAlKZO4RU572JOW67XKbvyQnXQ1tWRY/edit?usp=sharing" target="_blank" class="btn-hover block w-full bg-[#795548] text-white text-center py-3 px-6 rounded-sm">
                                <div class="flex items-center justify-center">
                                    <i class="fa-solid fa-chart-simple mr-3 text-wood-light opacity-80"></i>
                                    <span class="font-medium">簡報檔 (競賽規則詳解)</span>
                                </div>
                            </a>

                            <!-- 6. Registration: Light Brown -->
                            <a href="https://reurl.cc/qYxDn3" target="_blank" class="btn-hover block w-full bg-[#8d6e63] text-white text-center py-3 px-6 rounded-sm">
                                <div class="flex items-center justify-center">
                                    <i class="fa-solid fa-pen-to-square mr-3 text-wood-light opacity-80"></i>
                                    <span class="font-medium">報名連結 (點此進入)</span>
                                </div>
                            </a>
                            
                            <!-- 7. Sprint Results: Text Link -->
                            <a href="https://reurl.cc/gnkoLN" target="_blank" class="block w-full text-center py-3 px-6 text-sm text-[#a1887f] hover:text-wood-main transition-colors mt-2">
                                <i class="fa-solid fa-lock mr-2 text-xs"></i>學生短跑成績查詢 (教師參考用)
                            </a>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Divider -->
            <div class="wood-divider my-16"></div>

            <!-- Competition Items Section -->
            <div class="relative">
                 <!-- 裝飾：左下角跑步線條 -->
                 <div class="absolute bottom-0 -left-6 opacity-10 pointer-events-none hidden lg:block">
                    <svg width="150" height="150" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
                       <path d="M40 160 C 50 140, 70 150, 90 130" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/>
                       <path d="M90 130 C 95 110, 85 100, 100 80" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/>
                       <circle cx="105" cy="70" r="8" stroke="#4a4036" stroke-width="3"/>
                       <path d="M100 80 C 120 90, 130 80, 150 70" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/> <!-- Arm -->
                       <path d="M100 80 C 80 90, 70 80, 50 90" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/> <!-- Arm -->
                       <path d="M90 130 C 110 150, 120 140, 140 160" stroke="#4a4036" stroke-width="3" stroke-linecap="round"/> <!-- Leg -->
                       <!-- 裝飾圓點 -->
                       <circle cx="160" cy="50" r="2" fill="#a94442"/>
                       <circle cx="170" cy="60" r="2" fill="#a94442"/>
                       <circle cx="180" cy="40" r="2" fill="#a94442"/>
                   </svg>
               </div>

                <div class="flex flex-col items-center mb-12">
                    <h2 class="text-3xl font-bold text-wood-main tracking-widest uppercase mb-2">競賽項目</h2>
                    <p class="text-wood-accent text-sm mt-1 font-serif-title italic">Events Overview</p>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-x-12 gap-y-12 relative z-10">
                    
                    <!-- Event 1 -->
                    <div class="group bg-[#faf8f5] p-6 rounded-sm border border-transparent hover:border-[#eaddcf] transition duration-500">
                        <div class="flex items-baseline border-b border-[#eaddcf] pb-3 mb-4">
                            <span class="font-sport text-4xl text-[#d7ccc8] font-bold mr-4 group-hover:text-wood-main transition duration-500">01</span>
                            <h3 class="text-xl font-bold text-wood-main">全員賽跑</h3>
                        </div>
                        <ul class="space-y-3 text-wood-secondary">
                            <li class="flex items-center"><i class="fa-regular fa-clock w-6 text-center text-sm text-wood-red mr-2"></i> 114/11/25 (二) 08:00</li>
                            <li class="flex items-center"><i class="fa-solid fa-location-dot w-6 text-center text-sm text-wood-red mr-2"></i> 外操場</li>
                            <li class="flex items-center"><i class="fa-solid fa-users w-6 text-center text-sm text-wood-red mr-2"></i> 全校性活動</li>
                        </ul>
                    </div>

                    <!-- Event 2 -->
                    <div class="group bg-[#faf8f5] p-6 rounded-sm border border-transparent hover:border-[#eaddcf] transition duration-500">
                        <div class="flex items-baseline border-b border-[#eaddcf] pb-3 mb-4">
                            <span class="font-sport text-4xl text-[#d7ccc8] font-bold mr-4 group-hover:text-wood-main transition duration-500">02</span>
                            <h3 class="text-xl font-bold text-wood-main">路跑</h3>
                        </div>
                        <ul class="space-y-3 text-wood-secondary">
                            <li class="flex items-center"><i class="fa-regular fa-clock w-6 text-center text-sm text-wood-red mr-2"></i> 114/12/05 (五) 08:00</li>
                            <li class="flex items-center"><i class="fa-solid fa-location-dot w-6 text-center text-sm text-wood-red mr-2"></i> 校園及忠義國小周邊馬路</li>
                            <li class="flex items-start">
                                <i class="fa-solid fa-triangle-exclamation w-6 text-center text-sm text-wood-red mr-2 mt-1"></i> 
                                <span class="bg-[#eceff1] text-wood-main text-xs px-2 py-1 rounded">全校性 (需繳交同意書)</span>
                            </li>
                        </ul>
                    </div>

                    <!-- Event 3 -->
                    <div class="group bg-[#faf8f5] p-6 rounded-sm border border-transparent hover:border-[#eaddcf] transition duration-500">
                        <div class="flex items-baseline border-b border-[#eaddcf] pb-3 mb-4">
                            <span class="font-sport text-4xl text-[#d7ccc8] font-bold mr-4 group-hover:text-wood-main transition duration-500">03</span>
                            <h3 class="text-xl font-bold text-wood-main">忠義記錄賽 (個人賽)</h3>
                        </div>
                        <ul class="space-y-3 text-wood-secondary">
                            <li class="flex items-center"><i class="fa-solid fa-location-dot w-6 text-center text-sm text-wood-red mr-2"></i> 外操場</li>
                            <li class="flex items-center"><i class="fa-solid fa-play w-6 text-center text-sm text-wood-red mr-2"></i> 預賽: 11/28 (五) 08:10</li>
                            <li class="flex items-center"><i class="fa-solid fa-flag-checkered w-6 text-center text-sm text-wood-red mr-2"></i> 決賽: 12/02 (二) 08:10</li>
                        </ul>
                    </div>

                    <!-- Event 4 -->
                    <div class="group bg-[#faf8f5] p-6 rounded-sm border border-transparent hover:border-[#eaddcf] transition duration-500">
                        <div class="flex items-baseline border-b border-[#eaddcf] pb-3 mb-4">
                            <span class="font-sport text-4xl text-[#d7ccc8] font-bold mr-4 group-hover:text-wood-main transition duration-500">04</span>
                            <h3 class="text-xl font-bold text-wood-main">大隊接力</h3>
                        </div>
                        <div class="space-y-3 text-wood-secondary mb-4">
                            <div class="flex items-center"><i class="fa-regular fa-clock w-6 text-center text-sm text-wood-red mr-2"></i> 114/12/09 (二) 08:00</div>
                            <div class="flex items-center"><i class="fa-solid fa-location-dot w-6 text-center text-sm text-wood-red mr-2"></i> 外操場 (中高年級)</div>
                        </div>
                        
                        <!-- Grid Table for Teams -->
                        <div class="grid grid-cols-2 gap-2 text-xs">
                            <div class="bg-white border border-[#eaddcf] p-2 text-center text-wood-secondary">三年級 (18棒)</div>
                            <div class="bg-white border border-[#eaddcf] p-2 text-center text-wood-secondary">四年級 (16棒)</div>
                            <div class="bg-white border border-[#eaddcf] p-2 text-center text-wood-secondary">五年級 (22棒)</div>
                            <div class="bg-white border border-[#eaddcf] p-2 text-center text-wood-secondary">六年級 (14棒)</div>
                        </div>
                    </div>

                </div>
            </div>
        </div>

        <!-- Minimalist Footer -->
        <footer class="bg-[#faf8f5] py-8 text-center border-t border-[#eaddcf]">
            <p class="text-wood-main font-bold text-sm tracking-widest uppercase mb-2">Zhongyi Elementary School</p>
            <p class="text-[#a1887f] text-xs font-light">忠義國小體育組 © 2025</p>
        </footer>
    </div>

</body>
</html>
