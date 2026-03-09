<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    
    <title>忠義國小樂樂棒賽事整合平台</title>
    <meta name="description" content="忠義國小 114學年度 樂樂棒球班際體育競賽 - 賽程、規則、打線安排與成績公告整合平台">
    <meta name="theme-color" content="#18181b">
    
    <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>⚾</text></svg>">

    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>

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
                    }
                }
            }
        }
    </script>

    <style>
        .tab-content { display: none; }
        .tab-content.active { display: block; animation: fadeIn 0.3s ease-out forwards; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; -webkit-overflow-scrolling: touch; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .table-scroll-container { -webkit-overflow-scrolling: touch; scroll-behavior: smooth; }

        html, body {
            height: 100%;
            width: 100%;
            margin: 0;
            padding: 0;
            background-color: #000;
            color: #e4e4e7; 
            font-family: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            overscroll-behavior-y: none;
        }
        ::selection { background-color: #ec4899; color: white; }
        body.modal-open { overflow: hidden; }

        #main-container {
            min-height: 100vh; /* Fallback for older browsers */
            min-height: 100dvh; /* Dynamic viewport height for modern mobile browsers */
            display: flex;
            flex-direction: column;
        }

        input[type="text"], input[type="password"], input[type="number"], select, button {
            min-height: 44px; /* Mobile touch target standard */
        }

        /* 移除 number input 預設的上下箭頭，讓介面更乾淨 */
        input[type="number"]::-webkit-outer-spin-button,
        input[type="number"]::-webkit-inner-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type="number"] {
            -moz-appearance: textfield;
        }
        
        @keyframes shake {
            10%, 90% { transform: translate3d(-2px, 0, 0); }
            20%, 80% { transform: translate3d(3px, 0, 0); }
            30%, 50%, 70% { transform: translate3d(-5px, 0, 0); }
            40%, 60% { transform: translate3d(5px, 0, 0); }
        }
        .animate-shake { animation: shake 0.4s cubic-bezier(.36,.07,.19,.97) both; }
        @keyframes fadeIn {
            0% { opacity: 0; transform: translateY(5px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        @keyframes slideInUp {
            0% { opacity: 0; transform: translateY(10px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        .animate-in { animation: slideInUp 0.4s ease-out forwards; }
    </style>
</head>
<body class="bg-black text-zinc-200 no-scrollbar">

    <!-- 全局提示訊息框 -->
    <div id="toast" class="fixed top-5 left-1/2 transform -translate-x-1/2 z-50 hidden bg-pink-600 text-white px-6 py-3 rounded-full shadow-2xl items-center space-x-2 animate-in whitespace-nowrap">
        <i data-lucide="info" class="w-5 h-5"></i>
        <span id="toast-message" class="font-bold tracking-wide text-sm"></span>
    </div>

    <!-- 管理員登入 Modal -->
    <div id="admin-login-modal" class="fixed inset-0 z-[100] hidden items-center justify-center bg-black/80 backdrop-blur-sm p-4">
        <div class="bg-zinc-900 border border-zinc-800 rounded-xl w-full max-w-sm p-6 shadow-2xl relative">
            <button onclick="closeAdminModal()" class="absolute top-4 right-4 text-zinc-500 hover:text-white transition-colors p-2">
                <i data-lucide="x" class="w-5 h-5"></i>
            </button>
            <div class="flex flex-col items-center mb-6">
                <div class="w-12 h-12 bg-zinc-800 rounded-full flex items-center justify-center mb-3 text-pink-500 border border-zinc-700">
                    <i data-lucide="shield-check" class="w-6 h-6"></i>
                </div>
                <h3 class="text-xl font-black text-white tracking-widest">系統管理員登入</h3>
                <p class="text-xs text-zinc-500 mt-2">請輸入最高權限密碼 (六碼)</p>
            </div>
            <form onsubmit="handleAdminAuth(event)" class="space-y-4">
                <div>
                    <!-- text-base 防止 iOS 點擊輸入框時畫面放大跑版 -->
                    <input type="password" id="admin-passcode-input" class="w-full bg-black border border-zinc-800 rounded-md px-4 py-3 text-center text-white text-base font-mono tracking-widest focus:outline-none focus:border-pink-500 transition-colors" placeholder="••••••" maxlength="6">
                    <p id="admin-auth-error" class="hidden text-red-500 text-xs text-center mt-2 font-bold">密碼錯誤，請重新輸入</p>
                </div>
                <button type="submit" class="w-full bg-pink-600 hover:bg-pink-500 text-white font-bold py-3 rounded-md transition-all active:scale-95 tracking-widest">驗證解鎖</button>
            </form>
        </div>
    </div>

    <div id="main-container">
        <!-- 標題區塊重構：完美適應手機與電腦版 -->
        <header class="bg-zinc-950 border-b border-zinc-800 sticky top-0 z-40 shadow-sm">
            <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                
                <!-- 上半部：標題與動作按鈕 -->
                <div class="flex flex-col sm:flex-row sm:items-center justify-between py-3 sm:py-0 sm:h-20 gap-3 sm:gap-0">
                    
                    <!-- Logo & 標題區 -->
                    <div class="flex items-center space-x-3 shrink-0">
                        <div class="w-10 h-10 sm:w-12 sm:h-12 bg-gradient-to-br from-pink-500 to-pink-700 rounded-xl flex items-center justify-center shadow-lg transform -rotate-6 shrink-0">
                            <i data-lucide="trophy" class="w-5 h-5 sm:w-6 sm:h-6 text-white"></i>
                        </div>
                        <div class="flex flex-col justify-center">
                            <h1 class="text-lg sm:text-2xl font-black text-white tracking-wider leading-tight">
                                忠義國小樂樂棒<span class="text-pink-500 hidden sm:inline">賽事整合平台</span>
                            </h1>
                            <p class="text-[10px] sm:text-xs font-mono text-zinc-500 tracking-widest mt-0.5">114學年度班際體育競賽系統</p>
                        </div>
                    </div>
                    
                    <!-- 動作按鈕區 (手機版 50/50 均分並排，電腦版靠右並排) -->
                    <div class="grid grid-cols-2 gap-2 sm:flex sm:items-center sm:space-x-3 sm:w-auto">
                        <a href="https://drive.google.com/file/d/1w3WT5IjYvBD6BNSiOQlogWvd-HwJUTkG/view?usp=sharing" target="_blank" class="flex items-center justify-center space-x-1.5 bg-zinc-900 hover:bg-zinc-800 border border-zinc-700 hover:border-pink-500 text-pink-400 px-3 py-2 sm:px-4 sm:py-2 rounded-lg transition-all duration-300 shadow-sm active:scale-95 group">
                            <i data-lucide="map" class="w-3.5 h-3.5 sm:w-4 sm:h-4 group-hover:scale-110 transition-transform"></i>
                            <span class="text-xs sm:text-sm font-bold tracking-widest">場地圖</span>
                        </a>
                        <a href="https://drive.google.com/file/d/1GJLKIs04G4LRvf1omVmpTVhfwrC3nu7Z/view?usp=sharing" target="_blank" class="flex items-center justify-center space-x-1.5 bg-zinc-900 hover:bg-zinc-800 border border-zinc-700 hover:border-pink-500 text-pink-400 px-3 py-2 sm:px-4 sm:py-2 rounded-lg transition-all duration-300 shadow-sm active:scale-95 group">
                            <i data-lucide="external-link" class="w-3.5 h-3.5 sm:w-4 sm:h-4 group-hover:scale-110 transition-transform"></i>
                            <span class="text-xs sm:text-sm font-bold tracking-widest">大會總表</span>
                        </a>
                    </div>
                </div>
                
                <!-- 下半部：頁籤導航區 -->
                <div class="relative w-full -mx-4 px-4 sm:mx-0 sm:px-0 border-t border-zinc-800/50 sm:border-none mt-1 sm:mt-0">
                    <nav class="flex overflow-x-auto no-scrollbar space-x-1 sm:space-x-6 w-full relative">
                        <button class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1.5 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-[3px] text-pink-500 border-pink-500 bg-pink-500/10 text-xs sm:text-sm" data-target="rules" onclick="switchTab('rules')">
                            <i data-lucide="book-open" class="w-3.5 h-3.5 sm:w-4 sm:h-4"></i><span>賽事規則</span>
                        </button>
                        <button class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1.5 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-[3px] text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm" data-target="lineup" onclick="switchTab('lineup')">
                            <i data-lucide="users" class="w-3.5 h-3.5 sm:w-4 sm:h-4"></i><span>打線安排</span>
                        </button>
                        <button class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1.5 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-[3px] text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm" data-target="schedule" onclick="switchTab('schedule')">
                            <i data-lucide="calendar" class="w-3.5 h-3.5 sm:w-4 sm:h-4"></i><span>賽程成績</span>
                        </button>
                        <button class="tab-btn flex-shrink-0 flex items-center justify-center space-x-1.5 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-[3px] text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm" data-target="admin" id="tab-btn-admin" onclick="switchTab('admin')">
                            <i data-lucide="settings" class="w-3.5 h-3.5 sm:w-4 sm:h-4"></i><span>後台管理</span>
                        </button>
                    </nav>
                </div>
            </div>
        </header>

        <!-- 內容區塊 -->
        <main class="flex-1 max-w-7xl mx-auto w-full p-4 sm:p-6 lg:p-8">
            
            <!-- 賽事規則 Tab -->
            <div id="tab-rules" class="tab-content">
                <div class="mb-8">
                    <h2 class="text-2xl sm:text-3xl font-black text-white mb-2 flex items-center">
                        <i data-lucide="gavel" class="w-6 h-6 sm:w-8 sm:h-8 mr-3 text-pink-500"></i>大會規則與賽制
                    </h2>
                    <p class="text-zinc-400 text-sm sm:text-base mb-6">各年級賽制與計分方式皆有不同，請各班體育股長與導師詳閱。</p>
                    <div id="grade-cards-container" class="space-y-6"></div>
                </div>

                <div class="mt-12">
                    <h2 class="text-2xl sm:text-3xl font-black text-white mb-6 flex items-center">
                        <i data-lucide="clipboard-check" class="w-6 h-6 sm:w-8 sm:h-8 mr-3 text-pink-500"></i>賽前確認清單
                    </h2>
                    <div id="checklist-container" class="grid grid-cols-1 md:grid-cols-3 gap-4 bg-zinc-950 p-4 rounded-xl border border-zinc-800"></div>
                </div>
            </div>

            <!-- 打線安排 Tab -->
            <div id="tab-lineup" class="tab-content">
                <div class="bg-zinc-950 border border-zinc-800 rounded-xl p-4 sm:p-6 md:p-8 shadow-xl">
                    <div class="flex flex-col md:flex-row md:items-end justify-between mb-6 border-b border-zinc-800 pb-6 space-y-4 md:space-y-0">
                        <div>
                            <h2 class="text-2xl sm:text-3xl font-black text-white flex items-center mb-2">
                                <i data-lucide="clipboard-list" class="w-6 h-6 sm:w-8 sm:h-8 mr-3 text-pink-500"></i>打線名單繳交
                            </h2>
                            <p class="text-sm text-zinc-500">請<strong class="text-pink-500 font-bold">各班導師</strong>下載專屬範本，填寫完畢後上傳至大會雲端資料夾。</p>
                        </div>
                    </div>

                    <!-- 控制列 -->
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 sm:gap-6 mb-6">
                        <div class="flex flex-col">
                            <label class="text-xs font-bold text-zinc-500 mb-1.5 uppercase tracking-wider pl-1">步驟一：選擇年級下載範本</label>
                            <div class="flex space-x-2">
                                <select id="lineup-grade-select" class="bg-black border border-zinc-800 text-white text-base sm:text-sm rounded-md focus:ring-pink-500 focus:border-pink-500 block w-full p-2.5 sm:p-3 outline-none transition-colors">
                                    <option value="">-- 請選擇年級 --</option>
                                    <option value="3">三年級</option>
                                    <option value="4">四年級</option>
                                    <option value="5">五年級</option>
                                    <option value="6">六年級</option>
                                </select>
                                <button onclick="handleDownloadTemplate()" class="shrink-0 bg-zinc-800 hover:bg-zinc-700 text-white text-sm font-bold px-5 rounded flex items-center justify-center transition-colors border border-zinc-700" title="下載該年級空白表格">
                                    <i data-lucide="download" class="w-4 h-4 mr-1.5"></i>下載
                                </button>
                            </div>
                        </div>
                        <div class="flex flex-col">
                            <label class="text-xs font-bold text-zinc-500 mb-1.5 uppercase tracking-wider pl-1">步驟二：上傳已填妥的檔案</label>
                            <a href="https://drive.google.com/drive/folders/1bMo9acIoeT8-lWZXmY3yAf8pawh7v5f8?usp=drive_link" target="_blank" class="w-full h-[44px] bg-pink-600 hover:bg-pink-500 text-white text-sm font-bold rounded flex items-center justify-center transition-colors border border-pink-500 shadow-lg shadow-pink-900/20 group">
                                <i data-lucide="cloud-upload" class="w-5 h-5 mr-2 group-hover:-translate-y-1 transition-transform"></i>前往 Google 雲端繳交
                            </a>
                        </div>
                    </div>

                    <!-- 提示說明 -->
                    <div class="bg-black border border-zinc-800 p-4 sm:p-6 rounded-lg">
                        <h4 class="text-sm font-bold text-pink-400 mb-3 flex items-center"><i data-lucide="info" class="w-4 h-4 mr-2"></i>繳交流程說明</h4>
                        <ol class="list-decimal list-inside space-y-2 text-zinc-300 text-sm">
                            <li>請先於上方選單選擇您任教的年級，並點擊「下載」取得大會標準 CSV 範本。</li>
                            <li>使用 Excel 或 Numbers 開啟範本，依照棒次填入選手的「座號」與「姓名」。</li>
                            <li>檔案請以 <span class="font-mono text-pink-300 bg-zinc-900 px-1 rounded">年級班級_打線名單.csv</span> 命名 (例如：301_打線名單.csv)。</li>
                            <li>點擊上方「前往 Google 雲端繳交」按鈕，將檔案拖曳上傳至指定的雲端資料夾即完成報名。</li>
                        </ol>
                    </div>
                </div>
            </div>

            <!-- 賽程成績 Tab -->
            <div id="tab-schedule" class="tab-content">
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
                    <div class="lg:col-span-2">
                        <h2 class="text-2xl sm:text-3xl font-black text-white mb-6 flex items-center">
                            <i data-lucide="calendar-check" class="w-6 h-6 sm:w-8 sm:h-8 mr-3 text-pink-500"></i>賽程與成績表
                        </h2>
                        <div id="schedule-container" class="space-y-6"></div>
                    </div>
                    <div>
                        <h2 class="text-2xl sm:text-3xl font-black text-white mb-6 flex items-center">
                            <i data-lucide="folder-down" class="w-6 h-6 sm:w-8 sm:h-8 mr-3 text-pink-500"></i>大會文件下載
                        </h2>
                        <div class="bg-zinc-950 border border-zinc-800 rounded-xl overflow-hidden shadow-xl">
                            <div class="bg-zinc-900 px-4 py-3 border-b border-zinc-800 flex items-center justify-between">
                                <span class="text-xs font-bold text-zinc-400 uppercase tracking-widest">公開附件列表</span>
                            </div>
                            <div id="file-list-container" class="divide-y divide-zinc-800"></div>
                        </div>
                    </div>
                </div>

                <!-- 整合：成績公告與登錄面板 -->
                <div class="mt-8 border-t-2 border-zinc-800 pt-8">
                    <div class="flex flex-col sm:flex-row sm:items-center justify-between mb-6 gap-y-4">
                        <h2 class="text-2xl sm:text-3xl font-black text-white flex items-center">
                            <i data-lucide="megaphone" class="w-6 h-6 sm:w-8 sm:h-8 mr-3 text-pink-500"></i>成績公告與登錄
                        </h2>
                        <div id="schedule-admin-lock-status" class="w-full sm:w-auto"></div>
                    </div>
                    
                    <div id="scoreboard-wrapper" class="hidden">
                        <div class="bg-zinc-950 border border-zinc-800 p-4 sm:p-6 rounded-xl shadow-lg">
                            <div class="flex items-center justify-between border-b border-zinc-800 pb-3 mb-4">
                                <h3 class="text-base sm:text-lg font-black text-white flex items-center">
                                    <i data-lucide="pen-tool" class="w-4 h-4 sm:w-5 sm:h-5 mr-2 text-pink-500"></i>比分與賽果登錄面板
                                </h3>
                            </div>
                            <div id="scoreboard-container" class="space-y-6 max-h-[600px] overflow-y-auto pr-2 no-scrollbar"></div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 後台管理 Tab -->
            <div id="tab-admin" class="tab-content">
                <div class="flex flex-col md:flex-row md:items-center justify-between mb-6 sm:mb-8 border-b border-zinc-800 pb-4">
                    <div>
                        <h2 class="text-2xl sm:text-3xl font-black text-white flex items-center">
                            <i data-lucide="sliders-horizontal" class="w-6 h-6 sm:w-8 sm:h-8 mr-3 text-pink-500"></i>系統後台管理
                        </h2>
                        <p class="text-sm text-zinc-500 mt-1">體育組專用操作介面</p>
                    </div>
                    <div id="admin-tab-lock-status" class="mt-4 md:mt-0"></div>
                </div>

                <!-- 未解鎖狀態 -->
                <div id="admin-locked-message" class="flex flex-col items-center justify-center p-8 sm:p-24 bg-zinc-950 border border-zinc-800 rounded-xl shadow-inner mx-2 sm:mx-0">
                    <div class="w-16 h-16 sm:w-20 sm:h-20 bg-zinc-900 rounded-full flex items-center justify-center mb-6 text-zinc-600 border border-zinc-800">
                        <i data-lucide="shield-alert" class="w-8 h-8 sm:w-10 sm:h-10"></i>
                    </div>
                    <h3 class="text-xl sm:text-2xl font-black text-white mb-2">權限受限</h3>
                    <p class="text-sm sm:text-base text-zinc-400 text-center max-w-md mb-6">此區塊為體衛組長及管理人員專用。請點擊上方按鈕進行密碼驗證以解鎖完整功能。</p>
                </div>

                <!-- 已解鎖狀態 (管理面板) -->
                <div id="admin-dashboard-content" class="hidden space-y-8">

                    <!-- 資料庫儲存與還原 -->
                    <div class="bg-zinc-950 border border-zinc-800 p-4 sm:p-6 rounded-xl shadow-lg">
                        <h3 class="text-base sm:text-lg font-black text-white flex items-center border-b border-zinc-800 pb-3 mb-4">
                            <i data-lucide="save" class="w-5 h-5 mr-2 text-pink-500"></i>系統資料庫儲存與還原
                        </h3>
                        <div class="flex flex-col sm:flex-row gap-4">
                            <button onclick="backupSystemData()" class="flex-1 bg-zinc-800 hover:bg-zinc-700 text-white px-4 py-4 rounded-lg font-bold flex flex-col items-center justify-center transition-all shadow-md border border-zinc-700 hover:border-pink-500">
                                <i data-lucide="download-cloud" class="w-6 h-6 mb-2 text-pink-400"></i>
                                <span class="text-sm sm:text-base">備份 / 下載系統資料</span>
                                <span class="text-[10px] text-zinc-400 font-normal mt-1 text-center">手動儲存所有賽程與成績防止遺失</span>
                            </button>
                            <label class="flex-1 bg-zinc-800 hover:bg-zinc-700 text-white px-4 py-4 rounded-lg font-bold flex flex-col items-center justify-center transition-all shadow-md border border-zinc-700 hover:border-green-500 cursor-pointer">
                                <i data-lucide="upload-cloud" class="w-6 h-6 mb-2 text-green-400"></i>
                                <span class="text-sm sm:text-base">還原 / 上傳系統資料</span>
                                <span class="text-[10px] text-zinc-400 font-normal mt-1 text-center">從之前的 JSON 備份檔還原所有資料</span>
                                <input type="file" accept=".json" class="hidden" onchange="restoreSystemData(event)">
                            </label>
                        </div>
                    </div>

                    <!-- 檔案上傳區 -->
                    <div class="bg-zinc-950 border border-zinc-800 p-4 sm:p-6 rounded-xl shadow-lg h-fit">
                        <h3 class="text-base sm:text-lg font-black text-white flex items-center border-b border-zinc-800 pb-3 mb-4">
                            <i data-lucide="upload-cloud" class="w-5 h-5 mr-2 text-pink-500"></i>賽事附件發佈
                        </h3>
                        <div id="attachment-upload-container" class="mb-4">
                            <label for="file-upload" class="flex flex-col items-center justify-center w-full h-40 border-2 border-zinc-700 border-dashed rounded-lg cursor-pointer bg-black hover:bg-zinc-900 transition-colors group">
                                <div class="flex flex-col items-center justify-center pt-5 pb-6 text-center">
                                    <i data-lucide="cloud-upload" class="w-8 h-8 sm:w-10 sm:h-10 mb-3 text-zinc-500 group-hover:text-pink-500 transition-colors"></i>
                                    <p class="mb-2 text-sm text-zinc-400 font-bold" id="file-upload-text">點擊或拖曳檔案以上傳</p>
                                    <p class="text-[10px] sm:text-xs text-zinc-500">支援 PDF, DOCX, XLSX (上限 10MB)</p>
                                </div>
                                <input id="file-upload" type="file" class="hidden" onchange="handleFileUpload(event)">
                            </label>
                        </div>
                    </div>
                </div>
            </div>

        </main>
    </div>

    <!-- ==================== 核心系統邏輯 ==================== -->
    <script>
        "use strict";

        // 全局錯誤捕捉
        window.onerror = function(message, source, lineno, colno, error) {
            console.error(`系統錯誤: ${message} 於 ${source}:${lineno}:${colno}`, error);
        };

        // XSS 洗滌器
        const escapeHTML = (str) => {
            if (!str) return '';
            return String(str).replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/"/g, '&quot;').replace(/'/g, '&#039;');
        };

        // 全域狀態庫
        let state = {
            activeTab: 'rules',
            checklist: { lineup: false, safety: false, tactics: false },
            admin: { isUnlocked: false },
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
            files: []
        };

        const STORAGE_KEY = 'ZhongyiBaseballApp_Data_V6'; 

        // 雲端同步引擎 (Firebase) - GitHub Pages 發佈版本
        let isCloudReady = false;
        async function initCloudEngine() {
            try {
                // ==========================================
                // [雲端同步設定區 - GitHub Pages 專用]
                // 若要啟用跨設備雲端同步，請將您的 Firebase Config 貼在此處。
                // 若保持 null，系統將以「本機儲存 (localStorage)」模式安全運行。
                // ==========================================
                const firebaseConfig = null; 
                
                const appId = 'zhongyi-teeball-app';

                if (!firebaseConfig) {
                    console.log("未啟用 Firebase，目前以高安全本機 (localStorage) 模式執行。");
                    return;
                }

                // 動態載入 Firebase 模組
                const { initializeApp } = await import("https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js");
                const { getAuth, signInAnonymously, onAuthStateChanged } = await import("https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js");
                const { getFirestore, doc, setDoc, onSnapshot } = await import("https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js");

                const app = initializeApp(firebaseConfig);
                const auth = getAuth(app);
                const db = getFirestore(app);

                window.cloudDb = db;
                window.cloudSetDoc = setDoc;

                await signInAnonymously(auth);

                onAuthStateChanged(auth, (user) => {
                    if (user) {
                        isCloudReady = true;
                        window.cloudDocRef = doc(db, 'artifacts', appId, 'public', 'data', 'cloud_state', 'v1');
                        
                        onSnapshot(window.cloudDocRef, (docSnap) => {
                            if (docSnap.exists()) {
                                const data = docSnap.data();
                                let hasChanges = false;
                                
                                if (data.matches && JSON.stringify(data.matches) !== JSON.stringify(state.matches)) { state.matches = data.matches; hasChanges = true; }
                                if (data.files && JSON.stringify(data.files) !== JSON.stringify(state.files)) { state.files = data.files; hasChanges = true; }
                                if (data.checklist && JSON.stringify(data.checklist) !== JSON.stringify(state.checklist)) { state.checklist = data.checklist; hasChanges = true; }

                                if (hasChanges) {
                                    const activeId = document.activeElement ? document.activeElement.id : null;
                                    
                                    renderSchedule();
                                    renderScoreboard();
                                    renderFileList();
                                    renderChecklist();
                                    renderAdminDashboard();
                                    
                                    if (activeId) {
                                        const el = document.getElementById(activeId);
                                        if (el) el.focus();
                                    }
                                }
                            }
                        }, (err) => console.error("雲端同步失敗", err));
                    }
                });
            } catch(e) {
                console.error("雲端引擎初始化失敗", e);
            }
        }

        function saveStateToLocal() {
            try {
                const dataObj = { matches: state.matches, files: state.files, checklist: state.checklist };
                localStorage.setItem(STORAGE_KEY, JSON.stringify(dataObj));
                if (isCloudReady && window.cloudDb && window.cloudDocRef) {
                    window.cloudSetDoc(window.cloudDocRef, dataObj, { merge: true }).catch(e => console.error("雲端寫入失敗", e));
                }
            } catch (e) { console.warn('儲存引擎 Error', e); }
        }

        function loadStateFromLocal() {
            try {
                const saved = localStorage.getItem(STORAGE_KEY);
                if (saved) {
                    const parsed = JSON.parse(saved);
                    if (parsed.matches && Array.isArray(parsed.matches)) {
                        state.matches = state.matches.map(m => {
                            const sm = parsed.matches.find(x => x.id === m.id);
                            return sm ? { ...m, scoreA: sm.scoreA, scoreB: sm.scoreB, status: sm.status } : m;
                        });
                    }
                    if (parsed.files && Array.isArray(parsed.files)) state.files = parsed.files;
                    if (parsed.checklist && typeof parsed.checklist === 'object') state.checklist = { ...state.checklist, ...parsed.checklist };
                }
            } catch (e) { console.warn('LocalStorage parse failed. Safe mode engaged.', e); }
        }

        let toastTimeout;
        function showToast(message) {
            try {
                const toast = document.getElementById('toast');
                if(!toast) return;
                document.getElementById('toast-message').innerText = message;
                toast.style.display = 'flex';
                clearTimeout(toastTimeout);
                toastTimeout = setTimeout(() => { toast.style.display = 'none'; }, 3000);
            } catch(e) {}
        }

        function switchTab(tabId) {
            try {
                state.activeTab = tabId;
                const baseClass = 'tab-btn flex-shrink-0 flex items-center justify-center space-x-1.5 px-4 sm:px-6 py-3 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-[3px] text-xs sm:text-sm ';
                
                document.querySelectorAll('.tab-btn').forEach(btn => {
                    if (btn.dataset.target === tabId) {
                        btn.className = baseClass + 'text-pink-500 border-pink-500 bg-pink-500/10';
                        if (btn.scrollIntoView) {
                           try { btn.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'center' }); } catch(e){}
                        }
                    } else {
                        btn.className = baseClass + 'text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900';
                    }
                });
                document.querySelectorAll('.tab-content').forEach(content => { content.classList.remove('active'); });
                const targetTab = document.getElementById(`tab-${tabId}`);
                if(targetTab) targetTab.classList.add('active');

                if(tabId === 'admin') renderAdminDashboard();
            } catch(e) { console.error("Tab switch error", e); }
        }

        function openAdminModal() { 
            const modal = document.getElementById('admin-login-modal');
            if(modal) { 
                modal.style.display = 'flex'; 
                setTimeout(()=>{ const input = document.getElementById('admin-passcode-input'); if(input) input.focus(); }, 100); 
                document.body.classList.add('modal-open'); 
            }
        }
        function closeAdminModal() { 
            const modal = document.getElementById('admin-login-modal');
            if(modal) { modal.style.display = 'none'; document.body.classList.remove('modal-open'); }
        }

        function renderGradeCards() {
            try {
                const container = document.getElementById('grade-cards-container');
                if(!container) return;
                
                const createScoringIll = (type) => {
                    if(type === 'middle') return `
                        <div class="bg-black border border-zinc-800 p-3 sm:p-4 rounded-md">
                            <h4 class="text-xs sm:text-sm font-bold text-pink-500 mb-2 sm:mb-3 flex items-center"><i data-lucide="zap" class="w-3 h-3 sm:w-[14px] sm:h-[14px] mr-1"></i>推進得分機制</h4>
                            <div class="flex items-center justify-between text-zinc-400 max-w-sm mx-auto">
                                <div class="flex flex-col items-center"><div class="w-6 h-6 sm:w-8 sm:h-8 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1 sm:mb-2 bg-zinc-900 shadow-md"><span class="transform -rotate-45 text-[10px] sm:text-xs font-bold text-white">1</span></div><span class="text-[8px] sm:text-[10px] font-bold">一壘(1分)</span></div>
                                <i data-lucide="arrow-right" class="w-3 h-3 sm:w-4 sm:h-4 text-zinc-600"></i>
                                <div class="flex flex-col items-center"><div class="w-6 h-6 sm:w-8 sm:h-8 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1 sm:mb-2 bg-zinc-900 shadow-md"><span class="transform -rotate-45 text-[10px] sm:text-xs font-bold text-white">2</span></div><span class="text-[8px] sm:text-[10px] font-bold">二壘(2分)</span></div>
                                <i data-lucide="arrow-right" class="w-3 h-3 sm:w-4 sm:h-4 text-zinc-600"></i>
                                <div class="flex flex-col items-center"><div class="w-6 h-6 sm:w-8 sm:h-8 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1 sm:mb-2 bg-zinc-900 shadow-md"><span class="transform -rotate-45 text-[10px] sm:text-xs font-bold text-white">3</span></div><span class="text-[8px] sm:text-[10px] font-bold">三壘(3分)</span></div>
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
                        rules: ["兩局制（分上下半局）。猜拳勝隊選擇攻守先後。", "打線（三年級）：每半局 12-13 人打擊（下半局人員不重複）。打擊之兩半局共計24-26棒次。", "打線（四年級）：每半局 10-12 人打擊（下半局人員不重複）。打擊之兩半局共計20-24棒次。", "防守：固定派守備 9 分（每局結束後可換人）。", "限制：球擊出後，投手位置防守員才能越過投手板。違規重新打擊。"]
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
            } catch(e) { console.error('Render GradeCards failed:', e); }
        }

        function renderChecklist() {
            try {
                const container = document.getElementById('checklist-container');
                if(!container) return;
                const items = [
                    { id: 'lineup', icon: 'users', title: '陣容確認 LINEUP', desc: '請務必確認各年級打擊人數正確。<br><strong class="text-white">注意：棒次一經排定，除突發事件或請假外，絕不可變動！</strong>' },
                    { id: 'safety', icon: 'shield-alert', title: '安全宣導 SAFETY', desc: '上場前務必再次提醒全班：「絕對禁止滑壘與撲壘！」（犯規即出局）。提醒跑壘員踩橘色壘包。' },
                    { id: 'tactics', icon: 'swords', title: '戰術叮嚀 TACTICS', desc: '提醒守備員：只有封殺，沒有觸殺。帶著運動家精神上場，享受比賽！若有脫序情事，裁判可沒收比賽且直接判定勝負。' }
                ];

                container.innerHTML = items.map(item => {
                    const isChecked = state.checklist[item.id];
                    return `
                        <div class="flex items-start space-x-3 sm:space-x-4 group cursor-pointer transition-all duration-300 p-3 sm:p-3 rounded-lg hover:bg-zinc-800/30 border border-transparent ${isChecked ? 'border-zinc-800 bg-black/30' : ''}" onclick="toggleChecklistItem('${item.id}')">
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
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Checklist failed:', e); }
        }

        function toggleChecklistItem(id) {
            state.checklist[id] = !state.checklist[id];
            saveStateToLocal();
            renderChecklist();
        }

        function renderSchedule() {
            try {
                const container = document.getElementById('schedule-container');
                if(!container) return;

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
                        <div class="flex items-center space-x-1 sm:space-x-2 font-black text-lg sm:text-xl px-2 sm:px-0 z-10 relative">
                            <span class="${aWon ? 'text-pink-400' : (isTie ? 'text-zinc-400' : 'text-white')}">${m.scoreA}</span>
                            <span class="text-zinc-600 text-xs sm:text-sm pb-1">-</span>
                            <span class="${bWon ? 'text-pink-400' : (isTie ? 'text-zinc-400' : 'text-white')}">${m.scoreB}</span>
                        </div>
                    ` : `<span class="text-zinc-600 text-[10px] sm:text-xs font-black italic uppercase mx-2 px-2 sm:px-0 z-10 relative">vs</span>`;

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
                                <span class="w-8 sm:w-10 text-right font-black text-base sm:text-xl tracking-wider z-10 relative ${aWon ? 'text-pink-400' : (isTie ? 'text-zinc-300' : 'text-white')}">${m.teamA}</span>
                                ${scoreHtml}
                                <span class="w-8 sm:w-10 text-left font-black text-base sm:text-xl tracking-wider z-10 relative ${bWon ? 'text-pink-400' : (isTie ? 'text-zinc-300' : 'text-white')}">${m.teamB}</span>
                            </div>
                            ${isCompleted && !isTie ? '<div class="absolute inset-0 bg-gradient-to-r from-transparent via-pink-500/5 to-transparent opacity-50 z-0 pointer-events-none"></div>' : ''}
                            ${isTie ? '<div class="absolute inset-0 bg-gradient-to-r from-transparent via-zinc-500/10 to-transparent opacity-50 z-0 pointer-events-none"></div>' : ''}
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
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Schedule failed:', e); }
        }

        function getGradeAlertData(grade) {
            switch(grade) {
                case '3': return { text: "每半局 12-13 人打擊（下半局不重複）。兩局共計 24-26 棒次。", limit: 26 };
                case '4': return { text: "每半局 10-12 人打擊（下半局不重複）。兩局共計 20-24 棒次。", limit: 24 };
                case '5': return { text: "每半局 13-14 人打擊（下半局不重複）。防守須由當局攻擊者選派 9 人。", limit: 30 };
                case '6': return { text: "每半局 16-18 人打擊。循環打擊制（每人打兩次，上下半局打線完全相同）。", limit: 19 };
                default: return { text: "請先選擇年級以查看排棒限制。", limit: 19 };
            }
        }

        function handleDownloadTemplate() {
            const gradeSelect = document.getElementById('lineup-grade-select');
            const grade = gradeSelect ? gradeSelect.value : '';
            if (!grade) {
                showToast('請先選擇年級！');
                return;
            }
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
            showToast(`已下載範本 (共 ${limit} 棒)`);
        }

        function backupSystemData() {
            try {
                const data = {
                    matches: state.matches,
                    files: state.files,
                    checklist: state.checklist
                };
                const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `忠義國小樂樂棒_系統備份_${new Date().toISOString().split('T')[0]}.json`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
                showToast('系統資料庫備份檔下載完成！');
            } catch(e) {
                console.error("Backup failed", e);
                showToast('備份失敗，請稍後再試。');
            }
        }

        function restoreSystemData(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = (e) => {
                try {
                    const parsed = JSON.parse(e.target.result);
                    if (parsed.matches) state.matches = parsed.matches;
                    if (parsed.files) state.files = parsed.files;
                    if (parsed.checklist) state.checklist = parsed.checklist;
                    
                    saveStateToLocal();
                    
                    renderSchedule();
                    renderScoreboard();
                    renderFileList();
                    renderChecklist();
                    renderAdminDashboard();
                    
                    showToast('系統資料還原成功！所有設定已更新。');
                } catch (err) {
                    showToast('還原失敗：備份檔格式不正確。');
                    console.error(err);
                }
                event.target.value = ''; // Reset input
            };
            reader.readAsText(file);
        }

        function renderAdminDashboard() {
            try {
                const lockStatus = document.getElementById('admin-tab-lock-status');
                const content = document.getElementById('admin-dashboard-content');
                const lockedMsg = document.getElementById('admin-locked-message');
                
                if (state.admin.isUnlocked) {
                    if(lockStatus) lockStatus.innerHTML = `<span class="text-[10px] sm:text-xs font-bold text-green-400 border border-green-500/30 px-3 sm:px-4 py-1.5 sm:py-2 rounded-md flex items-center bg-green-500/10 shadow-inner w-fit"><i data-lucide="unlock" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5 sm:mr-2"></i> 已解鎖最高權限</span>`;
                    if(content) content.classList.remove('hidden');
                    if(lockedMsg) lockedMsg.classList.add('hidden');
                } else {
                    if(lockStatus) lockStatus.innerHTML = `<button onclick="openAdminModal()" class="text-[10px] sm:text-xs font-bold text-zinc-300 hover:text-white border border-zinc-700 hover:border-green-500 px-3 sm:px-5 py-2 sm:py-2.5 rounded-md flex items-center bg-zinc-800 hover:bg-zinc-700 transition-all shadow-md active:scale-95 group w-fit"><i data-lucide="lock" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5 sm:mr-2 text-green-500 group-hover:scale-110 transition-transform"></i> 管理員解鎖</button>`;
                    if(content) content.classList.add('hidden');
                    if(lockedMsg) lockedMsg.classList.remove('hidden');
                }
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Admin failed', e); }
        }

        function handleAdminAuth(e) {
            e.preventDefault();
            const input = document.getElementById('admin-passcode-input');
            const pwd = input ? input.value : '';
            
            // JPM Security: "840110" Base64 encoded
            let isMatch = false;
            try { isMatch = btoa(pwd) === "ODQwMTEw"; } catch(err) {}

            if (isMatch) {
                state.admin.isUnlocked = true;
                closeAdminModal(); 
                if(input) input.value = ''; 
                showToast('系統權限已完全解鎖');
                const btnAdmin = document.getElementById('tab-btn-admin');
                if(btnAdmin) {
                    btnAdmin.classList.remove('hidden');
                    btnAdmin.classList.add('flex-shrink-0', 'flex'); 
                }
                renderScoreboard(); renderFileList(); renderAdminDashboard();
            } else {
                const error = document.getElementById('admin-auth-error');
                if (error) error.classList.remove('hidden');
                if (input) {
                    input.classList.remove('border-zinc-800', 'focus:border-pink-500');
                    input.classList.add('border-red-500', 'focus:border-red-500', 'bg-red-950/30', 'animate-shake');
                    setTimeout(() => input.classList.remove('animate-shake'), 400);
                }
            }
        }

        function renderScoreboard() {
            try {
                const sStatus = document.getElementById('schedule-admin-lock-status');
                const wrapper = document.getElementById('scoreboard-wrapper');
                
                if (state.admin.isUnlocked) {
                    if(sStatus) sStatus.innerHTML = `<span class="text-[10px] sm:text-xs font-bold text-green-400 border border-green-500/30 px-3 sm:px-4 py-1.5 sm:py-2 rounded-md flex items-center justify-center sm:justify-start bg-green-500/10 shadow-inner w-full sm:w-auto"><i data-lucide="unlock" class="w-4 h-4 mr-2"></i> 已解鎖登錄權限</span>`;
                    if(wrapper) wrapper.classList.remove('hidden');
                } else {
                    if(sStatus) sStatus.innerHTML = `<button onclick="openAdminModal()" class="text-[10px] sm:text-xs font-bold text-zinc-300 hover:text-white border border-zinc-700 hover:border-pink-500 px-3 sm:px-5 py-2 sm:py-2.5 rounded-md flex items-center justify-center sm:justify-start bg-zinc-800 hover:bg-zinc-700 transition-all shadow-md active:scale-95 group w-full sm:w-auto"><i data-lucide="lock" class="w-4 h-4 mr-2 text-pink-500 group-hover:scale-110 transition-transform"></i> 管理員解鎖</button>`;
                    if(wrapper) wrapper.classList.add('hidden');
                }

                const container = document.getElementById('scoreboard-container');
                if(!container) return;

                container.innerHTML = ['3', '4', '5', '6'].map(grade => {
                    const currentMatches = Array.isArray(state.matches) ? state.matches.filter(m => m.grade === grade) : [];
                    const matchesHtml = currentMatches.map(m => {
                        const isComp = m.status === 'completed';
                        let contentHtml = '';
                        
                        if (state.admin.isUnlocked) {
                            if (isComp) {
                                contentHtml = `
                                    <div class="flex items-center justify-between mt-2 bg-zinc-900/50 p-2 sm:p-3 rounded border border-zinc-800/50">
                                        <div class="text-base sm:text-lg font-black text-white flex-1 text-center tracking-wider">${m.teamA} <span class="text-pink-500 mx-2 sm:mx-3">${m.scoreA} : ${m.scoreB}</span> ${m.teamB}</div>
                                        <button onclick="resetMatchScore('${m.id}')" class="ml-2 sm:ml-3 shrink-0 text-zinc-500 hover:text-red-400 bg-black p-1.5 sm:p-2 rounded border border-zinc-800 hover:border-red-500/50 transition-colors" title="刪除成績"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                                    </div>`;
                            } else {
                                contentHtml = `
                                    <form onsubmit="submitScore(event, '${m.id}')" class="flex items-center justify-between mt-2 space-x-1 sm:space-x-2 bg-zinc-900/50 p-2 sm:p-3 rounded border border-zinc-800/50">
                                        <span class="font-black text-zinc-300 text-xs sm:text-sm w-8 sm:w-10 text-right">${m.teamA}</span>
                                        <input name="scoreA" type="number" min="0" required placeholder="分" class="w-12 sm:w-16 bg-black border border-zinc-700 text-white text-center font-bold text-base rounded py-1.5 focus:border-pink-500 outline-none transition-colors appearance-none">
                                        <span class="text-zinc-600 text-[10px] sm:text-xs font-black italic">vs</span>
                                        <input name="scoreB" type="number" min="0" required placeholder="分" class="w-12 sm:w-16 bg-black border border-zinc-700 text-white text-center font-bold text-base rounded py-1.5 focus:border-pink-500 outline-none transition-colors appearance-none">
                                        <span class="font-black text-zinc-300 text-xs sm:text-sm w-8 sm:w-10">${m.teamB}</span>
                                        <button type="submit" class="bg-pink-600 hover:bg-pink-500 text-white p-2 rounded transition-transform active:scale-95 ml-1 sm:ml-2 shrink-0" title="儲存"><i data-lucide="save" class="w-4 h-4"></i></button>
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
                
                const uploadContainer = document.getElementById('attachment-upload-container');
                if (uploadContainer) uploadContainer.style.display = state.admin.isUnlocked ? 'block' : 'none';
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Scoreboard Error:', e); }
        }

        function submitScore(e, id) {
            e.preventDefault();
            const sA = escapeHTML(e.target.scoreA.value);
            const sB = escapeHTML(e.target.scoreB.value);
            if(!state || !Array.isArray(state.matches)) return;
            
            const matchIndex = state.matches.findIndex(m => m.id === id);
            if (matchIndex !== -1 && sA && sB) {
                state.matches[matchIndex] = { ...state.matches[matchIndex], scoreA: sA, scoreB: sB, status: 'completed' };
                saveStateToLocal(); 
                renderScoreboard(); 
                renderSchedule();
                showToast('比分發佈成功！已即時更新至賽程表。');
            }
        }

        function resetMatchScore(id) {
            if (window.confirm('確定要刪除這筆紀錄嗎？')) {
                if(!state || !Array.isArray(state.matches)) return;
                const matchIndex = state.matches.findIndex(m => m.id === id);
                if (matchIndex !== -1) {
                    state.matches[matchIndex] = { ...state.matches[matchIndex], scoreA: '', scoreB: '', status: 'pending' };
                    saveStateToLocal(); 
                    renderScoreboard(); 
                    renderSchedule(); 
                    showToast('紀錄已刪除');
                }
            }
        }

        function handleFileUpload(e) {
            const file = e.target.files[0];
            if (!file) return;
            const text = document.getElementById('file-upload-text');
            const origText = text ? text.innerText : '新增賽事附件';
            if (text) text.innerText = '上傳中...';
            
            setTimeout(() => {
                const newFile = {
                    id: Date.now(),
                    name: escapeHTML(file.name),
                    date: new Date().toISOString().split('T')[0]
                };
                if(Array.isArray(state.files)) state.files.unshift(newFile);
                if (text) text.innerText = origText;
                e.target.value = null;
                saveStateToLocal();
                renderFileList();
                showToast('檔案已成功上傳');
            }, 1000);
        }

        function renderFileList() {
            try {
                const container = document.getElementById('file-list-container');
                if(!container) return;
                
                const currentFiles = Array.isArray(state.files) ? state.files : [];
                if (currentFiles.length === 0) {
                    container.innerHTML = `
                        <div class="p-8 sm:p-16 flex flex-col items-center justify-center text-zinc-500 border-2 border-dashed border-zinc-800 m-4 sm:m-6 rounded-xl bg-black/20">
                            <i data-lucide="file-text" class="w-8 h-8 sm:w-12 sm:h-12 mb-2 sm:mb-4 text-zinc-700"></i>
                            <p class="font-bold tracking-widest uppercase text-xs sm:text-sm text-center">目前尚無發佈的成績或賽事檔案</p>
                        </div>`;
                } else {
                    container.innerHTML = currentFiles.map(f => `
                        <div class="p-4 sm:p-6 flex flex-col sm:flex-row sm:items-center justify-between hover:bg-zinc-800/80 transition-all duration-300 group">
                            <div class="flex items-center space-x-3 sm:space-x-5 mb-3 sm:mb-0">
                                <div class="p-3 sm:p-4 bg-black border border-zinc-800 text-pink-500 rounded-lg group-hover:scale-110 group-hover:border-pink-500/50 transition-all duration-300 shadow-inner shrink-0">
                                    <i data-lucide="file-text" class="w-5 h-5 sm:w-7 sm:h-7"></i>
                                </div>
                                <div class="min-w-0">
                                    <h4 class="text-base sm:text-lg font-black text-white group-hover:text-pink-400 transition-colors break-all line-clamp-1">${f.name}</h4>
                                    <p class="text-xs sm:text-sm text-zinc-500 font-mono mt-0.5 sm:mt-1 flex items-center"><i data-lucide="calendar-days" class="w-3 h-3 sm:w-3.5 sm:h-3.5 mr-1 sm:mr-1.5"></i>發佈: ${f.date}</p>
                                </div>
                            </div>
                            <button class="w-full sm:w-auto text-zinc-400 hover:text-white transition-colors px-4 sm:px-6 py-2 sm:py-3 border border-zinc-700 hover:border-pink-500 hover:bg-pink-600 uppercase text-[10px] sm:text-xs font-black tracking-widest rounded-sm transform hover:-translate-y-0.5 shadow-md shrink-0">下載</button>
                        </div>
                    `).join('');
                }
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Files Error:', e); }
        }

        /* ==================== 引擎啟動 ==================== */
        document.addEventListener("DOMContentLoaded", () => {
            const tabRules = document.getElementById('tab-rules');
            if (tabRules) {
                tabRules.classList.add('active');
            }
        });

        window.onload = () => {
            try { loadStateFromLocal(); } catch(e) { console.error('載入本地資料失敗', e); }
            
            initCloudEngine();
            
            const renderQueue = [
                renderGradeCards, 
                renderSchedule, 
                renderChecklist, 
                renderScoreboard, 
                renderFileList, 
                renderAdminDashboard
            ];
            
            renderQueue.forEach(func => {
                try { func(); } catch(e) { console.error(`模組渲染失敗: ${func.name}`, e); }
            });

            try { switchTab('rules'); } catch(e) {}
            
            if (window.parent && window.parent !== window) {
                setTimeout(() => {
                    try { window.parent.postMessage({ type: 'resize', height: document.body.scrollHeight }, '*'); } catch(e){}
                }, 500);
            }
        };

        try {
            const resizeObserver = new ResizeObserver(() => {
                 if (window.parent && window.parent !== window) {
                    try { window.parent.postMessage({ type: 'resize', height: document.body.scrollHeight }, '*'); } catch(e){}
                }
            });
            if (document.body instanceof Element) {
                resizeObserver.observe(document.body);
            }
        } catch(e) {
            console.warn("ResizeObserver initialization skipped.");
        }
    </script>
</body>
</html>
