
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Phẩm Hạnh - Phần Mềm Ghi Điểm</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.8.0/firebase-database-compat.js"></script>
    <script>tailwind.config = { darkMode: 'class' }</script>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap');

        :root {
            --bg-base: #0f172a;
            --primary-gradient: linear-gradient(135deg, #6366f1, #a855f7);
            --orb-1-color: rgba(99, 102, 241, 0.25);
            --orb-2-color: rgba(168, 85, 247, 0.25);
            --border-glow: #a855f7;
            --text-accent: #a5b4fc;
            --text-primary: #ffffff;
            --text-secondary: rgba(199, 210, 254, 0.7);
            --glass-bg: rgba(255, 255, 255, 0.05);
            --glass-border: rgba(255, 255, 255, 0.1);
            --glass-input-bg: rgba(15, 23, 42, 0.35);
        }

        body.theme-emerald {
            --primary-gradient: linear-gradient(135deg, #10b981, #06b6d4);
            --orb-1-color: rgba(16, 185, 129, 0.25);
            --orb-2-color: rgba(6, 182, 212, 0.25);
            --border-glow: #34d399;
            --text-accent: #6ee7b7;
            --text-primary: #ffffff;
            --text-secondary: rgba(110, 231, 183, 0.7);
        }

        body.theme-ruby {
            --primary-gradient: linear-gradient(135deg, #f43f5e, #fb7185);
            --orb-1-color: rgba(244, 63, 94, 0.25);
            --orb-2-color: rgba(251, 113, 133, 0.25);
            --border-glow: #fda4af;
            --text-accent: #fecdd3;
            --text-primary: #ffffff;
            --text-secondary: rgba(254, 205, 211, 0.7);
        }

        body.theme-light {
            --bg-base: #f1f5f9;
            --primary-gradient: linear-gradient(135deg, #6366f1, #0ea5e9);
            --orb-1-color: rgba(99, 102, 241, 0.15);
            --orb-2-color: rgba(14, 165, 233, 0.15);
            --border-glow: #6366f1;
            --text-accent: #4f46e5;
            --text-primary: #0f172a;
            --text-secondary: #475569;
            --glass-bg: rgba(255, 255, 255, 0.5);
            --glass-border: rgba(0, 0, 0, 0.08);
            --glass-input-bg: rgba(255, 255, 255, 0.7);
        }

        body.theme-floral {
            --bg-base: #064e3b; 
            --primary-gradient: linear-gradient(135deg, #34d399, #84cc16);
            --orb-1-color: rgba(52, 211, 153, 0.2);
            --orb-2-color: rgba(132, 204, 22, 0.2);
            --border-glow: #6ee7b7;
            --text-accent: #a7f3d0;
            --text-primary: #ffffff;
            --text-secondary: rgba(167, 243, 208, 0.7);
        }

        html, body {
            height: 100%; height: 100dvh; width: 100vw; margin: 0; padding: 0; overflow: hidden;
            background: var(--bg-base); font-family: 'Plus Jakarta Sans', sans-serif;
            color: var(--text-primary); font-weight: 300; transition: background 0.5s, color 0.5s;
        }

        .text-primary { color: var(--text-primary) !important; }
        .text-secondary { color: var(--text-secondary) !important; }
        .text-accent { color: var(--text-accent) !important; }
        .bg-theme-gradient { background: var(--primary-gradient) !important; color: #ffffff !important; }

        .glass-card {
            background: var(--glass-bg); backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border); box-shadow: 0 20px 50px rgba(0, 0, 0, 0.1);
        }

        .glass-pill { background: var(--glass-bg); backdrop-filter: blur(16px); border: 1px solid var(--glass-border); }

        .glass-input {
            background: var(--glass-input-bg); backdrop-filter: blur(10px); border: 1px solid var(--glass-border);
            color: var(--text-primary); transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
        }
        .glass-input::placeholder { color: var(--text-secondary); opacity: 0.5; }
        .glass-input:focus {
            background: var(--glass-bg); border-color: var(--border-glow);
            box-shadow: 0 0 12px rgba(0,0,0, 0.05); transform: translateY(-1px);
        }

        .spade-glow-beam {
            stroke-dasharray: 25 75; animation: run-neon 2.5s linear infinite;
            filter: drop-shadow(0 0 4px var(--border-glow)) drop-shadow(0 0 8px var(--border-glow));
        }

        .badge-owner {
            position: relative;
            background: rgba(16, 185, 129, 0.05);
        }
        .owner-glow-beam {
            stroke-dasharray: 30 70; animation: run-neon 2.5s linear infinite;
            filter: drop-shadow(0 0 3px var(--border-glow)) drop-shadow(0 0 6px var(--border-glow));
        }

        @keyframes run-neon { from { stroke-dashoffset: 100; } to { stroke-dashoffset: 0; } }

        @keyframes float-orb-1 { 0%, 100% { transform: translate(0, 0) scale(1) rotate(0deg); } 33% { transform: translate(30px, -50px) scale(1.2) rotate(120deg); } 66% { transform: translate(-20px, 20px) scale(0.9) rotate(240deg); } }
        @keyframes float-orb-2 { 0%, 100% { transform: translate(0, 0) scale(1.1) rotate(0deg); } 50% { transform: translate(-40px, 40px) scale(0.8) rotate(-180deg); } }
        .orb-1 { background: var(--orb-1-color); animation: float-orb-1 12s ease-in-out infinite; }
        .orb-2 { background: var(--orb-2-color); animation: float-orb-2 15s ease-in-out infinite; }

        .floral-pattern {
            position: absolute; inset: 0; opacity: 0; pointer-events: none; transition: opacity 0.5s ease;
            background-image: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M54.627 0l.83 22.091c-4.48-4.22-9.6-7.39-15.36-9.51C34.337 10.46 27.537 9.8 20 12c-5.46 1.6-10.14 4.54-14.04 8.82l-2.02-3.15C8.8 12.1 14.92 7.7 22 5.5c6.76-2.1 13.92-1.7 20.8 1.18 5.76 2.38 10.88 5.92 15.36 10.61L54.627 0zM0 54.627l22.091.83c-4.22-4.48-7.39-9.6-9.51-15.36-2.12-5.76-2.78-12.56-.58-20.1 1.6-5.46 4.54-10.14 8.82-14.04l-3.15-2.02C12.1 8.8 7.7 14.92 5.5 22c-2.1 6.76-1.7 13.92 1.18 20.8 2.38 5.76 5.92 10.88 10.61 15.36L0 54.627z' fill='%236ee7b7' fill-opacity='0.15' fill-rule='evenodd'/%3E%3C/svg%3E");
            background-size: 100px;
        }
        body.theme-floral .floral-pattern { opacity: 1; }

        .btn-bounce { transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1); }
        .btn-bounce:hover { transform: translateY(-2px); }
        .btn-bounce:active { transform: translateY(1px) scale(0.97); }

        .modal-overlay { opacity: 0; pointer-events: none; transition: opacity 0.3s; backdrop-filter: blur(12px); }
        .modal-overlay.active { opacity: 1; pointer-events: auto; }
        .modal-content { transform: scale(0.9) translateY(20px); opacity: 0; transition: all 0.4s; }
        .modal-overlay.active .modal-content { transform: scale(1) translateY(0); opacity: 1; }
    </style>
</head>
<body class="flex items-center justify-center relative w-full h-[100dvh]">

    <div class="fixed inset-0 overflow-hidden pointer-events-none z-0">
        <div class="floral-pattern"></div>
        <div class="absolute top-10 left-10 w-64 h-64 sm:w-80 sm:h-80 rounded-full blur-[90px] orb-1"></div>
        <div class="absolute bottom-10 right-10 w-80 h-80 sm:w-96 sm:h-96 rounded-full blur-[100px] orb-2"></div>
    </div>

    <!-- KHUNG TỔNG THỂ APP -->
    <div class="relative w-full h-full sm:h-[680px] sm:max-w-md sm:rounded-[40px] glass-card overflow-hidden z-10 flex flex-col p-4 sm:p-6 shadow-2xl">

        <!-- ================= MÀN HÌNH CHÍNH ================= -->
        <div id="screen-welcome" class="w-full h-full flex flex-col justify-between items-center text-center transition-all duration-500 transform scale-100 opacity-100 z-10">
            <div class="flex justify-between w-full items-center">
                <span id="current-user-badge" class="text-[10px] glass-pill text-secondary px-3.5 py-1 rounded-full font-light flex items-center gap-1.5 shadow-sm">
                    <svg class="w-3 h-3 text-accent" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"></path></svg> Khách
                </span>
                <div class="flex items-center gap-2">
                    <button onclick="openThemeModal()" class="text-[10px] text-secondary font-light p-1.5 rounded-full glass-pill hover:bg-white/10 transition btn-bounce flex items-center justify-center">
                        <svg class="w-3.5 h-3.5 text-accent" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9.53 16.122a3 3 0 00-5.78 1.128 2.25 2.25 0 01-2.4 2.245 4.5 4.5 0 008.4-2.245c0-.399-.078-.78-.22-1.128zm0 0a15.998 15.998 0 003.388-1.62m-5.043-.025a15.994 15.994 0 011.622-3.395m3.42 3.42a15.995 15.995 0 004.764-4.648l3.879-3.879a1.5 1.5 0 00-2.121-2.122l-3.879 3.879a15.994 15.994 0 00-4.648 4.764m4.648-4.764a5.25 5.25 0 01-7.424 7.424" /></svg>
                    </button>
                    <button onclick="openAuthModal()" id="auth-action-btn" class="text-[10px] text-secondary font-light px-3.5 py-1 rounded-full glass-pill hover:bg-white/10 transition btn-bounce flex items-center gap-1.5">
                        <svg class="w-3 h-3 text-accent" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 9V5.25A2.25 2.25 0 0013.5 3h-6a2.25 2.25 0 00-2.25 2.25v13.5A2.25 2.25 0 007.5 21h6a2.25 2.25 0 002.25-2.25V15M12 9l-3 3m0 0l3 3m-3-3h12.75"></path></svg> Đăng nhập
                    </button>
                </div>
            </div>

            <!-- LOGO QUÂN BÍCH -->
            <div class="flex flex-col items-center my-auto pt-2">
                <div class="relative w-16 h-16 sm:w-20 sm:h-20 mb-2 sm:mb-4 flex items-center justify-center">
                    <svg class="w-12 h-12 sm:w-16 sm:h-16 relative z-10 text-primary" viewBox="0 0 24 24" stroke-linejoin="round">
                        <path d="M12 2C7 7 4 10.5 4 14c0 2.21 1.79 4 4 4 1.3 0 2.45-.63 3.16-1.59L11 22h2l-.16-5.59c.71.96 1.86 1.59 3.16 1.59 2.21 0 4-1.79 4-4 0-3.5-3-7-8-12z" fill="none" stroke="currentColor" stroke-opacity="0.15" stroke-width="0.8"></path>
                        <path d="M12 2C7 7 4 10.5 4 14c0 2.21 1.79 4 4 4 1.3 0 2.45-.63 3.16-1.59L11 22h2l-.16-5.59c.71.96 1.86 1.59 3.16 1.59 2.21 0 4-1.79 4-4 0-3.5-3-7-8-12z" fill="none" stroke="var(--border-glow)" stroke-width="1.2" stroke-linecap="round" pathLength="100" class="spade-glow-beam"></path>
                    </svg>
                </div>
                <h1 class="text-xl sm:text-2xl font-semibold tracking-widest uppercase text-primary">PHẨM HẠNH</h1>
                <p class="text-[9px] text-accent tracking-[0.25em] uppercase font-light mt-1">Phần mềm ghi điểm</p>
            </div>

            <div class="w-full flex flex-col gap-2 sm:gap-3 pb-2">
                <button onclick="openSettingsModal()" class="w-full py-3 sm:py-3.5 bg-theme-gradient text-white font-medium rounded-2xl shadow-md btn-bounce flex items-center justify-center gap-2 text-xs tracking-wide">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15"></path></svg> TẠO BẢNG GHI MỚI
                </button>
                <button onclick="openJoinRoomModal()" class="w-full py-2.5 sm:py-3 glass-pill text-secondary font-light rounded-2xl hover:bg-white/10 transition btn-bounce flex items-center justify-center gap-2 text-xs">
                    <svg class="w-3.5 h-3.5 text-accent" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M5.25 5.653c0-.856.917-1.398 1.667-.986l11.54 6.348a1.125 1.125 0 010 1.971l-11.54 6.347a1.125 1.125 0 01-1.667-.985V5.653z"></path></svg> TIẾP TỤC VÁN ĐẤU
                </button>
                <div class="grid grid-cols-2 gap-2 sm:gap-2.5">
                    <button onclick="openHistoryList()" class="py-2 sm:py-2.5 glass-pill text-secondary text-[10px] sm:text-[11px] font-light rounded-2xl hover:bg-white/10 transition btn-bounce flex items-center justify-center gap-1.5">
                        <svg class="w-3.5 h-3.5 text-emerald-500" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6h4.5m4.5 0a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg> LỊCH SỬ
                    </button>
                    <button onclick="openAboutModal()" class="py-2 sm:py-2.5 glass-pill text-secondary text-[10px] sm:text-[11px] font-light rounded-2xl hover:bg-white/10 transition btn-bounce flex items-center justify-center gap-1.5">
                        <svg class="w-3.5 h-3.5 text-rose-500" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M11.25 11.25l.041-.02a.75.75 0 011.063.852l-.708 2.836a.75.75 0 001.063.853l.041-.021M21 12a9 9 0 11-18 0 9 9 0 0118 0zm-9-3.75h.008v.008H12V8.25z"></path></svg> THÔNG TIN
                    </button>
                </div>
            </div>
        </div>

        <!-- ================= MÀN HÌNH GAME CHÍNH ================= -->
        <div id="screen-game" class="absolute inset-0 p-4 sm:p-6 flex flex-col hidden transition-all duration-500 transform scale-90 opacity-0 z-10 h-full">
            
            <!-- HEADER GAME -->
            <div class="flex justify-between items-center border-b border-[var(--glass-border)] pb-2 shrink-0">
                <button onclick="returnToWelcome()" class="w-8 h-8 rounded-full glass-pill text-secondary hover:text-primary flex items-center justify-center btn-bounce">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5L8.25 12l7.5-7.5"></path></svg>
                </button>
                <div class="flex items-center gap-2 sm:gap-3">
                    <button onclick="toggleScoreVisibility()" class="text-secondary hover:text-primary transition btn-bounce" title="Ẩn/Hiện Điểm">
                        <svg id="eye-icon" class="w-4 h-4 sm:w-5 sm:h-5" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z"></path>
                            <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                        </svg>
                    </button>
                    <div class="glass-pill px-2.5 py-1 rounded-full border border-[var(--glass-border)] text-[9px] sm:text-[10px] font-mono font-normal text-amber-500 flex items-center gap-1 shadow-sm">
                        <span class="w-1.5 h-1.5 rounded-full bg-amber-500 animate-pulse"></span>
                        <span id="active-key-badge">KEY: ------</span>
                    </div>
                </div>
            </div>

            <!-- CONTAINER NGƯỜI CHƠI -->
            <div id="players-container" class="flex justify-center items-end gap-1.5 sm:gap-2 w-full shrink-0 my-3">
                <!-- Nội dung được sinh ra từ Javascript -->
            </div>

            <!-- NÚT CHỐT ĐIỂM -->
            <div class="relative w-full my-1 shrink-0">
                <button id="chotDiemBtn" onclick="chotDiemRealtime()" class="w-full py-2.5 sm:py-3 bg-theme-gradient text-white font-medium rounded-2xl shadow-md btn-bounce flex items-center justify-center relative overflow-hidden">
                    <svg class="w-4 h-4 relative z-10" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5"></path></svg>
                </button>
            </div>

            <!-- KHUNG LỊCH SỬ -->
            <div class="flex flex-col flex-1 min-h-0 mb-14 sm:mb-16 mt-2">
                <div id="wins-container" class="flex items-center glass-pill p-1.5 sm:p-2 rounded-xl text-[10px] sm:text-[11px] font-bold text-amber-400 mb-1.5 shrink-0 bg-white/10 shadow-sm border border-amber-500/20">
                </div>
                <div id="history-list" class="space-y-1.5 overflow-y-auto pr-1 h-full glass-input rounded-2xl p-2 sm:p-2.5">
                    <div class="text-center text-[10px] sm:text-[11px] text-secondary opacity-60 py-6 font-light">Chưa có lịch sử...</div>
                </div>
            </div>

            <!-- FLOATING DOCK -->
            <div class="absolute bottom-3 sm:bottom-4 left-1/2 -translate-x-1/2 glass-pill rounded-full p-1.5 flex items-center gap-2 sm:gap-3 shadow-xl border border-[var(--glass-border)] z-30">
                <button onclick="openChartModal()" class="w-8 h-8 sm:w-9 sm:h-9 rounded-full text-blue-500 hover:text-blue-400 flex items-center justify-center transition btn-bounce">
                    <svg class="w-3.5 h-3.5 sm:w-4 sm:h-4" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 13.125C3 12.504 3.504 12 4.125 12h2.25c.621 0 1.125.504 1.125 1.125v6.75C7.5 20.496 6.996 21 6.375 21h-2.25A1.125 1.125 0 013 19.875v-6.75zM9.75 8.625c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125v11.25c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V8.625zM16.5 4.125c0-.621.504-1.125 1.125-1.125h2.25C20.496 3 21 3.504 21 4.125v15.75c0 .621-.504 1.125-1.125 1.125h-2.25a1.125 1.125 0 01-1.125-1.125V4.125z"></path></svg>
                </button>
                <button onclick="callAI()" class="px-3 sm:px-4 h-8 sm:h-9 rounded-full bg-theme-gradient text-white font-medium text-[10px] sm:text-[11px] flex items-center justify-center gap-1 shadow-md btn-bounce">
                    <svg class="w-3 h-3 sm:w-3.5 sm:h-3.5" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9.813 15.904L9 18.75l-.813-2.846a4.5 4.5 0 00-3.09-3.09L2.25 12l2.846-.813a4.5 4.5 0 003.09-3.09L9 5.25l.813 2.846a4.5 4.5 0 003.09 3.09L15.75 12l-2.846.813a4.5 4.5 0 00-3.09 3.09z"></path></svg> <span>AI</span>
                </button>
                <button onclick="openResultModal()" class="w-8 h-8 sm:w-9 sm:h-9 rounded-full text-rose-500 hover:text-rose-400 flex items-center justify-center transition btn-bounce">
                    <svg class="w-3.5 h-3.5 sm:w-4 sm:h-4" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 3v1.5M3 21v-6m0 0l2.77-.693a14.45 14.45 0 014.66 0l2.767.693a14.45 14.45 0 004.66 0l2.767-.693V7.5l-2.767.693a14.45 14.45 0 01-4.66 0L10.43 7.5a14.45 14.45 0 00-4.66 0L3 8.193z"></path></svg>
                </button>
            </div>

            <!-- NÚT THÊM NGƯỜI CHƠI (GÓC PHẢI) -->
            <div id="add-player-btn-container" class="absolute bottom-3 sm:bottom-4 right-4 sm:right-6 glass-pill rounded-full p-1.5 flex items-center shadow-xl border border-[var(--glass-border)] z-30 hidden">
                <button onclick="openAddPlayerModal()" class="w-8 h-8 sm:w-9 sm:h-9 rounded-full text-emerald-500 hover:text-emerald-400 flex items-center justify-center transition btn-bounce">
                    <svg class="w-4 h-4 sm:w-[18px] sm:h-[18px]" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M19 7.5v3m0 0v3m0-3h3m-3 0h-3m-2.25-4.125a3.375 3.375 0 11-6.75 0 3.375 3.375 0 016.75 0zM4 19.235v-.11a6.375 6.375 0 0112.75 0v.109A12.318 12.318 0 0110.374 21c-2.331 0-4.512-.645-6.374-1.766z"></path></svg>
                </button>
            </div>
        </div>
    </div>

    <!-- MODAL THÊM NGƯỜI CHƠI -->
    <div id="add-player-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-xs rounded-3xl p-5 flex flex-col gap-3.5">
            <div class="flex justify-between items-center">
                <h3 class="font-normal text-secondary text-xs tracking-wider uppercase">Thêm Người Chơi</h3>
                <button onclick="closeModal('add-player-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <input type="text" id="new-player-name" placeholder="Tên người tham gia..." class="w-full glass-input rounded-2xl p-2.5 text-primary text-xs font-normal focus:outline-none">
            <button onclick="confirmAddPlayer()" class="w-full py-3 bg-theme-gradient text-white font-medium rounded-2xl text-xs btn-bounce shadow-md mt-1">THÊM VÀO BÀN</button>
        </div>
    </div>

    <!-- MODAL CHỌN GIAO DIỆN -->
    <div id="theme-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-sm rounded-3xl p-5 flex flex-col gap-4">
            <div class="flex justify-between items-center">
                <h3 class="font-normal text-secondary text-xs tracking-wider uppercase">Chọn Giao Diện</h3>
                <button onclick="closeModal('theme-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <div class="grid grid-cols-2 gap-3">
                <button onclick="setTheme('indigo')" class="flex flex-col items-center gap-2 p-3 rounded-2xl glass-pill hover:border-indigo-400 transition btn-bounce">
                    <div class="w-8 h-8 rounded-full bg-gradient-to-tr from-indigo-500 to-purple-500 shadow-md"></div>
                    <span class="text-[10px] text-primary font-light">Cyber Indigo</span>
                </button>
                <button onclick="setTheme('emerald')" class="flex flex-col items-center gap-2 p-3 rounded-2xl glass-pill hover:border-emerald-400 transition btn-bounce">
                    <div class="w-8 h-8 rounded-full bg-gradient-to-tr from-emerald-500 to-cyan-500 shadow-md"></div>
                    <span class="text-[10px] text-primary font-light">Emerald</span>
                </button>
                <button onclick="setTheme('ruby')" class="flex flex-col items-center gap-2 p-3 rounded-2xl glass-pill hover:border-rose-400 transition btn-bounce">
                    <div class="w-8 h-8 rounded-full bg-gradient-to-tr from-rose-500 to-pink-500 shadow-md"></div>
                    <span class="text-[10px] text-primary font-light">Ruby Red</span>
                </button>
                <button onclick="setTheme('light')" class="flex flex-col items-center gap-2 p-3 rounded-2xl glass-pill hover:border-blue-400 transition btn-bounce">
                    <div class="w-8 h-8 rounded-full bg-gradient-to-tr from-slate-200 to-blue-200 shadow-md border border-[var(--glass-border)]"></div>
                    <span class="text-[10px] text-primary font-light">Sáng (Light)</span>
                </button>
                <button onclick="setTheme('floral')" class="flex flex-col items-center gap-2 p-3 rounded-2xl glass-pill hover:border-lime-400 transition btn-bounce col-span-2">
                    <div class="w-8 h-8 rounded-full bg-gradient-to-tr from-emerald-600 to-lime-400 shadow-md"></div>
                    <span class="text-[10px] text-primary font-light">Hoa Lá Cành (Floral)</span>
                </button>
            </div>
            <button onclick="closeModal('theme-modal')" class="w-full py-2.5 glass-pill text-primary rounded-2xl text-xs font-light btn-bounce mt-1">Đóng</button>
        </div>
    </div>

    <!-- CÁC MODAL KHÁC -->
    <div id="auth-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-sm rounded-3xl p-5 flex flex-col gap-3.5">
            <div class="flex justify-between items-center">
                <h3 id="auth-title" class="font-normal text-secondary text-xs tracking-wider uppercase">Đăng Nhập</h3>
                <button onclick="closeModal('auth-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <input type="text" id="auth-username" placeholder="Tên tài khoản" class="w-full glass-input rounded-2xl p-2.5 text-primary text-xs font-normal focus:outline-none">
            <input type="password" id="auth-password" placeholder="Mật khẩu" class="w-full glass-input rounded-2xl p-2.5 text-primary text-xs font-normal focus:outline-none">
            <button onclick="handleUserAuth()" id="auth-submit-btn" class="w-full py-3 bg-theme-gradient text-white font-medium rounded-2xl text-xs btn-bounce shadow-md">ĐĂNG NHẬP</button>
            <button onclick="toggleAuthMode()" class="text-[10px] text-secondary hover:text-primary font-light text-center"><span id="auth-switch-text">Chưa có tài khoản? Đăng ký ngay</span></button>
        </div>
    </div>

    <div id="join-room-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-sm rounded-3xl p-5 flex flex-col gap-3.5">
            <div class="flex justify-between items-center">
                <h3 class="font-normal text-secondary text-xs tracking-wider uppercase">Tiếp Tục Ván Đấu</h3>
                <button onclick="closeModal('join-room-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <p class="text-[11px] text-secondary font-light">Nhập mã ván đấu (Chữ + Số):</p>
            <input type="text" id="input-key-7" placeholder="VD: K888666" class="w-full glass-input text-center rounded-2xl py-3 text-primary font-mono font-normal text-lg tracking-widest focus:outline-none" oninput="this.value = this.value.toUpperCase()">
            <button onclick="joinRoomWithKey()" class="w-full py-3 bg-theme-gradient text-white font-medium rounded-2xl text-xs btn-bounce shadow-md">XÁC NHẬN</button>
        </div>
    </div>

    <div id="settings-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-sm rounded-3xl p-5 flex flex-col gap-3.5 max-h-[85vh] overflow-y-auto">
            <div class="flex justify-between items-center">
                <h3 class="font-normal text-secondary text-xs tracking-wider uppercase">Tạo Bảng Ghi Mới</h3>
                <button onclick="closeModal('settings-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <div>
                <label class="text-[10px] text-secondary font-light tracking-wider uppercase">Mã bàn chơi (Chữ + 6 Số):</label>
                <div class="flex gap-2 mt-1">
                    <input type="text" id="generated-key" class="w-full glass-input rounded-2xl p-2.5 text-center text-primary font-mono font-normal text-xs tracking-widest focus:outline-none" oninput="this.value = this.value.toUpperCase()">
                    <button onclick="generateRandomKey()" class="glass-pill px-3.5 rounded-2xl text-accent hover:text-primary text-xs btn-bounce flex items-center justify-center">
                        <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M16.023 9.348h4.992v-.001M2.985 19.644v-4.992m0 0h4.992m-4.993 0l3.181 3.183a8.25 8.25 0 0013.803-3.7M4.031 9.865a8.25 8.25 0 0113.803-3.7l3.181 3.182m0-4.991v4.99" /></svg>
                    </button>
                </div>
            </div>
            <div>
                <label class="text-[10px] text-secondary font-light tracking-wider uppercase">Tên người chơi (Bỏ trống nếu ít hơn 5):</label>
                <div id="settings-inputs" class="space-y-2 mt-1"></div>
            </div>
            <button onclick="saveAndCreateRoom()" class="w-full py-3 bg-theme-gradient text-white font-medium rounded-2xl text-xs btn-bounce shadow-md">LƯU & BẮT ĐẦU</button>
        </div>
    </div>

    <div id="history-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-sm rounded-3xl p-5 flex flex-col max-h-[80vh]">
            <div class="flex justify-between items-center mb-3">
                <h3 class="font-normal text-emerald-500 text-xs tracking-wider uppercase">Lịch Sử Ván Đấu</h3>
                <button onclick="closeModal('history-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <div id="global-history-list" class="space-y-2.5 overflow-y-auto flex-1 text-xs text-primary pr-1">
                <div class="text-center text-secondary opacity-60 py-6 font-light">Đang tải lịch sử...</div>
            </div>
        </div>
    </div>

    <!-- THÔNG TIN -->
    <div id="about-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-sm rounded-3xl p-5 flex flex-col gap-3.5">
            <div class="flex justify-between items-center">
                <h3 class="font-normal text-accent text-xs tracking-wider uppercase">Thông Tin Ứng Dụng</h3>
                <button onclick="closeModal('about-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <div class="glass-input p-4 rounded-2xl flex flex-col gap-3.5 text-xs font-light max-h-[60vh] overflow-y-auto">
                <div class="text-center pb-2 border-b border-[var(--glass-border)]">
                    <h4 class="font-bold text-primary text-base tracking-widest uppercase">PHẨM HẠNH</h4>
                    <p class="text-[9px] text-accent uppercase tracking-widest mt-1">Hệ Sinh Thái Ghi Điểm Thông Minh</p>
                </div>
                <div class="text-[11px] leading-relaxed text-secondary text-justify space-y-2.5">
                    <p><b>Phẩm Hạnh</b> không chỉ là một ứng dụng tính điểm thông thường, mà là một trải nghiệm số được thiết kế tỉ mỉ nhằm mang lại sự tiện lợi tối đa cho mọi cuộc chơi.</p>
                    <p>Sở hữu giao diện <b>Glassmorphism (Kính mờ)</b> hiện đại, cùng khả năng tương tác mượt mà, ứng dụng giúp bạn dễ dàng theo dõi điểm số, đồng bộ dữ liệu thời gian thực (real-time) đa thiết bị mà không gặp bất kỳ độ trễ nào.</p>
                    <p>Đặc biệt, ứng dụng tích hợp <b>Trí tuệ Nhân tạo (AI)</b> phân tích chuyên sâu ván đấu, vẽ biểu đồ phong độ và đưa ra những lời bình luận sắc sảo, hài hước, biến những con số khô khan trở thành tâm điểm của niềm vui.</p>
                </div>
                <div class="text-[11px] space-y-2 text-secondary pt-3 border-t border-[var(--glass-border)]">
                    <p class="flex justify-between items-center"><span>Nhà phát triển:</span> <b class="text-primary font-medium">Nguyễn Đình Giang</b></p>
                    <p class="flex justify-between items-center"><span>Zalo Hỗ trợ:</span> <b class="text-accent font-mono font-medium">0925.462.888</b></p>
                    <p class="flex justify-between items-center"><span>Donate (Vietcombank):</span> <b class="text-emerald-400 font-mono font-medium text-sm">9888668956</b></p>
                </div>
            </div>
            <button onclick="closeModal('about-modal')" class="w-full py-3 glass-pill text-primary rounded-2xl text-xs font-medium btn-bounce mt-1">Đóng</button>
        </div>
    </div>

    <div id="ai-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-md rounded-3xl p-5 flex flex-col max-h-[85vh]">
            <div class="flex justify-between items-center mb-3">
                <h3 class="font-normal text-accent text-xs tracking-wider uppercase flex items-center gap-1.5">Bình Luận Viên AI</h3>
                <button onclick="closeModal('ai-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <div id="ai-content" class="text-xs leading-relaxed overflow-y-auto text-primary space-y-3 glass-input p-4 rounded-2xl flex-1 font-light"></div>
        </div>
    </div>

    <div id="result-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-sm rounded-3xl p-5 flex flex-col gap-3.5 max-h-[90vh]">
            <div id="capture-zone" class="glass-input p-3.5 rounded-2xl flex flex-col gap-2.5 flex-1 overflow-y-auto">
                <div class="text-center flex flex-col items-center justify-center">
                    <h3 class="font-normal text-xs text-amber-500 tracking-widest uppercase">BẢNG XẾP HẠNG</h3>
                    <p id="result-key-text" class="text-[9px] font-mono text-secondary tracking-widest font-light mt-0.5">KEY: ------</p>
                </div>
                <div id="ranking-list" class="space-y-1.5"></div>
                <div id="result-ai-comment" class="text-[10px] text-accent italic text-left p-3 glass-pill rounded-xl leading-relaxed font-light space-y-2">
                    <span class="animate-pulse">AI đang phân tích ván đấu...</span>
                </div>
            </div>
            <div class="grid grid-cols-3 gap-2">
                <button onclick="continueGameFromResult()" class="py-2.5 glass-pill text-secondary hover:text-primary rounded-xl flex items-center justify-center btn-bounce">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 15L3 9m0 0l6-6M3 9h12a6 6 0 010 12h-3"></path></svg>
                </button>
                <button onclick="shareScreenshot()" class="py-2.5 glass-pill text-emerald-500 hover:text-emerald-400 rounded-xl flex items-center justify-center btn-bounce">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 16.5v2.25A2.25 2.25 0 005.25 21h13.5A2.25 2.25 0 0021 18.75V16.5M16.5 12L12 16.5m0 0L7.5 12m4.5 4.5V3"></path></svg>
                </button>
                <button onclick="endGameCompletely()" class="py-2.5 glass-pill text-rose-500 hover:text-rose-400 rounded-xl flex items-center justify-center btn-bounce">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M5.636 5.636a9 9 0 1012.728 0M12 3v9"></path></svg>
                </button>
            </div>
        </div>
    </div>

    <div id="chart-modal" class="fixed inset-0 modal-overlay flex items-center justify-center p-4 z-50">
        <div class="modal-content glass-card w-full max-w-lg h-[85vh] rounded-3xl p-5 flex flex-col gap-3.5">
            <div class="flex justify-between items-center shrink-0">
                <h3 class="font-normal text-accent text-xs tracking-wider uppercase">Biểu Đồ Phong Độ</h3>
                <button onclick="closeModal('chart-modal')" class="text-secondary hover:text-primary text-xs">✕</button>
            </div>
            <div class="glass-input p-2 rounded-2xl flex-1 relative w-full min-h-[180px]">
                <canvas id="scoreChart"></canvas>
            </div>
            <div id="chart-ai-comment" class="glass-pill p-3.5 rounded-2xl text-[10px] text-accent italic leading-relaxed shrink-0 max-h-40 overflow-y-auto font-light text-left space-y-2">
                <span class="animate-pulse">AI đang soi biểu đồ...</span>
            </div>
        </div>
    </div>

    <!-- LOGIC JAVASCRIPT & FIREBASE -->
    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyDen-Lt05J8HH9q-Pf8tY9RS6A6-B00AcU",
            authDomain: "samloconline-5ab1b.firebaseapp.com",
            databaseURL: "https://samloconline-5ab1b-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "samloconline-5ab1b",
            storageBucket: "samloconline-5ab1b.firebasestorage.app",
            messagingSenderId: "180202096513",
            appId: "1:180202096513:web:6f0aea0360aef36a748a80"
        };

        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        let isRegisterMode = false;
        let currentUser = localStorage.getItem('samloc_current_user');
        let currentKey = localStorage.getItem('samloc_active_key') || "";
        let isScoreVisible = true;
        const apiKeyGemini = "AQ.Ab8RN6JbjDdOsyx5bXqbuAAVmzXGkj5SQphcCGyzf-3UwZfSSQ";

        window.onload = function() { 
            updateUserUI(); 
            let savedTheme = localStorage.getItem('samloc_theme') || 'indigo';
            setTheme(savedTheme);
        };

        function openThemeModal() { openModal('theme-modal'); }
        function setTheme(theme) {
            document.body.className = 'flex items-center justify-center relative w-full h-[100dvh]'; 
            if (theme !== 'indigo') document.body.classList.add('theme-' + theme);
            localStorage.setItem('samloc_theme', theme);
            closeModal('theme-modal');
        }

        function openModal(id) { document.getElementById(id).classList.add('active'); }
        function closeModal(id) { document.getElementById(id).classList.remove('active'); }
        
        function openAuthModal() { openModal('auth-modal'); }
        function openJoinRoomModal() { 
            let savedKey = currentKey || localStorage.getItem('samloc_active_key') || "";
            document.getElementById('input-key-7').value = savedKey;
            openModal('join-room-modal'); 
        }
        function openAboutModal() { openModal('about-modal'); }

        function toggleScoreVisibility() {
            isScoreVisible = !isScoreVisible;
            const eyeIcon = document.getElementById('eye-icon');
            if(isScoreVisible) {
                eyeIcon.innerHTML = `
                    <path stroke-linecap="round" stroke-linejoin="round" d="M2.036 12.322a1.012 1.012 0 010-.639C3.423 7.51 7.36 4.5 12 4.5c4.638 0 8.573 3.007 9.963 7.178.07.207.07.431 0 .639C20.577 16.49 16.64 19.5 12 19.5c-4.638 0-8.573-3.007-9.963-7.178z"></path>
                    <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                `;
            } else {
                eyeIcon.innerHTML = `
                    <path stroke-linecap="round" stroke-linejoin="round" d="M3.98 8.223A10.477 10.477 0 001.934 12C3.226 16.338 7.244 19.5 12 19.5c.993 0 1.953-.138 2.863-.395M6.228 6.228A10.45 10.45 0 0112 4.5c4.756 0 8.773 3.162 10.065 7.498a10.523 10.523 0 01-4.293 5.774M6.228 6.228L3 3m3.228 3.228l3.65 3.65m7.894 7.894L21 21m-3.228-3.228l-3.65-3.65m0 0a3 3 0 10-4.243-4.243m4.242 4.242L9.88 9.88"></path>
                `;
            }
            updateScoresAndHistoryOnly();
        }

        function updateUserUI() {
            if (currentUser) {
                document.getElementById('current-user-badge').innerHTML = `<svg class="w-3 h-3 text-accent" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"></path></svg> ${currentUser}`;
                document.getElementById('auth-action-btn').innerHTML = `<svg class="w-3 h-3 text-secondary" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 9V5.25A2.25 2.25 0 0013.5 3h-6a2.25 2.25 0 00-2.25 2.25v13.5A2.25 2.25 0 007.5 21h6a2.25 2.25 0 002.25-2.25V15M12 9l-3 3m0 0l3 3m-3-3h12.75"></path></svg> Đăng xuất`;
                document.getElementById('auth-action-btn').onclick = logoutUser;
            } else {
                document.getElementById('current-user-badge').innerHTML = `<svg class="w-3 h-3 text-accent" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"></path></svg> Khách`;
                document.getElementById('auth-action-btn').innerHTML = `<svg class="w-3 h-3 text-accent" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 9V5.25A2.25 2.25 0 0013.5 3h-6a2.25 2.25 0 00-2.25 2.25v13.5A2.25 2.25 0 007.5 21h6a2.25 2.25 0 002.25-2.25V15M12 9l-3 3m0 0l3 3m-3-3h12.75"></path></svg> Đăng nhập`;
                document.getElementById('auth-action-btn').onclick = openAuthModal;
            }
        }

        function toggleAuthMode() {
            isRegisterMode = !isRegisterMode;
            document.getElementById('auth-title').innerText = isRegisterMode ? "Đăng Ký Tài Khoản" : "Đăng Nhập";
            document.getElementById('auth-submit-btn').innerText = isRegisterMode ? "ĐĂNG KÝ" : "ĐĂNG NHẬP";
            document.getElementById('auth-switch-text').innerText = isRegisterMode ? "Đã có tài khoản? Đăng nhập ngay" : "Chưa có tài khoản? Đăng ký ngay";
        }

        function handleUserAuth() {
            let user = document.getElementById('auth-username').value.trim();
            let pass = document.getElementById('auth-password').value.trim();
            if (!user || !pass) return alert("Vui lòng nhập đầy đủ thông tin!");
            let safeUserKey = user.replace(/[.#$\/\[\]]/g, "_");
            db.ref('users/' + safeUserKey).once('value', (snapshot) => {
                let userData = snapshot.val();
                if (isRegisterMode) {
                    if (userData) return alert("Tên tài khoản này đã tồn tại!");
                    db.ref('users/' + safeUserKey).set({ password: pass, createdAt: new Date().toISOString() }, () => {
                        alert("Đăng ký thành công!");
                        toggleAuthMode();
                    });
                } else {
                    if (!userData || userData.password !== pass) return alert("Sai tài khoản hoặc mật khẩu!");
                    localStorage.setItem('samloc_current_user', user);
                    currentUser = user;
                    updateUserUI();
                    closeModal('auth-modal');
                }
            });
        }

        function logoutUser() {
            localStorage.removeItem('samloc_current_user');
            currentUser = null;
            updateUserUI();
        }

        function generateRandomKey() {
            const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
            const randomLetter = letters.charAt(Math.floor(Math.random() * letters.length));
            const randomNumbers = Math.floor(100000 + Math.random() * 900000).toString();
            document.getElementById('generated-key').value = randomLetter + randomNumbers;
        }

        function capitalizeFirstLetter(val) {
            if (!val) return "";
            let trimmed = val.trim();
            return trimmed.charAt(0).toUpperCase() + trimmed.slice(1);
        }

        function openSettingsModal() {
            generateRandomKey();
            let html = '';
            for (let i = 0; i < 5; i++) {
                let val = (i === 0 && currentUser) ? capitalizeFirstLetter(currentUser) : '';
                html += `<input type="text" id="setting-p-${i}" value="${val}" placeholder="Tên người chơi ${i+1}" class="w-full glass-input rounded-2xl p-2.5 text-primary text-xs font-normal focus:outline-none">`;
            }
            document.getElementById('settings-inputs').innerHTML = html;
            openModal('settings-modal');
        }

        function saveAndCreateRoom() {
            let key = document.getElementById('generated-key').value.trim().toUpperCase();
            if (key.length !== 7) return alert("Mã Key phải bao gồm 1 chữ cái và 6 số (VD: K888666)!");
            
            let newPlayers = [];
            for (let i = 0; i < 5; i++) {
                let rawName = document.getElementById(`setting-p-${i}`).value.trim();
                if (rawName) {
                    newPlayers.push({ 
                        name: capitalizeFirstLetter(rawName), score: 0, wins: 0, input: "", 
                        creatorUser: (newPlayers.length === 0 && currentUser) ? currentUser : "", activeUserTyping: ""
                    });
                }
            }
            
            if (newPlayers.length === 0) {
                 newPlayers.push({name: currentUser ? capitalizeFirstLetter(currentUser) : "Người 1", score: 0, wins: 0, input: "", creatorUser: currentUser || "", activeUserTyping: ""});
                 newPlayers.push({name: "Người 2", score: 0, wins: 0, input: "", creatorUser: "", activeUserTyping: ""});
            } else if (newPlayers.length === 1) {
                 newPlayers.push({name: "Người 2", score: 0, wins: 0, input: "", creatorUser: "", activeUserTyping: ""});
            }

            currentKey = key;
            localStorage.setItem('samloc_active_key', currentKey);
            db.ref('rooms/' + currentKey).set({ admin: currentUser || 'Khách', players: newPlayers, rounds: [] }, () => {
                closeModal('settings-modal');
                enterGameScreen();
            });
        }

        function openAddPlayerModal() {
            document.getElementById('new-player-name').value = '';
            openModal('add-player-modal');
        }

        function confirmAddPlayer() {
            let name = document.getElementById('new-player-name').value.trim();
            if (!name) return alert("Vui lòng nhập tên người chơi mới!");
            let players = window.roomData.players;
            if (players.length >= 5) return alert("Bàn đã đạt tối đa 5 người chơi!");
            
            players.push({
                name: capitalizeFirstLetter(name), score: 0, wins: 0, input: "",
                creatorUser: "", activeUserTyping: ""
            });
            
            db.ref(`rooms/${currentKey}`).update({ players: players }, () => {
                closeModal('add-player-modal');
            });
        }

        function joinRoomWithKey() {
            let key = document.getElementById('input-key-7').value.trim().toUpperCase();
            if (key.length !== 7) return alert("Vui lòng nhập đúng mã Key 7 ký tự (VD: K888666)!");
            db.ref('rooms/' + key).once('value', (snapshot) => {
                if (!snapshot.val()) return alert("Mã Key này không tồn tại!");
                currentKey = key;
                localStorage.setItem('samloc_active_key', currentKey);
                closeModal('join-room-modal');
                enterGameScreen();
            });
        }

        let isFirstRender = true;
        function enterGameScreen() {
            const welcome = document.getElementById('screen-welcome');
            const game = document.getElementById('screen-game');
            
            welcome.classList.replace('scale-100', 'scale-90');
            welcome.classList.replace('opacity-100', 'opacity-0');
            welcome.classList.add('pointer-events-none');
            
            game.classList.remove('hidden');
            setTimeout(() => {
                game.classList.replace('scale-90', 'scale-100');
                game.classList.replace('opacity-0', 'opacity-100');
            }, 50);

            document.getElementById('active-key-badge').innerText = `KEY: ${currentKey}`;
            isFirstRender = true;
            
            db.ref('rooms/' + currentKey).on('value', (snapshot) => {
                window.roomData = snapshot.val();
                if (window.roomData) {
                    let activeEl = document.activeElement;
                    let isTyping = activeEl && activeEl.id && activeEl.id.startsWith('input-field-');
                    if (isFirstRender || !isTyping) {
                        if(isFirstRender) { renderRealtime(); isFirstRender = false; } 
                        else { updateScoresAndHistoryOnly(); }
                    }
                }
            });
        }

        function returnToWelcome() {
            if (currentKey) db.ref('rooms/' + currentKey).off();
            const welcome = document.getElementById('screen-welcome');
            const game = document.getElementById('screen-game');
            
            game.classList.replace('scale-100', 'scale-90');
            game.classList.replace('opacity-100', 'opacity-0');
            
            setTimeout(() => {
                game.classList.add('hidden');
                welcome.classList.replace('scale-90', 'scale-100');
                welcome.classList.replace('opacity-0', 'opacity-100');
                welcome.classList.remove('pointer-events-none');
            }, 300);
        }

        function endGameCompletely() {
            if (currentKey) db.ref('rooms/' + currentKey).off();
            currentKey = "";
            localStorage.removeItem('samloc_active_key');
            closeModal('result-modal');
            returnToWelcome();
        }

        function continueGameFromResult() { closeModal('result-modal'); }

        function shareScreenshot() {
            let target = document.getElementById('capture-zone');
            let bgColor = getComputedStyle(document.documentElement).getPropertyValue('--bg-base').trim() || '#0f172a';
            html2canvas(target, { backgroundColor: bgColor, scale: 2 }).then(canvas => {
                canvas.toBlob(function(blob) {
                    let file = new File([blob], 'bang-xep-hang.png', { type: blob.type });
                    if (navigator.share && navigator.canShare && navigator.canShare({ files: [file] })) {
                        navigator.share({ title: 'Kết quả ván đấu', files: [file] }).catch(e => console.log(e));
                    } else {
                        let url = URL.createObjectURL(blob);
                        let a = document.createElement('a');
                        a.href = url; a.download = 'bang-xep-hang.png'; a.click();
                    }
                });
            });
        }

        function renderRealtime() {
            if (!window.roomData) return;
            let players = window.roomData.players;
            let html = '';
            
            players.forEach((p, i) => {
                let isOwner = currentUser && (p.creatorUser === currentUser || p.name === capitalizeFirstLetter(currentUser));
                let badgeClass = isOwner ? "badge-owner" : "glass-pill";
                let userTypingName = p.activeUserTyping || "";
                
                let ownerSvg = isOwner ? `
                    <svg class="absolute inset-0 w-full h-full pointer-events-none" style="z-index: 0; border-radius: 8px;">
                        <rect x="0.5" y="0.5" width="calc(100% - 1px)" height="calc(100% - 1px)" rx="7.5" fill="none" stroke="currentColor" stroke-opacity="0.15" stroke-width="1"></rect>
                        <rect x="0.5" y="0.5" width="calc(100% - 1px)" height="calc(100% - 1px)" rx="7.5" fill="none" stroke="var(--border-glow)" stroke-width="1.5" stroke-linecap="round" pathLength="100" class="owner-glow-beam"></rect>
                    </svg>
                ` : '';

                // Hiển thị tên người đang gõ kiểu Google Sheets (chấm tròn nhấp nháy + tên chính giữa phía trên ô nhập)
                let typingTag = userTypingName ? `
                    <div class="absolute top-1 left-1/2 -translate-x-1/2 z-20 flex items-center gap-1 bg-slate-900/90 border border-emerald-500/50 px-2 py-0.5 rounded-full text-[8px] font-medium shadow-lg animate-bounce pointer-events-none">
                        <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-ping"></span>
                        <span class="truncate max-w-[50px] text-white">${capitalizeFirstLetter(userTypingName)}</span>
                    </div>
                ` : '';

                let scoreDisplay = isScoreVisible ? p.score : '***';

                html += `
                    <div class="flex-1 min-w-[50px] max-w-[80px] flex flex-col items-center gap-1.5 transition-all relative">
                        
                        <!-- Ô TÊN VÀ ĐIỂM (CHỦ PHÒNG CÓ VIỀN SÁNG CHẠY) -->
                        <div id="player-head-${i}" class="${badgeClass} w-full py-1 sm:py-1.5 rounded-lg flex flex-col items-center justify-center relative shadow-sm border border-[var(--glass-border)]">
                            ${ownerSvg}
                            <div class="text-[8px] sm:text-[9px] font-semibold uppercase tracking-wider text-secondary truncate w-full text-center relative z-10 px-0.5">
                                ${capitalizeFirstLetter(p.name)}
                            </div>
                            <div id="score-${i}" class="text-xs sm:text-sm font-bold ${p.score >= 0 || !isScoreVisible ? 'text-primary' : 'text-rose-500'} relative z-10 mt-0.5">
                                ${scoreDisplay}
                            </div>
                        </div>

                        <!-- Ô NHẬP ĐIỂM -->
                        <div class="relative w-full h-14 sm:h-16 flex flex-col justify-end">
                            ${typingTag}
                            <input type="tel" id="input-field-${i}" placeholder="-" value="${p.input !== undefined ? p.input : ''}" 
                                oninput="localInput(${i}, this.value)" onfocus="handleFocusInput(${i})" onblur="handleBlurInput(${i}, this.value)"
                                class="w-full h-full glass-input text-center rounded-xl pt-5 pb-1 font-semibold text-lg sm:text-2xl focus:outline-none transition-all">
                        </div>
                    </div>
                `;
            });

            document.getElementById('players-container').innerHTML = html;

            const addContainer = document.getElementById('add-player-btn-container');
            if (addContainer) {
                if (players.length >= 5) {
                    addContainer.classList.add('hidden');
                    addContainer.classList.remove('flex');
                } else {
                    addContainer.classList.remove('hidden');
                    addContainer.classList.add('flex');
                }
            }
            updateHistoryOnly();
        }

        function updateScoresAndHistoryOnly() {
            if (!window.roomData) return;
            let players = window.roomData.players;
            
            let currentInputs = document.getElementById('players-container').querySelectorAll('input[type="tel"]').length;
            if (players.length !== currentInputs) return renderRealtime();

            const addContainer = document.getElementById('add-player-btn-container');
            if (addContainer) {
                if (players.length >= 5) {
                    addContainer.classList.add('hidden');
                    addContainer.classList.remove('flex');
                } else {
                    addContainer.classList.remove('hidden');
                    addContainer.classList.add('flex');
                }
            }

            players.forEach((p, i) => {
                let inputEl = document.getElementById(`input-field-${i}`);
                let headEl = document.getElementById(`player-head-${i}`);
                
                let isOwner = currentUser && (p.creatorUser === currentUser || p.name === capitalizeFirstLetter(currentUser));
                let badgeClass = isOwner ? "badge-owner" : "glass-pill";
                let ownerSvg = isOwner ? `
                    <svg class="absolute inset-0 w-full h-full pointer-events-none" style="z-index: 0; border-radius: 8px;">
                        <rect x="0.5" y="0.5" width="calc(100% - 1px)" height="calc(100% - 1px)" rx="7.5" fill="none" stroke="currentColor" stroke-opacity="0.15" stroke-width="1"></rect>
                        <rect x="0.5" y="0.5" width="calc(100% - 1px)" height="calc(100% - 1px)" rx="7.5" fill="none" stroke="var(--border-glow)" stroke-width="1.5" stroke-linecap="round" pathLength="100" class="owner-glow-beam"></rect>
                    </svg>
                ` : '';

                let scoreDisplay = isScoreVisible ? p.score : '***';

                if (headEl) {
                    headEl.className = `${badgeClass} w-full py-1 sm:py-1.5 rounded-lg flex flex-col items-center justify-center relative shadow-sm border border-[var(--glass-border)]`;
                    headEl.innerHTML = `
                        ${ownerSvg}
                        <div class="text-[8px] sm:text-[9px] font-semibold uppercase tracking-wider text-secondary truncate w-full text-center relative z-10 px-0.5">
                            ${capitalizeFirstLetter(p.name)}
                        </div>
                        <div id="score-${i}" class="text-xs sm:text-sm font-bold ${p.score >= 0 || !isScoreVisible ? 'text-primary' : 'text-rose-500'} relative z-10 mt-0.5">
                            ${scoreDisplay}
                        </div>
                    `;
                }

                let parentDiv = inputEl ? inputEl.parentElement : null;
                if(parentDiv) {
                    let oldTag = parentDiv.querySelector('.absolute.top-1');
                    if(oldTag) oldTag.remove();
                    
                    let userTypingName = p.activeUserTyping || "";
                    if(userTypingName) {
                        let newTag = document.createElement('div');
                        newTag.className = 'absolute top-1 left-1/2 -translate-x-1/2 z-20 flex items-center gap-1 bg-slate-900/90 border border-emerald-500/50 px-2 py-0.5 rounded-full text-[8px] font-medium shadow-lg animate-bounce pointer-events-none';
                        newTag.innerHTML = `
                            <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-ping"></span>
                            <span class="truncate max-w-[50px] text-white">${capitalizeFirstLetter(userTypingName)}</span>
                        `;
                        parentDiv.appendChild(newTag);
                    }
                }

                if(inputEl && document.activeElement !== inputEl) inputEl.value = p.input !== undefined ? p.input : '';
            });
            updateHistoryOnly();
        }

        function updateHistoryOnly() {
            let rounds = window.roomData.rounds || [];
            let players = window.roomData.players;
            
            let winsHTML = `
                <span class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-amber-400 opacity-90 flex items-center justify-center text-[10px] sm:text-[11px] mr-1.5 sm:mr-2 shrink-0">🏆</span>
                <div class="flex-1 grid text-center drop-shadow-md" style="grid-template-columns: repeat(${players.length}, minmax(0, 1fr));">
                    ${players.map(p => `<span>${p.wins}</span>`).join('')}
                </div>
            `;
            document.getElementById('wins-container').innerHTML = winsHTML;

            let historyHTML = rounds.length === 0 ? '<div class="text-center text-[10px] sm:text-[11px] text-secondary opacity-60 py-6 font-light">Chưa có lịch sử...</div>' : '';
            
            rounds.slice().reverse().forEach((h, index) => {
                let stt = rounds.length - index;
                let cells = '';
                
                for(let i=0; i<players.length; i++) {
                    let res = h[i];
                    if (res === undefined) res = '-';
                    cells += `<span class="${res === '0' ? 'text-emerald-500 font-medium' : (res === '-' ? 'opacity-30' : 'opacity-80')}">${res}</span>`;
                }

                historyHTML += `
                    <div class="flex items-center glass-pill p-1.5 sm:p-2 rounded-xl text-[10px] sm:text-xs font-light">
                        <span class="w-3.5 h-3.5 sm:w-4 sm:h-4 text-secondary opacity-80 rounded-full flex items-center justify-center text-[7px] sm:text-[8px] mr-1.5 sm:mr-2 border border-[var(--glass-border)] font-mono shrink-0">${stt}</span>
                        <div class="flex-1 grid text-center text-primary opacity-90" style="grid-template-columns: repeat(${players.length}, minmax(0, 1fr));">
                            ${cells}
                        </div>
                    </div>
                `;
            });
            document.getElementById('history-list').innerHTML = historyHTML;
        }

        function handleFocusInput(index) { db.ref(`rooms/${currentKey}/players/${index}/activeUserTyping`).set(currentUser || 'Khách'); }
        function handleBlurInput(index, value) { db.ref(`rooms/${currentKey}/players/${index}`).update({ input: value.replace(/[^0-9]/g, ''), activeUserTyping: "" }); }
        function localInput(index, value) { if(window.roomData && window.roomData.players[index]) window.roomData.players[index].input = value.replace(/[^0-9]/g, ''); }

        function chotDiemRealtime() {
            let players = window.roomData.players;
            let rounds = window.roomData.rounds || [];
            let activeCount = 0, zeroCount = 0, totalCards = 0;
            
            players.forEach(p => {
                let valStr = (p.input || "").trim();
                if (valStr !== '') {
                    let val = parseInt(valStr);
                    if (!isNaN(val) && val >= 0) {
                        activeCount++;
                        if (val === 0) zeroCount++;
                        totalCards += val;
                    }
                }
            });

            if (activeCount < 2 || zeroCount !== 1) return alert("Cần tối thiểu 2 người nhập điểm và đúng 1 người về nhất (điền 0)!");

            let roundRes = [];
            players.forEach(p => {
                let valStr = (p.input || "").trim();
                if (valStr === '') {
                    roundRes.push('-');
                } else {
                    let val = parseInt(valStr);
                    if (val === 0) { p.score += totalCards; p.wins++; roundRes.push('0'); } 
                    else { p.score -= val; roundRes.push('-' + val); }
                    p.input = ''; p.activeUserTyping = '';
                }
            });

            rounds.push(roundRes);
            db.ref(`rooms/${currentKey}`).update({ players: players, rounds: rounds }, () => {
                players.forEach((p, i) => { let el = document.getElementById(`input-field-${i}`); if(el) el.value = ''; });
            });

            let safeUserKey = (currentUser || 'Khách').replace(/[.#$\/\[\]]/g, "_");
            db.ref(`users/${safeUserKey}/history`).push({
                key: currentKey, date: new Date().toLocaleString('vi-VN'),
                results: players.map(p => ({ name: p.name, score: p.score }))
            });
        }

        function openHistoryList() {
            let safeUserKey = (currentUser || 'Khách').replace(/[.#$\/\[\]]/g, "_");
            document.getElementById('global-history-list').innerHTML = '<div class="text-center text-[11px] text-secondary opacity-60 py-6 font-light">Đang tải lịch sử...</div>';
            openModal('history-modal');

            db.ref(`users/${safeUserKey}/history`).once('value', (snapshot) => {
                let historyArray = Object.values(snapshot.val() || {});
                let html = historyArray.length === 0 ? '<div class="text-center text-[11px] text-secondary opacity-60 py-6 font-light">Chưa có lịch sử.</div>' : '';
                let uniqueHistory = [], seenKeys = new Set();
                
                historyArray.slice().reverse().forEach(g => { if(!seenKeys.has(g.key)){ seenKeys.add(g.key); uniqueHistory.push(g); } });

                uniqueHistory.forEach((g) => {
                    html += `
                        <div class="glass-pill p-3 rounded-2xl space-y-1.5 mb-2 cursor-pointer hover:border-accent transition" onclick="resumeHistoryGame('${g.key}')">
                            <div class="text-[9px] text-secondary font-light flex justify-between border-b border-[var(--glass-border)] pb-1 mb-1">
                                <span>🕒 ${g.date}</span>
                                <span class="text-accent font-mono">KEY: ${g.key}</span>
                            </div>
                            ${g.results.map(r => `<div class="flex justify-between text-[11px] py-0.5 font-light"><span class="text-primary opacity-90">${capitalizeFirstLetter(r.name)}</span><span class="${r.score >= 0 ? 'text-accent':'text-rose-500'}">${r.score}</span></div>`).join('')}
                            <div class="text-center text-accent opacity-80 text-[9px] mt-1.5 font-light border-t border-[var(--glass-border)] pt-1.5 tracking-widest uppercase">TIẾP TỤC VÁN ĐẤU</div>
                        </div>
                    `;
                });
                document.getElementById('global-history-list').innerHTML = html;
            });
        }

        function resumeHistoryGame(key) {
            document.getElementById('input-key-7').value = key;
            joinRoomWithKey();
            closeModal('history-modal');
        }

        function openResultModal() {
            if (!window.roomData) return;
            document.getElementById('result-key-text').innerText = `KEY: ${currentKey}`;
            let sorted = [...window.roomData.players].sort((a,b) => b.score - a.score);
            let html = '';
            sorted.forEach((p, i) => {
                html += `
                    <div class="flex justify-between items-center glass-pill p-2.5 rounded-xl border border-[var(--glass-border)]">
                        <div class="flex items-center gap-2">
                            <span class="w-5 h-5 rounded-full glass-input flex items-center justify-center text-[8px] text-accent font-mono font-light">#${i+1}</span>
                            <span class="font-light text-xs text-primary">${capitalizeFirstLetter(p.name)}</span>
                        </div>
                        <span class="font-normal text-xs ${p.score >= 0 ? 'text-accent' : 'text-rose-500'}">${p.score}</span>
                    </div>
                `;
            });
            document.getElementById('ranking-list').innerHTML = html;
            openModal('result-modal');
            callAIForResult(sorted);
        }

        function formatNamesBold(text, players) {
            players.forEach(p => { text = text.replace(new RegExp(`(${capitalizeFirstLetter(p.name)})`, 'gi'), `<strong class="text-accent font-semibold text-[11px]">$1</strong>`); });
            return text;
        }

        async function callAIForResult(sortedPlayers) {
            let aiBox = document.getElementById('result-ai-comment');
            aiBox.innerHTML = '<span class="animate-pulse">AI đang phân tích kết quả chi tiết...</span>';
            let pData = JSON.stringify(sortedPlayers.map(p => ({name: capitalizeFirstLetter(p.name), score: p.score})));
            let prompt = `Bạn là một chuyên gia phân tích hiện đại, dí dỏm, thực tế và xéo xắt. Dữ liệu: ${pData}. Yêu cầu: Nhận xét chi tiết kết quả từng người theo phong cách CÀ KHỊA, HỎI ĐỂU VÀ ĐƯA RA LỜI KHUYÊN THỰC TẾ. QUAN TRỌNG: Viết cho TỪNG người chơi 1 đoạn văn chi tiết gồm 2-3 câu. Tách mỗi người 1 dòng (hoặc đoạn). TUYỆT ĐỐI KHÔNG dùng từ ngữ kiếm hiệp hay tu tiên!`;
            try {
                let res = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-lite:generateContent?key=${apiKeyGemini}`, {
                    method: 'POST', headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
                });
                let data = await res.json();
                aiBox.innerHTML = formatNamesBold(data.candidates[0].content.parts[0].text.replace(/\n/g, '<br><br>'), sortedPlayers);
            } catch (e) { aiBox.innerHTML = "Lỗi kết nối AI!"; }
        }

        let myChart = null;
        function openChartModal() {
            if (!window.roomData) return;
            openModal('chart-modal');
            let ctx = document.getElementById('scoreChart').getContext('2d'), players = window.roomData.players, rounds = window.roomData.rounds || [];
            let labels = Array.from({length: rounds.length + 1}, (_, i) => i.toString()), chartData = players.map(() => [0]); 
            
            rounds.forEach(r => {
                let totalLost = 0;
                r.forEach(v => { if (v !== undefined && v !== '-' && v !== '0') totalLost += Math.abs(parseInt(v)); });
                players.forEach((_, i) => {
                    let v = r[i];
                    let cur = chartData[i][chartData[i].length - 1];
                    if (v === undefined || v === '-') chartData[i].push(cur); 
                    else if (v === '0') chartData[i].push(cur + totalLost); 
                    else chartData[i].push(cur - Math.abs(parseInt(v)));
                });
            });

            let datasets = players.map((p, idx) => {
                let colors = ['#818cf8', '#f472b6', '#60a5fa', '#34d399', '#fbbf24'];
                return { label: capitalizeFirstLetter(p.name), data: chartData[idx], borderColor: colors[idx], backgroundColor: colors[idx], tension: 0.4, borderWidth: 2 };
            });

            let lineColor = getComputedStyle(document.body).getPropertyValue('--text-secondary').trim() || '#94a3b8';
            let gridColor = getComputedStyle(document.body).getPropertyValue('--glass-border').trim() || 'rgba(255, 255, 255, 0.1)';

            if(myChart) myChart.destroy();
            myChart = new Chart(ctx, {
                type: 'line', data: { labels: labels, datasets: datasets },
                options: { 
                    responsive: true, maintainAspectRatio: false, 
                    scales: { y: { grid: { color: gridColor }, ticks: { color: lineColor, maxTicksLimit: 5, font: { size: 9, weight: '300' } } }, x: { grid: { display: false }, ticks: { color: lineColor, font: { size: 9, weight: '300' } } } }, 
                    plugins:{ legend: { labels: { color: lineColor, font: { size: 10, weight: '300' }, usePointStyle: true, boxWidth: 6 } } } 
                }
            });
            callAIForChart(players, rounds);
        }

        async function callAIForChart(players, rounds) {
            let chartAiBox = document.getElementById('chart-ai-comment');
            chartAiBox.innerHTML = '<span class="animate-pulse">AI đang phân tích biểu đồ chuyên sâu...</span>';
            let pData = JSON.stringify(players.map(p => ({name: capitalizeFirstLetter(p.name), score: p.score})));
            let prompt = `Bạn là BLV chuyên soi biểu đồ hiện đại. Dữ liệu sau ${rounds.length} ván: ${pData}. Yêu cầu: Bình luận chi tiết, phân tích sâu về đường dốc phong độ của từng người bằng văn phong HỎI ĐỂU, CÀ KHỊA VÀ KHUYÊN NHỦ HÀI HƯỚC. Viết cho mỗi người 1 đoạn gồm 2-3 câu. Tách mỗi người 1 đoạn rõ ràng. Tuyệt đối không dùng từ kiếm hiệp.`;
            try {
                let res = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-lite:generateContent?key=${apiKeyGemini}`, {
                    method: 'POST', headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
                });
                let data = await res.json();
                chartAiBox.innerHTML = formatNamesBold(data.candidates[0].content.parts[0].text.replace(/\n/g, '<br><br>'), players);
            } catch (e) { chartAiBox.innerHTML = "Lỗi kết nối AI!"; }
        }

        async function callAI() {
            openModal('ai-modal');
            document.getElementById('ai-content').innerHTML = "<span class='animate-pulse text-accent font-light'>AI đang nghĩ nhận xét thật dài...</span>";
            let players = window.roomData.players, rounds = window.roomData.rounds || [];
            let pData = JSON.stringify(players.map(p => ({name: capitalizeFirstLetter(p.name), score: p.score})));
            let prompt = `Bạn là một AI cà khịa bá đạo. Dữ liệu sau ${rounds.length} ván: ${pData}. Yêu cầu: Viết 1 đoạn văn gồm 2-3 câu chi tiết kết hợp cà khịa + hỏi đẩu + đưa lời khuyên thực tế lầy lội cho TỪNG NGƯỜI CHƠI. Nhận xét dài, phân tích tình huống hài hước. Tách mỗi người 1 đoạn rõ ràng. Hiện đại, hài hước, tuyệt đối không dùng phong cách kiếm hiệp/tu tiên!`;
            try {
                let res = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-lite:generateContent?key=${apiKeyGemini}`, {
                    method: 'POST', headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
                });
                let data = await res.json();
                document.getElementById('ai-content').innerHTML = formatNamesBold(data.candidates[0].content.parts[0].text.replace(/\n/g, '<br><br>'), players);
            } catch (e) { document.getElementById('ai-content').innerHTML = "Lỗi kết nối AI!"; }
        }
    </script>
</body>
</html>
