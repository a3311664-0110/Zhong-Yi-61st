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
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }

        input[type="text"], input[type="password"], input[type="number"], select, button {
            min-height: 44px;
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

    <!-- ==================== 核心系統邏輯 (純 Vanilla JS) ==================== -->
    <script>
        "use strict";

        // XSS 洗滌器
        window.escapeHTML = (str) => {
            if (!str) return '';
            return String(str).replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/"/g, '&quot;').replace(/'/g, '&#039;');
        };

        // 全域狀態庫
        window.state = {
            activeTab: 'rules',
            checklist: { lineup: false, safety: false, tactics: false },
            lineup: {
                grade: '', cls: '',
                data: Array.from({ length: 30 }, () => ({ number: '', name: '' })),
                lastUpdated: null, isUnlocked: false, allClassesData: {} 
            },
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

        window.classOptions = {
            '3': ['301', '302', '303'], '4': ['401', '402', '403'], '5': ['501', '502', '503'], '6': ['601', '602', '603'],
        };

        window.STORAGE_KEY = 'ZhongyiBaseballApp_Data_V3';

        // 本地儲存引擎
        window.saveStateToLocal = function() {
            try {
                localStorage.setItem(window.STORAGE_KEY, JSON.stringify({
                    matches: window.state.matches,
                    files: window.state.files,
                    checklist: window.state.checklist,
                    allClassesData: window.state.lineup.allClassesData
                }));
            } catch (e) { console.warn('LocalStorage Error', e); }
        };

        window.loadStateFromLocal = function() {
            try {
                const saved = localStorage.getItem(window.STORAGE_KEY);
                if (saved) {
                    const parsed = JSON.parse(saved);
                    if (parsed.matches && Array.isArray(parsed.matches)) {
                        window.state.matches = window.state.matches.map(m => {
                            const sm = parsed.matches.find(x => x.id === m.id);
                            return sm ? { ...m, scoreA: sm.scoreA, scoreB: sm.scoreB, status: sm.status } : m;
                        });
                    }
                    if (parsed.files && Array.isArray(parsed.files)) window.state.files = parsed.files;
                    if (parsed.checklist && typeof parsed.checklist === 'object') window.state.checklist = { ...window.state.checklist, ...parsed.checklist };
                    if (parsed.allClassesData && typeof parsed.allClassesData === 'object') window.state.lineup.allClassesData = parsed.allClassesData;
                }
            } catch (e) { console.warn('LocalStorage parse failed. Safe mode engaged.', e); }
        };

        // 提示訊息引擎
        let toastTimeout;
        window.showToast = function(message) {
            try {
                const toast = document.getElementById('toast');
                if(!toast) return;
                document.getElementById('toast-message').innerText = message;
                toast.style.display = 'flex';
                clearTimeout(toastTimeout);
                toastTimeout = setTimeout(() => { toast.style.display = 'none'; }, 3000);
            } catch(e) {}
        };

        // 切換頁籤邏輯
        window.switchTab = function(tabId) {
            try {
                window.state.activeTab = tabId;
                document.querySelectorAll('.tab-btn').forEach(btn => {
                    if (btn.dataset.target === tabId) {
                        btn.className = 'tab-btn snap-start flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-5 sm:px-6 py-3.5 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-pink-500 border-pink-500 bg-pink-500/10 text-xs sm:text-sm';
                        if (btn.scrollIntoView) {
                           try { btn.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'center' }); } catch(e){}
                        }
                    } else {
                        btn.className = 'tab-btn snap-start flex-shrink-0 flex items-center justify-center space-x-1 sm:space-x-2 px-5 sm:px-6 py-3.5 sm:py-4 font-bold tracking-widest uppercase transition-all whitespace-nowrap border-b-2 text-zinc-400 border-transparent hover:text-zinc-200 hover:bg-zinc-900 text-xs sm:text-sm';
                    }
                });
                document.querySelectorAll('.tab-content').forEach(content => { content.classList.remove('active'); });
                const targetTab = document.getElementById(`tab-${tabId}`);
                if(targetTab) targetTab.classList.add('active');

                if(tabId === 'admin' && typeof window.renderAdminDashboard === 'function') window.renderAdminDashboard();
            } catch(e) { console.error("Tab switch error", e); }
        };

        // Modal 控制引擎
        window.openImageModal = function() { 
            const modal = document.getElementById('image-zoom-modal');
            if(modal) { modal.style.display = 'flex'; document.body.classList.add('modal-open'); }
        };
        window.closeImageModal = function() { 
            const modal = document.getElementById('image-zoom-modal');
            if(modal) { modal.style.display = 'none'; document.body.classList.remove('modal-open'); }
        };
        window.openAdminModal = function() { 
            const modal = document.getElementById('admin-login-modal');
            if(modal) { 
                modal.style.display = 'flex'; 
                setTimeout(()=>{ const input = document.getElementById('admin-passcode-input'); if(input) input.focus(); }, 100); 
                document.body.classList.add('modal-open'); 
            }
        };
        window.closeAdminModal = function() { 
            const modal = document.getElementById('admin-login-modal');
            if(modal) { modal.style.display = 'none'; document.body.classList.remove('modal-open'); }
        };

        // ==================== UI 渲染引擎 (Vanilla JS) ====================
        window.renderGradeCards = function() {
            try {
                const container = document.getElementById('grade-cards-container');
                if(!container) return;
                
                const createScoringIll = (type) => {
                    if(type === 'middle') return `
                        <div class="bg-black border border-zinc-800 p-3 sm:p-4 rounded-lg shadow-inner">
                            <h4 class="text-xs sm:text-sm font-bold text-pink-500 mb-3 sm:mb-4 flex items-center"><i data-lucide="zap" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5"></i>推進得分機制</h4>
                            <div class="flex items-center justify-between text-zinc-400 max-w-sm mx-auto">
                                <div class="flex flex-col items-center"><div class="w-7 h-7 sm:w-9 sm:h-9 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1.5 sm:mb-2 bg-zinc-900 shadow-md"><span class="transform -rotate-45 text-[10px] sm:text-xs font-black text-white">1</span></div><span class="text-[9px] sm:text-[10px] font-bold tracking-wider">一壘(1分)</span></div>
                                <i data-lucide="arrow-right" class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-zinc-600"></i>
                                <div class="flex flex-col items-center"><div class="w-7 h-7 sm:w-9 sm:h-9 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1.5 sm:mb-2 bg-zinc-900 shadow-md"><span class="transform -rotate-45 text-[10px] sm:text-xs font-black text-white">2</span></div><span class="text-[9px] sm:text-[10px] font-bold tracking-wider">二壘(2分)</span></div>
                                <i data-lucide="arrow-right" class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-zinc-600"></i>
                                <div class="flex flex-col items-center"><div class="w-7 h-7 sm:w-9 sm:h-9 border-2 border-zinc-600 transform rotate-45 flex items-center justify-center mb-1.5 sm:mb-2 bg-zinc-900 shadow-md"><span class="transform -rotate-45 text-[10px] sm:text-xs font-black text-white">3</span></div><span class="text-[9px] sm:text-[10px] font-bold tracking-wider">三壘(3分)</span></div>
                                <i data-lucide="arrow-right" class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-zinc-600"></i>
                                <div class="flex flex-col items-center"><i data-lucide="home" class="w-6 h-6 sm:w-8 sm:h-8 text-pink-500 mb-1 sm:mb-1.5 drop-shadow-[0_0_8px_rgba(236,72,153,0.5)]"></i><span class="text-[9px] sm:text-[10px] font-black text-pink-400 tracking-wider">本壘(4分)</span></div>
                            </div>
                        </div>`;
                    return `
                        <div class="bg-black border border-zinc-800 p-3 sm:p-4 rounded-lg shadow-inner">
                            <h4 class="text-xs sm:text-sm font-bold text-pink-500 mb-3 sm:mb-4 flex items-center"><i data-lucide="activity" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5"></i>戰術與得分機制</h4>
                            <div class="flex flex-col space-y-4 sm:space-y-5">
                                <div class="flex items-center justify-around">
                                    <div class="flex flex-col items-center"><div class="relative"><i data-lucide="home" class="w-7 h-7 sm:w-9 sm:h-9 text-pink-500 drop-shadow-[0_0_8px_rgba(236,72,153,0.3)]"></i><div class="absolute -top-1 -right-2 bg-pink-600 text-white text-[9px] sm:text-[10px] font-black px-1.5 rounded-full shadow-sm">+1</div></div><span class="text-[10px] sm:text-xs font-bold mt-2 text-white tracking-widest">回本壘 1 分</span></div>
                                    <div class="h-8 sm:h-12 w-px bg-zinc-700 mx-2 sm:mx-4"></div>
                                    <div class="flex flex-col items-center"><div class="flex items-center mb-1.5 sm:mb-2"><i data-lucide="footprints" class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-zinc-400 mr-1.5 sm:mr-2"></i><div class="h-5 sm:h-6 border-l-2 border-dashed border-red-500 mx-1 sm:mx-2"></div><i data-lucide="target" class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-pink-500 ml-1.5 sm:ml-2"></i></div><span class="text-[9px] sm:text-[10px] font-bold text-zinc-300">5公尺不可折返線</span><span class="text-[8px] sm:text-[10px] text-zinc-500 mt-1">打者碰球後才起跑</span></div>
                                </div>
                                <div class="bg-zinc-900 border border-zinc-700 p-2 sm:p-2.5 rounded-md flex items-center justify-center"><i data-lucide="repeat" class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-pink-500 mr-1.5 sm:mr-2 shrink-0"></i><span class="text-[9px] sm:text-[11px] text-zinc-300 font-bold tracking-widest">殘壘延續：下半局回到原位置繼續跑</span></div>
                            </div>
                        </div>`;
                };

                const createBattingChart = (type) => `
                    <div class="mt-4 sm:mt-6 bg-black border border-zinc-700 rounded-lg overflow-hidden shadow-lg">
                        <div class="bg-zinc-800/90 text-[10px] sm:text-xs font-bold text-center py-1.5 sm:py-2 text-pink-400 tracking-widest border-b border-zinc-700">棒次安排圖解 (${type === 'continuous' ? '接續打擊制' : '循環打擊制'})</div>
                        <div class="flex divide-x divide-zinc-700 text-center">
                            <div class="flex-1 p-2 sm:p-4"><div class="text-[9px] sm:text-[10px] text-zinc-500 mb-1.5 font-black tracking-widest">第一半局攻擊</div><div class="font-mono text-xs sm:text-sm text-white font-bold bg-zinc-900 py-1.5 rounded border border-zinc-800 shadow-inner">1 ~ N 棒</div></div>
                            <div class="flex-1 p-2 sm:p-4"><div class="text-[9px] sm:text-[10px] text-zinc-500 mb-1.5 font-black tracking-widest">第二半局攻擊</div><div class="font-mono text-xs sm:text-[13px] text-pink-400 font-bold bg-zinc-900 py-1.5 rounded border border-pink-500/40 shadow-[0_0_10px_rgba(236,72,153,0.1)]">${type === 'continuous' ? '(N+1)棒 起接續' : '1 ~ N 棒(重複)'}</div></div>
                        </div>
                        <div class="bg-zinc-900/80 p-2 sm:p-2.5 text-[9px] sm:text-[10px] text-zinc-400 text-center border-t border-zinc-700/50">${type === 'continuous' ? '⚠️ 兩個半局打擊人員絕對不可重複' : '⚠️ 循環打擊制：第一、第二半局打線完全相同'}</div>
                    </div>`;

                const formatRule = (rule) => {
                    const isHighlight = rule.includes('打線') || rule.includes('防守') || rule.includes('殘壘延續');
                    const parts = rule.split('：');
                    const formattedText = parts.length > 1 ? `<span class="text-pink-400 font-black mr-1">${parts[0]}：</span>${parts[1]}` : parts[0];
                    return `
                        <li class="flex items-start text-sm sm:text-base ${isHighlight ? 'bg-zinc-800/60 p-3 sm:p-5 rounded-xl border-l-[3px] border-pink-500 text-white font-bold shadow-md transform hover:scale-[1.01] transition-transform border border-zinc-700/50' : 'p-1.5 sm:p-2'}">
                            ${!isHighlight ? '<span class="w-1.5 h-1.5 rounded-full bg-pink-500 mt-1.5 sm:mt-2 mr-2.5 sm:mr-3 shrink-0"></span>' : ''}
                            <span class="leading-relaxed tracking-wide ${isHighlight ? 'ml-1' : ''}">${formattedText}</span>
                        </li>`;
                };

                const cards = [
                    { 
                        grade: '中年級 (三、四年級)', 
                        badge: '推進得分制', 
                        ill: createScoringIll('middle'), 
                        chart: createBattingChart('continuous'), 
                        rules: [
                            "比賽時間：分成上半局、下半局。",
                            "得分方式：跑一個壘包得 1 分，跑回本壘可得 4 分！",
                            "打擊順序：如果上半局派 1~12 號打擊，下半局就必須換人，由 13~24 號接著打。每個人只能上場打一次！",
                            "防守規定：固定派 9 個人上場防守，每半局結束才可以換人。",
                            "特別限制：球被打出去之前，投手位置的防守員不可以越過投手板，否則要重新打擊。"
                        ] 
                    },
                    { 
                        grade: '五年級專屬', 
                        badge: '本壘得分制', 
                        ill: createScoringIll('high'), 
                        chart: createBattingChart('continuous'), 
                        rules: [
                            "比賽時間：分成上半局、下半局。",
                            "得分方式：一定要跑回「本壘」才算得 1 分。",
                            "打擊順序：跟中年級一樣，兩個半局的打擊人員「絕對不可以重複」。每個人只能打一次！",
                            "防守規定：上場防守的 9 個人，必須是「正在打擊的這群人」裡面挑出來的。",
                            "殘壘延續：上半局結束時，如果有跑者還停在壘包上，下半局開始時，這些跑者可以回到原本的壘包繼續跑！",
                            "平手怎麼辦？：如果分數一樣，先比哪一隊留在壘包上的人多（多者勝）。如果還是平手，再比誰離本壘比較近（近者勝）。"
                        ] 
                    },
                    { 
                        grade: '六年級專屬', 
                        badge: '本壘得分制', 
                        ill: createScoringIll('high'), 
                        chart: createBattingChart('loop'), 
                        rules: [
                            "比賽時間：分成上半局、下半局。",
                            "得分方式：一定要跑回「本壘」才算得 1 分。",
                            "循環打擊：每個人都會打到「兩次」！第一半局 1~16 號打完後，第二半局還是由「同一批人、同一個順序」再打一次。",
                            "防守規定：固定 9 個人防守，第一半局不可以換人，第二半局才可以換。",
                            "殘壘延續：跟五年級一樣，上半局結束時留在壘包上的人，下半局可以回原位繼續跑。",
                            "平手怎麼辦？：如果分數一樣，先比哪一隊留在壘包上的人多（多者勝）。如果還是平手，再比誰離本壘比較近（近者勝）。"
                        ] 
                    }
                ];

                container.innerHTML = cards.map(c => `
                    <div class="bg-zinc-900 border border-zinc-800 relative overflow-hidden group hover:border-zinc-700 transition-all duration-300 flex flex-col md:flex-row shadow-xl rounded-2xl">
                        <div class="md:w-2/5 p-5 sm:p-6 md:p-8 bg-zinc-950 border-b md:border-b-0 md:border-r border-zinc-800 relative flex flex-col justify-center">
                            <div class="absolute top-0 left-0 bg-pink-600 text-white text-[10px] sm:text-xs font-bold px-3 sm:px-5 py-1.5 sm:py-2 uppercase tracking-widest z-10 rounded-br-2xl shadow-lg">${c.badge}</div>
                            <h3 class="text-xl sm:text-2xl font-black text-white mt-6 sm:mt-8 mb-5 sm:mb-6 tracking-wider relative z-10 flex items-center"><span class="w-1 h-5 bg-pink-500 mr-2"></span>${c.grade}</h3>
                            <div class="relative z-10 w-full transform sm:scale-[1.02] origin-left lg:pr-2">${c.ill}</div>
                            <div class="relative z-10 w-full transform sm:scale-[1.02] origin-left mt-3 lg:pr-2">${c.chart}</div>
                        </div>
                        <div class="md:w-3/5 p-5 sm:p-6 md:p-8 flex items-center bg-zinc-900">
                            <ul class="space-y-3 sm:space-y-4 w-full">${c.rules.map(formatRule).join('')}</ul>
                        </div>
                    </div>
                `).join('');
            } catch(e) { console.error('Render GradeCards failed:', e); }
        };

        window.renderChecklist = function() {
            try {
                const container = document.getElementById('checklist-container');
                if(!container) return;
                const items = [
                    { id: 'lineup', icon: 'users', title: '陣容確認 LINEUP', desc: '請務必確認各年級打擊人數正確。<br><strong class="text-white">注意：棒次一經排定，除突發事件或請假外，絕不可變動！</strong>' },
                    { id: 'safety', icon: 'shield-alert', title: '安全宣導 SAFETY', desc: '上場前務必再次提醒全班：「絕對禁止滑壘與撲壘！」（犯規即出局）。提醒跑壘員踩橘色壘包。' },
                    { id: 'tactics', icon: 'swords', title: '戰術叮嚀 TACTICS', desc: '提醒守備員：只有封殺，沒有觸殺。帶著運動家精神上場，享受比賽！若有脫序情事，裁判可沒收比賽且直接判定勝負。' }
                ];

                container.innerHTML = items.map(item => {
                    const isChecked = window.state.checklist && window.state.checklist[item.id];
                    return `
                        <div class="flex items-start space-x-3 sm:space-x-5 group cursor-pointer transition-all duration-300 p-3 sm:p-4 rounded-xl hover:bg-zinc-800/40 border border-transparent ${isChecked ? 'border-zinc-700 bg-black/40 shadow-inner' : ''}" onclick="window.toggleChecklistItem('${item.id}')">
                            <div class="mt-1 shrink-0">
                                <div class="w-10 h-10 sm:w-12 sm:h-12 border-2 rounded-lg flex items-center justify-center transition-all duration-300 transform ${isChecked ? 'bg-pink-600 border-pink-500 scale-105 shadow-[0_0_15px_rgba(236,72,153,0.4)]' : 'border-zinc-600 group-hover:border-pink-500 group-hover:bg-pink-900/20'}">
                                    <div class="transition-colors duration-300 ${isChecked ? 'text-white' : 'text-zinc-500 group-hover:text-pink-400'}">
                                        <i data-lucide="${isChecked ? 'check-square' : item.icon}" class="w-5 h-5 sm:w-6 sm:h-6"></i>
                                    </div>
                                </div>
                            </div>
                            <div class="transition-all duration-500 ${isChecked ? 'opacity-40 grayscale' : 'opacity-100'}">
                                <h4 class="text-base sm:text-xl font-black mb-1 sm:mb-2 uppercase tracking-wider transition-all duration-300 ${isChecked ? 'text-zinc-500 line-through decoration-pink-500 decoration-2' : 'text-white group-hover:text-pink-400'}">${item.title}</h4>
                                <div class="text-zinc-400 leading-relaxed text-xs sm:text-sm md:text-base">${item.desc}</div>
                            </div>
                        </div>`;
                }).join('');
                if(window.state.activeTab === 'tactics' && window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Checklist failed:', e); }
        };

        window.toggleChecklistItem = function(id) {
            if(window.state && window.state.checklist) {
                window.state.checklist[id] = !window.state.checklist[id];
                window.saveStateToLocal();
                window.renderChecklist();
            }
        };

        window.renderSchedule = function() {
            try {
                const container = document.getElementById('schedule-container');
                if(!container) return;
                container.className = "flex flex-col space-y-12 sm:space-y-16 relative z-10 w-full";

                const groups = [
                    { grade: '3', title: '三年級預賽', classes: '301, 302, 303' },
                    { grade: '4', title: '四年級預賽', classes: '401, 402, 403' },
                    { grade: '5', title: '五年級預賽', classes: '501, 502, 503' },
                    { grade: '6', title: '六年級預賽', classes: '601, 602, 603' }
                ];

                container.innerHTML = groups.map(g => {
                    const currentMatches = Array.isArray(window.state.matches) ? window.state.matches.filter(m => m.grade === g.grade) : [];
                    const matchesHtml = currentMatches.map(m => {
                        const isCompleted = m.status === 'completed';
                        const scoreA = parseInt(m.scoreA);
                        const scoreB = parseInt(m.scoreB);
                        const isTie = isCompleted && scoreA === scoreB;
                        const aWon = isCompleted && scoreA > scoreB;
                        const bWon = isCompleted && scoreB > scoreA;

                        const scoreHtml = isCompleted ? `
                            <div class="flex flex-col items-center justify-center">
                                <div class="flex items-center space-x-3 sm:space-x-5">
                                    <span class="text-3xl sm:text-4xl font-black ${aWon ? 'text-pink-500 drop-shadow-[0_0_12px_rgba(236,72,153,0.8)]' : (isTie ? 'text-zinc-400' : 'text-zinc-500')}">${m.scoreA}</span>
                                    <span class="text-zinc-700 text-lg sm:text-2xl font-black">:</span>
                                    <span class="text-3xl sm:text-4xl font-black ${bWon ? 'text-pink-500 drop-shadow-[0_0_12px_rgba(236,72,153,0.8)]' : (isTie ? 'text-zinc-400' : 'text-zinc-500')}">${m.scoreB}</span>
                                </div>
                                <span class="text-[9px] sm:text-[10px] text-green-400 font-black tracking-widest mt-1.5 sm:mt-2 bg-green-500/10 px-2 py-0.5 rounded uppercase border border-green-500/20 shadow-inner">Final</span>
                            </div>
                        ` : `
                            <div class="flex flex-col items-center justify-center">
                                <div class="bg-zinc-800/80 text-zinc-400 w-10 h-10 sm:w-12 sm:h-12 rounded-full flex items-center justify-center font-black text-xs sm:text-sm italic border border-zinc-700 shadow-inner">VS</div>
                                <span class="text-[9px] sm:text-[10px] text-zinc-500 font-bold tracking-widest mt-1.5 sm:mt-2 uppercase">Upcoming</span>
                            </div>
                        `;

                        return `
                            <div class="bg-zinc-900/60 border border-zinc-800/80 rounded-2xl overflow-hidden transition-all duration-300 hover:border-pink-500/40 hover:shadow-[0_0_20px_rgba(236,72,153,0.1)] group hover:-translate-y-1 flex flex-col relative w-full">
                                ${isCompleted && !isTie ? '<div class="absolute inset-0 bg-gradient-to-br from-pink-500/5 via-transparent to-transparent pointer-events-none"></div>' : ''}
                                
                                <div class="bg-zinc-950/80 px-4 sm:px-5 py-2.5 sm:py-3 flex justify-between items-center border-b border-zinc-800/80 relative z-10">
                                    <div class="flex items-center space-x-2 sm:space-x-3">
                                        <span class="bg-pink-600 text-white text-[9px] sm:text-[10px] font-black px-2 sm:px-2.5 py-0.5 sm:py-1 rounded shadow-sm tracking-widest uppercase">${m.match}</span>
                                        <span class="text-zinc-400 text-[10px] sm:text-xs font-mono font-bold flex items-center tracking-wider"><i data-lucide="calendar-days" class="w-3 h-3 sm:w-3.5 sm:h-3.5 mr-1.5 text-zinc-500"></i>${m.date}</span>
                                    </div>
                                    <span class="text-zinc-500 text-[10px] sm:text-xs font-mono font-bold flex items-center tracking-wider"><i data-lucide="clock" class="w-3 h-3 sm:w-3.5 sm:h-3.5 mr-1.5 text-zinc-600"></i>${m.time}</span>
                                </div>
                                
                                <div class="p-5 sm:p-8 flex items-center justify-between relative z-10 flex-1">
                                    <div class="flex-1 flex flex-col items-center sm:items-start justify-center overflow-hidden">
                                        <span class="text-2xl sm:text-4xl lg:text-5xl font-black tracking-wider ${aWon ? 'text-white' : (isCompleted ? 'text-zinc-500' : 'text-zinc-200')} font-mono drop-shadow-md whitespace-nowrap">${m.teamA}</span>
                                        <span class="text-[9px] sm:text-[10px] ${aWon ? 'text-pink-400' : 'text-zinc-600'} font-black tracking-widest mt-1.5 uppercase hidden sm:block">TEAM A</span>
                                    </div>
                                    <div class="shrink-0 px-4 sm:px-8">
                                        ${scoreHtml}
                                    </div>
                                    <div class="flex-1 flex flex-col items-center sm:items-end justify-center overflow-hidden">
                                        <span class="text-2xl sm:text-4xl lg:text-5xl font-black tracking-wider ${bWon ? 'text-white' : (isCompleted ? 'text-zinc-500' : 'text-zinc-200')} font-mono drop-shadow-md whitespace-nowrap">${m.teamB}</span>
                                        <span class="text-[9px] sm:text-[10px] ${bWon ? 'text-pink-400' : 'text-zinc-600'} font-black tracking-widest mt-1.5 uppercase hidden sm:block">TEAM B</span>
                                    </div>
                                </div>
                            </div>
                        `;
                    }).join('');

                    return `
                        <div class="w-full">
                            <div class="flex flex-col sm:flex-row sm:items-center justify-between mb-4 sm:mb-6 gap-2">
                                <h4 class="text-xl sm:text-2xl font-black text-white tracking-widest flex items-center uppercase drop-shadow-md">
                                    <span class="w-1.5 sm:w-2 h-6 sm:h-8 bg-pink-500 mr-2.5 sm:mr-3 transform -skew-x-12"></span>
                                    ${g.title}
                                </h4>
                                <span class="text-[10px] sm:text-xs font-bold text-pink-400 bg-pink-950/40 border border-pink-500/30 px-3 py-1 sm:py-1.5 rounded-full tracking-widest shadow-sm w-fit">${g.classes}</span>
                            </div>
                            <div class="grid grid-cols-1 lg:grid-cols-2 xl:grid-cols-3 gap-4 sm:gap-6">
                                ${matchesHtml}
                            </div>
                        </div>`;
                }).join('');
                
                if(window.state.activeTab === 'schedule' && window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Schedule failed:', e); }
        };

        // ==================== 打線安排邏輯 (防呆防抖) ====================
        window.getGradeAlertData = function(grade) {
            switch(grade) {
                case '3': return { text: "每半局 12-13 人打擊（不重複）。共計 24-26 棒次。", limit: 26 };
                case '4': return { text: "每半局 10-12 人打擊（不重複）。共計 20-24 棒次。", limit: 24 };
                case '5': return { text: "每半局 13-14 人打擊（不重複）。須選派 9 人防守。", limit: 30 };
                case '6': return { text: "每半局 16-18 人打擊，每人皆打擊兩次（第一、第二半局打線相同）。", limit: 19 };
                default: return { text: "請先選擇年級以查看排棒限制。", limit: 19 };
            }
        };

        window.handleLineupGradeChange = function() {
            try {
                window.state.lineup.grade = document.getElementById('lineup-grade-select').value;
                window.state.lineup.cls = '';
                
                const classSelect = document.getElementById('lineup-class-select');
                if(classSelect) {
                    classSelect.innerHTML = '<option value="">-- 班級 --</option>';
                    if (window.state.lineup.grade && window.classOptions[window.state.lineup.grade]) {
                        window.classOptions[window.state.lineup.grade].forEach(cls => { classSelect.innerHTML += `<option value="${cls}">${cls} 班</option>`; });
                        classSelect.disabled = false;
                    } else {
                        classSelect.disabled = true;
                    }
                }
                window.resetLineupUI();
                window.updateLineupUIState();
            } catch(e) { console.error(e); }
        };

        window.handleLineupClassChange = function() {
            try {
                const cls = document.getElementById('lineup-class-select').value;
                window.state.lineup.cls = cls;
                window.state.lineup.isUnlocked = false; 
                
                if (cls && window.state.lineup.allClassesData && window.state.lineup.allClassesData[cls]) {
                    window.state.lineup.data = [...window.state.lineup.allClassesData[cls].data];
                    window.state.lineup.lastUpdated = window.state.lineup.allClassesData[cls].lastUpdated;
                } else {
                    window.resetLineupUI();
                }
                window.updateLineupUIState();
            } catch(e) { console.error(e); }
        };

        window.resetLineupUI = function() {
            if(window.state && window.state.lineup) {
                window.state.lineup.data = Array.from({ length: 30 }, () => ({ number: '', name: '' }));
                window.state.lineup.lastUpdated = null;
                window.state.lineup.isUnlocked = false;
            }
            const passcodeInput = document.getElementById('lineup-passcode-input');
            const authError = document.getElementById('lineup-auth-error');
            if (passcodeInput) passcodeInput.value = '';
            if (authError) authError.classList.add('hidden');
        };

        window.updateLineupUIState = function() {
            try {
                const lState = window.state.lineup;
                if(!lState) return;

                const alertText = document.getElementById('lineup-alert-text');
                const subtext = document.getElementById('lineup-alert-subtext');
                const alertBox = document.getElementById('lineup-alert-box');
                const alertIcon = document.getElementById('lineup-alert-icon');
                
                if (alertText) alertText.innerText = window.getGradeAlertData(lState.grade).text;

                if (lState.grade && lState.cls) {
                    if(subtext) { subtext.innerHTML = `目前編輯：<span class="font-black text-white px-1 bg-black/50 rounded">${lState.cls} 班</span>`; subtext.classList.remove('hidden'); }
                    if(alertBox) alertBox.className = 'p-3 sm:p-4 mb-5 sm:mb-6 rounded-xl border flex items-start space-x-2 sm:space-x-3 transition-opacity duration-300 shadow-inner ' + (!lState.isUnlocked ? 'opacity-60 blur-[1px] ' : '') + 'bg-pink-900/10 border-pink-500/50 text-pink-200';
                    if(alertIcon) { alertIcon.className = 'w-5 h-5 sm:w-6 sm:h-6 shrink-0 mt-0.5 text-pink-500'; alertIcon.setAttribute('data-lucide', 'alert-circle'); }
                } else {
                    if(subtext) subtext.classList.add('hidden');
                    if(alertBox) alertBox.className = 'p-3 sm:p-4 mb-5 sm:mb-6 rounded-xl border flex items-start space-x-2 sm:space-x-3 bg-zinc-900/80 border-zinc-700 text-zinc-400 shadow-inner';
                    if(alertIcon) { alertIcon.className = 'w-5 h-5 sm:w-6 sm:h-6 shrink-0 mt-0.5 text-zinc-500'; alertIcon.setAttribute('data-lucide', 'alert-circle'); }
                }

                const lockIconDiv = document.getElementById('lineup-lock-icon');
                if (lockIconDiv) {
                    if (lState.cls) {
                        lockIconDiv.classList.remove('hidden');
                        lockIconDiv.className = `p-1.5 sm:p-2 rounded-md shrink-0 border ${lState.isUnlocked ? 'bg-green-500/10 text-green-500 border-green-500/30' : 'bg-red-500/10 text-red-500 border-red-500/30'}`;
                        lockIconDiv.innerHTML = `<i data-lucide="${lState.isUnlocked ? 'unlock' : 'lock'}" class="w-4 h-4 sm:w-5 sm:h-5"></i>`;
                    } else {
                        lockIconDiv.classList.add('hidden');
                    }
                }

                const controls = document.getElementById('lineup-controls');
                const exportBtn = document.getElementById('export-lineup-btn');
                
                if (controls) {
                    if (lState.cls && lState.isUnlocked) {
                        controls.classList.remove('opacity-50', 'pointer-events-none', 'grayscale');
                        const csvUpload = document.getElementById('csv-upload');
                        const csvUploadLabel = document.getElementById('csv-upload-label');
                        if (csvUpload) csvUpload.disabled = false;
                        if (csvUploadLabel) csvUploadLabel.classList.remove('cursor-not-allowed');
                        controls.querySelectorAll('button').forEach(b => b.disabled = false);
                        
                        if (exportBtn) {
                            if (lState.lastUpdated) {
                                exportBtn.classList.remove('opacity-50', 'cursor-not-allowed');
                                exportBtn.title = `匯出 ${lState.cls} 班打線名單`;
                            } else {
                                exportBtn.classList.add('opacity-50', 'cursor-not-allowed');
                                exportBtn.title = "請先儲存資料後再匯出";
                            }
                        }
                    } else {
                        controls.classList.add('opacity-50', 'pointer-events-none', 'grayscale');
                        const csvUpload = document.getElementById('csv-upload');
                        const csvUploadLabel = document.getElementById('csv-upload-label');
                        if (csvUpload) csvUpload.disabled = true;
                        if (csvUploadLabel) csvUploadLabel.classList.add('cursor-not-allowed');
                        controls.querySelectorAll('button').forEach(b => b.disabled = true);
                    }
                }

                const updatedDiv = document.getElementById('lineup-last-updated-container');
                const updatedText = document.getElementById('lineup-last-updated-text');
                if (updatedDiv && updatedText) {
                    if (lState.lastUpdated) {
                        updatedText.innerText = lState.lastUpdated;
                        updatedDiv.style.display = 'flex';
                    } else {
                        updatedDiv.style.display = 'none';
                    }
                }

                const overlayClass = document.getElementById('lineup-overlay-select-class');
                const overlayPwd = document.getElementById('lineup-overlay-password');
                const tableContainer = document.getElementById('lineup-table-container');
                const pwdOverlayText = document.getElementById('password-overlay-text');
                const tableWrapper = document.getElementById('lineup-table-wrapper');

                if (overlayClass && overlayPwd && tableContainer && tableWrapper) {
                    if (!lState.cls) {
                        overlayClass.style.display = 'flex'; overlayPwd.style.display = 'none';
                        tableContainer.className = "table-scroll-container overflow-x-auto transition-all duration-500 opacity-20 pointer-events-none blur-[4px] select-none grayscale bg-black";
                        tableWrapper.className = "relative rounded-lg overflow-hidden border border-zinc-800 max-h-[400px]";
                    } else if (!lState.isUnlocked) {
                        overlayClass.style.display = 'none'; overlayPwd.style.display = 'flex';
                        if (pwdOverlayText) pwdOverlayText.innerText = `請輸入 ${lState.cls} 班導師密碼解鎖。`;
                        tableContainer.className = "table-scroll-container overflow-x-auto transition-all duration-500 opacity-20 pointer-events-none blur-[4px] select-none grayscale bg-black";
                        tableWrapper.className = "relative rounded-lg overflow-hidden border border-zinc-800 max-h-[400px]";
                    } else {
                        overlayClass.style.display = 'none'; overlayPwd.style.display = 'none';
                        tableContainer.className = "table-scroll-container overflow-x-auto transition-all duration-500 opacity-100 bg-black";
                        tableWrapper.className = "relative rounded-lg overflow-visible border border-zinc-800 h-auto";
                    }
                }

                window.renderLineupTable();
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('UI State update failed', e); }
        };

        window.handleLineupUnlock = function(e) {
            e.preventDefault();
            const input = document.getElementById('lineup-passcode-input');
            const pwd = input ? input.value : '';
            const cls = window.state.lineup.cls;
            
            // JPM Security: Base64
            const classPasscodesB64 = {
                '301': 'MzAxMTEx', '302': 'MzAyMjIy', '303': 'MzAzMzMz',
                '401': 'NDAxMTEx', '402': 'NDAyMjIy', '403': 'NDAzMzMz',
                '501': 'NTAxMTEx', '502': 'NTAyMjIy', '503': 'NTAzMzMz',
                '601': 'NjAxMTEx', '602': 'NjAyMjIy', '603': 'NjAzMzMz'
            };

            let isMatch = false;
            try { isMatch = btoa(pwd) === classPasscodesB64[cls]; } catch(err) {}

            if (isMatch) {
                window.state.lineup.isUnlocked = true;
                window.showToast(`${cls} 班打線編輯權限已解鎖`);
                window.updateLineupUIState();
            } else {
                const errorText = document.getElementById('lineup-auth-error');
                if (errorText) errorText.classList.remove('hidden');
                if (input) {
                    input.classList.remove('border-zinc-800', 'focus:border-pink-500');
                    input.classList.add('border-red-500', 'focus:border-red-500', 'bg-red-950/30', 'animate-shake');
                    setTimeout(() => input.classList.remove('animate-shake'), 400);
                }
            }
        };

        window.renderLineupTable = function() {
            try {
                const tbody = document.getElementById('lineup-tbody');
                if (!tbody) return;
                
                const limit = window.getGradeAlertData(window.state.lineup.grade).limit;
                let html = '';

                for (let i = 0; i < limit; i++) {
                    const player = (window.state.lineup.data && window.state.lineup.data[i]) ? window.state.lineup.data[i] : { number: '', name: '' };
                    const isHalfStart = i === Math.floor(limit / 2) && window.state.lineup.grade !== '6';
                    const disabledStr = !window.state.lineup.isUnlocked ? 'disabled' : '';

                    html += `
                        <tr id="lineup-row-${i}" class="lineup-row transition-colors group hover:bg-zinc-800/60 ${isHalfStart ? 'border-t-[3px] sm:border-t-4 border-pink-500/60' : ''}">
                            <td class="py-2 sm:py-3 px-2 sm:px-5 relative text-center">
                                ${isHalfStart ? '<div class="absolute -top-3 left-1/2 transform -translate-x-1/2 bg-pink-600 text-white text-[9px] sm:text-[10px] font-black px-2 py-0.5 rounded shadow-md whitespace-nowrap z-10">第二半局</div>' : ''}
                                <span id="lineup-badge-${i}" class="lineup-badge inline-flex items-center justify-center w-7 h-7 sm:w-8 sm:h-8 rounded-full border font-mono text-[11px] sm:text-xs font-bold transition-colors bg-zinc-900 border-zinc-700 text-zinc-400 group-hover:border-pink-500 group-hover:text-pink-400 group-hover:bg-zinc-800 shadow-inner">${i + 1}</span>
                            </td>
                            <td class="py-1.5 sm:py-2 px-1.5 sm:px-4"><input type="text" id="lineup-input-num-${i}" placeholder="座號" value="${window.escapeHTML(player.number)}" oninput="window.handleLineupInput(${i}, 'number', this)" class="lineup-number-input w-full px-2.5 sm:px-3 py-2 sm:py-2.5 text-white font-mono text-sm sm:text-base transition-colors focus:outline-none focus:ring-1 focus:ring-pink-500 focus:border-pink-500 bg-zinc-950 border border-zinc-800 focus:bg-black hover:border-zinc-600 rounded-md shadow-inner" ${disabledStr}></td>
                            <td class="py-1.5 sm:py-2 px-1.5 sm:px-4"><input type="text" placeholder="姓名" value="${window.escapeHTML(player.name)}" oninput="window.handleLineupInput(${i}, 'name', this)" class="lineup-name-input w-full px-2.5 sm:px-3 py-2 sm:py-2.5 text-white text-sm sm:text-base transition-colors focus:outline-none focus:ring-1 focus:ring-pink-500 focus:border-pink-500 bg-zinc-950 border border-zinc-800 focus:bg-black hover:border-zinc-600 rounded-md min-w-[80px] shadow-inner" ${disabledStr}></td>
                            <td class="py-1.5 sm:py-2 px-1.5 sm:px-4 text-center" id="lineup-status-${i}">
                                ${isHalfStart ? '<span class="text-[9px] sm:text-[10px] bg-zinc-800 text-zinc-400 px-2 py-1 rounded-md whitespace-nowrap font-bold shadow-inner">起點</span>' : ''}
                            </td>
                        </tr>
                    `;
                }
                tbody.innerHTML = html;
                if(window.state.lineup.grade && window.state.lineup.grade !== '6') window.checkDuplicates();
            } catch(e) { console.error('Table render failed', e); }
        };

        let inputTimeout;
        window.handleLineupInput = function(idx, field, el) {
            if(!window.state || !window.state.lineup || !window.state.lineup.data) return;
            window.state.lineup.data[idx][field] = window.escapeHTML(el.value);
            if (field === 'number' && window.state.lineup.grade !== '6') window.checkDuplicates();

            clearTimeout(inputTimeout);
            inputTimeout = setTimeout(() => {
                if (window.state.lineup.cls) {
                    window.state.lineup.allClassesData[window.state.lineup.cls] = {
                        data: [...window.state.lineup.data],
                        lastUpdated: window.state.lineup.lastUpdated // 保持原有時間，直到按儲存
                    };
                    window.saveStateToLocal();
                }
            }, 300);
        };

        window.checkDuplicates = function() {
            try {
                if(!window.state || !window.state.lineup || !window.state.lineup.data) return;
                const limit = window.getGradeAlertData(window.state.lineup.grade).limit;
                const counts = {};
                for (let i = 0; i < limit; i++) {
                    const num = window.state.lineup.data[i]?.number?.trim();
                    if (num) counts[num] = (counts[num] || 0) + 1;
                }

                for (let i = 0; i < limit; i++) {
                    const num = window.state.lineup.data[i]?.number?.trim();
                    const isDup = num && counts[num] > 1;
                    
                    const row = document.getElementById(`lineup-row-${i}`);
                    const badge = document.getElementById(`lineup-badge-${i}`);
                    const numInput = document.getElementById(`lineup-input-num-${i}`);
                    const statusCell = document.getElementById(`lineup-status-${i}`);
                    const isHalfStart = i === Math.floor(limit / 2) && window.state.lineup.grade !== '6';

                    if (!row) continue;

                    if (isDup) {
                        row.classList.add('bg-red-900/30'); row.classList.remove('hover:bg-zinc-800/60');
                        if(badge) badge.className = 'lineup-badge inline-flex items-center justify-center w-7 h-7 sm:w-8 sm:h-8 rounded-full border font-mono text-[11px] sm:text-xs font-black transition-colors bg-red-950 border-red-500 text-red-300 shadow-[0_0_8px_rgba(239,68,68,0.4)]';
                        if(numInput) numInput.className = 'lineup-number-input w-full px-2.5 sm:px-3 py-2 sm:py-2.5 text-white font-mono text-sm sm:text-base transition-colors focus:outline-none focus:ring-1 focus:ring-red-500 focus:border-red-500 bg-red-950/60 border border-red-500/80 rounded-md';
                        if(statusCell) statusCell.innerHTML = '<span class="flex items-center justify-center text-[9px] sm:text-[11px] text-red-200 font-bold bg-red-900 px-1.5 sm:px-2.5 py-1 rounded-md whitespace-nowrap shadow-sm"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" class="mr-1 sm:mr-1.5"><path d="m21.73 18-8-14a2 2 0 0 0-3.48 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3Z"></path><line x1="12" y1="9" x2="12" y2="13"></line><line x1="12" y1="17" x2="12.01" y2="17"></line></svg>重複</span>';
                    } else {
                        row.classList.remove('bg-red-900/30'); row.classList.add('hover:bg-zinc-800/60');
                        if(badge) badge.className = 'lineup-badge inline-flex items-center justify-center w-7 h-7 sm:w-8 sm:h-8 rounded-full border font-mono text-[11px] sm:text-xs font-bold transition-colors bg-zinc-900 border-zinc-700 text-zinc-400 group-hover:border-pink-500 group-hover:text-pink-400 group-hover:bg-zinc-800 shadow-inner';
                        if(numInput) numInput.className = 'lineup-number-input w-full px-2.5 sm:px-3 py-2 sm:py-2.5 text-white font-mono text-sm sm:text-base transition-colors focus:outline-none focus:ring-1 focus:ring-pink-500 focus:border-pink-500 bg-zinc-950 border border-zinc-800 focus:bg-black hover:border-zinc-600 rounded-md shadow-inner';
                        if(statusCell) statusCell.innerHTML = isHalfStart ? '<span class="text-[9px] sm:text-[10px] bg-zinc-800 text-zinc-400 px-2 py-1 rounded-md whitespace-nowrap font-bold shadow-inner">起點</span>' : '';
                    }
                }
            } catch(e) { console.error('Dup check failed', e); }
        };

        window.handleCsvImport = function(e) {
            const file = e.target.files[0];
            if (!file) return;
            const icon = document.getElementById('csv-upload-icon');
            const text = document.getElementById('csv-upload-text');
            if (icon) icon.classList.add('animate-bounce'); 
            if (text) text.innerText = '解析...';

            const reader = new FileReader();
            reader.onload = (ev) => {
                try {
                    const txt = ev.target.result;
                    const rows = txt.split(/\r?\n/).filter(row => row.trim().length > 0).map(row => row.split(','));
                    const limit = window.getGradeAlertData(window.state.lineup.grade).limit;
                    const newD = [];

                    for (let i = 1; i <= limit; i++) {
                        if (rows[i]) newD.push({ number: window.escapeHTML(rows[i][1]?.trim() || ''), name: window.escapeHTML(rows[i][2]?.trim() || '') });
                        else newD.push({ number: '', name: '' });
                    }
                    window.state.lineup.data = Array.from({ length: 30 }, (_, i) => newD[i] || { number: '', name: '' });
                    window.state.lineup.lastUpdated = new Date().toLocaleString('zh-TW', { hour12: false });
                    if (window.state.lineup.cls) {
                        window.state.lineup.allClassesData[window.state.lineup.cls] = { data: [...window.state.lineup.data], lastUpdated: window.state.lineup.lastUpdated };
                        if (window.saveToCloud) window.saveToCloud('lineups', window.state.lineup.cls, window.state.lineup.allClassesData[window.state.lineup.cls]);
                        window.saveStateToLocal();
                    }
                    window.showToast(`匯入成功：${window.state.lineup.cls} 班`);
                } catch (error) {
                    window.showToast('CSV 解析失敗，請確認格式');
                } finally {
                    if (icon) icon.classList.remove('animate-bounce'); 
                    if (text) text.innerText = '匯入';
                    e.target.value = null; window.updateLineupUIState();
                }
            };
            reader.readAsText(file);
        };

        window.handleDownloadTemplate = function() {
            const grade = window.state.lineup.grade;
            if (!grade) {
                window.showToast('請先選擇年級！');
                return;
            }
            const limit = window.getGradeAlertData(grade).limit;
            let csvContent = '\uFEFF' + "棒次,座號,選手姓名\n";
            for (let i = 1; i <= limit; i++) {
                csvContent += (i === 1) ? `${i},1,王小明\n` : `${i},,\n`;
            }
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            link.href = URL.createObjectURL(blob);
            link.download = `忠義國小_${grade}年級_打線範本.csv`;
            document.body.appendChild(link); link.click(); document.body.removeChild(link);
            window.showToast(`已下載範本 (至 ${limit} 棒)`);
        };

        window.handleClearLineup = function() {
            if (!window.state.lineup.cls) return;
            if (window.confirm(`確定清空 ${window.state.lineup.cls} 班資料？`)) {
                window.state.lineup.data = Array.from({ length: 30 }, () => ({ number: '', name: '' }));
                if (window.state.lineup.cls) {
                    window.state.lineup.allClassesData[window.state.lineup.cls] = { data: [...window.state.lineup.data], lastUpdated: null };
                    if (window.saveToCloud) window.saveToCloud('lineups', window.state.lineup.cls, window.state.lineup.allClassesData[window.state.lineup.cls]);
                    window.saveStateToLocal();
                }
                window.updateLineupUIState(); window.showToast('資料已清空');
            }
        };

        window.handleSaveLineup = function() {
            if (!window.state.lineup.cls) return;
            if (!navigator.onLine) {
                window.showToast('偵測到網路離線，資料暫存於本地端，請連線後再次點擊儲存！');
                window.state.lineup.lastUpdated = new Date().toLocaleString('zh-TW', { hour12: false }) + " (離線暫存)";
                window.saveStateToLocal();
                window.updateLineupUIState();
                return;
            }
            
            window.state.lineup.lastUpdated = new Date().toLocaleString('zh-TW', { hour12: false });
            if (window.state.lineup.cls) {
                window.state.lineup.allClassesData[window.state.lineup.cls] = { data: [...window.state.lineup.data], lastUpdated: window.state.lineup.lastUpdated };
                if (window.saveToCloud) window.saveToCloud('lineups', window.state.lineup.cls, window.state.lineup.allClassesData[window.state.lineup.cls]);
                window.saveStateToLocal();
            }
            window.updateLineupUIState(); window.showToast(`${window.state.lineup.cls} 班打擊順序已儲存並同步至雲端`);
        };

        window.handleExportLineup = function() {
            const lState = window.state.lineup;
            if (!lState.cls || !lState.isUnlocked || !lState.lastUpdated) {
                window.showToast('請先完成編輯並儲存後，再進行匯出！');
                return;
            }

            const limit = window.getGradeAlertData(lState.grade).limit;
            let csvContent = '\uFEFF' + "棒次,座號,選手姓名\n";
            
            for (let i = 0; i < limit; i++) {
                const player = lState.data[i];
                const safeNumber = (player.number || '').replace(/,/g, '，');
                const safeName = (player.name || '').replace(/,/g, '，');
                csvContent += `${i + 1},${safeNumber},${safeName}\n`;
            }

            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            const url = URL.createObjectURL(blob);
            const fileName = `忠義國小_${lState.cls}班_樂樂棒打線名單.csv`;
            
            link.setAttribute("href", url);
            link.setAttribute("download", fileName);
            link.style.visibility = 'hidden';
            
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            URL.revokeObjectURL(url);
            window.showToast(`已成功匯出 ${lState.cls} 班打線名單！`);
        };

        // 後台匯出總管
        window.exportMasterLineup = function() {
            const allClasses = window.state.lineup.allClassesData;
            const classKeys = Object.keys(allClasses).sort();
            
            if (classKeys.length === 0) {
                window.showToast('目前後台沒有任何已繳交的打線資料！');
                return;
            }

            let csvContent = '\uFEFF' + "班級,棒次,座號,選手姓名,繳交時間\n";
            
            classKeys.forEach(cls => {
                const grade = cls.charAt(0);
                const limit = window.getGradeAlertData(grade).limit;
                const data = allClasses[cls].data;
                const lastUpdated = allClasses[cls].lastUpdated || '未知';
                
                for (let i = 0; i < limit; i++) {
                    const player = data[i] || { number: '', name: '' };
                    const safeNumber = (player.number || '').replace(/,/g, '，');
                    const safeName = (player.name || '').replace(/,/g, '，');
                    csvContent += `${cls},${i + 1},${safeNumber},${safeName},${lastUpdated}\n`;
                }
            });

            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement("a");
            const url = URL.createObjectURL(blob);
            const dateStr = new Date().toISOString().split('T')[0];
            const fileName = `忠義國小_全校打線總表_${dateStr}.csv`;
            
            link.setAttribute("href", url);
            link.setAttribute("download", fileName);
            link.style.visibility = 'hidden';
            
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            URL.revokeObjectURL(url);
            window.showToast(`已成功匯出全校打線總表！`);
        };

        window.renderAdminDashboard = function() {
            try {
                const lockStatus = document.getElementById('admin-tab-lock-status');
                const content = document.getElementById('admin-dashboard-content');
                const lockedMsg = document.getElementById('admin-locked-message');
                
                if (window.state.admin.isUnlocked) {
                    if(lockStatus) lockStatus.innerHTML = `<span class="text-[10px] sm:text-xs font-bold text-green-400 border border-green-500/30 px-3 sm:px-4 py-1.5 sm:py-2 rounded-md flex items-center bg-green-500/10 w-fit shadow-inner"><i data-lucide="unlock" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5 sm:mr-2"></i> 已解鎖最高權限</span>`;
                    if(content) content.classList.remove('hidden');
                    if(lockedMsg) lockedMsg.classList.add('hidden');
                    
                    const container = document.getElementById('admin-submitted-classes');
                    if (!container) return;

                    const allClasses = window.state.lineup.allClassesData;
                    const classKeys = Object.keys(allClasses).sort();

                    if (classKeys.length === 0) {
                        container.innerHTML = `<div class="col-span-full p-8 text-center text-zinc-500 border-2 border-dashed border-zinc-800 rounded-lg">目前尚無任何班級繳交打線名單</div>`;
                    } else {
                        container.innerHTML = classKeys.map(cls => {
                            const data = allClasses[cls];
                            return `
                                <div class="bg-black border border-zinc-700 p-4 rounded-lg shadow-inner flex flex-col items-center justify-center relative group hover:border-green-500/50 transition-colors">
                                    <div class="absolute top-2 right-2 bg-green-500/20 text-green-400 text-[9px] px-1.5 py-0.5 rounded shadow-sm flex items-center"><i data-lucide="check-circle-2" class="w-3 h-3 mr-1"></i>已收件</div>
                                    <span class="text-3xl font-black text-white mt-2">${cls}</span>
                                    <span class="text-[10px] text-zinc-500 mt-2 font-mono">${data.lastUpdated}</span>
                                </div>
                            `;
                        }).join('');
                    }
                } else {
                    if(lockStatus) lockStatus.innerHTML = `<button onclick="window.openAdminModal()" class="text-[10px] sm:text-xs font-bold text-zinc-300 hover:text-white border border-zinc-700 hover:border-green-500 px-3 sm:px-5 py-2 sm:py-2.5 rounded-md flex items-center bg-zinc-800 hover:bg-zinc-700 w-fit transition-all shadow-md active:scale-95 group"><i data-lucide="lock" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5 sm:mr-2 text-green-500 group-hover:scale-110 transition-transform"></i> 管理員解鎖</button>`;
                    if(content) content.classList.add('hidden');
                    if(lockedMsg) lockedMsg.classList.remove('hidden');
                }
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Admin failed', e); }
        };

        /* --- 成績區邏輯 --- */
        window.handleAdminAuth = function(e) {
            e.preventDefault();
            const input = document.getElementById('admin-passcode-input');
            const pwd = input ? input.value : '';
            
            // JPM Security: "840110" Base64 encoded
            let isMatch = false;
            try { isMatch = btoa(pwd) === "ODQwMTEw"; } catch(err) {}

            if (isMatch) {
                window.state.admin.isUnlocked = true;
                window.closeAdminModal(); 
                if(input) input.value = ''; 
                window.showToast('系統權限已完全解鎖');
                const btnAdmin = document.getElementById('tab-btn-admin');
                if(btnAdmin) {
                    btnAdmin.classList.remove('hidden');
                    btnAdmin.classList.add('flex-shrink-0', 'flex'); 
                }
                window.renderScoreboard(); window.renderFileList(); window.renderAdminDashboard();
            } else {
                const error = document.getElementById('admin-auth-error');
                if (error) error.classList.remove('hidden');
                if (input) {
                    input.classList.remove('border-zinc-800', 'focus:border-pink-500');
                    input.classList.add('border-red-500', 'focus:border-red-500', 'bg-red-950/30', 'animate-shake');
                    setTimeout(() => input.classList.remove('animate-shake'), 400);
                }
            }
        };

        window.renderScoreboard = function() {
            try {
                const sStatus = document.getElementById('admin-lock-status');
                if (window.state.admin.isUnlocked) {
                    if(sStatus) sStatus.innerHTML = `<span class="text-[10px] sm:text-xs font-bold text-green-400 border border-green-500/30 px-3 sm:px-4 py-1.5 sm:py-2 rounded-md flex items-center bg-green-500/10 w-fit shadow-inner"><i data-lucide="unlock" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5 sm:mr-2"></i> 管理模式：可新增/刪除比分</span>`;
                } else {
                    if(sStatus) sStatus.innerHTML = `<button onclick="window.openAdminModal()" class="text-[10px] sm:text-xs font-bold text-zinc-300 hover:text-white border border-zinc-700 hover:border-pink-500 px-3 sm:px-5 py-2 sm:py-2.5 rounded-md flex items-center bg-zinc-800 hover:bg-zinc-700 w-fit transition-all shadow-md active:scale-95 group"><i data-lucide="lock" class="w-3.5 h-3.5 sm:w-4 sm:h-4 mr-1.5 sm:mr-2 text-pink-500 group-hover:scale-110 transition-transform"></i> 管理員解鎖</button>`;
                }

                const container = document.getElementById('scoreboard-container');
                if(!container) return;

                container.innerHTML = ['3', '4', '5', '6'].map(grade => {
                    const currentMatches = Array.isArray(window.state.matches) ? window.state.matches.filter(m => m.grade === grade) : [];
                    const matchesHtml = currentMatches.map(m => {
                        const isComp = m.status === 'completed';
                        let contentHtml = '';
                        
                        if (window.state.admin.isUnlocked) {
                            if (isComp) {
                                contentHtml = `
                                    <div class="flex items-center justify-between mt-3 bg-zinc-950 p-2.5 sm:p-3 rounded-lg border border-zinc-800/80 shadow-inner">
                                        <div class="text-base sm:text-lg font-black text-white flex-1 text-center tracking-wider whitespace-nowrap">${m.teamA} <span class="text-pink-500 mx-2 sm:mx-4 drop-shadow-[0_0_5px_rgba(236,72,153,0.5)]">${m.scoreA} : ${m.scoreB}</span> ${m.teamB}</div>
                                        <button onclick="window.resetMatchScore('${m.id}')" class="ml-2 sm:ml-3 text-zinc-500 hover:text-red-400 bg-black p-2 rounded-md border border-zinc-800 hover:border-red-500/50 transition-colors shadow-sm active:scale-95 shrink-0" title="刪除成績"><i data-lucide="trash-2" class="w-4 h-4 sm:w-5 sm:h-5"></i></button>
                                    </div>`;
                            } else {
                                contentHtml = `
                                    <form onsubmit="window.submitScore(event, '${m.id}')" class="flex items-center justify-center gap-2 sm:gap-3 mt-3 bg-zinc-950 p-2 sm:p-3 rounded-lg border border-zinc-800/80 shadow-inner w-full flex-wrap sm:flex-nowrap">
                                        <div class="flex items-center justify-end flex-1">
                                            <span class="font-black text-zinc-300 text-sm sm:text-base whitespace-nowrap">${m.teamA}</span>
                                        </div>
                                        <div class="flex items-center justify-center shrink-0 space-x-1 sm:space-x-2">
                                            <input name="scoreA" type="number" min="0" required placeholder="分" class="w-12 sm:w-14 bg-black border border-zinc-700 text-white text-center font-bold text-sm sm:text-base rounded-md py-1.5 focus:ring-1 focus:ring-pink-500 outline-none transition-colors shadow-inner">
                                            <span class="text-zinc-600 text-[10px] sm:text-xs font-black italic">vs</span>
                                            <input name="scoreB" type="number" min="0" required placeholder="分" class="w-12 sm:w-14 bg-black border border-zinc-700 text-white text-center font-bold text-sm sm:text-base rounded-md py-1.5 focus:ring-1 focus:ring-pink-500 outline-none transition-colors shadow-inner">
                                        </div>
                                        <div class="flex items-center justify-start flex-1">
                                            <span class="font-black text-zinc-300 text-sm sm:text-base whitespace-nowrap">${m.teamB}</span>
                                        </div>
                                        <div class="w-full sm:w-auto flex justify-center mt-2 sm:mt-0 shrink-0">
                                            <button type="submit" class="bg-pink-600 hover:bg-pink-500 text-white p-1.5 sm:p-2 rounded-md transition-transform active:scale-95 shadow-md flex items-center justify-center w-full sm:w-auto" title="儲存"><i data-lucide="save" class="w-4 h-4 sm:w-5 sm:h-5"></i><span class="sm:hidden ml-1 text-xs font-bold">儲存</span></button>
                                        </div>
                                    </form>`;
                            }
                        } else {
                            if (isComp) {
                                const sA = parseInt(m.scoreA), sB = parseInt(m.scoreB);
                                contentHtml = `
                                    <div class="flex items-center justify-center mt-3 py-2 bg-zinc-950/50 rounded-lg border border-zinc-800/30 w-full px-2">
                                        <div class="flex-1 text-right font-black text-lg sm:text-xl tracking-widest whitespace-nowrap ${sA > sB ? 'text-pink-400 drop-shadow-[0_0_8px_rgba(236,72,153,0.5)] scale-105 transform transition-transform' : (sA === sB ? 'text-zinc-400' : 'text-zinc-300')}">
                                            ${m.teamA} <span class="ml-1 sm:ml-2 text-white">${m.scoreA}</span>
                                        </div>
                                        <div class="shrink-0 text-center text-zinc-600 mx-3 sm:mx-4 font-normal text-sm sm:text-base">-</div>
                                        <div class="flex-1 text-left font-black text-lg sm:text-xl tracking-widest whitespace-nowrap ${sB > sA ? 'text-pink-400 drop-shadow-[0_0_8px_rgba(236,72,153,0.5)] scale-105 transform transition-transform' : (sA === sB ? 'text-zinc-400' : 'text-zinc-300')}">
                                            <span class="mr-1 sm:mr-2 text-white">${m.scoreB}</span> ${m.teamB}
                                        </div>
                                    </div>`;
                            } else {
                                contentHtml = `
                                    <div class="flex items-center justify-center mt-3 py-2.5 bg-zinc-950/30 rounded-lg border border-zinc-800/20 w-full px-2">
                                        <div class="flex-1 text-right text-sm sm:text-base font-bold text-zinc-400 tracking-widest whitespace-nowrap">${m.teamA}</div>
                                        <div class="shrink-0 text-center text-[10px] sm:text-xs italic text-zinc-600 mx-3 sm:mx-4">vs</div>
                                        <div class="flex-1 text-left text-sm sm:text-base font-bold text-zinc-400 tracking-widest whitespace-nowrap">${m.teamB}</div>
                                    </div>`;
                            }
                        }

                        return `
                            <div class="flex flex-col bg-black border ${isComp ? 'border-pink-500/40 shadow-[0_0_10px_rgba(236,72,153,0.05)]' : 'border-zinc-800 hover:border-pink-500/50'} p-4 sm:p-5 rounded-xl transition-all duration-300 w-full">
                                <div class="flex justify-between items-center mb-1 sm:mb-2 border-b border-zinc-800/50 pb-2.5 sm:pb-3">
                                    <span class="text-[10px] sm:text-xs text-zinc-400 font-mono bg-zinc-900 px-2 sm:px-2.5 py-1 sm:py-1.5 rounded-md font-bold shadow-inner">${m.match}</span>
                                    <span class="text-[9px] sm:text-[10px] ${isComp ? 'bg-green-500/20 text-green-400 border border-green-500/30' : 'bg-zinc-800 text-zinc-500'} px-2 sm:px-2.5 py-1 sm:py-1.5 rounded-md uppercase tracking-widest font-black">${isComp ? '已發佈' : '未登錄'}</span>
                                </div>
                                ${contentHtml}
                            </div>`;
                    }).join('');

                    return `
                        <div class="space-y-3 sm:space-y-4 bg-zinc-950 p-4 sm:p-6 rounded-2xl border border-zinc-800/80 shadow-lg w-full">
                            <h4 class="text-sm sm:text-base font-black text-pink-500 tracking-widest border-b-2 border-zinc-800 pb-2.5 sm:pb-3 mb-3 sm:mb-4 flex items-center"><span class="bg-pink-900/30 border border-pink-500/20 px-2.5 py-1 rounded-md mr-2 sm:mr-2.5 shadow-sm">${grade}</span> 年級</h4>
                            ${matchesHtml}
                        </div>`;
                }).join('');
                
                const uploadContainer = document.getElementById('attachment-upload-container');
                if (uploadContainer) uploadContainer.style.display = window.state.admin.isUnlocked ? 'block' : 'none';
                if(window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Scoreboard Error:', e); }
        };

        window.submitScore = function(e, id) {
            e.preventDefault();
            const sA = window.escapeHTML(e.target.scoreA.value);
            const sB = window.escapeHTML(e.target.scoreB.value);
            if(!window.state || !Array.isArray(window.state.matches)) return;
            
            const matchIndex = window.state.matches.findIndex(m => m.id === id);
            if (matchIndex !== -1 && sA && sB) {
                window.state.matches[matchIndex] = { ...window.state.matches[matchIndex], scoreA: sA, scoreB: sB, status: 'completed' };
                if (window.saveToCloud) window.saveToCloud('matches', id, window.state.matches[matchIndex]);
                window.saveStateToLocal(); window.renderScoreboard(); window.renderSchedule(); window.showToast('比分發佈成功！');
            }
        };

        window.resetMatchScore = function(id) {
            if (window.confirm('確定要刪除這筆紀錄嗎？')) {
                if(!window.state || !Array.isArray(window.state.matches)) return;
                const matchIndex = window.state.matches.findIndex(m => m.id === id);
                if (matchIndex !== -1) {
                    window.state.matches[matchIndex] = { ...window.state.matches[matchIndex], scoreA: '', scoreB: '', status: 'pending' };
                    if (window.saveToCloud) window.saveToCloud('matches', id, window.state.matches[matchIndex]);
                    window.saveStateToLocal(); window.renderScoreboard(); window.renderSchedule(); window.showToast('紀錄已刪除');
                }
            }
        };

        window.handleFileUpload = function(e) {
            const file = e.target.files[0];
            if (!file) return;
            const text = document.getElementById('file-upload-text');
            const origText = text ? text.innerText : '新增賽事附件';
            if (text) text.innerText = '上傳中...';
            setTimeout(() => {
                const newFile = { id: Date.now(), name: window.escapeHTML(file.name), date: new Date().toISOString().split('T')[0] };
                if(Array.isArray(window.state.files)) window.state.files.unshift(newFile);
                if (text) text.innerText = origText; 
                e.target.value = null;
                if (window.saveToCloud) window.saveToCloud('files', newFile.id.toString(), newFile);
                window.saveStateToLocal(); window.renderFileList(); window.showToast('檔案已成功發佈');
            }, 1000);
        };

        window.renderFileList = function() {
            try {
                const container = document.getElementById('file-list-container');
                if(!container) return;
                
                const currentFiles = Array.isArray(window.state.files) ? window.state.files : [];
                if (currentFiles.length === 0) {
                    container.innerHTML = `
                        <div class="p-8 sm:p-16 flex flex-col items-center justify-center text-zinc-500 border-2 border-dashed border-zinc-800 m-4 sm:m-6 rounded-2xl bg-black/40">
                            <i data-lucide="file-text" class="w-10 h-10 sm:w-12 sm:h-12 mb-3 sm:mb-4 text-zinc-600"></i>
                            <p class="font-bold tracking-widest uppercase text-xs sm:text-sm text-center">目前無官方附件</p>
                        </div>`;
                } else {
                    container.innerHTML = currentFiles.map(f => `
                        <div class="p-4 sm:p-6 flex flex-col sm:flex-row sm:items-center justify-between hover:bg-zinc-800/60 transition-all duration-300 group">
                            <div class="flex items-center space-x-3 sm:space-x-5 mb-3 sm:mb-0">
                                <div class="p-3 sm:p-4 bg-black border border-zinc-700 text-pink-500 rounded-xl group-hover:scale-105 group-hover:border-pink-500/50 transition-all duration-300 shadow-inner">
                                    <i data-lucide="file-text" class="w-5 h-5 sm:w-6 sm:h-6"></i>
                                </div>
                                <div>
                                    <h4 class="text-base sm:text-lg font-black text-white group-hover:text-pink-400 transition-colors break-all line-clamp-1 leading-snug">${f.name}</h4>
                                    <p class="text-[10px] sm:text-xs text-zinc-500 font-mono mt-1 sm:mt-1.5 flex items-center font-bold tracking-wider"><i data-lucide="calendar-days" class="w-3 h-3 sm:w-3.5 sm:h-3.5 mr-1.5"></i>發佈: ${f.date}</p>
                                </div>
                            </div>
                            <button class="w-full sm:w-auto text-zinc-300 hover:text-white transition-all px-4 sm:px-6 py-2.5 sm:py-3 border border-zinc-600 hover:border-pink-500 hover:bg-pink-600 uppercase text-[10px] sm:text-xs font-black tracking-widest rounded-lg shadow-md active:scale-95">下載附件</button>
                        </div>
                    `).join('');
                }
                if(window.state.activeTab === 'results' && window.lucide) window.lucide.createIcons();
            } catch(e) { console.error('Render Files Error:', e); }
        };

        /* ==================== 啟動引擎 (生命週期防護) ==================== */
        window.addEventListener('DOMContentLoaded', () => {
            try { if (typeof window.loadStateFromLocal === 'function') window.loadStateFromLocal(); } catch(e) { console.error('Init Error', e); }
            try { if (typeof window.renderGradeCards === 'function') window.renderGradeCards(); } catch(e) { console.error('Init Error', e); }
            try { if (typeof window.renderSchedule === 'function') window.renderSchedule(); } catch(e) { console.error('Init Error', e); }
            try { if (typeof window.renderChecklist === 'function') window.renderChecklist(); } catch(e) { console.error('Init Error', e); }
            try { if (typeof window.renderScoreboard === 'function') window.renderScoreboard(); } catch(e) { console.error('Init Error', e); }
            try { if (typeof window.renderFileList === 'function') window.renderFileList(); } catch(e) { console.error('Init Error', e); }
            try { if (typeof window.renderAdminDashboard === 'function') window.renderAdminDashboard(); } catch(e) { console.error('Init Error', e); }
            try { if (typeof window.switchTab === 'function') window.switchTab('rules'); } catch(e) { console.error('Init Error', e); }
        });
    </script>
    
    <!-- ==================== Firebase 雲端資料庫模組 ==================== -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInWithCustomToken, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, doc, setDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const appId = typeof __app_id !== 'undefined' ? __app_id : 'zhongyi-baseball-v4';
        let firebaseConfig = null;
        let isFirebaseConfigured = false;

        if (typeof __firebase_config !== 'undefined') {
            try {
                firebaseConfig = JSON.parse(__firebase_config);
                isFirebaseConfigured = true;
            } catch(e){}
        }

        let db, auth;
        window.cloudUser = null;

        if (isFirebaseConfigured) {
            try {
                const app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);

                const initAuth = async () => {
                    if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                        await signInWithCustomToken(auth, __initial_auth_token);
                    } else {
                        await signInAnonymously(auth);
                    }
                };
                initAuth();

                onAuthStateChanged(auth, (user) => {
                    window.cloudUser = user;
                    if (user) {
                        setupRealtimeListeners();
                    }
                });
            } catch(e) { console.warn("Firebase Init Skipped"); }
        }

        window.saveToCloud = async function(collectionName, docId, data) {
            if (!window.cloudUser || !db) return;
            try {
                const ref = doc(db, 'artifacts', appId, 'public', 'data', collectionName, docId);
                await setDoc(ref, data);
            } catch(e) { console.error("Cloud Save Error", e); }
        }

        function setupRealtimeListeners() {
            try {
                const lineupsRef = collection(db, 'artifacts', appId, 'public', 'data', 'lineups');
                onSnapshot(lineupsRef, (snapshot) => {
                    if(window.state && window.state.lineup && window.state.lineup.allClassesData) {
                        snapshot.forEach(doc => {
                            window.state.lineup.allClassesData[doc.id] = doc.data();
                        });
                        if (window.state.activeTab === 'lineup' && typeof window.updateLineupUIState === 'function') {
                            window.updateLineupUIState();
                        }
                        if (window.state.activeTab === 'admin' && typeof window.renderAdminDashboard === 'function') {
                            window.renderAdminDashboard();
                        }
                    }
                });

                const matchesRef = collection(db, 'artifacts', appId, 'public', 'data', 'matches');
                onSnapshot(matchesRef, (snapshot) => {
                    if(window.state && Array.isArray(window.state.matches)) {
                        snapshot.forEach(docSnap => {
                            const mData = docSnap.data();
                            const idx = window.state.matches.findIndex(m => m.id === docSnap.id);
                            if (idx !== -1) {
                                window.state.matches[idx] = { ...window.state.matches[idx], ...mData };
                            }
                        });
                        
                        if (window.state.activeTab === 'schedule' && typeof window.renderSchedule === 'function') {
                            window.renderSchedule();
                        }
                        if (window.state.activeTab === 'results' && typeof window.renderScoreboard === 'function') {
                            window.renderScoreboard();
                        }
                    }
                });
            } catch(e) { console.warn("Firebase Sync Guard Blocked an Error:", e); }
        }
    </script>
</body>
</html>
