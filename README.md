# kakezan-kuku-app
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>九九マスター！たのしいかけざん学習アプリ</title>

    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            blue: '#3b82f6',
                            green: '#10b981',
                            yellow: '#f59e0b',
                            red: '#ef4444',
                            purple: '#8b5cf6',
                            pink: '#ec4899',
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'Hiragino Kaku Gothic ProN', 'Meiryo', 'sans-serif'],
                    }
                }
            }
        }
    </script>

    <!-- FontAwesome & Canvas Confetti -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

    <style>
        @keyframes bounce-subtle {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-6px); }
        }
        .animate-bounce-subtle {
            animation: bounce-subtle 2s infinite ease-in-out;
        }
        .pop-btn {
            transition: all 0.15s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .pop-btn:active {
            transform: scale(0.93);
        }
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
            border-radius: 9999px;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 9999px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 font-sans min-h-screen flex flex-col justify-between selection:bg-amber-200 selection:text-amber-900">

    <header class="bg-white border-b-4 border-amber-300 shadow-sm sticky top-0 z-30">
        <div class="max-w-4xl mx-auto px-4 py-3 flex items-center justify-between gap-2">
            <!-- App Title / Logo -->
            <div class="flex items-center gap-2">
                <div class="w-10 h-10 rounded-2xl bg-amber-400 text-slate-900 font-black text-xl flex items-center justify-center shadow-md border-2 border-amber-300 animate-bounce-subtle">
                    九
                </div>
                <div>
                    <h1 class="font-black text-lg md:text-xl text-slate-800 leading-none">九九マスター！</h1>
                    <p class="text-[10px] sm:text-xs font-bold text-amber-600">たのしくおぼえる かけざん</p>
                </div>
            </div>

            <!-- Dashboard Stats & Settings Badges -->
            <div class="flex items-center gap-2 sm:gap-3">
                <div class="bg-amber-50 border-2 border-amber-200 px-3 py-1 rounded-full flex items-center gap-1.5 shadow-sm">
                    <i class="fa-solid fa-star text-amber-400 text-sm sm:text-base"></i>
                    <span id="totalStars" class="font-black text-slate-800 text-sm sm:text-base">0</span>
                </div>
                <div class="bg-blue-50 border-2 border-blue-200 px-3 py-1 rounded-full flex items-center gap-1.5 shadow-sm hidden sm:flex">
                    <i class="fa-solid fa-trophy text-blue-500 text-sm"></i>
                    <span id="clearedStageCount" class="font-black text-blue-900 text-xs">0 / 9 段</span>
                </div>

                <!-- API Key Settings Button -->
                <button onclick="openApiKeyModal()" title="APIキー設定" class="w-9 h-9 rounded-full bg-slate-100 hover:bg-amber-100 text-slate-600 hover:text-amber-700 flex items-center justify-center text-sm font-bold transition border border-slate-200">
                    <i class="fa-solid fa-key"></i>
                </button>

                <!-- Sound Toggle -->
                <button id="soundToggleBtn" onclick="toggleMute()" class="w-9 h-9 rounded-full bg-slate-100 hover:bg-slate-200 text-slate-600 flex items-center justify-center text-sm font-bold transition border border-slate-200">
                    <i id="soundIcon" class="fa-solid fa-volume-high"></i>
                </button>
            </div>
        </div>
    </header>

    <main class="max-w-4xl mx-auto w-full px-4 py-6 flex-1">
        <!-- Navigation Tabs -->
        <nav class="grid grid-cols-3 sm:grid-cols-6 gap-2 mb-6">
            <button onclick="switchTab('table')" id="tab-table" class="nav-tab bg-white border-2 border-blue-500 text-blue-600 hover:bg-blue-50 font-black py-2 px-1 rounded-2xl shadow-sm flex flex-col sm:flex-row items-center justify-center gap-1 text-xs sm:text-sm transition">
                <i class="fa-solid fa-table-cells text-base sm:text-lg"></i> <span>九九ひょう</span>
            </button>
            <button onclick="switchTab('practice')" id="tab-practice" class="nav-tab bg-white border-2 border-emerald-500 text-emerald-600 hover:bg-emerald-50 font-black py-2 px-1 rounded-2xl shadow-sm flex flex-col sm:flex-row items-center justify-center gap-1 text-xs sm:text-sm transition">
                <i class="fa-solid fa-graduation-cap text-base sm:text-lg"></i> <span>れんしゅう</span>
            </button>
            <button onclick="switchTab('challenge')" id="tab-challenge" class="nav-tab bg-white border-2 border-rose-500 text-rose-600 hover:bg-rose-50 font-black py-2 px-1 rounded-2xl shadow-sm flex flex-col sm:flex-row items-center justify-center gap-1 text-xs sm:text-sm transition">
                <i class="fa-solid fa-stopwatch text-base sm:text-lg"></i> <span>タイムアタック</span>
            </button>
            <button onclick="switchTab('mushikui')" id="tab-mushikui" class="nav-tab bg-white border-2 border-purple-500 text-purple-600 hover:bg-purple-50 font-black py-2 px-1 rounded-2xl shadow-sm flex flex-col sm:flex-row items-center justify-center gap-1 text-xs sm:text-sm transition">
                <i class="fa-solid fa-puzzle-piece text-base sm:text-lg"></i> <span>むしくい</span>
            </button>
            <button onclick="switchTab('ai')" id="tab-ai" class="nav-tab bg-white border-2 border-amber-500 text-amber-600 hover:bg-amber-50 font-black py-2 px-1 rounded-2xl shadow-sm flex flex-col sm:flex-row items-center justify-center gap-1 text-xs sm:text-sm transition">
                <i class="fa-solid fa-wand-magic-sparkles text-base sm:text-lg"></i> <span>AIせんせい</span>
            </button>
            <button onclick="switchTab('rewards')" id="tab-rewards" class="nav-tab bg-white border-2 border-pink-500 text-pink-600 hover:bg-pink-50 font-black py-2 px-1 rounded-2xl shadow-sm flex flex-col sm:flex-row items-center justify-center gap-1 text-xs sm:text-sm transition">
                <i class="fa-solid fa-gift text-base sm:text-lg"></i> <span>ごほうび</span>
            </button>
        </nav>

        <!-- VIEW 1: KUKU TABLE VIEW -->
        <section id="view-table" class="view-panel space-y-4">
            <div class="bg-white rounded-3xl p-4 sm:p-6 border-2 border-slate-200 shadow-sm space-y-4">
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2">
                    <div>
                        <h2 class="text-xl font-black text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-table-cells text-blue-500"></i> 九九ひょう（タップしてみよう！）
                        </h2>
                        <p class="text-xs text-slate-500 font-bold">数字をタップすると、読みあげとドット図がみれるよ！</p>
                    </div>
                </div>

                <!-- Interactive Kuku Grid -->
                <div class="overflow-x-auto pb-2">
                    <table class="w-full min-w-[500px] border-collapse text-center select-none">
                        <thead>
                            <tr class="bg-slate-100 text-slate-600 font-black text-sm">
                                <th class="p-2 border border-slate-200 rounded-tl-xl">×</th>
                                <th class="p-2 border border-slate-200">1</th>
                                <th class="p-2 border border-slate-200">2</th>
                                <th class="p-2 border border-slate-200">3</th>
                                <th class="p-2 border border-slate-200">4</th>
                                <th class="p-2 border border-slate-200">5</th>
                                <th class="p-2 border border-slate-200">6</th>
                                <th class="p-2 border border-slate-200">7</th>
                                <th class="p-2 border border-slate-200">8</th>
                                <th class="p-2 border border-slate-200 rounded-tr-xl">9</th>
                            </tr>
                        </thead>
                        <tbody id="kukuTableBody">
                            <!-- Dynamically generated 9x9 table -->
                        </tbody>
                    </table>
                </div>

                <!-- Visual Dot Representation Card -->
                <div id="dotVisualizerCard" class="bg-amber-50 rounded-2xl p-4 border border-amber-200 hidden flex flex-col sm:flex-row items-center justify-between gap-4">
                    <div class="space-y-1 text-center sm:text-left">
                        <div class="text-xs font-bold text-amber-700">選択中のかけ算</div>
                        <div id="dotFormulaDisplay" class="text-2xl font-black text-slate-800">3 × 4 = 12</div>
                        <div id="dotReadingDisplay" class="text-sm font-bold text-amber-800">さざんがじゅうに</div>
                    </div>
                    <!-- Dot Grid -->
                    <div id="dotGridContainer" class="flex flex-col gap-1.5 p-3 bg-white rounded-xl border border-amber-300 shadow-inner">
                        <!-- Dynamic dot matrix -->
                    </div>
                    <button onclick="speakSelectedKuku()" class="pop-btn bg-amber-400 hover:bg-amber-500 text-slate-900 font-black text-xs py-2 px-4 rounded-xl shadow-sm flex items-center gap-1.5">
                        <i class="fa-solid fa-volume-high"></i> もういちど聞く
                    </button>
                </div>
            </div>
        </section>

        <!-- VIEW 2: PRACTICE QUIZ -->
        <section id="view-practice" class="view-panel hidden space-y-4">
            <div class="bg-white rounded-3xl p-5 border-2 border-slate-200 shadow-sm space-y-3">
                <h2 class="text-xl font-black text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-graduation-cap text-emerald-500"></i> れんしゅうする「段」をえらぼう
                </h2>
                <div class="grid grid-cols-3 sm:grid-cols-5 gap-2" id="practiceDanSelector">
                    <!-- Dynamic 1-9 & All Dan Buttons -->
                </div>
            </div>

            <!-- Quiz Card Box -->
            <div id="practiceQuizCard" class="bg-white rounded-3xl p-6 sm:p-8 border-4 border-emerald-300 shadow-md space-y-6 hidden">
                <div class="flex justify-between items-center text-sm font-bold text-slate-500">
                    <span id="practiceProgressText">もんだい 1 / 9</span>
                    <span id="practiceScoreText" class="text-emerald-600 font-black">せいかい: 0</span>
                </div>

                <div class="text-center py-4">
                    <div class="inline-block bg-emerald-50 border-2 border-emerald-200 rounded-3xl px-8 py-4 shadow-inner">
                        <span id="practiceQuestionText" class="text-5xl sm:text-6xl font-black text-slate-800 tracking-wider">
                            7 × 8 = ?
                        </span>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3 sm:gap-4 max-w-md mx-auto" id="practiceChoicesGrid">
                    <!-- Dynamic choices -->
                </div>

                <div id="practiceFeedback" class="text-center min-h-[40px] font-black text-lg flex items-center justify-center"></div>
            </div>
        </section>

        <!-- VIEW 3: TIME ATTACK CHALLENGE -->
        <section id="view-challenge" class="view-panel hidden space-y-4">
            <div class="bg-gradient-to-br from-rose-500 to-red-600 rounded-3xl p-6 text-white shadow-lg space-y-4">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-2xl font-black flex items-center gap-2">
                            <i class="fa-solid fa-stopwatch"></i> スピードタイムアタック
                        </h2>
                        <p class="text-xs text-rose-100 font-bold">全81問題からランダム10問題！さいそくを目指そう！</p>
                    </div>
                    <div class="bg-white/20 backdrop-blur-md px-4 py-2 rounded-2xl text-right">
                        <div class="text-[10px] uppercase font-bold text-rose-200">さいこうきろく</div>
                        <div id="challengeBestRecord" class="font-black text-lg text-yellow-300">-- 秒</div>
                    </div>
                </div>

                <div id="challengeStartArea" class="bg-white text-slate-800 rounded-2xl p-6 text-center space-y-4 shadow-sm">
                    <div class="w-16 h-16 rounded-full bg-rose-100 text-rose-500 flex items-center justify-center text-3xl mx-auto font-black">
                        ⏱️
                    </div>
                    <h3 class="text-xl font-black">準備はいいかな？</h3>
                    <p class="text-xs font-bold text-slate-500">スピードと正確さが試されるよ！</p>
                    <button onclick="startTimeAttack()" class="pop-btn bg-rose-500 hover:bg-rose-600 text-white font-black text-lg px-8 py-3 rounded-2xl shadow-lg border-b-4 border-rose-700 w-full sm:w-auto">
                        スタート！ <i class="fa-solid fa-play"></i>
                    </button>
                </div>
            </div>

            <div id="challengePlayArea" class="bg-white rounded-3xl p-6 sm:p-8 border-4 border-rose-400 shadow-md space-y-6 hidden">
                <div class="flex justify-between items-center font-black">
                    <div class="flex items-center gap-2">
                        <span class="text-xs text-slate-400">タイム</span>
                        <span id="challengeTimer" class="text-2xl font-mono text-rose-600">00.00</span>
                    </div>
                    <div class="text-sm text-slate-600">
                        のこり: <span id="challengeRemaining" class="text-rose-600 text-xl">10</span> 問
                    </div>
                </div>

                <div class="text-center py-4">
                    <span id="challengeQuestionText" class="text-5xl sm:text-6xl font-black text-slate-800 tracking-wider">
                        6 × 9 = ?
                    </span>
                </div>

                <div class="grid grid-cols-2 gap-3 max-w-md mx-auto" id="challengeChoicesGrid">
                    <!-- Dynamic choices -->
                </div>
            </div>
        </section>

        <!-- VIEW 4: MUSHIKUI QUIZ -->
        <section id="view-mushikui" class="view-panel hidden space-y-4">
            <div class="bg-white rounded-3xl p-6 border-2 border-slate-200 shadow-sm space-y-4">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-xl font-black text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-puzzle-piece text-purple-500"></i> むしくいクイズ（穴埋め）
                        </h2>
                        <p class="text-xs text-slate-500 font-bold">「？」にあてはまる正しい数字を考えよう！</p>
                    </div>
                    <button onclick="startMushikuiQuiz()" class="pop-btn bg-purple-500 hover:bg-purple-600 text-white font-black text-xs px-4 py-2 rounded-xl shadow-sm">
                        つぎの問題 <i class="fa-solid fa-rotate-right"></i>
                    </button>
                </div>

                <div class="bg-purple-50 rounded-2xl p-6 sm:p-10 border-2 border-purple-200 text-center space-y-6 shadow-inner">
                    <div id="mushikuiFormula" class="text-4xl sm:text-5xl font-black text-slate-800 tracking-wider">
                        ? × 4 = 28
                    </div>

                    <div class="grid grid-cols-3 gap-2 max-w-xs mx-auto">
                        <script>
                            for(let i=1; i<=9; i++) {
                                document.write(`
                                    <button onclick="submitMushikuiAnswer(${i})" class="pop-btn bg-white hover:bg-purple-100 border-2 border-purple-300 text-slate-800 font-black text-2xl py-3 rounded-2xl shadow-sm text-center">
                                        ${i}
                                    </button>
                                `);
                            }
                        </script>
                    </div>

                    <div id="mushikuiFeedback" class="min-h-[30px] font-black text-base"></div>
                </div>
            </div>
        </section>

        <!-- VIEW 5: AI SENSEI & STORY MODE -->
        <section id="view-ai" class="view-panel hidden space-y-6">
            <div class="bg-gradient-to-br from-amber-100 via-orange-50 to-yellow-100 rounded-3xl p-6 border-3 border-amber-300 shadow-sm space-y-4">
                <div class="flex items-center gap-4">
                    <div class="w-16 h-16 rounded-full bg-amber-400 text-slate-800 flex items-center justify-center text-3xl shadow-inner border-2 border-amber-200 shrink-0">
                        🦉
                    </div>
                    <div>
                        <h2 class="text-2xl font-black text-slate-800 flex items-center gap-2">
                            AIせんせい「くく丸」
                            <span class="text-xs bg-amber-200 text-amber-800 px-2 py-0.5 rounded-full font-bold">Gemini AI</span>
                        </h2>
                        <p class="text-slate-600 text-sm font-bold">九九のおぼえかたやコツをなんでもきいてね！</p>
                    </div>
                </div>

                <div class="flex flex-wrap gap-2 pt-2">
                    <button onclick="askAiQuick('7の段のおぼえかたのコツを教えて！')" class="bg-white hover:bg-amber-50 border border-amber-300 text-amber-800 font-bold text-xs py-1.5 px-3 rounded-full shadow-sm transition">
                        💡 7の段のおぼえかた
                    </button>
                    <button onclick="askAiQuick('九九っておぼえるとどんな良いことがあるの？')" class="bg-white hover:bg-amber-50 border border-amber-300 text-amber-800 font-bold text-xs py-1.5 px-3 rounded-full shadow-sm transition">
                        ❓ 九九をべんきょうする理由
                    </button>
                    <button onclick="askAiQuick('8×7のひみつや面白いおぼえかたを教えて！')" class="bg-white hover:bg-amber-50 border border-amber-300 text-amber-800 font-bold text-xs py-1.5 px-3 rounded-full shadow-sm transition">
                        ✨ 8×7のおもしろい覚え方
                    </button>
                </div>

                <div id="aiChatBox" class="bg-white/90 rounded-2xl p-4 border border-amber-200 min-h-[120px] max-h-[250px] overflow-y-auto space-y-3 text-slate-800 font-medium text-sm">
                    <div class="flex gap-2">
                        <span class="font-bold text-amber-600 shrink-0">くく丸:</span>
                        <div>こんにちは！九九のことで分からないことや、覚えにくい段があったら何でも聞いてね！「AI声で聞く」ボタンで声でもお話しするよ！</div>
                    </div>
                </div>

                <div class="flex items-center justify-between gap-2 pt-1">
                    <button id="aiTtsBtn" onclick="playAiSpeech()" disabled class="bg-slate-200 text-slate-400 font-bold text-xs px-3 py-1.5 rounded-xl flex items-center gap-1.5 transition">
                        <i class="fa-solid fa-volume-high"></i> AIのこえで聞く
                    </button>
                    <span id="aiLoadingIndicator" class="hidden text-amber-600 font-bold text-xs animate-pulse">
                        <i class="fa-solid fa-spinner fa-spin"></i> くく丸が考え中...
                    </span>
                </div>

                <div class="flex gap-2">
                    <input type="text" id="aiQueryInput" placeholder="例: 6の段のおぼえかたを教えて！" class="flex-1 bg-white border-2 border-amber-300 rounded-2xl px-4 py-2 text-sm focus:outline-none focus:border-amber-500 font-bold">
                    <button onclick="sendAiMessage()" class="pop-btn bg-amber-500 hover:bg-amber-600 text-white font-black px-5 py-2 rounded-2xl shadow-sm flex items-center gap-1">
                        きく <i class="fa-solid fa-paper-plane"></i>
                    </button>
                </div>
            </div>

            <!-- Story Word Problems -->
            <div class="bg-white rounded-3xl p-6 border-2 border-slate-200 shadow-sm space-y-4">
                <div class="flex justify-between items-center">
                    <div>
                        <h3 class="text-xl font-black text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-book-open text-amber-500"></i> AIつくり話・ぶんしょうだい
                        </h3>
                        <p class="text-slate-500 text-xs font-bold">AIがたのしい物語の九九問題をつくるよ！</p>
                    </div>
                    <button onclick="generateAiStoryProblem()" class="pop-btn bg-amber-400 hover:bg-amber-500 text-slate-900 font-black text-sm px-4 py-2 rounded-2xl shadow-sm flex items-center gap-1">
                        <i class="fa-solid fa-dice"></i> あたらしい問題をつくる
                    </button>
                </div>

                <div id="aiStoryContainer" class="bg-amber-50/60 rounded-2xl p-5 border border-amber-200 space-y-4">
                    <p class="text-slate-500 text-sm font-bold text-center py-4">「あたらしい問題をつくる」ボタンを押してね！</p>
                </div>
            </div>
        </section>

        <!-- VIEW 6: AI REWARDS & STICKER GENERATOR -->
        <section id="view-rewards" class="view-panel hidden space-y-6">
            <div class="bg-gradient-to-r from-pink-400 to-rose-400 rounded-3xl p-6 text-white shadow-lg space-y-3 relative overflow-hidden">
                <div class="relative z-10 flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
                    <div>
                        <h2 class="text-2xl md:text-3xl font-black">AIごほうびステッカーアルバム</h2>
                        <p class="text-pink-100 font-bold text-sm">たまった ⭐️ (ほし) を使って、AIに自分だけのステッカーを描いてもらおう！</p>
                    </div>
                    <div class="bg-white/20 backdrop-blur-md px-4 py-2 rounded-2xl border border-white/40 flex items-center gap-2 font-black text-xl">
                        <i class="fa-solid fa-star text-yellow-300"></i>
                        <span id="rewardStarsCount">0</span>
                    </div>
                </div>
            </div>

            <div class="bg-white rounded-3xl p-6 border-2 border-slate-200 shadow-sm space-y-4">
                <h3 class="text-lg font-black text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-paint-brush text-pink-500"></i> 新しいステッカーをつくる (⭐️ 3つ使用)
                </h3>
                <div class="flex flex-col sm:flex-row gap-3">
                    <input type="text" id="stickerPromptInput" placeholder="例: 宇宙をとぶ虹色のねこ, 王冠をかぶったドラゴン" class="flex-1 border-2 border-pink-200 rounded-2xl px-4 py-2.5 text-sm font-bold focus:outline-none focus:border-pink-400">
                    <button onclick="generateAiSticker()" class="pop-btn bg-pink-500 hover:bg-pink-600 text-white font-black px-6 py-2.5 rounded-2xl shadow-[0_4px_0_#be185d] active:translate-y-1 flex items-center justify-center gap-2">
                        <i class="fa-solid fa-wand-magic-sparkles"></i> ステッカーをつくる！
                    </button>
                </div>
                <div id="stickerGenStatus" class="hidden text-pink-600 text-xs font-bold flex items-center gap-2 animate-pulse">
                    <i class="fa-solid fa-spinner fa-spin"></i> AIがおえかき中... しばらくまってね！
                </div>
            </div>

            <div class="bg-white rounded-3xl p-6 border-2 border-slate-200 shadow-sm space-y-4">
                <h3 class="text-lg font-black text-slate-800">あつめたステッカー</h3>
                <div id="stickerAlbumGrid" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4">
                    <!-- Dynamic images -->
                </div>
            </div>
        </section>
    </main>

    <!-- API KEY SETTINGS MODAL -->
    <div id="apiKeyModal" class="fixed inset-0 bg-black/50 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl p-6 max-w-md w-full shadow-2xl space-y-4 border-4 border-amber-300">
            <div class="flex justify-between items-center">
                <h3 class="text-lg font-black text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-key text-amber-500"></i> Gemini APIキーの設定
                </h3>
                <button onclick="closeApiKeyModal()" class="text-slate-400 hover:text-slate-600 font-bold text-xl">&times;</button>
            </div>
            <p class="text-xs text-slate-600 font-bold leading-relaxed">
                GitHub Pages等のWeb公開環境でAI機能（質問・文章題作成・ステッカー生成）を利用するには、ご自身のGoogle Gemini APIキーを入力してください。（ブラウザ内に安全に保存されます）
            </p>
            <input type="password" id="userApiKeyInput" placeholder="AIzaSy..." class="w-full border-2 border-amber-300 rounded-xl px-3 py-2 text-sm font-mono focus:outline-none focus:border-amber-500">
            <div class="flex justify-end gap-2">
                <button onclick="saveApiKey()" class="bg-amber-500 hover:bg-amber-600 text-white font-black py-2 px-5 rounded-xl shadow-md text-sm transition">
                    保存する
                </button>
            </div>
        </div>
    </div>

    <footer class="bg-white border-t border-slate-200 py-4 text-center text-xs font-bold text-slate-400">
        九九マスター！ かけざん学習Webアプリ &copy; 2026
    </footer>

    <script>
        let state = {
            stars: 0,
            clearedDan: [],
            totalCorrect: 0,
            bestTime: null
        };

        let isMuted = false;
        let selectedKuku = { a: 3, b: 4 };

        const kukuReadings = {
            "1x1": "いんいちがいち", "1x2": "いんにがに", "1x3": "いんさんがさん", "1x4": "いんしがし", "1x5": "いんごがご", "1x6": "いんろくがろく", "1x7": "いんしちにしち", "1x8": "いんはちがはち", "1x9": "いんくがく",
            "2x1": "にいちがに", "2x2": "ににんがし", "2x3": "にかさんろく", "2x4": "にしがはち", "2x5": "にごじゅう", "2x6": "にはちじゅうに", "2x7": "にしちじゅうし", "2x8": "にはちじゅうろく", "2x9": "にくじゅうはち",
            "3x1": "さざんがいち", "3x2": "さざんがろく", "3x3": "さざんがく", "3x4": "さざんがじゅうに", "3x5": "さんごじゅうご", "3x6": "サブろくじゅうはち", "3x7": "さんしちにじゅういち", "3x8": "さんぱにじゅうし", "3x9": "さんくにじゅうなな",
            "4x1": "しいちがし", "4x2": "しにごはち", "4x3": "しさんじゅうに", "4x4": "ししじゅうろく", "4x5": "しごにじゅう", "4x6": "しろくにじゅうし", "4x7": "ししちにじゅうはち", "4x8": "しはちさんじゅうに", "4x9": "しくさんじゅうろく",
            "5x1": "ごいちがご", "5x2": "ごにじゅう", "5x3": "ごさんじゅうご", "5x4": "ごしにじゅう", "5x5": "ごごにじゅうご", "5x6": "ごろくさんじゅう", "5x7": "ごしちさんじゅうご", "5x8": "ごはちしじゅう", "5x9": "ごくしじゅうご",
            "6x1": "ろくいちがろく", "6x2": "ろくにじゅうに", "6x3": "ろくさんじゅうはち", "6x4": "ろくしにじゅうし", "6x5": "ろくごさんじゅう", "6x6": "ろくろくさんじゅうろく", "6x7": "ろくしちしじゅうに", "6x8": "ろくはちしじゅうはち", "6x9": "ろくごじゅうし",
            "7x1": "しちいちがしち", "7x2": "しちにじゅうし", "7x3": "しちさんにじゅういち", "7x4": "しちしにじゅうはち", "7x5": "しちごさんじゅうご", "7x6": "しちろくしじゅうに", "7x7": "しちしちしじゅうく", "7x8": "しちはちごじゅうろく", "7x9": "しちくろくじゅうさん",
            "8x1": "はちいちがはち", "8x2": "はちにじゅうろく", "8x3": "はちちにじゅうし", "8x4": "はちしさんじゅうに", "8x5": "はちごしじゅう", "8x6": "はちろくしじゅうはち", "8x7": "はちしちごじゅうろく", "8x8": "はっぱろくじゅうし", "8x9": "はちくしちじゅうに",
            "9x1": "くいちがく", "9x2": "くにじゅうはち", "9x3": "くさんにじゅうなな", "9x4": "くしさんじゅうろく", "9x5": "くごしじゅうご", "9x6": "くろくごじゅうし", "9x7": "くしちろくじゅうさん", "9x8": "くはちしじゅうに", "9x9": "くくはちじゅういち"
        };

        function switchTab(tabId) {
            document.querySelectorAll('.view-panel').forEach(panel => panel.classList.add('hidden'));
            document.querySelectorAll('.nav-tab').forEach(tab => {
                tab.classList.remove('ring-4', 'ring-amber-300', 'ring-offset-1', 'scale-105');
            });

            const targetView = document.getElementById(`view-${tabId}`);
            if (targetView) targetView.classList.remove('hidden');

            const targetTab = document.getElementById(`tab-${tabId}`);
            if (targetTab) targetTab.classList.add('ring-4', 'ring-amber-300', 'ring-offset-1', 'scale-105');

            if (tabId === 'rewards') renderAlbum();
            if (tabId === 'mushikui') startMushikuiQuiz();
        }

        function toggleMute() {
            isMuted = !isMuted;
            const icon = document.getElementById('soundIcon');
            icon.className = isMuted ? "fa-solid fa-volume-xmark text-rose-500" : "fa-solid fa-volume-high";
        }

        function playSound(type) {
            if (isMuted) return;
            try {
                const ctx = new (window.AudioContext || window.webkitAudioContext)();
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                osc.connect(gain);
                gain.connect(ctx.destination);

                if (type === 'correct' || type === 'fanfare') {
                    osc.frequency.setValueAtTime(523.25, ctx.currentTime);
                    osc.frequency.setValueAtTime(659.25, ctx.currentTime + 0.1);
                    gain.gain.setValueAtTime(0.2, ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.3);
                    osc.start();
                    osc.stop(ctx.currentTime + 0.3);
                } else if (type === 'wrong') {
                    osc.frequency.setValueAtTime(220, ctx.currentTime);
                    gain.gain.setValueAtTime(0.2, ctx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.2);
                    osc.start();
                    osc.stop(ctx.currentTime + 0.2);
                }
            } catch (e) {}
        }

        function speakText(text) {
            if (isMuted || !('speechSynthesis' in window)) return;
            window.speechSynthesis.cancel();
            const synth = new SpeechSynthesisUtterance(text);
            synth.lang = 'ja-JP';
            synth.rate = 1.0;
            window.speechSynthesis.speak(synth);
        }

        function initKukuTable() {
            const tbody = document.getElementById('kukuTableBody');
            if(!tbody) return;
            tbody.innerHTML = '';

            for (let r = 1; r <= 9; r++) {
                const tr = document.createElement('tr');
                tr.innerHTML = `<td class="p-2 font-black bg-slate-100 text-slate-700 border border-slate-200">${r}の段</td>`;
                for (let c = 1; c <= 9; c++) {
                    const ans = r * c;
                    const td = document.createElement('td');
                    td.className = "p-2 border border-slate-200 hover:bg-amber-100 cursor-pointer transition font-black text-sm text-slate-700 active:scale-95";
                    td.innerText = ans;
                    td.onclick = () => selectKukuCell(r, c);
                    tr.appendChild(td);
                }
                tbody.appendChild(tr);
            }
        }

        function selectKukuCell(r, c) {
            selectedKuku = { a: r, b: c };
            const card = document.getElementById('dotVisualizerCard');
            const formula = document.getElementById('dotFormulaDisplay');
            const reading = document.getElementById('dotReadingDisplay');
            const grid = document.getElementById('dotGridContainer');

            card.classList.remove('hidden');
            formula.innerText = `${r} × ${c} = ${r * c}`;
            const key = `${r}x${c}`;
            const textReading = kukuReadings[key] || `${r} かける ${c} は ${r * c}`;
            reading.innerText = textReading;

            grid.innerHTML = '';
            for (let i = 0; i < r; i++) {
                const rowDiv = document.createElement('div');
                rowDiv.className = 'flex gap-1.5';
                for (let j = 0; j < c; j++) {
                    const dot = document.createElement('div');
                    dot.className = 'w-3 h-3 rounded-full bg-amber-400 border border-amber-500 shadow-sm';
                    rowDiv.appendChild(dot);
                }
                grid.appendChild(rowDiv);
            }

            speakSelectedKuku();
        }

        function speakSelectedKuku() {
            const key = `${selectedKuku.a}x${selectedKuku.b}`;
            const textReading = kukuReadings[key] || `${selectedKuku.a} かける ${selectedKuku.b} は ${selectedKuku.a * selectedKuku.b}`;
            speakText(textReading);
        }

        let currentPracticeDan = 1;
        let practiceQuestions = [];
        let practiceIndex = 0;
        let practiceScore = 0;

        function initPracticeSelector() {
            const container = document.getElementById('practiceDanSelector');
            if(!container) return;
            let html = '';
            for(let i=1; i<=9; i++) {
                html += `
                    <button onclick="startPracticeMode(${i})" class="pop-btn bg-white hover:bg-emerald-50 border-2 border-emerald-300 text-emerald-800 font-black py-3 rounded-2xl shadow-sm text-center flex flex-col items-center justify-center gap-1">
                        <span class="text-base">${i}の段</span>
                        <span id="star-dan-${i}" class="text-xs text-amber-400"><i class="fa-regular fa-star"></i></span>
                    </button>
                `;
            }
            html += `
                <button onclick="startPracticeMode('all')" class="pop-btn bg-emerald-500 hover:bg-emerald-600 border-2 border-emerald-600 text-white font-black py-3 rounded-2xl shadow-sm text-center col-span-3 sm:col-span-1 flex flex-col items-center justify-center">
                    <span class="text-base">ぜんぶ！</span>
                    <span class="text-[10px] text-emerald-100">ランダム</span>
                </button>
            `;
            container.innerHTML = html;
        }

        function startPracticeMode(dan) {
            currentPracticeDan = dan;
            practiceQuestions = [];
            practiceIndex = 0;
            practiceScore = 0;

            if (dan === 'all') {
                for (let i = 0; i < 9; i++) {
                    const a = Math.floor(Math.random() * 9) + 1;
                    const b = Math.floor(Math.random() * 9) + 1;
                    practiceQuestions.push({ a, b, ans: a * b });
                }
            } else {
                for (let b = 1; b <= 9; b++) {
                    practiceQuestions.push({ a: dan, b, ans: dan * b });
                }
                practiceQuestions.sort(() => Math.random() - 0.5);
            }

            document.getElementById('practiceQuizCard').classList.remove('hidden');
            renderPracticeQuestion();
        }

        function renderPracticeQuestion() {
            if (practiceIndex >= practiceQuestions.length) {
                finishPracticeMode();
                return;
            }
            const q = practiceQuestions[practiceIndex];
            document.getElementById('practiceProgressText').innerText = `もんだい ${practiceIndex + 1} / ${practiceQuestions.length}`;
            document.getElementById('practiceScoreText').innerText = `せいかい: ${practiceScore}`;
            document.getElementById('practiceQuestionText').innerText = `${q.a} × ${q.b} = ?`;
            document.getElementById('practiceFeedback').innerText = '';

            const choices = new Set([q.ans]);
            while (choices.size < 4) {
                const fake = (Math.floor(Math.random() * 9) + 1) * (Math.floor(Math.random() * 9) + 1);
                choices.add(fake);
            }
            const choicesArr = Array.from(choices).sort(() => Math.random() - 0.5);

            const grid = document.getElementById('practiceChoicesGrid');
            grid.innerHTML = choicesArr.map(c => `
                <button onclick="checkPracticeAnswer(${c}, ${q.ans})" class="pop-btn bg-white hover:bg-emerald-50 border-2 border-emerald-300 text-slate-800 font-black text-2xl py-4 rounded-2xl shadow-sm">
                    ${c}
                </button>
            `).join('');
        }

        function checkPracticeAnswer(selected, correct) {
            const fb = document.getElementById('practiceFeedback');
            if (selected === correct) {
                playSound('correct');
                practiceScore++;
                state.stars += 1;
                state.totalCorrect += 1;
                fb.className = "text-emerald-600 font-black";
                fb.innerText = "⭕️ だいせいかい！";
            } else {
                playSound('wrong');
                fb.className = "text-rose-500 font-black";
                fb.innerText = `❌ おしい！ こたえは ${correct}`;
            }
            saveProgress();
            practiceIndex++;
            setTimeout(renderPracticeQuestion, 800);
        }

        function finishPracticeMode() {
            if (typeof currentPracticeDan === 'number' && !state.clearedDan.includes(currentPracticeDan)) {
                state.clearedDan.push(currentPracticeDan);
            }
            saveProgress();

            if (typeof confetti === 'function') confetti({ particleCount: 80, spread: 70, origin: { y: 0.6 } });

            showModal("🎉 おめでとう！", `れんしゅうクリア！ ${practiceQuestions.length}問ちゅう ${practiceScore}問せいかい！`, "⭐️ ほしをゲットしたよ！");
            document.getElementById('practiceQuizCard').classList.add('hidden');
        }

        let challengeTimerInterval = null;
        let challengeStartTime = 0;
        let challengeIndex = 0;
        let challengeQuestions = [];

        function startTimeAttack() {
            document.getElementById('challengeStartArea').classList.add('hidden');
            document.getElementById('challengePlayArea').classList.remove('hidden');

            challengeQuestions = [];
            for(let i=0; i<10; i++) {
                const a = Math.floor(Math.random() * 9) + 1;
                const b = Math.floor(Math.random() * 9) + 1;
                challengeQuestions.push({ a, b, ans: a * b });
            }

            challengeIndex = 0;
            challengeStartTime = Date.now();
            challengeTimerInterval = setInterval(() => {
                const elapsed = (Date.now() - challengeStartTime) / 1000;
                document.getElementById('challengeTimer').innerText = elapsed.toFixed(2);
            }, 30);

            renderChallengeQuestion();
        }

        function renderChallengeQuestion() {
            if (challengeIndex >= 10) {
                finishTimeAttack();
                return;
            }
            const q = challengeQuestions[challengeIndex];
            document.getElementById('challengeRemaining').innerText = 10 - challengeIndex;
            document.getElementById('challengeQuestionText').innerText = `${q.a} × ${q.b} = ?`;

            const choices = new Set([q.ans]);
            while (choices.size < 4) {
                const fake = (Math.floor(Math.random() * 9) + 1) * (Math.floor(Math.random() * 9) + 1);
                choices.add(fake);
            }
            const choicesArr = Array.from(choices).sort(() => Math.random() - 0.5);

            const grid = document.getElementById('challengeChoicesGrid');
            grid.innerHTML = choicesArr.map(c => `
                <button onclick="checkChallengeAnswer(${c}, ${q.ans})" class="pop-btn bg-white hover:bg-rose-50 border-2 border-rose-300 text-slate-800 font-black text-2xl py-4 rounded-2xl shadow-sm">
                    ${c}
                </button>
            `).join('');
        }

        function checkChallengeAnswer(selected, correct) {
            if (selected === correct) {
                playSound('correct');
            } else {
                playSound('wrong');
                challengeStartTime -= 2000;
            }
            challengeIndex++;
            renderChallengeQuestion();
        }

        function finishTimeAttack() {
            clearInterval(challengeTimerInterval);
            const totalTime = parseFloat(((Date.now() - challengeStartTime) / 1000).toFixed(2));
            
            if(!state.bestTime || totalTime < state.bestTime) {
                state.bestTime = totalTime;
            }
            state.stars += 3;
            saveProgress();

            if (typeof confetti === 'function') confetti({ particleCount: 100, spread: 80, origin: { y: 0.6 } });

            showModal("⚡️ タイムアタックかんりょう！", `きろく: ${totalTime} 秒！`, "⭐️ ほしを 3つゲットしたよ！");

            document.getElementById('challengePlayArea').classList.add('hidden');
            document.getElementById('challengeStartArea').classList.remove('hidden');
        }

        let currentMushikuiAns = 0;

        function startMushikuiQuiz() {
            const a = Math.floor(Math.random() * 9) + 1;
            const b = Math.floor(Math.random() * 9) + 1;
            const ans = a * b;
            const missingType = Math.floor(Math.random() * 2);

            if (missingType === 0) {
                document.getElementById('mushikuiFormula').innerText = `? × ${b} = ${ans}`;
                currentMushikuiAns = a;
            } else {
                document.getElementById('mushikuiFormula').innerText = `${a} × ? = ${ans}`;
                currentMushikuiAns = b;
            }
            document.getElementById('mushikuiFeedback').innerText = '';
        }

        function submitMushikuiAnswer(val) {
            const fb = document.getElementById('mushikuiFeedback');
            if (val === currentMushikuiAns) {
                playSound('correct');
                state.stars += 1;
                saveProgress();
                fb.className = "text-purple-600 font-black";
                fb.innerText = "🎉 せいかい！つぎの問題にいくよ！";
                setTimeout(startMushikuiQuiz, 800);
            } else {
                playSound('wrong');
                fb.className = "text-rose-500 font-black";
                fb.innerText = "❌ もういちど考えてみよう！";
            }
        }

        function showModal(title, message, subtext = "") {
            let modal = document.getElementById('appCustomModal');
            if (!modal) {
                modal = document.createElement('div');
                modal.id = 'appCustomModal';
                modal.className = 'fixed inset-0 bg-black/50 backdrop-blur-sm z-50 flex items-center justify-center p-4';
                modal.innerHTML = `
                    <div class="bg-white rounded-3xl p-6 max-w-sm w-full shadow-2xl space-y-4 text-center border-4 border-amber-300 animate-bounce-subtle">
                        <h4 id="customModalTitle" class="text-xl font-black text-slate-800"></h4>
                        <p id="customModalMsg" class="text-sm font-bold text-slate-600"></p>
                        <p id="customModalSub" class="text-xs text-amber-700 font-bold bg-amber-50 p-2 rounded-xl"></p>
                        <button onclick="document.getElementById('appCustomModal').classList.add('hidden')" class="bg-amber-500 hover:bg-amber-600 text-white font-black py-2 px-6 rounded-2xl shadow-md transition">OK</button>
                    </div>
                `;
                document.body.appendChild(modal);
            }
            document.getElementById('customModalTitle').innerText = title;
            document.getElementById('customModalMsg').innerText = message;
            document.getElementById('customModalSub').innerText = subtext;
            modal.classList.remove('hidden');
        }

        let lastAiTextResponse = "";
        let collectedStickers = [];

        function getStoredApiKey() {
            return localStorage.getItem('kuku_gemini_api_key') || "";
        }

        function openApiKeyModal() {
            document.getElementById('userApiKeyInput').value = getStoredApiKey();
            document.getElementById('apiKeyModal').classList.remove('hidden');
        }

        function closeApiKeyModal() {
            document.getElementById('apiKeyModal').classList.add('hidden');
        }

        function saveApiKey() {
            const key = document.getElementById('userApiKeyInput').value.trim();
            localStorage.setItem('kuku_gemini_api_key', key);
            closeApiKeyModal();
            showModal("🔑 APIキー保存完了", "Gemini APIキーを保存しました！", "これでAI機能をお使いいただけます。");
        }

        function loadProgress() {
            const saved = localStorage.getItem('kuku_app_data');
            if(saved) {
                try {
                    const parsed = JSON.parse(saved);
                    state.stars = parsed.stars || 0;
                    state.clearedDan = parsed.clearedDan || [];
                    state.totalCorrect = parsed.totalCorrect || 0;
                    state.bestTime = parsed.bestTime || null;
                    collectedStickers = parsed.collectedStickers || [];
                } catch(e){}
            }
            updateDashboardUI();
        }

        function saveProgress() {
            localStorage.setItem('kuku_app_data', JSON.stringify({
                stars: state.stars,
                clearedDan: state.clearedDan,
                totalCorrect: state.totalCorrect,
                bestTime: state.bestTime,
                collectedStickers: collectedStickers
            }));
            updateDashboardUI();
        }

        function updateDashboardUI() {
            const setElemText = (id, text) => {
                const el = document.getElementById(id);
                if (el) el.innerText = text;
            };

            setElemText('totalStars', state.stars);
            setElemText('rewardStarsCount', state.stars);
            setElemText('clearedStageCount', `${state.clearedDan.length} / 9 段`);
            setElemText('challengeBestRecord', state.bestTime ? `${state.bestTime} 秒` : '-- 秒');

            for(let i=1; i<=9; i++) {
                const el = document.getElementById(`star-dan-${i}`);
                if(el) {
                    if(state.clearedDan.includes(i)) {
                        el.innerHTML = `<i class="fa-solid fa-star text-amber-400"></i>`;
                    } else {
                        el.innerHTML = `<i class="fa-regular fa-star text-slate-300"></i>`;
                    }
                }
            }
        }

        async function fetchGeminiText(prompt, systemInstruction = "") {
            const apiKey = getStoredApiKey();
            if(!apiKey) {
                openApiKeyModal();
                throw new Error("APIキーが未設定です");
            }

            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ parts: [{ text: prompt }] }],
            };
            if(systemInstruction) {
                payload.systemInstruction = { parts: [{ text: systemInstruction }] };
            }

            let retries = 3;
            let delay = 1000;
            for(let i = 0; i < retries; i++) {
                try {
                    const res = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    if(!res.ok) throw new Error(`HTTP error! status: ${res.status}`);
                    const data = await res.json();
                    return data.candidates?.[0]?.content?.parts?.[0]?.text || "ごめんなさい、うまく答えられなかったよ。もう一度きいてね！";
                } catch(err) {
                    if(i === retries - 1) throw err;
                    await new Promise(r => setTimeout(r, delay));
                    delay *= 2;
                }
            }
        }

        function askAiQuick(query) {
            document.getElementById('aiQueryInput').value = query;
            sendAiMessage();
        }

        async function sendAiMessage() {
            const input = document.getElementById('aiQueryInput');
            const query = input.value.trim();
            if(!query) return;

            const chatBox = document.getElementById('aiChatBox');
            const loading = document.getElementById('aiLoadingIndicator');
            const ttsBtn = document.getElementById('aiTtsBtn');

            chatBox.innerHTML += `
                <div class="flex gap-2 justify-end">
                    <div class="bg-amber-200 text-slate-800 rounded-2xl px-3 py-1.5 text-xs font-bold max-w-[80%]">${query}</div>
                </div>
            `;
            input.value = "";
            chatBox.scrollTop = chatBox.scrollHeight;

            loading.classList.remove('hidden');
            ttsBtn.disabled = true;

            const sysInstruction = "You are 'Kuku-maru' (くく丸), a friendly owl math coach for Japanese elementary school children learning multiplication tables (九九). Answer concisely, enthusiastically, and easily in friendly Japanese with emojis. Keep responses short (2-4 sentences max).";

            try {
                const answer = await fetchGeminiText(query, sysInstruction);
                lastAiTextResponse = answer;
                chatBox.innerHTML += `
                    <div class="flex gap-2">
                        <span class="font-bold text-amber-600 shrink-0">くく丸:</span>
                        <div>${answer}</div>
                    </div>
                `;
                chatBox.scrollTop = chatBox.scrollHeight;
                ttsBtn.disabled = false;
                ttsBtn.className = "bg-amber-500 hover:bg-amber-600 text-white font-bold text-xs px-3 py-1.5 rounded-xl flex items-center gap-1.5 shadow-sm cursor-pointer";
            } catch(e) {
                if(e.message !== "APIキーが未設定です") {
                    chatBox.innerHTML += `<div class="text-rose-500 text-xs font-bold">APIキーの設定を確認してね！</div>`;
                }
            } finally {
                loading.classList.add('hidden');
            }
        }

        async function playAiSpeech() {
            if(!lastAiTextResponse) return;
            speakText(lastAiTextResponse);
        }

        async function generateAiStoryProblem() {
            const container = document.getElementById('aiStoryContainer');
            const apiKey = getStoredApiKey();
            if(!apiKey) {
                openApiKeyModal();
                return;
            }

            container.innerHTML = `<div class="text-center font-bold text-amber-600 py-4"><i class="fa-solid fa-spinner fa-spin"></i> AIがたのしい物語を作っています...</div>`;

            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ parts: [{ text: "小学生向けの九九（掛け算）の文章題を1つ作成してください。" }] }],
                generationConfig: {
                    responseMimeType: "application/json",
                    responseSchema: {
                        type: "OBJECT",
                        properties: {
                            story: { type: "STRING" },
                            question: { type: "STRING" },
                            answer: { type: "INTEGER" },
                            choices: { type: "ARRAY", items: { type: "INTEGER" } },
                            explanation: { type: "STRING" }
                        },
                        required: ["story", "question", "answer", "choices", "explanation"]
                    }
                }
            };

            try {
                const res = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                const data = await res.json();
                const jsonText = data?.candidates?.[0]?.content?.parts?.[0]?.text;
                if(jsonText) {
                    const prob = JSON.parse(jsonText);
                    container.innerHTML = `
                        <div class="space-y-3">
                            <div class="text-base font-bold text-slate-800 leading-relaxed bg-white p-4 rounded-xl border border-amber-200">
                                📖 ${prob.story}<br><br>
                                ❓ <span class="text-amber-700">${prob.question}</span>
                            </div>
                            <div class="grid grid-cols-2 gap-2" id="aiStoryChoices">
                                ${prob.choices.map(c => `
                                    <button onclick="checkAiStoryAnswer(${c}, ${prob.answer}, '${prob.explanation}')" class="pop-btn bg-white hover:bg-amber-100 border-2 border-amber-300 text-slate-800 font-black text-xl py-3 rounded-xl shadow-sm">
                                        ${c}
                                    </button>
                                `).join('')}
                            </div>
                            <div id="aiStoryResult" class="hidden text-sm font-bold p-3 rounded-xl"></div>
                        </div>
                    `;
                }
            } catch(e) {
                container.innerHTML = `<div class="text-rose-500 text-xs font-bold text-center">エラーが発生しました。APIキーを確認して試してね！</div>`;
            }
        }

        function checkAiStoryAnswer(selected, correct, explanation) {
            const resBox = document.getElementById('aiStoryResult');
            resBox.classList.remove('hidden');
            if(selected === correct) {
                playSound('correct');
                state.stars += 2;
                saveProgress();
                resBox.className = "bg-emerald-100 text-emerald-800 p-3 rounded-xl font-bold border border-emerald-300";
                resBox.innerHTML = `🎉 せいかい！ (⭐️ ほしを 2つゲット！)<br><span class="text-xs font-medium">${explanation}</span>`;
            } else {
                playSound('wrong');
                resBox.className = "bg-rose-100 text-rose-800 p-3 rounded-xl font-bold border border-rose-300";
                resBox.innerHTML = `❌ ざんねん！もういちど考えてみよう！`;
            }
        }

        async function generateAiSticker() {
            if(state.stars < 3) {
                showModal("⭐️ ほしがたりないよ", "ステッカーを作るには ほしが 3つ 必要だよ！", "練習やタイムアタックで ほしをあつめよう！");
                return;
            }

            const apiKey = getStoredApiKey();
            if(!apiKey) {
                openApiKeyModal();
                return;
            }

            const input = document.getElementById('stickerPromptInput');
            const prompt = input.value.trim() || "かわいい動物の九九マスターバッジ";
            const status = document.getElementById('stickerGenStatus');

            status.classList.remove('hidden');

            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-lite-image:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ parts: [{ text: `Cute sticker badge for kids learning math: ${prompt}, colorful, vector style, white background` }] }],
                generationConfig: { responseModalities: ['TEXT', 'IMAGE'] }
            };

            try {
                const res = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                const data = await res.json();
                const base64Data = data?.candidates?.[0]?.content?.parts?.find(p => p.inlineData)?.inlineData?.data;

                if(base64Data) {
                    const imgUrl = `data:image/png;base64,${base64Data}`;
                    state.stars -= 3;
                    collectedStickers.unshift({ url: imgUrl, title: prompt, date: new Date().toLocaleDateString() });
                    saveProgress();
                    renderAlbum();
                    playSound('fanfare');
                    input.value = "";
                }
            } catch(e) {
                showModal("生成エラー", "ステッカーの生成に失敗しました。", "APIキーを確認してください。");
            } finally {
                status.classList.add('hidden');
            }
        }

        function renderAlbum() {
            const grid = document.getElementById('stickerAlbumGrid');
            if(!grid) return;

            if(collectedStickers.length === 0) {
                grid.innerHTML = `<div class="col-span-full text-center text-slate-400 font-bold py-6 text-sm">まだステッカーがありません。ほしをあつめて作ろう！</div>`;
                return;
            }

            grid.innerHTML = collectedStickers.map(st => `
                <div class="bg-pink-50 rounded-2xl p-2 border border-pink-200 flex flex-col items-center gap-1 shadow-sm">
                    <img src="${st.url}" class="w-full h-28 object-contain rounded-xl bg-white border border-pink-100">
                    <span class="text-xs font-bold text-slate-700 truncate w-full text-center">${st.title}</span>
                </div>
            `).join('');
        }

        window.addEventListener('DOMContentLoaded', () => {
            initKukuTable();
            initPracticeSelector();
            loadProgress();
            switchTab('table');
            selectKukuCell(3, 4);
        });
    </script>
</body>
</html>
