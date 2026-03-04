<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>忠義國小樂樂棒賽事整合平台</title>
    
    <!-- 引入 Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- 引入 Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>

    <!-- Tailwind 自訂配置 -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        zinc: {
                            50: '#fafafa', 100: '#f4f4f5', 200: '#e4e4e7', 300: '#d4d4d8', 400: '#a1a1aa',
                            500: '#71717a', 600: '#52525b', 700: '#3f3f46', 800: '#27272a', 900: '#18181b', 950: '#09090b',
                        },
                        pink: {
                            400: '#f472b6', 500: '#ec4899', 600: '#db2777', 900: '#831843',
                        }
                    },
                    animation: {
                        'shake': 'shake 0.5s ease-in-out',
                        'fade-in': 'fadeIn 0.3s ease-out',
                        'slide-in-up': 'slideInUp 0.4s ease-out',
                    },
                    keyframes: {
                        shake: {
                            '0%, 100%': { transform: 'translateX(0)' },
                            '10%, 30%, 50%, 70%, 90%': { transform: 'translateX(-5px)' },
                            '20%, 40%, 60%, 80%': { transform: 'translateX(5px)' },
                        },
                        fadeIn: {
                            '0%': { opacity: '0' },
                            '100%': { opacity: '1' },
                        },
                        slideInUp: {
                            '0%': { opacity: '0', transform: 'translateY(10px)' },
                            '100%': { opacity: '1', transform: 'translateY(0)' },
                        }
                    }
                }
            }
        }
    </script>

    <style>
        .tab-content { display: none; }
        .tab-content.active { display: block; animation: fadeIn 0.3s ease-out forwards; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

        /* Replit 滿版優化 */
        html, body {
            height: 100%;
            width: 100%;
            margin: 0;
            padding: 0;
            background-color: black; 
            color: #e4e4e7; 
            font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif;
            -webkit-font-smoothing: antialiased;
        }
        ::selection { background-color: #ec4899; color: white; }
        body.modal-open { overflow: hidden; }

        #main-container {
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }
    </style>
</head>
<body>
    <div id="main-container" class="pb-20 relative w-full">
        <!-- 頂部導航列 -->
        <header class="relative bg-zinc-900 border-b-4 border-pink-500 overflow-hidden shrink-0">
            <div class="absolute inset-0 opacity-10 pointer-events-none" 
                 style="background-image: repeating-linear-gradient(45deg, #000 25%, transparent 25%, transparent 75%, #000 75%, #000), repeating-linear-gradient(45deg, #000 25%, #222 25%, #222 75%, #000 75%, #000); background-position: 0 0, 10px 10px; background-size: 20px 20px;">
            </div>
            <div class="w-full px-4 sm:px-8 md:px-12 py-8 sm:py-12 relative z-10 flex flex-col items-start justify-between max-w-7xl mx-auto">
                <div>
                    <div class="flex flex-wrap items-center gap-2 sm:space-x-3 mb-2 sm:mb-3">
                        <span class="bg-pink-600 text-white text-[10px] sm:text-xs font-black uppercase tracking-widest px-2 py-1 transform -skew-x-12 whitespace-nowrap">
                            Zhongyi Elementary
                        </span>
                        <span class="text-zinc-500 text-[10px] sm:text-sm tracking-widest uppercase">Dept. of Physical Education</span>
                    </div>
                    <h1 class="text-2xl sm:text-4xl md:text-5xl font-black text-white tracking-tight uppercase leading-tight">
                        忠義國小 114學年度 <br class="sm:hidden"><span class="text-transparent bg-clip-text bg-gradient-to-r from-pink-500 to-red-500">樂樂棒球</span> 班際體育競賽
                    </h1>
                </div>
            </div>
        </header>

        <!-- 導覽標籤頁 -->
        <nav class="sticky top-0 z-40 bg-black/95 backdrop-blur-md border-b border-zinc-800 shrink-0 w-full shadow-lg">
            <div class="w-full max-w-7xl mx-auto px-2 sm:px-8 md:px-12 flex overflow-x-auto no-scrollbar scroll-smooth" id="nav-tabs">
                <button onclick="switchTab('rules')" class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-pink-500 border-pink-500 bg-pink-500/10 text-xs sm:text-sm" data-target="rules">
                    <i data-lucide="shield-alert" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i><span>競賽規則</span>
                </button>
                <button onclick="switchTab('schedule')" class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm" data-target="schedule">
                    <i data-lucide="calendar-days" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i><span>賽程總覽</span>
                </button>
                <button onclick="switchTab('tactics')" class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm" data-target="tactics">
                    <i data-lucide="trophy" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i><span>導師戰術板</span>
                </button>
                <button onclick="switchTab('lineup')" class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm" data-target="lineup">
                    <i data-lucide="list-ordered" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i><span>打線安排區</span>
                </button>
                <button onclick="switchTab('results')" class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm" data-target="results">
                    <i data-lucide="file-text" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i><span>成績公告區</span>
                </button>
            </div>
        </nav>

        <!-- 主要內容區 -->
        <main class="w-full max-w-7xl mx-auto px-4 sm:px-8 md:px-12 mt-6 sm:mt-10 flex-grow">

            <!-- 競賽規則 Tab -->
            <div id="tab-rules" class="tab-content active space-y-8 sm:space-y-12">
                <div class="flex justify-end mb-2 sm:mb-4">
                    <a href="https://drive.google.com/file/d/1GJLKIs04G4LRvf1omVmpTVhfwrC3nu7Z/view?usp=drive_link" target="_blank" rel="noopener noreferrer" class="flex items-center space-x-2 text-pink-500 hover:text-pink-400 font-bold tracking-wider transition-colors border border-pink-500/50 hover:border-pink-500 px-3 sm:px-4 py-2 rounded-sm bg-pink-500/10 text-xs sm:text-sm">
                        <i data-lucide="external-link" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i>
                        <span>檢視競賽總規則</span>
                    </a>
                </div>
                
                <section>
                    <h2 class="text-2xl sm:text-3xl font-black text-white uppercase tracking-wide flex items-center mb-4 sm:mb-6">
                        <span class="w-1.5 sm:w-2 h-6 sm:h-8 bg-pink-500 mr-2 sm:mr-3 transform -skew-x-12"></span>
                        賽事規則簡述
                    </h2>
                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-4 sm:gap-6">
                        <!-- 圖片區塊 -->
                        <div class="lg:col-span-1 flex flex-col">
                            <div class="bg-zinc-900 border border-zinc-800 p-2 sm:p-4 w-full h-full flex flex-col justify-center items-center shadow-xl group hover:border-pink-500/50 transition-colors rounded-lg overflow-hidden cursor-pointer" onclick="openImageModal()" title="點擊放大觀看場地圖">
                                <div class="w-full relative flex items-center justify-center bg-black/50 rounded-md p-2 h-full overflow-hidden min-h-[200px] sm:min-h-[auto]">
                                    <img src="https://lh3.googleusercontent.com/d/1w3WT5IjYvBD6BNSiOQlogWvd-HwJUTkG" alt="樂樂棒球宣傳圖" class="object-contain w-full h-auto max-h-[300px] sm:max-h-[400px] opacity-90 group-hover:opacity-100 transition-all duration-500 transform group-hover:scale-105" onerror="this.onerror=null; this.parentElement.innerHTML='<div class=\'flex flex-col items-center justify-center text-zinc-600 p-8 text-center h-full w-full border border-dashed border-zinc-700 m-2 sm:m-4 rounded\'><i data-lucide=\'camera\' class=\'mb-4 w-10 h-10 sm:w-12 sm:h-12 text-pink-500/50\'></i><span class=\'text-xs sm:text-sm font-bold uppercase tracking-widest text-zinc-500\'>賽事形象圖</span></div>'; lucide.createIcons();">
                                    <div class="absolute inset-0 bg-black/0 group-hover:bg-black/20 transition-colors flex items-center justify-center">
                                        <div class="bg-pink-600 text-white rounded-full p-2 sm:p-3 opacity-0 group-hover:opacity-100 transform scale-50 group-hover:scale-100 transition-all duration-300">
                                            <i data-lucide="plus" class="w-5 h-5 sm:w-6 sm:h-6"></i>
                                        </div>
                                    </div>
                                    <div class="absolute -bottom-2 sm:-bottom-3 -right-2 sm:-right-3 z-10 pointer-events-none">
                                        <span class="text-white font-black uppercase tracking-widest text-[10px] sm:text-xs bg-pink-600 px-2 sm:px-3 py-1 sm:py-1.5 transform -skew-x-12 inline-block shadow-lg">2026 EDITION</span>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- 規則卡片區塊 -->
                        <div class="lg:col-span-2 grid grid-cols-1 md:grid-cols-2 gap-4 sm:gap-6">
                            <!-- 進攻與跑壘安全 -->
                            <div class="bg-zinc-900 border border-pink-500/30 p-4 sm:p-6 md:p-8 relative hover:border-zinc-600 transition-colors duration-300 rounded-lg">
                                <h3 class="text-lg sm:text-xl font-black text-white mb-4 sm:mb-6 tracking-wider uppercase border-b border-zinc-800 pb-3 sm:pb-4 flex items-center">
                                    <i data-lucide="target" class="w-5 h-5 sm:w-6 sm:h-6 text-pink-500 mr-2"></i>進攻與跑壘安全
                                </h3>
                                <ul class="space-y-3 sm:space-y-4 text-zinc-300 text-sm sm:text-base">
                                    <li class="flex items-start bg-zinc-800/30 p-2 sm:p-3 rounded-md border-l-2 border-pink-500/50">
                                        <i data-lucide="target" class="w-4 h-4 sm:w-5 sm:h-5 text-pink-500 shrink-0 mt-0.5 mr-2 sm:mr-3"></i>
                                        <div><span class="font-bold text-white">1好球：</span>揮棒落空、界外球、或滾地球未出5公尺線。<br><span class="text-pink-400 font-bold">累計3好球即出局。</span></div>
                                    </li>
                                    <li class="flex items-start bg-zinc-800/30 p-2 sm:p-3 rounded-md border-l-2 border-pink-500/50">
                                        <i data-lucide="map-pin" class="w-4 h-4 sm:w-5 sm:h-5 text-pink-500 shrink-0 mt-0.5 mr-2 sm:mr-3"></i>
                                        <div><span class="font-bold text-white">壘包識別：</span><span class="bg-white text-black px-1 font-bold rounded-sm">白色</span>為防守壘包；<span class="bg-orange-500 text-white px-1 font-bold rounded-sm">橘色</span>為跑壘壘包。</div>
                                    </li>
                                    <li class="flex items-start bg-zinc-800/30 p-2 sm:p-3 rounded-md border-l-2 border-pink-500/50">
                                        <i data-lucide="zap" class="w-4 h-4 sm:w-5 sm:h-5 text-pink-500 shrink-0 mt-0.5 mr-2 sm:mr-3"></i>
                                        <div><span class="font-bold text-white">安全上壘：</span>踩過橘色壘包且比球快到（允許衝過壘包）。<span class="text-red-400 font-bold">漏踩直接判定出局。</span></div>
                                    </li>
                                    <li class="flex items-start bg-zinc-800/30 p-2 sm:p-3 rounded-md border-l-2 border-pink-500/50">
                                        <i data-lucide="x-octagon" class="w-4 h-4 sm:w-5 sm:h-5 text-pink-500 shrink-0 mt-0.5 mr-2 sm:mr-3"></i>
                                        <div><span class="font-bold text-white">妨礙守備：</span>故意妨礙，或為閃躲防守者嚴重偏離路線（超過1公尺）即判定出局。</div>
                                    </li>
                                </ul>
                                <div class="mt-4 sm:mt-6 bg-red-900/30 border border-red-500/50 p-3 sm:p-4 rounded-md">
                                    <div class="flex items-center text-red-400 font-bold mb-1 sm:mb-2 text-sm sm:text-base">
                                        <i data-lucide="alert-triangle" class="w-4 h-4 sm:w-5 sm:h-5 mr-2 animate-pulse"></i>嚴禁滑壘或撲壘！
                                    </div>
                                    <p class="text-xs sm:text-sm text-red-200">跑者使用滑或撲等危險動作，不論有意無意、上壘與否，一律判定出局！</p>
                                </div>
                            </div>

                            <!-- 防守底線與死球狀態 -->
                            <div class="bg-zinc-900 border border-zinc-800 p-4 sm:p-6 md:p-8 relative hover:border-zinc-600 transition-colors duration-300 rounded-lg">
                                <h3 class="text-lg sm:text-xl font-black text-white mb-4 sm:mb-6 tracking-wider uppercase border-b border-zinc-800 pb-3 sm:pb-4 flex items-center">
                                    <i data-lucide="shield" class="w-5 h-5 sm:w-6 sm:h-6 text-pink-500 mr-2"></i>防守底線與死球狀態
                                </h3>
                                <ul class="space-y-3 sm:space-y-4 text-zinc-300 text-sm sm:text-base">
                                    <li class="flex items-start bg-zinc-800/30 p-2 sm:p-3 rounded-md border-l-2 border-zinc-500">
                                        <i data-lucide="shield" class="w-4 h-4 sm:w-5 sm:h-5 text-zinc-400 shrink-0 mt-0.5 mr-2 sm:mr-3"></i>
                                        <div><span class="font-bold text-white">僅限封殺：</span>嚴禁觸殺！如果持球碰觸到跑壘員，不判出局，比賽繼續進行。踩壘包封殺跑者。</div>
                                    </li>
                                    <li class="flex items-start bg-zinc-800/30 p-2 sm:p-3 rounded-md border-l-2 border-zinc-500">
                                        <i data-lucide="activity" class="w-4 h-4 sm:w-5 sm:h-5 text-zinc-400 shrink-0 mt-0.5 mr-2 sm:mr-3"></i>
                                        <div><span class="font-bold text-white">飛球接殺：</span>接殺飛球觸發「死球狀態」，該打者直接出局。</div>
                                    </li>
                                    <li class="flex items-start bg-zinc-800/30 p-2 sm:p-3 rounded-md border-l-2 border-zinc-500">
                                        <i data-lucide="users" class="w-4 h-4 sm:w-5 sm:h-5 text-zinc-400 shrink-0 mt-0.5 mr-2 sm:mr-3"></i>
                                        <div><span class="font-bold text-white">觀眾規範：</span>觀眾屬於球場「靜物」的一部分，絕對不得故意干擾比賽進行。</div>
                                    </li>
                                </ul>
                                <div class="mt-4 sm:mt-6 border border-zinc-700 bg-black p-3 sm:p-4 rounded-md relative overflow-hidden">
                                    <div class="absolute right-0 top-0 opacity-10 transform translate-x-4 -translate-y-4">
                                        <i data-lucide="shield" class="w-16 h-16 sm:w-20 sm:h-20"></i>
                                    </div>
                                    <h4 class="text-white font-bold mb-2 sm:mb-3 flex items-center text-xs sm:text-sm">
                                        <i data-lucide="x-octagon" class="w-3 h-3 sm:w-4 sm:h-4 text-pink-500 mr-2"></i>死球狀態觸發時機
                                    </h4>
                                    <div class="flex flex-wrap gap-1.5 sm:gap-2 text-[10px] sm:text-xs font-bold relative z-10">
                                        <span class="bg-zinc-800 border border-zinc-700 text-zinc-300 px-2 sm:px-3 py-1 rounded-full">封殺跑者</span>
                                        <span class="bg-zinc-800 border border-zinc-700 text-zinc-300 px-2 sm:px-3 py-1 rounded-full">接殺飛球</span>
                                        <span class="bg-zinc-800 border border-zinc-700 text-zinc-300 px-2 sm:px-3 py-1 rounded-full">內野手傳回投手/本壘</span>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </section>

                <section>
                    <h2 class="text-2xl sm:text-3xl font-black text-white uppercase tracking-wide flex items-center mb-4 sm:mb-6">
                        <span class="w-1.5 sm:w-2 h-6 sm:h-8 bg-pink-500 mr-2 sm:mr-3 transform -skew-x-12"></span>
                        各年級比賽重點注意事項
                    </h2>
                    <div class="flex flex-col space-y-6 sm:space-y-8" id="grade-cards-container">
                        <!-- 這裡的內容由 JS 動態生成 -->
                    </div>
                </section>
            </div>

            <!-- 賽程總覽 Tab -->
            <div id="tab-schedule" class="tab-content space-y-8 sm:space-y-12">
                <div class="bg-zinc-900 border border-zinc-800 p-4 sm:p-6 md:p-8 relative overflow-hidden rounded-lg">
                    <div class="absolute right-0 top-0 w-48 sm:w-64 h-48 sm:h-64 bg-pink-500/5 transform rotate-12 translate-x-24 sm:translate-x-32 -translate-y-24 sm:-translate-y-32 pointer-events-none"></div>
                    <h3 class="text-xl sm:text-2xl font-black text-white mb-6 sm:mb-8 tracking-wide flex items-center">
                        <i data-lucide="calendar-days" class="text-pink-500 mr-2 sm:mr-3 w-5 h-5 sm:w-7 sm:h-7"></i>單循環賽程表
                    </h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-8 sm:gap-y-12 relative z-10" id="schedule-container">
                        <!-- 這裡由 JS 動態渲染賽程表 -->
                    </div>
                    <div class="mt-8 sm:mt-12 p-4 sm:p-5 bg-zinc-800/40 border-l-4 border-pink-500 text-xs sm:text-sm text-zinc-300 relative overflow-hidden group hover:bg-zinc-800/60 transition-colors rounded-r-md">
                        <div class="absolute right-0 top-0 h-full w-1/2 bg-gradient-to-l from-pink-500/5 to-transparent pointer-events-none"></div>
                        <strong class="text-white text-sm sm:text-base flex items-center mb-1.5 sm:mb-2"><i data-lucide="alert-triangle" class="w-3 h-3 sm:w-4 sm:h-4 text-pink-500 mr-1.5 sm:mr-2"></i>雨備延賽資訊：</strong>
                        <ul class="space-y-1 ml-4 sm:ml-6 list-disc list-outside marker:text-pink-500">
                            <li>中年級如遇雨天，則延至 <span class="text-white font-bold">5/6(三) 至 5/07(四)、5/12(二)</span> 進行，比賽時間及賽程不變。</li>
                            <li>高年級如遇雨天，則延至 <span class="text-white font-bold">5/13(三) 至 5/15(五)</span> 進行，比賽時間及賽程不變。</li>
                        </ul>
                    </div>
                </div>
            </div>

            <!-- 導師戰術板 Tab -->
            <div id="tab-tactics" class="tab-content space-y-6 sm:space-y-8">
                <h2 class="text-2xl sm:text-3xl font-black text-white uppercase tracking-wide flex items-center mb-4 sm:mb-6">
                    <span class="w-1.5 sm:w-2 h-6 sm:h-8 bg-pink-500 mr-2 sm:mr-3 transform -skew-x-12"></span>賽前最終檢核表
                </h2>
                
                <div class="bg-pink-900/10 border-2 border-pink-500/50 p-4 sm:p-6 md:p-8 relative overflow-hidden group rounded-lg">
                    <div class="absolute top-0 right-0 bg-pink-600 text-white text-[10px] sm:text-xs font-bold px-3 sm:px-4 py-1 uppercase tracking-widest transform translate-x-2 sm:translate-x-3 translate-y-1 sm:translate-y-2 rotate-45 shadow-lg">極重要</div>
                    <h3 class="text-xl sm:text-2xl font-black text-pink-400 mb-4 sm:mb-6 tracking-wide flex items-center border-b border-pink-500/30 pb-3 sm:pb-4">
                        <i data-lucide="alert-circle" class="w-5 h-5 sm:w-7 sm:h-7 mr-2 sm:mr-3"></i>排棒戰術與打線差異
                    </h3>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 sm:gap-6">
                        <div class="bg-black/50 p-4 sm:p-5 rounded-md border border-zinc-700/50 hover:border-pink-500/50 transition-colors">
                            <h4 class="text-base sm:text-lg font-bold text-white mb-2 sm:mb-3 flex items-center flex-wrap gap-2">
                                <span class="bg-zinc-800 text-pink-400 px-2 py-1 text-xs sm:text-sm rounded mr-1">三、四、五年級</span>接續打擊制
                            </h4>
                            <p class="text-zinc-300 leading-relaxed text-xs sm:text-sm md:text-base">
                                上下半局的打擊人員 <span class="text-pink-400 font-bold border-b border-pink-500">完全不重複</span>。<br><br>
                                <span class="text-zinc-400 block p-2 bg-zinc-900 rounded">💡 範例：若每半局排定 12 人打擊，上半局由第 1~12 棒上場，<strong class="text-white">下半局則必須由第 13 棒開始接續打擊</strong>（13~24棒）。</span><br>
                                <strong class="text-pink-500 font-bold tracking-wide flex items-center mt-2"><i data-lucide="shield" class="w-4 h-4 mr-1"></i> 五年級防守特規：</strong>必須由該半局參與攻擊的 13-14 人中，選派 9 位上場防守。
                            </p>
                        </div>
                        <div class="bg-black/50 p-4 sm:p-5 rounded-md border border-zinc-700/50 hover:border-pink-500/50 transition-colors">
                            <h4 class="text-base sm:text-lg font-bold text-white mb-2 sm:mb-3 flex items-center flex-wrap gap-2">
                                <span class="bg-zinc-800 text-pink-400 px-2 py-1 text-xs sm:text-sm rounded mr-1">六年級限定</span>循環打擊制
                            </h4>
                            <p class="text-zinc-300 leading-relaxed text-xs sm:text-sm md:text-base">
                                每位同學皆打擊兩次。第一局與第二局的打擊人員與順序 <span class="text-pink-400 font-bold border-b border-pink-500">完全相同</span>。<br><br>
                                <span class="text-zinc-400 block p-2 bg-zinc-900 rounded">💡 範例：上半局第 1~16 棒打完後，下半局同樣由第 1~16 棒再次依照原順序上場打擊。</span><br>
                                <strong class="text-pink-500 font-bold tracking-wide flex items-center mt-2"><i data-lucide="shield" class="w-4 h-4 mr-1"></i> 六年級防守特規：</strong>固定 9 位防守人員，第一局不得換人（第二局開始後方可更換）。
                            </p>
                        </div>
                    </div>
                </div>

                <div class="bg-zinc-900 border border-zinc-800 p-4 sm:p-6 md:p-8 relative overflow-hidden group mt-4 sm:mt-6 rounded-lg">
                    <div class="absolute right-0 top-0 w-24 sm:w-32 h-24 sm:h-32 bg-pink-500/5 transform rotate-45 translate-x-12 sm:translate-x-16 -translate-y-12 sm:-translate-y-16 group-hover:bg-pink-500/10 transition-colors"></div>
                    <div class="space-y-4 sm:space-y-8 relative z-10" id="checklist-container">
                        <!-- 檢核表由 JS 渲染 -->
                    </div>
                </div>
            </div>

            <!-- 打線安排區 Tab -->
            <div id="tab-lineup" class="tab-content space-y-6 sm:space-y-8">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-end mb-4 sm:mb-6 gap-2 sm:gap-4">
                    <div>
                        <h2 class="text-2xl sm:text-3xl font-black text-white uppercase tracking-wide flex items-center mb-1 sm:mb-2">
                            <span class="w-1.5 sm:w-2 h-6 sm:h-8 bg-pink-500 mr-2 sm:mr-3 transform -skew-x-12"></span>班級打線安排
                        </h2>
                        <p class="text-zinc-400 text-xs sm:text-base">導師可透過 Google Sheet/CSV 匯入，或直接線上編排各班棒次表。</p>
                    </div>
                    <div id="lineup-last-updated-container" style="display:none;" class="flex items-center text-[10px] sm:text-xs font-mono text-zinc-500 bg-zinc-900 border border-zinc-800 px-2 sm:px-3 py-1 sm:py-1.5 rounded-sm">
                        <i data-lucide="clock" class="w-3 h-3 sm:w-[14px] sm:h-[14px] mr-1 sm:mr-2 text-pink-500"></i>
                        最後更新: <span id="lineup-last-updated-text" class="text-white ml-1 sm:ml-2"></span>
                    </div>
                </div>

                <div class="bg-zinc-900 border border-zinc-800 p-4 sm:p-6 shadow-xl relative rounded-lg">
                    <div class="flex flex-col lg:flex-row justify-between items-start lg:items-center gap-4 sm:gap-6 mb-6 sm:mb-8 border-b border-zinc-800 pb-6 sm:pb-8">
                        <div class="flex flex-wrap gap-3 sm:gap-4 items-center relative z-20 w-full lg:w-auto">
                            <div class="flex flex-col space-y-1 sm:space-y-2 flex-1 sm:flex-none">
                                <label class="text-[10px] sm:text-xs font-bold text-zinc-500 uppercase tracking-widest">選擇年級</label>
                                <select id="lineup-grade-select" onchange="handleLineupGradeChange()" class="w-full bg-black border border-zinc-700 text-white px-3 sm:px-4 py-2 focus:outline-none focus:border-pink-500 font-bold text-sm sm:text-base rounded">
                                    <option value="">-- 選擇 --</option>
                                    <option value="3">三年級</option>
                                    <option value="4">四年級</option>
                                    <option value="5">五年級</option>
                                    <option value="6">六年級</option>
                                </select>
                            </div>
                            <div class="flex flex-col space-y-1 sm:space-y-2 flex-1 sm:flex-none">
                                <label class="text-[10px] sm:text-xs font-bold text-zinc-500 uppercase tracking-widest">選擇班級</label>
                                <div class="flex items-center space-x-2">
                                    <select id="lineup-class-select" onchange="handleLineupClassChange()" class="w-full bg-black border border-zinc-700 text-white px-3 sm:px-4 py-2 focus:outline-none focus:border-pink-500 font-bold disabled:opacity-50 text-sm sm:text-base rounded" disabled>
                                        <option value="">-- 選擇 --</option>
                                    </select>
                                    <div id="lineup-lock-icon" class="p-2 rounded bg-red-500/10 text-red-500 hidden shrink-0">
                                        <i data-lucide="lock" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div id="lineup-controls" class="grid grid-cols-2 sm:flex sm:flex-wrap gap-2 sm:gap-3 w-full lg:w-auto transition-opacity duration-300 relative z-20 opacity-50 pointer-events-none">
                            <button onclick="handleDownloadTemplate()" class="flex items-center justify-center space-x-1 sm:space-x-2 px-2 sm:px-4 py-2 border border-zinc-700 text-zinc-300 hover:text-white hover:border-zinc-500 hover:bg-zinc-800 transition-colors text-xs sm:text-sm font-bold rounded">
                                <i data-lucide="download" class="w-3 h-3 sm:w-4 sm:h-4"></i><span class="truncate">下載範本</span>
                            </button>
                            <div class="relative w-full sm:w-auto">
                                <input type="file" id="csv-upload" class="hidden" accept=".csv,.xlsx" onchange="handleCsvImport(event)">
                                <label for="csv-upload" id="csv-upload-label" class="flex items-center justify-center space-x-1 sm:space-x-2 px-2 sm:px-4 py-2 bg-zinc-800 hover:bg-zinc-700 text-white transition-colors text-xs sm:text-sm font-bold cursor-pointer h-full rounded w-full">
                                    <i data-lucide="upload" class="w-3 h-3 sm:w-4 sm:h-4" id="csv-upload-icon"></i>
                                    <span id="csv-upload-text" class="truncate">匯入CSV</span>
                                </label>
                            </div>
                            <button onclick="handleClearLineup()" class="col-span-2 sm:col-span-1 flex items-center justify-center space-x-1 sm:space-x-2 px-2 sm:px-4 py-2 border border-red-900/50 text-red-400 hover:bg-red-900/20 hover:text-red-300 transition-colors text-xs sm:text-sm font-bold rounded">
                                <i data-lucide="trash-2" class="w-3 h-3 sm:w-4 sm:h-4"></i><span>清空</span>
                            </button>
                            <button onclick="handleSaveLineup()" class="col-span-2 sm:col-span-1 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-2 bg-pink-600 hover:bg-pink-500 text-white text-xs sm:text-sm font-bold transition-transform transform active:scale-95 rounded">
                                <i data-lucide="save" class="w-3 h-3 sm:w-4 sm:h-4"></i><span>儲存棒次表</span>
                            </button>
                        </div>
                    </div>

                    <div id="lineup-alert-box" class="p-3 sm:p-4 mb-4 sm:mb-6 rounded-md border flex items-start space-x-2 sm:space-x-3 bg-zinc-800/50 border-zinc-700 text-zinc-400">
                        <i data-lucide="alert-circle" id="lineup-alert-icon" class="w-4 h-4 sm:w-5 sm:h-5 shrink-0 mt-0.5 text-zinc-500"></i>
                        <div>
                            <p id="lineup-alert-text" class="font-bold text-xs sm:text-sm tracking-wide">請先選擇年級以查看排棒限制。</p>
                            <p id="lineup-alert-subtext" class="text-[10px] sm:text-xs mt-1 opacity-80 hidden"></p>
                        </div>
                    </div>

                    <!-- 編輯表格與密碼鎖定層 -->
                    <div class="relative">
                        <div id="lineup-table-container" class="overflow-x-auto transition-all duration-500 opacity-30 pointer-events-none blur-[4px] select-none grayscale -mx-4 sm:mx-0 px-4 sm:px-0">
                            <table class="w-full text-left border-collapse min-w-[500px]">
                                <thead>
                                    <tr class="bg-black border-y border-zinc-800">
                                        <th class="py-2 sm:py-3 px-2 sm:px-4 font-black text-zinc-500 tracking-wider text-xs sm:text-sm w-16 sm:w-20">棒次</th>
                                        <th class="py-2 sm:py-3 px-2 sm:px-4 font-black text-zinc-500 tracking-wider text-xs sm:text-sm w-24 sm:w-32">座號</th>
                                        <th class="py-2 sm:py-3 px-2 sm:px-4 font-black text-zinc-500 tracking-wider text-xs sm:text-sm min-w-[120px]">選手姓名</th>
                                        <th class="py-2 sm:py-3 px-2 sm:px-4 font-black text-zinc-500 tracking-wider text-xs sm:text-sm w-20 sm:w-24 text-center">狀態</th>
                                    </tr>
                                </thead>
                                <tbody id="lineup-tbody" class="divide-y divide-zinc-800/50">
                                    <!-- 打線表格行由 JS 動態渲染 -->
                                </tbody>
                            </table>
                        </div>
                        
                        <!-- 狀態覆蓋層 -->
                        <div id="lineup-overlay-select-class" class="absolute inset-0 flex items-center justify-center z-10">
                            <div class="bg-zinc-900/90 backdrop-blur-sm border border-zinc-700 px-4 sm:px-6 py-3 sm:py-4 rounded-lg shadow-2xl flex items-center m-4 text-center">
                                <i data-lucide="list-ordered" class="w-4 h-4 sm:w-5 sm:h-5 text-zinc-400 mr-2 sm:mr-3 hidden sm:block"></i>
                                <span class="font-bold text-white tracking-wide text-xs sm:text-base">請先於上方選擇班級以載入表單</span>
                            </div>
                        </div>
                        
                        <div id="lineup-overlay-password" class="absolute inset-0 flex items-center justify-center z-10 p-4 hidden">
                            <div class="bg-black/80 backdrop-blur-md border border-zinc-700 p-6 sm:p-8 rounded-xl shadow-2xl flex flex-col items-center max-w-sm w-full animate-[fadeIn_0.3s_ease-out]">
                                <div class="w-12 h-12 sm:w-16 sm:h-16 bg-zinc-900 rounded-full flex items-center justify-center mb-4 sm:mb-6 border-2 border-zinc-700">
                                    <i data-lucide="key-round" class="w-6 h-6 sm:w-8 sm:h-8 text-pink-500"></i>
                                </div>
                                <h3 class="text-lg sm:text-xl font-black text-white mb-1 sm:mb-2 tracking-widest">班級專屬密碼</h3>
                                <p id="password-overlay-text" class="text-zinc-400 text-xs sm:text-sm mb-4 sm:mb-6 text-center">請輸入班級導師密碼以解鎖編輯權限。</p>
                                
                                <form onsubmit="handleLineupUnlock(event)" class="w-full relative">
                                    <input type="password" id="lineup-passcode-input" placeholder="請輸入密碼" oninput="document.getElementById('lineup-auth-error').classList.add('hidden'); this.classList.remove('border-red-500', 'focus:border-red-500'); this.classList.add('border-zinc-700', 'focus:border-pink-500');" class="w-full bg-zinc-900 border border-zinc-700 focus:border-pink-500 text-center text-white px-3 sm:px-4 py-2 sm:py-3 rounded-md focus:outline-none transition-colors font-mono tracking-widest mb-4 text-sm sm:text-base">
                                    <p id="lineup-auth-error" class="text-red-400 text-[10px] sm:text-xs absolute -bottom-2 sm:-bottom-2 w-full text-center hidden">密碼錯誤，請重新輸入</p>
                                    <button type="submit" class="w-full bg-pink-600 hover:bg-pink-500 text-white font-bold py-2 sm:py-3 rounded-md uppercase tracking-widest transition-transform transform active:scale-95 flex justify-center items-center mt-2 sm:mt-4 text-sm sm:text-base">
                                        <i data-lucide="unlock" class="w-4 h-4 sm:w-[18px] sm:h-[18px] mr-2"></i> 解鎖表單
                                    </button>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 成績公告區 Tab -->
            <div id="tab-results" class="tab-content space-y-6 sm:space-y-8">
                <!-- 賽事比分板 -->
                <div class="bg-zinc-900 border border-zinc-800 rounded-lg overflow-hidden shadow-2xl mb-6 sm:mb-8">
                    <div class="p-4 sm:p-6 border-b border-zinc-800 flex flex-col md:flex-row md:justify-between items-start md:items-center bg-black/50 gap-3 sm:gap-4">
                        <h3 class="text-xl sm:text-2xl font-black text-white flex items-center tracking-wide">
                            <i data-lucide="trophy" class="text-pink-500 mr-2 sm:mr-3 w-5 h-5 sm:w-7 sm:h-7"></i>各年級賽事比分板
                        </h3>
                        <div id="admin-lock-status" class="w-full md:w-auto">
                            <!-- 由 JS 渲染鎖定狀態按鈕 -->
                        </div>
                    </div>
                    <div class="p-4 sm:p-6 grid grid-cols-1 xl:grid-cols-2 gap-6 sm:gap-8" id="scoreboard-container">
                        <!-- 比分板由 JS 動態渲染 -->
                    </div>
                </div>

                <!-- 附件公告 -->
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-4 sm:mb-6 gap-4">
                    <h2 class="text-2xl sm:text-3xl font-black text-white uppercase tracking-wide flex items-center">
                        <span class="w-1.5 sm:w-2 h-6 sm:h-8 bg-pink-500 mr-2 sm:mr-3 transform -skew-x-12"></span>賽事官方附件公告
                    </h2>
                    <div class="w-full md:w-auto relative" id="attachment-upload-container" style="display: none;">
                        <div class="relative animate-[fadeIn_0.3s_ease-out]">
                            <input type="file" id="file-upload" class="hidden" accept=".pdf,.doc,.docx" onchange="handleFileUpload(event)">
                            <label for="file-upload" id="file-upload-label" class="flex justify-center items-center space-x-2 px-4 sm:px-6 py-2 sm:py-3 bg-pink-600 hover:bg-pink-500 text-white font-bold tracking-wider uppercase cursor-pointer transition-all transform -skew-x-12 shadow-lg text-xs sm:text-sm rounded">
                                <i data-lucide="upload" class="w-4 h-4 sm:w-[18px] sm:h-[18px] transform skew-x-12"></i>
                                <span class="transform skew-x-12" id="file-upload-text">新增賽事附件</span>
                            </label>
                        </div>
                    </div>
                </div>

                <div class="bg-zinc-900 border border-zinc-800 rounded-lg overflow-hidden shadow-2xl">
                    <div class="grid grid-cols-1 divide-y divide-zinc-800" id="file-list-container">
                        <!-- 檔案列表由 JS 渲染 -->
                    </div>
                </div>
            </div>

        </main>
    </div>

    <!-- Modal 區域 -->
    
    <!-- 圖片放大 Modal -->
    <div id="image-zoom-modal" class="fixed inset-0 bg-black/95 z-[60] hidden items-center justify-center p-2 sm:p-4 md:p-8 cursor-zoom-out backdrop-blur-sm" onclick="closeImageModal()">
        <button class="absolute top-4 sm:top-6 right-4 sm:right-6 text-zinc-400 hover:text-white bg-zinc-900/50 hover:bg-pink-600 p-2 rounded-full transition-colors z-[70]" onclick="event.stopPropagation(); closeImageModal()">
            <i data-lucide="x" class="w-5 h-5 sm:w-7 sm:h-7"></i>
        </button>
        <img src="https://lh3.googleusercontent.com/d/1w3WT5IjYvBD6BNSiOQlogWvd-HwJUTkG" alt="樂樂棒球宣傳圖 (放大)" class="max-w-[95%] max-h-[90vh] object-contain shadow-2xl ring-1 ring-zinc-800 rounded-md animate-[fadeIn_0.3s_ease-out]" onclick="event.stopPropagation()">
    </div>

    <!-- 體衛組長登入 Modal -->
    <div id="admin-login-modal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 hidden items-center justify-center p-4">
        <div class="bg-zinc-900 border border-zinc-700 p-6 sm:p-8 rounded-xl shadow-2xl flex flex-col items-center max-w-sm w-full animate-[fadeIn_0.3s_ease-out]">
            <div class="w-12 h-12 sm:w-16 sm:h-16 bg-black rounded-full flex items-center justify-center mb-4 sm:mb-6 border-2 border-zinc-700">
                <i data-lucide="shield-alert" class="w-6 h-6 sm:w-8 sm:h-8 text-pink-500"></i>
            </div>
            <h3 class="text-lg sm:text-xl font-black text-white mb-1 sm:mb-2 tracking-widest text-center">系統管理員解鎖</h3>
            <p class="text-zinc-400 text-xs sm:text-sm mb-4 sm:mb-6 text-center">請輸入體衛組長專屬密碼以新增賽事成績與公告。</p>
            
            <form onsubmit="handleAdminAuth(event)" class="w-full relative">
                <input type="password" id="admin-passcode-input" placeholder="請輸入密碼" oninput="document.getElementById('admin-auth-error').classList.add('hidden'); this.classList.remove('border-red-500', 'focus:border-red-500'); this.classList.add('border-zinc-700', 'focus:border-pink-500');" class="w-full bg-black border border-zinc-700 text-center text-white px-3 sm:px-4 py-2 sm:py-3 rounded-md focus:outline-none focus:border-pink-500 transition-colors font-mono tracking-widest mb-4 text-sm sm:text-base">
                <p id="admin-auth-error" class="text-red-400 text-[10px] sm:text-xs absolute -bottom-2 w-full text-center hidden">密碼錯誤，請重新輸入</p>
                <div class="flex space-x-2 sm:space-x-3 mt-2 sm:mt-4">
                    <button type="button" onclick="closeAdminModal()" class="w-1/3 bg-zinc-800 hover:bg-zinc-700 text-white font-bold py-2 sm:py-3 rounded-md uppercase tracking-widest transition-colors text-xs sm:text-sm">取消</button>
                    <button type="submit" class="w-2/3 bg-pink-600 hover:bg-pink-500 text-white font-bold py-2 sm:py-3 rounded-md uppercase tracking-widest transition-transform transform active:scale-95 flex justify-center items-center text-xs sm:text-sm">
                        <i data-lucide="unlock" class="w-4 h-4 sm:w-[18px] sm:h-[18px] mr-1 sm:mr-2"></i> 解鎖
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- Toast 提示 -->
    <div id="toast" class="fixed bottom-4 sm:bottom-6 right-4 sm:right-6 bg-zinc-800 border-l-4 border-green-500 text-white px-4 sm:px-6 py-3 sm:py-4 shadow-2xl items-center animate-[slideInUp_0.4s_ease-out] z-[80] hidden text-sm sm:text-base rounded">
        <i data-lucide="check-circle-2" class="w-4 h-4 sm:w-5 sm:h-5 text-green-500 mr-2 sm:mr-3 shrink-0"></i>
        <span id="toast-message" class="font-bold tracking-wide"></span>
    </div>

    <!-- 系統核心 JavaScript -->
    <script>
        // 初始化 Lucide Icons
        lucide.createIcons();

        // MIT 防護：防止 XSS 注入攻擊
        const escapeHTML = (str) => {
            if (!str) return '';
            return str.toString().replace(/[&<>'"]/g, 
                tag => ({
                    '&': '&amp;',
                    '<': '&lt;',
                    '>': '&gt;',
                    "'": '&#39;',
                    '"': '&quot;'
                }[tag] || tag)
            );
        };

        /* --- 全域狀態管理 --- */
        let state = {
            activeTab: 'rules',
            checklist: { lineup: false, safety: false, tactics: false },
            lineup: {
                grade: '',
                cls: '',
                data: Array.from({ length: 30 }, () => ({ number: '', name: '' })),
                lastUpdated: null,
                isUnlocked: false,
                allClassesData: {} // MIT 新增：用來緩存各班級輸入的資料
            },
            admin: {
                isUnlocked: false
            },
            matches: [
                { id: 'g3-1', grade: '3', gradeName: '三年級', date: '4/21 (二)', time: '08:10', match: '場次一', teamA: '301', teamB: '302', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g3-2', grade: '3', gradeName: '三年級', date: '4/22 (三)', time: '08:10', match: '場次二', teamA: '301', teamB: '303', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g3-3', grade: '3', gradeName: '三年級', date: '4/23 (四)', time: '08:10', match: '場次三', teamA: '302', teamB: '303', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g4-1', grade: '4', gradeName: '四年級', date: '4/21 (二)', time: '08:10', match: '場次一', teamA: '401', teamB: '402', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g4-2', grade: '4', gradeName: '四年級', date: '4/22 (三)', time: '08:10', match: '場次二', teamA: '401', teamB: '403', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g4-3', grade: '4', gradeName: '四年級', date: '4/23 (四)', time: '08:10', match: '場次三', teamA: '402', teamB: '403', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g5-1', grade: '5', gradeName: '五年級', date: '4/27 (一)', time: '08:10', match: '場次一', teamA: '501', teamB: '502', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g5-2', grade: '5', gradeName: '五年級', date: '4/28 (二)', time: '08:10', match: '場次二', teamA: '501', teamB: '503', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g5-3', grade: '5', gradeName: '五年級', date: '4/30 (四)', time: '08:10', match: '場次三', teamA: '502', teamB: '503', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g6-1', grade: '6', gradeName: '六年級', date: '4/27 (一)', time: '08:10', match: '場次一', teamA: '601', teamB: '602', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g6-2', grade: '6', gradeName: '六年級', date: '4/28 (二)', time: '08:10', match: '場次二', teamA: '601', teamB: '603', scoreA: '', scoreB: '', status: 'pending' },
                { id: 'g6-3', grade: '6', gradeName: '六年級', date: '4/30 (四)', time: '08:10', match: '場次三', teamA: '602', teamB: '603', scoreA: '', scoreB: '', status: 'pending' },
            ],
            files: [
                { id: 1, name: '三年級預賽成績公告.pdf', date: '2026-04-21' }
            ]
        };

        const classOptions = {
            '3': ['301', '302', '303'],
            '4': ['401', '402', '403'],
            '5': ['501', '502', '503'],
            '6': ['601', '602', '603'],
        };

        /* --- 系統級資料持久化 (LocalStorage) --- */
        const STORAGE_KEY = 'ZhongyiBaseballApp_Data';

        function saveStateToLocal() {
            try {
                const dataToSave = {
                    matches: state.matches,
                    files: state.files,
                    checklist: state.checklist,
                    allClassesData: state.lineup.allClassesData
                };
                localStorage.setItem(STORAGE_KEY, JSON.stringify(dataToSave));
            } catch (e) {
                console.error('儲存至 LocalStorage 失敗', e);
            }
        }

        function loadStateFromLocal() {
            try {
                const saved = localStorage.getItem(STORAGE_KEY);
                if (saved) {
                    const parsed = JSON.parse(saved);
                    if (parsed.matches) state.matches = parsed.matches;
                    if (parsed.files) state.files = parsed.files;
                    if (parsed.checklist) state.checklist = parsed.checklist;
                    if (parsed.allClassesData) state.lineup.allClassesData = parsed.allClassesData;
                }
            } catch (e) {
                console.error('從 LocalStorage 讀取失敗', e);
            }
        }

        /* --- 工具函數 --- */
        function showToast(message) {
            const toast = document.getElementById('toast');
            document.getElementById('toast-message').innerText = message;
            toast.style.display = 'flex';
            setTimeout(() => { toast.style.display = 'none'; }, 3000);
        }

        /* --- Tab 切換邏輯 (O(1) 零延遲切換) --- */
        function switchTab(tabId) {
            state.activeTab = tabId;
            document.querySelectorAll('.tab-btn').forEach(btn => {
                if (btn.dataset.target === tabId) {
                    btn.className = 'tab-btn flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-pink-500 border-pink-500 bg-pink-500/10 text-xs sm:text-sm';
                } else {
                    btn.className = 'tab-btn flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm';
                }
            });
            document.querySelectorAll('.tab-content').forEach(content => {
                content.classList.remove('active');
            });
            document.getElementById(`tab-${tabId}`).classList.add('active');
        }

        /* --- Modal 邏輯 --- */
        function openImageModal() {
            document.getElementById('image-zoom-modal').style.display = 'flex';
            document.body.classList.add('modal-open');
        }
        function closeImageModal() {
            document.getElementById('image-zoom-modal').style.display = 'none';
            document.body.classList.remove('modal-open');
        }
        function openAdminModal() {
            document.getElementById('admin-login-modal').style.display = 'flex';
            document.getElementById('admin-passcode-input').focus();
            document.body.classList.add('modal-open');
        }
        function closeAdminModal() {
            document.getElementById('admin-login-modal').style.display = 'none';
            document.body.classList.remove('modal-open');
        }

        /* --- 規則頁：年級卡片渲染 --- */
        function renderGradeCards() {
            const container = document.getElementById('grade-cards-container');
            
            const createScoringIll = (type) => {
                if(type === 'middle') return `
                    <div class="bg-black border border-zinc-800 p-3 sm:p-4 rounded-md">
                        <h4 class="text-xs sm:text-sm font-bold text-pink-500 mb-2 sm:mb-3 flex items-center"><i data-lucide="zap" class="w-3 h-3 sm:w-[14px] sm:h-[14px] mr-1"></i>推進得分機制</h4>
                        <div class="flex items-center justify-between text-zinc-400 max-w-sm mx-auto">
                            <div class="flex flex-col items-center"><div class="w-6 h-6 sm:w-8 sm:h-8 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1 sm:mb-2 bg-zinc-900"><span class="transform -rotate-45 text-[10px] sm:text-xs font-bold text-white">1</span></div><span class="text-[8px] sm:text-[10px] font-bold">一壘(1分)</span></div>
                            <i data-lucide="arrow-right" class="w-3 h-3 sm:w-4 sm:h-4 text-zinc-600"></i>
                            <div class="flex flex-col items-center"><div class="w-6 h-6 sm:w-8 sm:h-8 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1 sm:mb-2 bg-zinc-900"><span class="transform -rotate-45 text-[10px] sm:text-xs font-bold text-white">2</span></div><span class="text-[8px] sm:text-[10px] font-bold">二壘(2分)</span></div>
                            <i data-lucide="arrow-right" class="w-3 h-3 sm:w-4 sm:h-4 text-zinc-600"></i>
                            <div class="flex flex-col items-center"><div class="w-6 h-6 sm:w-8 sm:h-8 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1 sm:mb-2 bg-zinc-900"><span class="transform -rotate-45 text-[10px] sm:text-xs font-bold text-white">3</span></div><span class="text-[8px] sm:text-[10px] font-bold">三壘(3分)</span></div>
                            <i data-lucide="arrow-right" class="w-3 h-3 sm:w-4 sm:h-4 text-zinc-600"></i>
                            <div class="flex flex-col items-center"><i data-lucide="home" class="w-5 h-5 sm:w-6 sm:h-6 text-pink-500 mb-1 sm:mb-2"></i><span class="text-[8px] sm:text-[10px] font-bold text-pink-400">本壘(4分)</span></div>
                        </div>
                    </div>`;
                return `
                    <div class="bg-black border border-zinc-800 p-3 sm:p-4 rounded-md">
                        <h4 class="text-xs sm:text-sm font-bold text-pink-500 mb-2 sm:mb-3 flex items-center"><i data-lucide="activity" class="w-3 h-3 sm:w-[14px] sm:h-[14px] mr-1"></i>戰術與得分機制</h4>
                        <div class="flex flex-col space-y-3 sm:space-y-4">
                            <div class="flex items-center justify-around">
                                <div class="flex flex-col items-center"><div class="relative"><i data-lucide="home" class="w-6 h-6 sm:w-8 sm:h-8 text-pink-500"></i><div class="absolute -top-1 -right-2 bg-pink-600 text-white text-[8px] sm:text-[10px] font-bold px-1 rounded-full">+1</div></div><span class="text-[10px] sm:text-xs font-bold mt-1 sm:mt-2 text-white">回本壘得1分</span></div>
                                <div class="h-8 sm:h-12 w-px bg-zinc-700 mx-2 sm:mx-4"></div>
                                <div class="flex flex-col items-center"><div class="flex items-center mb-1 sm:mb-2"><i data-lucide="footprints" class="w-3 h-3 sm:w-4 sm:h-4 text-zinc-400 mr-1 sm:mr-2"></i><div class="h-4 sm:h-6 border-l-2 border-dashed border-red-500 mx-1 sm:mx-2"></div><i data-lucide="target" class="w-3 h-3 sm:w-4 sm:h-4 text-pink-500 ml-1 sm:ml-2"></i></div><span class="text-[8px] sm:text-[10px] font-bold text-zinc-300">5公尺不可折返線</span><span class="text-[8px] sm:text-[10px] text-zinc-500 mt-0.5 sm:mt-1">打者觸球後跑者才離壘</span></div>
                            </div>
                            <div class="bg-zinc-900/80 border border-zinc-800 p-1.5 sm:p-2 rounded flex items-center justify-center"><i data-lucide="repeat" class="w-3 h-3 sm:w-[14px] sm:h-[14px] text-pink-500 mr-1 sm:mr-2 shrink-0"></i><span class="text-[8px] sm:text-[10px] text-zinc-300 font-bold tracking-wide">殘壘延續：下一個打擊半局，跑者回到原殘壘位</span></div>
                        </div>
                    </div>`;
            };

            const createBattingChart = (type) => `
                <div class="mt-4 sm:mt-6 bg-black border border-zinc-700 rounded-md overflow-hidden shadow-inner">
                    <div class="bg-zinc-800/80 text-[10px] sm:text-xs font-bold text-center py-1 sm:py-1.5 text-pink-400 tracking-widest border-b border-zinc-700">棒次安排圖解 (${type === 'continuous' ? '接續打擊制' : '循環打擊制'})</div>
                    <div class="flex divide-x divide-zinc-700 text-center">
                        <div class="flex-1 p-2 sm:p-3"><div class="text-[8px] sm:text-[10px] text-zinc-500 mb-1 font-black tracking-widest">第一局攻擊</div><div class="font-mono text-xs sm:text-sm text-white font-bold bg-zinc-900 py-1 rounded border border-zinc-800">1 ~ N 棒</div></div>
                        <div class="flex-1 p-2 sm:p-3"><div class="text-[8px] sm:text-[10px] text-zinc-500 mb-1 font-black tracking-widest">第二局攻擊</div><div class="font-mono text-xs sm:text-[13px] text-pink-400 font-bold bg-zinc-900 py-1 rounded border border-pink-500/30">${type === 'continuous' ? '(N+1)棒 起接續' : '1 ~ N 棒(重複)'}</div></div>
                    </div>
                    <div class="bg-zinc-900/50 p-1.5 sm:p-2 text-[8px] sm:text-[10px] text-zinc-400 text-center border-t border-zinc-700/50">${type === 'continuous' ? '⚠️ 上下半局打擊人員絕對不可重複' : '⚠️ 循環打擊制：上下半局打線完全相同'}</div>
                </div>`;

            const formatRule = (rule) => {
                const isHighlight = rule.includes('打線') || rule.includes('防守') || rule.includes('殘壘延續');
                const parts = rule.split('：');
                const formattedText = parts.length > 1 
                    ? `<span class="text-pink-400 font-black mr-1">${parts[0]}：</span>${parts[1]}` 
                    : parts[0];
                
                return `
                    <li class="flex items-start text-xs sm:text-sm ${isHighlight ? 'bg-zinc-800/80 p-3 sm:p-4 rounded-lg border-l-4 border-pink-500 text-white font-bold shadow-md transform hover:scale-[1.01] transition-transform' : 'p-1.5 sm:p-2'}">
                        ${!isHighlight ? '<span class="w-1.5 h-1.5 rounded-full bg-pink-500 mt-1 sm:mt-1.5 mr-2 sm:mr-3 shrink-0"></span>' : ''}
                        <span class="leading-relaxed tracking-wide ${isHighlight ? 'ml-1' : ''}">${formattedText}</span>
                    </li>`;
            };

            const cards = [
                {
                    grade: '中年級 (三、四年級)', badge: '推進得分制', ill: createScoringIll('middle'), chart: createBattingChart('continuous'),
                    rules: ["兩局制（分上下半局）。猜拳勝隊選擇攻守先後。", "打線（三年級）：每半局 12-13 人打擊（下半局人員不重複）。打擊之兩半局共計24-26棒次。", "打線（四年級）：每半局 10-12 人打擊（下半局人員不重複）。打擊之兩半局共計20-24棒次。", "防守：固定派守備 9 人（每局結束後可換人）。", "限制：球擊出後，投手位置防守員才能越過投手板。違規重新打擊。"]
                },
                {
                    grade: '五年級專屬', badge: '本壘得分制', ill: createScoringIll('high'), chart: createBattingChart('continuous'),
                    rules: ["兩局制（分上下半局）。", "打線：每半局 13-14 人打擊（下半局人員不重複）。打擊之兩半局共計26-28棒次。", "防守：必須由該局參與打擊的 13-14 位選手中，選派 9 人進行防守。", "死球狀態：內野手控制球傳回投手/本壘即死球。", "殘壘延續：半局結束時之殘壘，於下一個打擊半局回到原壘包上。", "平手裁決：1. 比較殘壘數（多者勝） 2. 比較壘包與本壘距離（近者勝）。"]
                },
                {
                    grade: '六年級專屬', badge: '本壘得分制', ill: createScoringIll('high'), chart: createBattingChart('loop'),
                    rules: ["兩局制（分上下半局）。", "打線：每半局 16-18 人打擊，每人皆打擊兩次（上下半局打線相同）。", "防守：固定 9 位防守人員。第二局開始後方可進行換人。", "死球狀態：內野手控制球傳回投手/本壘即死球。", "殘壘延續：半局結束時之殘壘，於下一個打擊半局回到原壘包上。", "平手裁決：1. 比較殘壘數（多者勝） 2. 比較壘包與本壘距離（近者勝）。"]
                }
            ];

            container.innerHTML = cards.map(c => `
                <div class="bg-zinc-900 border border-zinc-800 relative overflow-hidden group hover:border-zinc-600 transition-all duration-300 flex flex-col md:flex-row shadow-lg rounded-lg">
                    <div class="md:w-2/5 p-4 sm:p-6 md:p-8 bg-zinc-950 border-b md:border-b-0 md:border-r border-zinc-800 relative flex flex-col justify-center">
                        <div class="absolute top-0 left-0 bg-pink-600 text-white text-[10px] sm:text-xs font-bold px-3 sm:px-4 py-1 sm:py-1.5 uppercase tracking-widest z-10 rounded-br-xl shadow-md">${c.badge}</div>
                        <h3 class="text-xl sm:text-2xl font-black text-white mt-4 sm:mt-6 mb-4 sm:mb-6 tracking-wider relative z-10">${c.grade}</h3>
                        <div class="relative z-10 w-full transform sm:scale-105 origin-left lg:pr-4">${c.ill}</div>
                        <div class="relative z-10 w-full transform sm:scale-105 origin-left mt-2 lg:pr-4">${c.chart}</div>
                    </div>
                    <div class="md:w-3/5 p-4 sm:p-6 md:p-8 flex items-center bg-zinc-900">
                        <ul class="space-y-3 sm:space-y-4 text-zinc-300 text-base w-full">${c.rules.map(formatRule).join('')}</ul>
                    </div>
                </div>
            `).join('');
        }

        /* --- 戰術板：檢核表渲染 --- */
        function renderChecklist() {
            const container = document.getElementById('checklist-container');
            const items = [
                { id: 'lineup', icon: 'users', title: '陣容確認 LINEUP', desc: '請務必確認各年級打擊人數正確。<br><strong class="text-white">注意：棒次一經排定，除突發事件或請假外，絕不可變動！</strong>' },
                { id: 'safety', icon: 'shield-alert', title: '安全宣導 SAFETY', desc: '上場前務必再次提醒全班：「絕對禁止滑壘與撲壘！」（犯規即出局）。提醒跑壘員踩橘色壘包。' },
                { id: 'tactics', icon: 'swords', title: '戰術叮嚀 TACTICS', desc: '提醒守備員：只有封殺，沒有觸殺。帶著運動家精神上場，享受比賽！若有脫序情事，裁判可沒收比賽且直接判定勝負。' }
            ];

            container.innerHTML = items.map(item => {
                const isChecked = state.checklist[item.id];
                return `
                    <div class="flex items-start space-x-3 sm:space-x-4 group cursor-pointer transition-all duration-300 p-2 sm:p-3 rounded-lg hover:bg-zinc-800/30 border border-transparent ${isChecked ? 'border-zinc-800 bg-black/30' : ''}" onclick="toggleChecklistItem('${item.id}')">
                        <div class="mt-1 shrink-0">
                            <div class="w-8 h-8 sm:w-10 sm:h-10 border-2 rounded-md flex items-center justify-center transition-all duration-300 transform ${isChecked ? 'bg-pink-500 border-pink-500 scale-105 sm:scale-110' : 'border-zinc-600 group-hover:border-pink-500 group-hover:bg-pink-500/10'}">
                                <div class="transition-colors duration-300 ${isChecked ? 'text-white' : 'text-zinc-500 group-hover:text-pink-500'}">
                                    <i data-lucide="${isChecked ? 'check-square' : item.icon}" class="w-4 h-4 sm:w-5 sm:h-5"></i>
                                </div>
                            </div>
                        </div>
                        <div class="transition-all duration-500 ${isChecked ? 'opacity-50' : 'opacity-100'}">
                            <h4 class="text-base sm:text-xl font-black mb-1 uppercase tracking-wide transition-all duration-300 ${isChecked ? 'text-zinc-500 line-through decoration-pink-500 decoration-2' : 'text-white group-hover:text-pink-400'}">${item.title}</h4>
                            <div class="text-zinc-400 leading-relaxed text-xs sm:text-sm md:text-base">${item.desc}</div>
                        </div>
                    </div>`;
            }).join('');
            lucide.createIcons();
        }

        function toggleChecklistItem(id) {
            state.checklist[id] = !state.checklist[id];
            saveStateToLocal();
            renderChecklist();
        }

        /* --- 賽程表渲染 --- */
        function renderSchedule() {
            const container = document.getElementById('schedule-container');
            const groups = [
                { grade: '3', title: '三年級', classes: '301, 302, 303' },
                { grade: '4', title: '四年級', classes: '401, 402, 403' },
                { grade: '5', title: '五年級', classes: '501, 502, 503' },
                { grade: '6', title: '六年級', classes: '601, 602, 603' }
            ];

            const createMatchItem = (m) => {
                const isCompleted = m.status === 'completed';
                const scoreA = parseInt(m.scoreA);
                const scoreB = parseInt(m.scoreB);
                const isTie = isCompleted && scoreA === scoreB;
                const aWon = isCompleted && scoreA > scoreB;
                const bWon = isCompleted && scoreB > scoreA;

                const scoreHtml = isCompleted ? `
                    <div class="flex items-center space-x-1 sm:space-x-2 font-black text-lg sm:text-xl px-2 sm:px-0">
                        <span class="${aWon ? 'text-pink-400' : (isTie ? 'text-zinc-400' : 'text-white')}">${m.scoreA}</span>
                        <span class="text-zinc-600 text-xs sm:text-sm pb-1">-</span>
                        <span class="${bWon ? 'text-pink-400' : (isTie ? 'text-zinc-400' : 'text-white')}">${m.scoreB}</span>
                    </div>
                ` : `<span class="text-zinc-600 text-[10px] sm:text-xs font-black italic uppercase mx-2 px-2 sm:px-0">vs</span>`;

                return `
                    <li class="flex flex-col sm:flex-row sm:items-center justify-between bg-black p-3 sm:p-4 border transition-all duration-300 group cursor-default relative overflow-hidden rounded-md ${isCompleted ? 'border-pink-500/30' : 'border-zinc-800 hover:border-pink-500/50'}">
                        <div class="flex items-center space-x-2 sm:space-x-3 mb-2 sm:mb-0 relative z-10 w-full sm:w-auto justify-between sm:justify-start border-b sm:border-0 border-zinc-800 pb-2 sm:pb-0">
                            <div class="flex items-center space-x-2 sm:space-x-3">
                                <div class="px-1.5 sm:px-2 py-0.5 sm:py-1 font-mono font-bold border-l-2 text-[10px] sm:text-sm transition-colors ${isCompleted ? 'bg-pink-600 text-white border-pink-400' : 'bg-zinc-900 text-zinc-300 border-pink-500 group-hover:bg-pink-600 group-hover:text-white'}">${m.date}</div>
                                <div class="text-zinc-500 font-mono text-[10px] sm:text-sm">${m.time}</div>
                            </div>
                            <div class="text-pink-500 font-bold text-[9px] sm:text-xs border border-pink-500/30 px-1.5 sm:px-2 py-0.5 rounded-sm uppercase tracking-wider">${m.match}</div>
                        </div>
                        <div class="flex items-center justify-center space-x-2 sm:space-x-4 px-2 sm:px-4 py-1.5 sm:py-2 rounded-sm border transition-colors relative z-10 w-full sm:w-auto ${isCompleted ? 'bg-zinc-900/80 border-zinc-700' : 'bg-zinc-900/50 border-zinc-800/50 group-hover:border-pink-500/30'}">
                            <span class="w-8 sm:w-10 text-right font-black text-base sm:text-xl tracking-wider ${aWon ? 'text-pink-400' : (isTie ? 'text-zinc-300' : 'text-white')}">${m.teamA}</span>
                            ${scoreHtml}
                            <span class="w-8 sm:w-10 text-left font-black text-base sm:text-xl tracking-wider ${bWon ? 'text-pink-400' : (isTie ? 'text-zinc-300' : 'text-white')}">${m.teamB}</span>
                        </div>
                        ${isCompleted && !isTie ? '<div class="absolute inset-0 bg-gradient-to-r from-transparent via-pink-500/5 to-transparent opacity-50"></div>' : ''}
                        ${isTie ? '<div class="absolute inset-0 bg-gradient-to-r from-transparent via-zinc-500/10 to-transparent opacity-50"></div>' : ''}
                    </li>
                `;
            };

            container.innerHTML = groups.map(g => `
                <div class="bg-zinc-950 p-4 sm:p-5 rounded-lg border border-zinc-800/50">
                    <h4 class="text-lg sm:text-xl font-black text-white mb-3 sm:mb-4 border-b-2 border-zinc-800 pb-2 sm:pb-3 flex items-center justify-between">
                        ${g.title} <span class="text-[10px] sm:text-xs font-bold text-pink-500 bg-pink-500/10 px-2 py-1 rounded-sm tracking-widest">${g.classes}</span>
                    </h4>
                    <ul class="space-y-2 sm:space-y-3">
                        ${state.matches.filter(m => m.grade === g.grade).map(createMatchItem).join('')}
                    </ul>
                </div>
            `).join('');
        }

        /* --- 打線區邏輯 --- */
        function getGradeAlertData(grade) {
            switch(grade) {
                case '3': return { text: "每半局 12-13 人打擊（下半局不重複）。兩局共計 24-26 棒次。", limit: 26 };
                case '4': return { text: "每半局 10-12 人打擊（下半局不重複）。兩局共計 20-24 棒次。", limit: 24 };
                case '5': return { text: "每半局 13-14 人打擊（下半局不重複）。防守須由當局攻擊者選派 9 人。", limit: 30 };
                case '6': return { text: "每半局 16-18 人打擊。循環打擊制（每人打兩次，上下半局打線完全相同）。", limit: 19 };
                default: return { text: "請先選擇年級以查看排棒限制。", limit: 19 };
            }
        }

        function handleLineupGradeChange() {
            const select = document.getElementById('lineup-grade-select');
            state.lineup.grade = select.value;
            state.lineup.cls = '';
            
            const classSelect = document.getElementById('lineup-class-select');
            classSelect.innerHTML = '<option value="">-- 選擇 --</option>';
            if (state.lineup.grade) {
                classOptions[state.lineup.grade].forEach(cls => {
                    classSelect.innerHTML += `<option value="${cls}">${cls} 班</option>`;
                });
                classSelect.disabled = false;
            } else {
                classSelect.disabled = true;
            }
            
            resetLineupUI();
            updateLineupUIState();
        }

        function handleLineupClassChange() {
            const cls = document.getElementById('lineup-class-select').value;
            state.lineup.cls = cls;
            
            if (cls && state.lineup.allClassesData[cls]) {
                state.lineup.data = [...state.lineup.allClassesData[cls].data];
                state.lineup.lastUpdated = state.lineup.allClassesData[cls].lastUpdated;
            } else {
                resetLineupUI();
            }
            
            // 如果已驗證過，可選擇保留狀態，這邊嚴格一點切換班級重新解鎖
            state.lineup.isUnlocked = false; 
            updateLineupUIState();
        }

        function resetLineupUI() {
            state.lineup.data = Array.from({ length: 30 }, () => ({ number: '', name: '' }));
            state.lineup.lastUpdated = null;
            state.lineup.isUnlocked = false;
            document.getElementById('lineup-passcode-input').value = '';
            document.getElementById('lineup-auth-error').classList.add('hidden');
        }

        function updateLineupUIState() {
            const lState = state.lineup;
            const alertData = getGradeAlertData(lState.grade);
            
            document.getElementById('lineup-alert-text').innerText = alertData.text;
            const subtext = document.getElementById('lineup-alert-subtext');
            const alertBox = document.getElementById('lineup-alert-box');
            const alertIcon = document.getElementById('lineup-alert-icon');

            if (lState.grade && lState.cls) {
                subtext.innerHTML = `目前編輯：<span class="font-black text-white">${lState.cls} 班</span>`;
                subtext.classList.remove('hidden');
                alertBox.className = 'p-3 sm:p-4 mb-4 sm:mb-6 rounded-md border flex items-start space-x-2 sm:space-x-3 transition-opacity duration-300 ' + (!lState.isUnlocked ? 'opacity-50 blur-sm ' : '') + 'bg-pink-900/10 border-pink-500/50 text-pink-200';
                alertIcon.className = 'w-4 h-4 sm:w-5 sm:h-5 shrink-0 mt-0.5 text-pink-500';
                alertIcon.setAttribute('data-lucide', 'alert-circle');
            } else {
                subtext.classList.add('hidden');
                alertBox.className = 'p-3 sm:p-4 mb-4 sm:mb-6 rounded-md border flex items-start space-x-2 sm:space-x-3 bg-zinc-800/50 border-zinc-700 text-zinc-400';
                alertIcon.className = 'w-4 h-4 sm:w-5 sm:h-5 shrink-0 mt-0.5 text-zinc-500';
            }

            const lockIconDiv = document.getElementById('lineup-lock-icon');
            if (lState.cls) {
                lockIconDiv.classList.remove('hidden');
                lockIconDiv.className = `p-1 sm:p-2 rounded shrink-0 ${lState.isUnlocked ? 'bg-green-500/10 text-green-500' : 'bg-red-500/10 text-red-500'}`;
                lockIconDiv.innerHTML = `<i data-lucide="${lState.isUnlocked ? 'unlock' : 'lock'}" class="w-4 h-4 sm:w-[18px] sm:h-[18px]"></i>`;
            } else {
                lockIconDiv.classList.add('hidden');
            }

            const controls = document.getElementById('lineup-controls');
            const csvInput = document.getElementById('csv-upload');
            const csvLabel = document.getElementById('csv-upload-label');
            const actionBtns = controls.querySelectorAll('button');

            if (lState.cls && lState.isUnlocked) {
                controls.classList.remove('opacity-50', 'pointer-events-none');
                csvInput.disabled = false;
                csvLabel.classList.remove('cursor-not-allowed');
                actionBtns.forEach(btn => btn.disabled = false);
            } else {
                controls.classList.add('opacity-50', 'pointer-events-none');
                csvInput.disabled = true;
                csvLabel.classList.add('cursor-not-allowed');
                actionBtns.forEach(btn => btn.disabled = true);
            }

            const updatedDiv = document.getElementById('lineup-last-updated-container');
            if (lState.lastUpdated) {
                document.getElementById('lineup-last-updated-text').innerText = lState.lastUpdated;
                updatedDiv.style.display = 'flex';
            } else {
                updatedDiv.style.display = 'none';
            }

            const overlayClass = document.getElementById('lineup-overlay-select-class');
            const overlayPwd = document.getElementById('lineup-overlay-password');
            const tableContainer = document.getElementById('lineup-table-container');

            if (!lState.cls) {
                overlayClass.style.display = 'flex';
                overlayPwd.style.display = 'none';
                tableContainer.className = "overflow-x-auto transition-all duration-500 opacity-30 pointer-events-none blur-[4px] select-none grayscale -mx-4 sm:mx-0 px-4 sm:px-0";
            } else if (!lState.isUnlocked) {
                overlayClass.style.display = 'none';
                overlayPwd.style.display = 'flex';
                document.getElementById('password-overlay-text').innerText = `請輸入 ${lState.cls} 班的導師密碼以解鎖編輯權限。`;
                tableContainer.className = "overflow-x-auto transition-all duration-500 opacity-30 pointer-events-none blur-[4px] select-none grayscale -mx-4 sm:mx-0 px-4 sm:px-0";
            } else {
                overlayClass.style.display = 'none';
                overlayPwd.style.display = 'none';
                tableContainer.className = "overflow-x-auto transition-all duration-500 opacity-100 -mx-4 sm:mx-0 px-4 sm:px-0";
            }

            renderLineupTable();
            lucide.createIcons();
        }

        function handleLineupUnlock(e) {
            e.preventDefault();
            const input = document.getElementById('lineup-passcode-input');
            const pwd = input.value;
            const cls = state.lineup.cls;
            const classPasscodes = {
                '301': '301111', '302': '302222', '303': '303333',
                '401': '401111', '402': '402222', '403': '403333',
                '501': '501111', '502': '502222', '503': '503333',
                '601': '601111', '602': '602222', '603': '603333'
            };

            if (pwd === classPasscodes[cls]) {
                state.lineup.isUnlocked = true;
                showToast(`${cls} 班打線編輯權限已解鎖`);
                updateLineupUIState();
            } else {
                const errorText = document.getElementById('lineup-auth-error');
                errorText.classList.remove('hidden');
                input.classList.remove('border-zinc-700', 'focus:border-pink-500');
                input.classList.add('border-red-500', 'focus:border-red-500', 'animate-shake');
                setTimeout(() => input.classList.remove('animate-shake'), 500);
            }
        }

        // 首次渲染整張表格
        function renderLineupTable() {
            const tbody = document.getElementById('lineup-tbody');
            const limit = getGradeAlertData(state.lineup.grade).limit;
            let html = '';

            for (let i = 0; i < limit; i++) {
                const player = state.lineup.data[i];
                const isHalfStart = i === Math.floor(limit / 2) && state.lineup.grade !== '6';
                const disabledStr = !state.lineup.isUnlocked ? 'disabled' : '';

                html += `
                    <tr id="lineup-row-${i}" class="lineup-row transition-colors group hover:bg-zinc-800/50 ${isHalfStart ? 'border-t-[3px] sm:border-t-4 border-pink-500/50' : ''}">
                        <td class="py-2 sm:py-3 px-2 sm:px-4 relative">
                            ${isHalfStart ? '<div class="absolute -top-2.5 sm:-top-3 left-1 sm:left-2 bg-pink-600 text-white text-[8px] sm:text-[10px] font-black px-1 sm:px-2 py-0.5 rounded shadow whitespace-nowrap">下半局</div>' : ''}
                            <span id="lineup-badge-${i}" class="lineup-badge inline-flex items-center justify-center w-6 h-6 sm:w-8 sm:h-8 rounded-full border font-mono text-[10px] sm:text-xs font-bold transition-colors bg-zinc-900 border-zinc-700 text-zinc-400 group-hover:border-pink-500 group-hover:text-pink-400">${i + 1}</span>
                        </td>
                        <td class="py-1.5 sm:py-2 px-1 sm:px-4"><input type="text" id="lineup-input-num-${i}" placeholder="座號" value="${escapeHTML(player.number)}" oninput="handleLineupInput(${i}, 'number', this)" class="lineup-number-input w-full px-2 sm:px-3 py-1.5 sm:py-2 text-white font-mono text-xs sm:text-sm transition-colors focus:outline-none focus:border-pink-500 bg-black border border-zinc-800 focus:bg-zinc-900 hover:border-zinc-600 rounded" ${disabledStr}></td>
                        <td class="py-1.5 sm:py-2 px-1 sm:px-4"><input type="text" placeholder="姓名" value="${escapeHTML(player.name)}" oninput="handleLineupInput(${i}, 'name', this)" class="lineup-name-input w-full px-2 sm:px-3 py-1.5 sm:py-2 text-white text-xs sm:text-sm transition-colors focus:outline-none focus:border-pink-500 bg-black border border-zinc-800 focus:bg-zinc-900 hover:border-zinc-600 rounded min-w-[80px]" ${disabledStr}></td>
                        <td class="py-1.5 sm:py-2 px-1 sm:px-4 text-center" id="lineup-status-${i}">
                            ${isHalfStart ? '<span class="text-[8px] sm:text-[10px] bg-zinc-800 text-zinc-400 px-1 sm:px-2 py-0.5 sm:py-1 rounded whitespace-nowrap">起點</span>' : ''}
                        </td>
                    </tr>
                `;
            }
            tbody.innerHTML = html;
            // 執行一次查重以更新渲染樣式
            if(state.lineup.grade && state.lineup.grade !== '6') checkDuplicates();
        }

        // MIT: 防抖動輸入處理與自動儲存至 LocalStorage
        let inputTimeout;
        function handleLineupInput(idx, field, el) {
            state.lineup.data[idx][field] = escapeHTML(el.value);
            
            if (field === 'number' && state.lineup.grade !== '6') {
                checkDuplicates();
            }

            clearTimeout(inputTimeout);
            inputTimeout = setTimeout(() => {
                if (state.lineup.cls) {
                    state.lineup.allClassesData[state.lineup.cls] = {
                        data: [...state.lineup.data],
                        lastUpdated: state.lineup.lastUpdated || new Date().toLocaleString('zh-TW', { hour12: false })
                    };
                    saveStateToLocal();
                }
            }, 500);
        }

        // MIT: 動態精準重複偵測標記
        function checkDuplicates() {
            const limit = getGradeAlertData(state.lineup.grade).limit;
            
            for (let i = 0; i < limit; i++) {
                const currentNum = state.lineup.data[i].number.trim();
                let isDup = false;

                if (currentNum !== '') {
                    const occurrences = state.lineup.data.slice(0, limit).filter(p => p.number.trim() === currentNum).length;
                    if (occurrences > 1) isDup = true;
                }

                const row = document.getElementById(`lineup-row-${i}`);
                const badge = document.getElementById(`lineup-badge-${i}`);
                const numInput = document.getElementById(`lineup-input-num-${i}`);
                const statusCell = document.getElementById(`lineup-status-${i}`);
                const isHalfStart = i === Math.floor(limit / 2) && state.lineup.grade !== '6';

                if (!row) continue;

                if (isDup) {
                    row.classList.add('bg-red-900/20');
                    row.classList.remove('hover:bg-zinc-800/50');
                    badge.className = 'lineup-badge inline-flex items-center justify-center w-6 h-6 sm:w-8 sm:h-8 rounded-full border font-mono text-[10px] sm:text-xs font-bold transition-colors bg-red-900 border-red-500 text-red-200';
                    numInput.className = 'lineup-number-input w-full px-2 sm:px-3 py-1.5 sm:py-2 text-white font-mono text-xs sm:text-sm transition-colors focus:outline-none focus:border-pink-500 bg-red-950/50 border border-red-500 rounded';
                    statusCell.innerHTML = '<span class="flex items-center justify-center text-[8px] sm:text-[10px] text-red-400 font-bold bg-red-900/30 px-1 sm:px-2 py-0.5 sm:py-1 rounded whitespace-nowrap"><svg xmlns="http://www.w3.org/2000/svg" width="10" height="10" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="mr-1 hidden sm:block"><path d="m21.73 18-8-14a2 2 0 0 0-3.48 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3Z"></path><line x1="12" y1="9" x2="12" y2="13"></line><line x1="12" y1="17" x2="12.01" y2="17"></line></svg>重複</span>';
                } else {
                    row.classList.remove('bg-red-900/20');
                    row.classList.add('hover:bg-zinc-800/50');
                    badge.className = 'lineup-badge inline-flex items-center justify-center w-6 h-6 sm:w-8 sm:h-8 rounded-full border font-mono text-[10px] sm:text-xs font-bold transition-colors bg-zinc-900 border-zinc-700 text-zinc-400 group-hover:border-pink-500 group-hover:text-pink-400';
                    numInput.className = 'lineup-number-input w-full px-2 sm:px-3 py-1.5 sm:py-2 text-white font-mono text-xs sm:text-sm transition-colors focus:outline-none focus:border-pink-500 bg-black border border-zinc-800 focus:bg-zinc-900 hover:border-zinc-600 rounded';
                    statusCell.innerHTML = isHalfStart ? '<span class="text-[8px] sm:text-[10px] bg-zinc-800 text-zinc-400 px-1 sm:px-2 py-0.5 sm:py-1 rounded whitespace-nowrap">起點</span>' : '';
                }
            }
        }

        function handleCsvImport(e) {
            const file = e.target.files[0];
            if (!file) return;
            
            const icon = document.getElementById('csv-upload-icon');
            const text = document.getElementById('csv-upload-text');
            icon.classList.add('animate-bounce');
            text.innerText = '解析中...';

            const reader = new FileReader();
            reader.onload = (ev) => {
                try {
                    const txt = ev.target.result;
                    const rows = txt.split(/\r?\n/).filter(row => row.trim().length > 0).map(row => row.split(','));
                    const limit = getGradeAlertData(state.lineup.grade).limit;
                    const newD = [];

                    for (let i = 1; i <= limit; i++) {
                        if (rows[i]) {
                            newD.push({ number: escapeHTML(rows[i][1]?.trim() || ''), name: escapeHTML(rows[i][2]?.trim() || '') });
                        } else {
                            newD.push({ number: '', name: '' });
                        }
                    }
                    state.lineup.data = Array.from({ length: 30 }, (_, i) => newD[i] || { number: '', name: '' });
                    state.lineup.lastUpdated = new Date().toLocaleString('zh-TW', { hour12: false });
                    
                    if (state.lineup.cls) {
                        state.lineup.allClassesData[state.lineup.cls] = {
                            data: [...state.lineup.data],
                            lastUpdated: state.lineup.lastUpdated
                        };
                        saveStateToLocal();
                    }
                    
                    showToast(`已成功匯入 ${state.lineup.cls} 班打線`);
                } catch (error) {
                    showToast('CSV 解析失敗，請確認格式');
                } finally {
                    icon.classList.remove('animate-bounce');
                    text.innerText = '匯入CSV';
                    e.target.value = null;
                    updateLineupUIState();
                }
            };
            reader.readAsText(file);
        }

        function handleDownloadTemplate() {
            const grade = state.lineup.grade;
            const limit = getGradeAlertData(grade).limit;
            let csvContent = '\uFEFF' + "棒次,座號,選手姓名\n";
            for (let i = 1; i <= limit; i++) {
                csvContent += (i === 1) ? `${i},1,王小明\n` : `${i},,\n`;
            }
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            link.href = URL.createObjectURL(blob);
            link.download = `忠義國小_${grade}年級_打線範本.csv`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showToast(`已下載範本 (至 ${limit} 棒)`);
        }

        function handleClearLineup() {
            if (window.confirm(`確定要清空 ${state.lineup.cls} 班的資料嗎？`)) {
                state.lineup.data = Array.from({ length: 30 }, () => ({ number: '', name: '' }));
                if (state.lineup.cls) {
                    state.lineup.allClassesData[state.lineup.cls] = {
                        data: [...state.lineup.data],
                        lastUpdated: null
                    };
                    saveStateToLocal();
                }
                updateLineupUIState();
                showToast('資料已清空');
            }
        }

        function handleSaveLineup() {
            state.lineup.lastUpdated = new Date().toLocaleString('zh-TW', { hour12: false });
            if (state.lineup.cls) {
                state.lineup.allClassesData[state.lineup.cls] = {
                    data: [...state.lineup.data],
                    lastUpdated: state.lineup.lastUpdated
                };
                saveStateToLocal();
            }
            updateLineupUIState();
            showToast(`${state.lineup.cls} 班打擊順序已儲存`);
        }

        /* --- 成績區邏輯 --- */
        function handleAdminAuth(e) {
            e.preventDefault();
            const input = document.getElementById('admin-passcode-input');
            if (input.value === '840110') {
                state.admin.isUnlocked = true;
                closeAdminModal();
                input.value = '';
                showToast('管理權限解鎖');
                renderScoreboard();
                renderFileList();
            } else {
                const error = document.getElementById('admin-auth-error');
                error.classList.remove('hidden');
                input.classList.remove('border-zinc-700', 'focus:border-pink-500');
                input.classList.add('border-red-500', 'focus:border-red-500', 'animate-shake');
                setTimeout(() => input.classList.remove('animate-shake'), 500);
            }
        }

        function renderScoreboard() {
            const sStatus = document.getElementById('admin-lock-status');
            if (state.admin.isUnlocked) {
                sStatus.innerHTML = `<span class="text-[10px] sm:text-xs font-bold text-green-400 border border-green-500/30 px-2 sm:px-3 py-1 sm:py-1.5 rounded flex items-center bg-green-500/10 w-fit"><i data-lucide="unlock" class="w-3 h-3 sm:w-[14px] sm:h-[14px] mr-1 sm:mr-2"></i> 管理模式：可新增/刪除比分</span>`;
            } else {
                sStatus.innerHTML = `<button onclick="openAdminModal()" class="text-[10px] sm:text-xs font-bold text-zinc-300 hover:text-white border border-zinc-700 hover:border-pink-500 px-2 sm:px-4 py-1.5 sm:py-2 rounded flex items-center bg-zinc-800 hover:bg-zinc-700 w-fit transition-colors group"><i data-lucide="lock" class="w-3 h-3 sm:w-[14px] sm:h-[14px] mr-1 sm:mr-2 text-pink-500 group-hover:scale-110 transition-transform"></i> 管理員解鎖</button>`;
            }

            const container = document.getElementById('scoreboard-container');
            container.innerHTML = ['3', '4', '5', '6'].map(grade => {
                const matches = state.matches.filter(m => m.grade === grade);
                const matchesHtml = matches.map(m => {
                    const isComp = m.status === 'completed';
                    let contentHtml = '';
                    
                    if (state.admin.isUnlocked) {
                        if (isComp) {
                            contentHtml = `
                                <div class="flex items-center justify-between mt-2 bg-zinc-900/50 p-2 sm:p-3 rounded border border-zinc-800/50">
                                    <div class="text-base sm:text-lg font-black text-white flex-1 text-center tracking-wider">${m.teamA} <span class="text-pink-500 mx-2 sm:mx-3">${m.scoreA} : ${m.scoreB}</span> ${m.teamB}</div>
                                    <button onclick="resetMatchScore('${m.id}')" class="ml-2 sm:ml-3 text-zinc-500 hover:text-red-400 bg-black p-1.5 sm:p-2 rounded border border-zinc-800 hover:border-red-500/50 transition-colors" title="刪除成績"><i data-lucide="trash-2" class="w-3 h-3 sm:w-4 sm:h-4"></i></button>
                                </div>`;
                        } else {
                            contentHtml = `
                                <form onsubmit="submitScore(event, '${m.id}')" class="flex items-center justify-between mt-2 space-x-1 sm:space-x-2 bg-zinc-900/50 p-2 sm:p-3 rounded border border-zinc-800/50">
                                    <span class="font-black text-zinc-300 text-xs sm:text-sm w-6 sm:w-10 text-right">${m.teamA}</span>
                                    <input name="scoreA" type="number" min="0" required placeholder="分" class="w-10 sm:w-14 bg-black border border-zinc-700 text-white text-center font-bold text-sm sm:text-base rounded py-1 sm:py-1.5 focus:border-pink-500 outline-none transition-colors">
                                    <span class="text-zinc-600 text-[10px] sm:text-xs font-black italic">vs</span>
                                    <input name="scoreB" type="number" min="0" required placeholder="分" class="w-10 sm:w-14 bg-black border border-zinc-700 text-white text-center font-bold text-sm sm:text-base rounded py-1 sm:py-1.5 focus:border-pink-500 outline-none transition-colors">
                                    <span class="font-black text-zinc-300 text-xs sm:text-sm w-6 sm:w-10">${m.teamB}</span>
                                    <button type="submit" class="bg-pink-600 hover:bg-pink-500 text-white p-1.5 sm:p-2 rounded transition-transform active:scale-95 ml-1 sm:ml-2" title="儲存"><i data-lucide="save" class="w-3 h-3 sm:w-4 sm:h-4"></i></button>
                                </form>`;
                        }
                    } else {
                        if (isComp) {
                            const sA = parseInt(m.scoreA), sB = parseInt(m.scoreB);
                            contentHtml = `
                                <div class="text-lg sm:text-xl font-black text-zinc-300 flex items-center justify-center mt-2 py-1 sm:py-2">
                                    <span class="${sA > sB ? 'text-pink-400 drop-shadow-[0_0_8px_rgba(236,72,153,0.5)]' : (sA === sB ? 'text-white' : 'text-zinc-500')}">${m.teamA} <span class="ml-1 sm:ml-2 text-white">${m.scoreA}</span></span>
                                    <span class="text-zinc-600 mx-2 sm:mx-4 font-normal">-</span>
                                    <span class="${sB > sA ? 'text-pink-400 drop-shadow-[0_0_8px_rgba(236,72,153,0.5)]' : (sA === sB ? 'text-white' : 'text-zinc-500')}"><span class="mr-1 sm:mr-2 text-white">${m.scoreB}</span> ${m.teamB}</span>
                                </div>`;
                        } else {
                            contentHtml = `<div class="text-lg sm:text-xl font-black text-zinc-300 flex items-center justify-center mt-2 py-1 sm:py-2"><span class="text-sm sm:text-base font-bold text-zinc-600 tracking-widest">${m.teamA} <span class="mx-1 sm:mx-2 text-[10px] sm:text-xs italic">vs</span> ${m.teamB}</span></div>`;
                        }
                    }

                    return `
                        <div class="flex flex-col bg-black border ${isComp ? 'border-pink-500/30' : 'border-zinc-800'} p-3 sm:p-4 rounded-md hover:border-zinc-600 transition-colors shadow-inner">
                            <div class="flex justify-between items-center mb-1 sm:mb-3">
                                <span class="text-[10px] sm:text-xs text-zinc-400 font-mono bg-zinc-900 px-1.5 sm:px-2 py-0.5 sm:py-1 rounded">${m.match}</span>
                                <span class="text-[8px] sm:text-[10px] ${isComp ? 'bg-green-500/20 text-green-400 font-bold' : 'bg-zinc-800 text-zinc-500'} px-1.5 sm:px-2 py-0.5 sm:py-1 rounded uppercase tracking-widest">${isComp ? '已發佈' : '未登錄'}</span>
                            </div>
                            ${contentHtml}
                        </div>`;
                }).join('');

                return `
                    <div class="space-y-3 sm:space-y-4 bg-zinc-950 p-4 rounded-lg border border-zinc-800/50">
                        <h4 class="text-sm sm:text-base font-black text-pink-500 tracking-widest border-b border-zinc-800 pb-2 mb-2 sm:mb-4 flex items-center"><span class="bg-pink-500/20 px-2 py-0.5 rounded-sm mr-2">${grade}</span> 年級</h4>
                        ${matchesHtml}
                    </div>`;
            }).join('');
            
            document.getElementById('attachment-upload-container').style.display = state.admin.isUnlocked ? 'block' : 'none';
            lucide.createIcons();
        }

        function submitScore(e, id) {
            e.preventDefault();
            const sA = escapeHTML(e.target.scoreA.value);
            const sB = escapeHTML(e.target.scoreB.value);
            const matchIndex = state.matches.findIndex(m => m.id === id);
            if (matchIndex !== -1 && sA && sB) {
                state.matches[matchIndex] = { ...state.matches[matchIndex], scoreA: sA, scoreB: sB, status: 'completed' };
                saveStateToLocal();
                renderScoreboard();
                renderSchedule(); 
                showToast('賽事比分已登錄與發佈！');
            }
        }

        function resetMatchScore(id) {
            if (window.confirm('確定要刪除這筆紀錄嗎？')) {
                const matchIndex = state.matches.findIndex(m => m.id === id);
                if (matchIndex !== -1) {
                    state.matches[matchIndex] = { ...state.matches[matchIndex], scoreA: '', scoreB: '', status: 'pending' };
                    saveStateToLocal();
                    renderScoreboard();
                    renderSchedule(); 
                    showToast('比分紀錄已刪除');
                }
            }
        }

        function handleFileUpload(e) {
            const file = e.target.files[0];
            if (!file) return;
            const text = document.getElementById('file-upload-text');
            const origText = text.innerText;
            text.innerText = '上傳中...';
            
            setTimeout(() => {
                state.files.unshift({
                    id: Date.now(),
                    name: escapeHTML(file.name),
                    date: new Date().toISOString().split('T')[0]
                });
                text.innerText = origText;
                e.target.value = null;
                saveStateToLocal();
                renderFileList();
                showToast('檔案已成功上傳');
            }, 1000);
        }

        function renderFileList() {
            const container = document.getElementById('file-list-container');
            if (state.files.length === 0) {
                container.innerHTML = `
                    <div class="p-8 sm:p-16 flex flex-col items-center justify-center text-zinc-500 border-2 border-dashed border-zinc-800 m-4 sm:m-6 rounded-xl bg-black/20">
                        <i data-lucide="file-text" class="w-8 h-8 sm:w-12 sm:h-12 mb-2 sm:mb-4 text-zinc-700"></i>
                        <p class="font-bold tracking-widest uppercase text-xs sm:text-sm text-center">目前尚無發佈的成績或賽事檔案</p>
                    </div>`;
            } else {
                container.innerHTML = state.files.map(f => `
                    <div class="p-4 sm:p-6 flex flex-col sm:flex-row sm:items-center justify-between hover:bg-zinc-800/80 transition-all duration-300 group">
                        <div class="flex items-center space-x-3 sm:space-x-5 mb-3 sm:mb-0">
                            <div class="p-3 sm:p-4 bg-black border border-zinc-800 text-pink-500 rounded-lg group-hover:scale-110 group-hover:border-pink-500/50 transition-all duration-300 shadow-inner">
                                <i data-lucide="file-text" class="w-5 h-5 sm:w-7 sm:h-7"></i>
                            </div>
                            <div>
                                <h4 class="text-base sm:text-lg font-black text-white group-hover:text-pink-400 transition-colors break-all line-clamp-1">${f.name}</h4>
                                <p class="text-xs sm:text-sm text-zinc-500 font-mono mt-0.5 sm:mt-1 flex items-center"><i data-lucide="calendar-days" class="w-3 h-3 sm:w-3.5 sm:h-3.5 mr-1 sm:mr-1.5"></i>發佈: ${f.date}</p>
                            </div>
                        </div>
                        <button class="w-full sm:w-auto text-zinc-400 hover:text-white transition-colors px-4 sm:px-6 py-2 sm:py-3 border border-zinc-700 hover:border-pink-500 hover:bg-pink-600 uppercase text-[10px] sm:text-xs font-black tracking-widest rounded-sm transform hover:-translate-y-0.5 shadow-md">下載</button>
                    </div>
                `).join('');
            }
            lucide.createIcons();
        }

        /* --- 系統初始化 --- */
        window.onload = () => {
            // 讀取本地端緩存 (MIT: 確保跨頁面資料不遺失)
            loadStateFromLocal();
            
            // 首次渲染全部 DOM
            renderGradeCards();
            renderSchedule();
            renderChecklist();
            renderScoreboard();
            renderFileList();
            switchTab('rules');
        };

    </script>
</body>
</html>
