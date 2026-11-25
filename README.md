<!DOCTYPE html>
<html lang="id" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QurbanKu - Aplikasi Perencana & Tracker Tabungan Qurban</title>
    
    <!-- Google Fonts: Plus Jakarta Sans & Amiri (untuk sentuhan kaligrafi/Islami) -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <!-- Chart.js untuk Grafik Perkembangan Tabungan -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <!-- Konfigurasi Tailwind Custom Colors & Fonts -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['"Plus Jakarta Sans"', 'sans-serif'],
                        arabic: ['"Amiri"', 'serif'],
                    },
                    colors: {
                        emerald: {
                            50: '#ecfdf5',
                            100: '#d1fae5',
                            200: '#a7f3d0',
                            300: '#6ee7b7',
                            400: '#34d399',
                            500: '#10b981',
                            600: '#059669',
                            700: '#047857',
                            800: '#065f46',
                            900: '#064e3b',
                            950: '#022c22',
                        },
                        gold: {
                            50: '#fffbeb',
                            100: '#fef3c7',
                            200: '#fde68a',
                            300: '#fcd34d',
                            400: '#fbbf24',
                            500: '#f59e0b',
                            600: '#d97706',
                            700: '#b45309',
                        }
                    }
                }
            }
        }
    </script>

    <style>
        /* Pola Geometri Islami Halus untuk Latar Belakang Hero */
        .islamic-pattern {
            background-color: #064e3b;
            background-image: radial-gradient(#10b981 0.75px, transparent 0.75px), radial-gradient(#d97706 0.75px, #064e3b 0.75px);
            background-size: 30px 30px;
            background-position: 0 0, 15px 15px;
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #059669;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #047857;
        }

        /* Styling khusus saat mode Cetak / Print Laporan */
        @media print {
            body {
                background: white !important;
                color: black !important;
            }
            .no-print {
                display: none !important;
            }
            .print-only {
                display: block !important;
            }
            .shadow-lg, .shadow-md, .shadow-xl {
                box-shadow: none !important;
                border: 1px solid #e2e8f0 !important;
            }
            header, footer {
                display: none !important;
            }
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 antialiased selection:bg-emerald-600 selection:text-white">

    <!-- ==================== NOTIFIKASI TOAST & MODAL KUSTOM (PENGGANTI ALERT/CONFIRM) ==================== -->
    <div id="toast-container" class="fixed top-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <!-- Modal Konfirmasi Kustom -->
    <div id="confirm-modal" class="fixed inset-0 z-50 hidden items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4 no-print">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl border border-slate-100 transform transition-all scale-95 opacity-0" id="confirm-modal-box">
            <div class="flex items-center gap-3 text-amber-600 mb-3">
                <div class="w-10 h-10 rounded-full bg-amber-100 flex items-center justify-center text-lg">
                    <i class="fa-solid fa-triangle-exclamation"></i>
                </div>
                <h3 class="text-lg font-bold text-slate-800" id="confirm-title">Konfirmasi Tindakan</h3>
            </div>
            <p class="text-slate-600 text-sm mb-6 leading-relaxed" id="confirm-message">Apakah Anda yakin ingin melanjutkan tindakan ini?</p>
            <div class="flex justify-end gap-3">
                <button id="confirm-cancel-btn" class="px-4 py-2.5 rounded-xl text-sm font-semibold text-slate-600 hover:bg-slate-100 transition">Batal</button>
                <button id="confirm-ok-btn" class="px-4 py-2.5 rounded-xl text-sm font-semibold bg-rose-600 text-white hover:bg-rose-700 shadow-sm transition">Ya, Lanjutkan</button>
            </div>
        </div>
    </div>

    <!-- ==================== HEADER / NAVBAR ==================== -->
    <header class="sticky top-0 z-40 bg-white/90 backdrop-blur-md border-b border-emerald-100 shadow-sm no-print">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between items-center h-16">
                <!-- Brand Logo -->
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-emerald-700 to-emerald-900 flex items-center justify-center text-gold-400 shadow-md">
                        <i class="fa-solid fa-kaaba text-lg"></i>
                    </div>
                    <div>
                        <span class="font-extrabold text-xl tracking-tight text-emerald-900">Qurban<span class="text-gold-600">Ku</span></span>
                        <span class="hidden sm:inline-block text-[11px] font-medium bg-emerald-50 text-emerald-700 px-2 py-0.5 rounded-full ml-2 border border-emerald-200">1448 H / 2027</span>
                    </div>
                </div>

                <!-- Navigasi Desktop -->
                <nav class="hidden md:flex items-center gap-6 text-sm font-semibold text-slate-600">
                    <a href="#pilih-hewan" class="hover:text-emerald-700 transition">Target Hewan</a>
                    <a href="#tracker-tabungan" class="hover:text-emerald-700 transition">Tracker & Grafik</a>
                    <a href="#patungan-sapi" class="hover:text-emerald-700 transition">Patungan 1/7</a>
                    <a href="#panduan-qurban" class="hover:text-emerald-700 transition">Panduan Qurban</a>
                </nav>

                <!-- Tombol Aksi Laporan & Reset -->
                <div class="flex items-center gap-2">
                    <button onclick="exportReport()" title="Cetak atau Simpan PDF Laporan" class="flex items-center gap-2 px-3.5 py-2 rounded-xl bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs sm:text-sm font-semibold border border-emerald-200 transition">
                        <i class="fa-solid fa-print text-emerald-600"></i>
                        <span class="hidden sm:inline">Laporan</span>
                    </button>
                    <button onclick="confirmResetDemo()" title="Reset Data Demo" class="flex items-center gap-2 px-3 py-2 rounded-xl bg-slate-100 hover:bg-rose-50 text-slate-600 hover:text-rose-600 text-xs sm:text-sm font-semibold border border-slate-200 hover:border-rose-200 transition">
                        <i class="fa-solid fa-rotate-left"></i>
                        <span class="hidden lg:inline">Reset Demo</span>
                    </button>
                </div>
            </div>
        </div>
    </header>

    <!-- ==================== 1. HERO SECTION & COUNTDOWN IDUL ADHA ==================== -->
    <section class="relative islamic-pattern text-white overflow-hidden">
        <!-- Dekorasi Cahaya -->
        <div class="absolute inset-0 bg-gradient-to-br from-emerald-950/95 via-emerald-900/90 to-emerald-800/85"></div>
        <div class="absolute -top-24 -right-24 w-96 h-96 bg-gold-500/10 rounded-full blur-3xl pointer-events-none"></div>
        <div class="absolute -bottom-24 -left-24 w-96 h-96 bg-emerald-400/10 rounded-full blur-3xl pointer-events-none"></div>

        <div class="relative max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-10 lg:py-14">
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-center">
                
                <!-- Kolom Kiri: Salam, Deskripsi & Countdown -->
                <div class="lg:col-span-7 space-y-6">
                    <div class="inline-flex items-center gap-2 px-3.5 py-1.5 rounded-full bg-emerald-800/80 border border-emerald-600/50 text-gold-300 text-xs sm:text-sm font-medium">
                        <i class="fa-solid fa-moon"></i>
                        <span>Persiapan Ibadah Qurban Idul Adha 1448 H</span>
                    </div>

                    <div>
                        <p class="font-arabic text-2xl sm:text-3xl text-gold-400 mb-1">بِسْمِ اللَّهِ الرَّحْمَنِ الرَّحِيم</p>
                        <h1 class="text-3xl sm:text-4xl lg:text-5xl font-extrabold tracking-tight leading-tight">
                            Wujudkan Niat Suci <span class="text-transparent bg-clip-text bg-gradient-to-r from-gold-300 via-gold-400 to-amber-300">Berqurban Tahun Ini</span>
                        </h1>
                        <p class="mt-3 text-emerald-100/90 text-sm sm:text-base max-w-2xl leading-relaxed">
                            Rencanakan tabungan qurban Anda secara terukur, pantau setoran harian hingga bulanan, dan kelola patungan qurban sapi bersama keluarga atau sahabat dengan transparan.
                        </p>
                    </div>

                    <!-- Countdown Timer Box -->
                    <div class="bg-emerald-950/70 backdrop-blur-md border border-emerald-700/60 rounded-2xl p-4 sm:p-5 max-w-xl">
                        <div class="flex flex-wrap items-center justify-between gap-2 mb-3">
                            <span class="text-xs font-semibold uppercase tracking-wider text-emerald-300 flex items-center gap-1.5">
                                <i class="fa-regular fa-calendar-check text-gold-400"></i>
                                Estimasi Idul Adha (17 Mei 2027)
                            </span>
                            <!-- Input Ubah Tanggal Estimasi jika diperlukan -->
                            <div class="flex items-center gap-1.5 no-print">
                                <label for="target-date-input" class="text-[11px] text-emerald-300">Ubah Tgl:</label>
                                <input type="date" id="target-date-input" value="2027-05-17" onchange="updateTargetDate(this.value)" class="bg-emerald-900 text-white text-xs rounded-lg px-2 py-1 border border-emerald-700 focus:outline-none focus:border-gold-400">
                            </div>
                        </div>
                        
                        <div class="grid grid-cols-4 gap-2 sm:gap-4 text-center">
                            <div class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2">
                                <span id="cd-days" class="block text-2xl sm:text-3xl font-extrabold text-gold-400">000</span>
                                <span class="text-[11px] sm:text-xs text-emerald-200 font-medium">Hari</span>
                            </div>
                            <div class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2">
                                <span id="cd-hours" class="block text-2xl sm:text-3xl font-extrabold text-white">00</span>
                                <span class="text-[11px] sm:text-xs text-emerald-200 font-medium">Jam</span>
                            </div>
                            <div class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2">
                                <span id="cd-minutes" class="block text-2xl sm:text-3xl font-extrabold text-white">00</span>
                                <span class="text-[11px] sm:text-xs text-emerald-200 font-medium">Menit</span>
                            </div>
                            <div class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2">
                                <span id="cd-seconds" class="block text-2xl sm:text-3xl font-extrabold text-emerald-300">00</span>
                                <span class="text-[11px] sm:text-xs text-emerald-200 font-medium">Detik</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Kolom Kanan: Kartu Ringkasan Saldo & Progres Utama -->
                <div class="lg:col-span-5">
                    <div class="bg-white text-slate-800 rounded-3xl p-6 sm:p-7 shadow-2xl border-2 border-gold-400/50 relative overflow-hidden">
                        <div class="absolute top-0 right-0 w-32 h-32 bg-gradient-to-bl from-gold-100 to-transparent rounded-bl-full opacity-70 pointer-events-none"></div>
                        
                        <div class="flex items-center justify-between mb-4">
                            <div>
                                <span class="text-xs font-bold uppercase tracking-wider text-emerald-700 bg-emerald-50 px-2.5 py-1 rounded-lg">Ringkasan Tabungan</span>
                                <h2 id="hero-animal-name" class="text-lg font-bold text-slate-900 mt-2">Sapi 1/7 (Patungan)</h2>
                            </div>
                            <div class="w-12 h-12 rounded-2xl bg-gold-100 text-gold-600 flex items-center justify-center text-2xl shadow-inner">
                                <i id="hero-animal-icon" class="fa-solid fa-cow"></i>
                            </div>
                        </div>

                        <div class="my-5">
                            <p class="text-xs text-slate-500 font-medium">Total Dana Terkumpul</p>
                            <div class="flex items-baseline gap-2 mt-1">
                                <span id="hero-total-saved" class="text-3xl sm:text-4xl font-extrabold text-emerald-800 tracking-tight">Rp 0</span>
                            </div>
                            <div class="flex flex-wrap items-center gap-2 mt-1">
                                <p class="text-xs text-slate-500">
                                    dari target <span id="hero-target-price" class="font-bold text-slate-700">Rp 3.000.000</span>
                                </p>
                                <button type="button" onclick="openQuickTargetEdit()" class="text-[11px] font-bold text-emerald-700 hover:text-emerald-900 bg-emerald-50 hover:bg-emerald-100 px-2 py-0.5 rounded-md border border-emerald-200 transition no-print">
                                    <i class="fa-solid fa-pen-to-square mr-1"></i>Ubah Target
                                </button>
                            </div>
                        </div>

                        <!-- Progress Bar -->
                        <div class="space-y-2 mb-5">
                            <div class="flex justify-between text-xs font-bold">
                                <span class="text-emerald-700" id="hero-progress-pct">0% Tercapai</span>
                                <span class="text-amber-600" id="hero-remaining-text">Kurang Rp 3.200.000</span>
                            </div>
                            <div class="w-full h-3.5 bg-slate-100 rounded-full overflow-hidden p-0.5 border border-slate-200">
                                <div id="hero-progress-bar" class="h-full bg-gradient-to-r from-emerald-600 via-emerald-500 to-gold-500 rounded-full transition-all duration-700 ease-out" style="width: 0%"></div>
                            </div>
                        </div>

                        <!-- Quick Action Button -->
                        <div class="flex gap-3 no-print">
                            <a href="#form-setoran-card" onclick="focusDepositInput()" class="flex-1 bg-gradient-to-r from-emerald-700 to-emerald-800 hover:from-emerald-800 hover:to-emerald-900 text-white font-bold py-3 px-4 rounded-xl text-sm text-center shadow-md shadow-emerald-900/20 transition flex items-center justify-center gap-2">
                                <i class="fa-solid fa-circle-plus text-gold-400"></i>
                                Catat Setoran
                            </a>
                            <a href="#kalkulator-section" class="px-4 py-3 rounded-xl bg-gold-50 hover:bg-gold-100 text-gold-700 font-bold text-sm border border-gold-200 transition flex items-center justify-center" title="Lihat Rencana Nabung">
                                <i class="fa-solid fa-calculator"></i>
                            </a>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </section>

    <!-- ==================== MAIN CONTENT CONTAINER ==================== -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-10 space-y-12">

        <!-- ==================== 2. PILIHAN TARGET HEWAN QURBAN ==================== -->
        <section id="pilih-hewan" class="scroll-mt-20">
            <div class="flex flex-col sm:flex-row sm:items-end justify-between mb-6 gap-2">
                <div>
                    <span class="text-xs font-bold uppercase tracking-wider text-gold-600">Langkah 1 • Tentukan Niat & Target</span>
                    <h2 class="text-2xl sm:text-3xl font-extrabold text-slate-900 mt-1">Pilih Target Hewan Qurban</h2>
                </div>
                <p class="text-sm text-slate-500">Pilih paket hewan qurban atau klik <strong>Ubah Nominal</strong> untuk menyesuaikan harga daerah Anda.</p>
            </div>

            <!-- Grid 2 Kartu Pilihan Hewan Simetris -->
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6" id="animal-cards-container">
                <!-- Di-render secara dinamis via JavaScript agar interaktif -->
            </div>

            <!-- Panel Sesuaikan Nominal Target Aktif (Cepat) -->
            <div id="quick-target-panel" class="mt-5 bg-white border border-emerald-200 rounded-2xl p-4 sm:p-5 shadow-sm flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 no-print">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 rounded-xl bg-gold-50 text-gold-600 border border-gold-200 flex items-center justify-center text-base shrink-0">
                        <i class="fa-solid fa-pen-ruler"></i>
                    </div>
                    <div>
                        <h3 class="font-bold text-sm text-slate-900">Sesuaikan Nominal Target Aktif (<span id="quick-active-animal-label" class="text-emerald-700">Sapi 1/7</span>)</h3>
                        <p class="text-xs text-slate-500">Ubah nominal target paket aktif kapan saja sesuai kesepakatan panitia qurban Anda.</p>
                    </div>
                </div>
                <div class="flex items-center gap-2.5 w-full sm:w-auto">
                    <div class="relative flex-1 sm:w-48">
                        <span class="absolute left-3.5 top-2.5 text-xs font-bold text-emerald-700">Rp</span>
                        <input type="number" id="quick-active-price-input" min="100000" step="50000" placeholder="3000000" class="w-full pl-9 pr-3 py-2 rounded-xl border border-slate-300 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500">
                    </div>
                    <button type="button" onclick="applyQuickActivePrice()" class="px-4 py-2 rounded-xl bg-emerald-700 hover:bg-emerald-800 text-white font-bold text-xs sm:text-sm transition shadow-sm shrink-0">
                        Simpan Nominal
                    </button>
                </div>
            </div>
        </section>

        <!-- ==================== 3. KALKULATOR & PERENCANA SETORAN OTOMATIS ==================== -->
        <section id="kalkulator-section" class="scroll-mt-20">
            <div class="bg-gradient-to-br from-emerald-900 to-emerald-950 rounded-3xl p-6 sm:p-8 text-white shadow-xl relative overflow-hidden">
                <div class="absolute right-0 top-0 opacity-10 text-9xl pointer-events-none -mr-6 -mt-6">
                    <i class="fa-solid fa-mosque"></i>
                </div>

                <div class="relative z-10">
                    <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 pb-6 border-b border-emerald-800">
                        <div>
                            <span class="text-xs font-bold uppercase tracking-wider text-gold-400">Langkah 2 • Strategi Menabung</span>
                            <h2 class="text-2xl font-extrabold mt-1">Rencana Setoran Otomatis Menuju Idul Adha</h2>
                            <p class="text-sm text-emerald-200 mt-1">
                                Dihitung otomatis dari sisa kekurangan dana (<span id="calc-remaining-label" class="font-bold text-white">Rp 0</span>) dibagi sisa waktu (<span id="calc-days-left-label" class="font-bold text-gold-300">0 hari</span>).
                            </p>
                        </div>
                        <div class="bg-emerald-800/80 border border-emerald-700 px-4 py-2.5 rounded-2xl text-xs">
                            <span class="text-emerald-300 block">Status Target:</span>
                            <span id="calc-status-badge" class="font-bold text-sm text-gold-400">Sedang Berjalan</span>
                        </div>
                    </div>

                    <!-- 3 Kartu Rekomendasi Setoran: Harian, Mingguan, Bulanan -->
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-5 mt-6">
                        <!-- Harian -->
                        <div class="bg-white/10 backdrop-blur-sm border border-emerald-700/70 rounded-2xl p-5 hover:bg-white/15 transition">
                            <div class="flex items-center justify-between mb-3">
                                <span class="text-xs font-bold uppercase tracking-wider text-emerald-300">Opsi 1 • Setoran Harian</span>
                                <span class="w-8 h-8 rounded-lg bg-emerald-800 text-gold-400 flex items-center justify-center text-sm">
                                    <i class="fa-solid fa-sun"></i>
                                </span>
                            </div>
                            <div class="text-2xl sm:text-3xl font-extrabold text-white" id="plan-daily">Rp 0</div>
                            <p class="text-xs text-emerald-200 mt-1.5">Per hari selama <span id="plan-days-count" class="font-semibold text-white">0</span> hari ke depan</p>
                            <button onclick="quickFillDeposit('daily')" class="mt-4 w-full py-2 rounded-xl bg-emerald-700/80 hover:bg-emerald-600 text-xs font-semibold text-white border border-emerald-600 transition no-print">
                                Gunakan Nominal Ini
                            </button>
                        </div>

                        <!-- Mingguan -->
                        <div class="bg-white/10 backdrop-blur-sm border-2 border-gold-400/70 rounded-2xl p-5 relative hover:bg-white/15 transition">
                            <span class="absolute -top-3 right-4 bg-gold-500 text-emerald-950 text-[10px] font-extrabold uppercase px-2.5 py-0.5 rounded-full">Paling Populer</span>
                            <div class="flex items-center justify-between mb-3">
                                <span class="text-xs font-bold uppercase tracking-wider text-gold-300">Opsi 2 • Setoran Mingguan</span>
                                <span class="w-8 h-8 rounded-lg bg-gold-500/20 text-gold-400 flex items-center justify-center text-sm">
                                    <i class="fa-solid fa-calendar-week"></i>
                                </span>
                            </div>
                            <div class="text-2xl sm:text-3xl font-extrabold text-gold-400" id="plan-weekly">Rp 0</div>
                            <p class="text-xs text-emerald-200 mt-1.5">Per pekan (tiap Jumat) selama <span id="plan-weeks-count" class="font-semibold text-white">0</span> pekan</p>
                            <button onclick="quickFillDeposit('weekly')" class="mt-4 w-full py-2 rounded-xl bg-gold-500 hover:bg-gold-400 text-xs font-bold text-emerald-950 transition shadow-sm no-print">
                                Gunakan Nominal Ini
                            </button>
                        </div>

                        <!-- Bulanan -->
                        <div class="bg-white/10 backdrop-blur-sm border border-emerald-700/70 rounded-2xl p-5 hover:bg-white/15 transition">
                            <div class="flex items-center justify-between mb-3">
                                <span class="text-xs font-bold uppercase tracking-wider text-emerald-300">Opsi 3 • Setoran Bulanan</span>
                                <span class="w-8 h-8 rounded-lg bg-emerald-800 text-gold-400 flex items-center justify-center text-sm">
                                    <i class="fa-solid fa-calendar-days"></i>
                                </span>
                            </div>
                            <div class="text-2xl sm:text-3xl font-extrabold text-white" id="plan-monthly">Rp 0</div>
                            <p class="text-xs text-emerald-200 mt-1.5">Per bulan (saat gajian) selama <span id="plan-months-count" class="font-semibold text-white">0</span> bulan</p>
                            <button onclick="quickFillDeposit('monthly')" class="mt-4 w-full py-2 rounded-xl bg-emerald-700/80 hover:bg-emerald-600 text-xs font-semibold text-white border border-emerald-600 transition no-print">
                                Gunakan Nominal Ini
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- ==================== 4. TRACKER PROGRES TABUNGAN INTERAKTIF & GRAFIK ==================== -->
        <section id="tracker-tabungan" class="scroll-mt-20 space-y-6">
            <div>
                <span class="text-xs font-bold uppercase tracking-wider text-gold-600">Langkah 3 • Catat & Pantau</span>
                <h2 class="text-2xl sm:text-3xl font-extrabold text-slate-900 mt-1">Tracker Progres & Riwayat Setoran</h2>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                
                <!-- Kolom Kiri (5 Kolom): Form Tambah Setoran -->
                <div class="lg:col-span-5 space-y-6 no-print" id="form-setoran-card">
                    <div class="bg-white rounded-3xl p-6 shadow-md border border-slate-200/80">
                        <div class="flex items-center gap-3 mb-5">
                            <div class="w-10 h-10 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center text-lg">
                                <i class="fa-solid fa-piggy-bank"></i>
                            </div>
                            <div>
                                <h3 class="font-bold text-lg text-slate-900">Tambah Setoran Baru</h3>
                                <p class="text-xs text-slate-500">Data tersimpan otomatis di browser Anda</p>
                            </div>
                        </div>

                        <!-- Kartu Informasi Rekening Tujuan Transfer -->
                        <div class="mb-5 p-4 rounded-2xl bg-gradient-to-br from-emerald-50 to-gold-50/60 border border-emerald-200/90 relative overflow-hidden">
                            <div class="flex items-center justify-between gap-2 mb-2">
                                <span class="text-[11px] font-extrabold uppercase tracking-wider text-emerald-800 flex items-center gap-1.5">
                                    <i class="fa-solid fa-building-columns text-gold-600"></i>
                                    Rekening Tujuan Transfer
                                </span>
                                <span class="text-[10px] font-bold px-2 py-0.5 rounded-full bg-white text-emerald-700 border border-emerald-200">BSI (Kode: 451)</span>
                            </div>
                            <div class="flex items-center justify-between gap-2 bg-white/90 px-3.5 py-2.5 rounded-xl border border-emerald-100">
                                <div>
                                    <p id="bank-account-number" class="font-mono font-extrabold text-base sm:text-lg text-slate-900 tracking-wide">7123456789</p>
                                    <p class="text-xs text-slate-600 font-medium">a.n. <strong class="text-emerald-900">Panitia QurbanKu</strong></p>
                                </div>
                                <button type="button" onclick="copyBankAccount()" class="px-3 py-2 rounded-xl bg-emerald-700 hover:bg-emerald-800 active:scale-95 text-white text-xs font-bold transition flex items-center gap-1.5 shrink-0 shadow-sm">
                                    <i class="fa-regular fa-copy"></i>
                                    <span>Salin Rekening</span>
                                </button>
                            </div>
                            <p class="text-[11px] text-slate-500 mt-2 leading-relaxed">
                                <i class="fa-solid fa-circle-info text-emerald-600 mr-1"></i>
                                Silakan transfer ke rekening di atas, lalu catat nama penyetor & nominalnya di bawah ini.
                            </p>
                        </div>

                        <form id="deposit-form" onsubmit="handleAddDeposit(event)" class="space-y-4">
                            <!-- Nama Penyetor (Wajib) -->
                            <div>
                                <label for="deposit-sender" class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">
                                    Nama Penyetor <span class="text-rose-600">*</span>
                                </label>
                                <div class="relative">
                                    <span class="absolute left-3.5 top-2.5 text-sm text-emerald-700">
                                        <i class="fa-solid fa-user"></i>
                                    </span>
                                    <input type="text" id="deposit-sender" required placeholder="Masukkan nama lengkap penyetor" class="w-full pl-10 pr-4 py-2.5 rounded-xl border border-slate-300 text-sm font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500">
                                </div>
                            </div>

                            <!-- Nominal Setoran -->
                            <div>
                                <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Nominal Setoran (Rp) <span class="text-rose-600">*</span></label>
                                <div class="relative">
                                    <span class="absolute left-4 top-3 text-sm font-bold text-emerald-700">Rp</span>
                                    <input type="number" id="deposit-amount" required min="1000" step="1000" placeholder="Contoh: 250000" class="w-full pl-11 pr-4 py-2.5 rounded-xl border border-slate-300 text-base font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500">
                                </div>
                                <!-- Tombol Cepat Nominal -->
                                <div class="flex flex-wrap gap-1.5 mt-2">
                                    <button type="button" onclick="setDepositAmount(50000)" class="text-xs px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-100 text-slate-700 hover:text-emerald-800 font-semibold transition">+50rb</button>
                                    <button type="button" onclick="setDepositAmount(100000)" class="text-xs px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-100 text-slate-700 hover:text-emerald-800 font-semibold transition">+100rb</button>
                                    <button type="button" onclick="setDepositAmount(250000)" class="text-xs px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-100 text-slate-700 hover:text-emerald-800 font-semibold transition">+250rb</button>
                                    <button type="button" onclick="setDepositAmount(500000)" class="text-xs px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-100 text-slate-700 hover:text-emerald-800 font-semibold transition">+500rb</button>
                                </div>
                            </div>

                            <!-- Tanggal Setoran -->
                            <div>
                                <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Tanggal Setoran</label>
                                <input type="date" id="deposit-date" required class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-sm font-medium text-slate-800 focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            </div>

                            <!-- Metode Simpan -->
                            <div>
                                <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Metode Pembayaran</label>
                                <select id="deposit-method" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-sm font-medium text-slate-800 bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500">
                                    <option value="Transfer Bank">Transfer Bank</option>
                                    <option value="E-Wallet / QRIS">E-Wallet / QRIS (GoPay / OVO / Dana)</option>
                                    <option value="Setor Tunai">Setor Tunai</option>
                                </select>
                            </div>

                            <!-- Catatan -->
                            <div>
                                <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Catatan / Niat (Opsional)</label>
                                <input type="text" id="deposit-note" placeholder="Misal: Setoran gaji bulan ini / Sedekah Jumat" class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-sm text-slate-800 focus:outline-none focus:ring-2 focus:ring-emerald-500">
                            </div>

                            <button type="submit" class="w-full py-3.5 px-5 rounded-xl bg-gradient-to-r from-emerald-700 to-emerald-800 hover:from-emerald-800 hover:to-emerald-900 text-white font-bold text-sm shadow-lg shadow-emerald-900/15 transition flex items-center justify-center gap-2">
                                <i class="fa-solid fa-check-circle text-gold-400"></i>
                                Simpan Setoran Qurban
                            </button>
                        </form>
                    </div>
                </div>

                <!-- Kolom Kanan (7 Kolom): Grafik & Riwayat Transaksi -->
                <div class="lg:col-span-7 space-y-6">
                    
                    <!-- Grafik Perkembangan Tabungan Bulanan -->
                    <div class="bg-white rounded-3xl p-6 shadow-md border border-slate-200/80">
                        <div class="flex flex-wrap items-center justify-between gap-2 mb-4">
                            <div>
                                <h3 class="font-bold text-lg text-slate-900">Grafik Akumulasi Tabungan</h3>
                                <p class="text-xs text-slate-500">Visualisasi pertumbuhan saldo tabungan qurban dari waktu ke waktu</p>
                            </div>
                            <span class="text-xs font-semibold px-3 py-1 rounded-full bg-emerald-50 text-emerald-700 border border-emerald-200">
                                Target: <span id="chart-target-label">Rp 3.200.000</span>
                            </span>
                        </div>
                        <div class="relative h-60 w-full">
                            <canvas id="savingsChart"></canvas>
                        </div>
                    </div>

                    <!-- Riwayat Transaksi Setoran -->
                    <div class="bg-white rounded-3xl p-6 shadow-md border border-slate-200/80">
                        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-4">
                            <div>
                                <h3 class="font-bold text-lg text-slate-900">Riwayat Setoran (<span id="tx-count">0</span>)</h3>
                                <p class="text-xs text-slate-500">Daftar catatan setoran beserta nama penyetor</p>
                            </div>

                            <!-- Pencarian Nama & Filter Metode -->
                            <div class="flex flex-wrap items-center gap-2 no-print">
                                <div class="relative">
                                    <i class="fa-solid fa-magnifying-glass text-slate-400 text-xs absolute left-3 top-2.5"></i>
                                    <input type="text" id="search-sender" oninput="renderTransactions()" placeholder="Cari nama penyetor..." class="text-xs font-medium border border-slate-200 rounded-xl pl-8 pr-3 py-2 bg-slate-50 text-slate-700 focus:outline-none focus:border-emerald-600 w-40 sm:w-44">
                                </div>
                                <select id="filter-method" onchange="renderTransactions()" class="text-xs font-semibold border border-slate-200 rounded-xl px-3 py-2 bg-slate-50 text-slate-700 focus:outline-none focus:border-emerald-600">
                                    <option value="ALL">Semua Metode</option>
                                    <option value="Transfer Bank">Transfer Bank</option>
                                    <option value="E-Wallet / QRIS">E-Wallet / QRIS</option>
                                    <option value="Setor Tunai">Setor Tunai</option>
                                </select>
                            </div>
                        </div>

                        <!-- Tabel / List Riwayat -->
                        <div class="overflow-x-auto max-h-80 overflow-y-auto pr-1">
                            <table class="w-full text-left border-collapse">
                                <thead>
                                    <tr class="border-b border-slate-200 text-[11px] font-bold uppercase tracking-wider text-slate-400">
                                        <th class="py-2.5 px-3">Tanggal</th>
                                        <th class="py-2.5 px-3">Penyetor</th>
                                        <th class="py-2.5 px-3">Metode & Catatan</th>
                                        <th class="py-2.5 px-3 text-right">Nominal</th>
                                        <th class="py-2.5 px-2 text-center no-print">Aksi</th>
                                    </tr>
                                </thead>
                                <tbody id="transaction-list-body" class="divide-y divide-slate-100 text-sm">
                                    <!-- Diisi via JavaScript -->
                                </tbody>
                            </table>
                        </div>
                    </div>

                </div>
            </div>
        </section>

        <!-- ==================== 5. FITUR KELOMPOK PATUNGAN SAPI (1/7) ==================== -->
        <section id="patungan-sapi" class="scroll-mt-20">
            <div class="bg-white rounded-3xl p-6 sm:p-8 shadow-md border border-slate-200/80">
                <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-4 pb-6 border-b border-slate-100">
                    <div>
                        <div class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-gold-50 text-gold-700 text-xs font-bold border border-gold-200 mb-2">
                            <i class="fa-solid fa-users"></i>
                            <span>Fitur Khusus Qurban Kolektif</span>
                        </div>
                        <h2 class="text-2xl font-extrabold text-slate-900">Kelompok Patungan Sapi (Maksimal 7 Shohibul Qurban)</h2>
                        <p class="text-sm text-slate-500 mt-1">Kelola daftar peserta patungan 1 ekor sapi lengkap dengan status setoran masing-masing anggota.</p>
                    </div>

                    <!-- Atur Harga 1 Ekor Sapi Patungan -->
                    <div class="bg-slate-50 p-3.5 rounded-2xl border border-slate-200 flex flex-wrap items-center gap-3">
                        <div>
                            <label class="block text-[11px] font-bold text-slate-500 uppercase">Harga 1 Ekor Sapi Utuh</label>
                            <div class="flex items-center gap-2 mt-1">
                                <span class="text-xs font-bold text-slate-600">Rp</span>
                                <input type="number" id="patungan-total-cow-price" value="22400000" step="100000" onchange="updatePatunganCowPrice(this.value)" class="w-36 px-2.5 py-1 rounded-lg border border-slate-300 text-sm font-bold text-emerald-800 bg-white focus:outline-none focus:border-emerald-600">
                            </div>
                        </div>
                        <div class="border-l border-slate-200 pl-3">
                            <span class="block text-[11px] font-bold text-slate-500 uppercase">Iuran Per Orang (1/7)</span>
                            <span id="patungan-per-person-price" class="text-base font-extrabold text-gold-600">Rp 3.200.000</span>
                        </div>
                    </div>
                </div>

                <!-- Progres Kolektif Kelompok Patungan -->
                <div class="my-6 bg-emerald-50/70 border border-emerald-200/80 rounded-2xl p-5">
                    <div class="flex flex-wrap justify-between items-center gap-2 mb-2">
                        <span class="text-sm font-bold text-emerald-900">
                            Progres Kolektif Kelompok (<span id="patungan-paid-count">0</span>/7 Anggota Lunas)
                        </span>
                        <span class="text-sm font-extrabold text-emerald-800">
                            Terkumpul: <span id="patungan-collected-total">Rp 0</span> / <span id="patungan-target-total">Rp 22.400.000</span>
                        </span>
                    </div>
                    <div class="w-full h-3.5 bg-white rounded-full overflow-hidden p-0.5 border border-emerald-200">
                        <div id="patungan-progress-bar" class="h-full bg-gradient-to-r from-emerald-600 to-gold-500 rounded-full transition-all duration-500" style="width: 0%"></div>
                    </div>
                </div>

                <!-- Form Tambah Anggota Patungan (Jika belum 7 orang) -->
                <div id="patungan-add-form" class="mb-6 bg-slate-50 p-4 rounded-2xl border border-slate-200 no-print">
                    <form onsubmit="handleAddPatunganMember(event)" class="grid grid-cols-1 sm:grid-cols-12 gap-3 items-end">
                        <div class="sm:col-span-5">
                            <label class="block text-xs font-bold text-slate-700 mb-1">Nama Shohibul Qurban (Bin/Binti)</label>
                            <input type="text" id="member-name" required placeholder="Contoh: Ahmad Fauzi bin Abdullah" class="w-full px-3.5 py-2 rounded-xl border border-slate-300 text-sm focus:outline-none focus:border-emerald-600">
                        </div>
                        <div class="sm:col-span-4">
                            <label class="block text-xs font-bold text-slate-700 mb-1">Sudah Setor (Rp)</label>
                            <input type="number" id="member-paid" required min="0" step="50000" value="0" class="w-full px-3.5 py-2 rounded-xl border border-slate-300 text-sm font-semibold focus:outline-none focus:border-emerald-600">
                        </div>
                        <div class="sm:col-span-3">
                            <button type="submit" id="btn-add-member" class="w-full py-2 px-4 rounded-xl bg-emerald-700 hover:bg-emerald-800 text-white font-bold text-sm transition flex items-center justify-center gap-2">
                                <i class="fa-solid fa-user-plus"></i>
                                Tambah Anggota
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Grid 7 Slot Anggota Patungan -->
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4" id="patungan-members-grid">
                    <!-- Di-render oleh JavaScript -->
                </div>
            </div>
        </section>

        <!-- ==================== 6. PANDUAN & EDUKASI SINGKAT QURBAN ==================== -->
        <section id="panduan-qurban" class="scroll-mt-20">
            <div class="mb-6">
                <span class="text-xs font-bold uppercase tracking-wider text-gold-600">Bekal Ilmu Ibadah</span>
                <h2 class="text-2xl sm:text-3xl font-extrabold text-slate-900 mt-1">Panduan & Edukasi Seputar Qurban</h2>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- Kartu 1: Syarat Sah Hewan Qurban -->
                <div class="bg-white rounded-3xl p-6 shadow-sm border border-slate-200/80 hover:border-emerald-300 transition">
                    <div class="w-12 h-12 rounded-2xl bg-emerald-50 text-emerald-700 flex items-center justify-center text-xl mb-4">
                        <i class="fa-solid fa-clipboard-check"></i>
                    </div>
                    <h3 class="text-lg font-bold text-slate-900 mb-3">Syarat Sah Hewan Qurban</h3>
                    <ul class="space-y-2.5 text-sm text-slate-600">
                        <li class="flex items-start gap-2.5">
                            <i class="fa-solid fa-circle-check text-emerald-600 mt-1 text-xs"></i>
                            <span><strong>Hewan Ternak (Bahimatul An'am):</strong> Unta, sapi/kerbau, atau kambing/domba.</span>
                        </li>
                        <li class="flex items-start gap-2.5">
                            <i class="fa-solid fa-circle-check text-emerald-600 mt-1 text-xs"></i>
                            <span><strong>Cukup Umur (Musinnah):</strong> Domba minimal 6 bulan (jika sulit mencari yg 1 thn), Kambing kacang min. 1 tahun, Sapi min. 2 tahun.</span>
                        </li>
                        <li class="flex items-start gap-2.5">
                            <i class="fa-solid fa-circle-check text-emerald-600 mt-1 text-xs"></i>
                            <span><strong>Sehat & Tidak Cacat:</strong> Tidak buta sebelah, tidak sakit parah, tidak pincang, dan tidak terlalu kurus hingga tak bersumsum.</span>
                        </li>
                        <li class="flex items-start gap-2.5">
                            <i class="fa-solid fa-circle-check text-emerald-600 mt-1 text-xs"></i>
                            <span><strong>Waktu Penyembelihan:</strong> Setelah shalat Idul Adha (10 Dzulhijjah) hingga terbenam matahari 13 Dzulhijjah (Hari Tasyrik).</span>
                        </li>
                    </ul>
                </div>

                <!-- Kartu 2: Keutamaan Berqurban -->
                <div class="bg-white rounded-3xl p-6 shadow-sm border border-slate-200/80 hover:border-gold-300 transition">
                    <div class="w-12 h-12 rounded-2xl bg-gold-50 text-gold-600 flex items-center justify-center text-xl mb-4">
                        <i class="fa-solid fa-star-and-crescent"></i>
                    </div>
                    <h3 class="text-lg font-bold text-slate-900 mb-3">Keutamaan Ibadah Qurban</h3>
                    <div class="bg-emerald-50/80 border-l-4 border-emerald-600 p-3.5 rounded-r-xl mb-3">
                        <p class="font-arabic text-right text-lg text-emerald-950 leading-loose">فَصَلِّ لِرَبِّكَ وَانْحَرْ</p>
                        <p class="text-xs italic text-slate-700 mt-1">"Maka dirikanlah shalat karena Tuhanmu; dan berqurbanlah." (QS. Al-Kautsar: 2)</p>
                    </div>
                    <ul class="space-y-2 text-sm text-slate-600">
                        <li class="flex items-start gap-2.5">
                            <i class="fa-solid fa-heart text-gold-500 mt-1 text-xs"></i>
                            <span>Amalan yang paling dicintai Allah pada Hari Raya Idul Adha.</span>
                        </li>
                        <li class="flex items-start gap-2.5">
                            <i class="fa-solid fa-heart text-gold-500 mt-1 text-xs"></i>
                            <span>Setiap helai bulu hewan qurban bernilai kebaikan di sisi Allah SWT.</span>
                        </li>
                        <li class="flex items-start gap-2.5">
                            <i class="fa-solid fa-heart text-gold-500 mt-1 text-xs"></i>
                            <span>Meneladani ketaatan Nabi Ibrahim AS dan keikhlasan Nabi Ismail AS.</span>
                        </li>
                    </ul>
                </div>

                <!-- Kartu 3: Tips Konsisten Menabung -->
                <div class="bg-white rounded-3xl p-6 shadow-sm border border-slate-200/80 hover:border-emerald-300 transition">
                    <div class="w-12 h-12 rounded-2xl bg-emerald-50 text-emerald-700 flex items-center justify-center text-xl mb-4">
                        <i class="fa-solid fa-lightbulb"></i>
                    </div>
                    <h3 class="text-lg font-bold text-slate-900 mb-3">Tips Konsisten Nabung Qurban</h3>
                    <div class="space-y-3 text-sm text-slate-600">
                        <div class="p-3 rounded-xl bg-slate-50 border border-slate-100">
                            <h4 class="font-bold text-slate-800 text-xs uppercase tracking-wider text-emerald-700">1. Pisahkan Rekening / Kantong</h4>
                            <p class="text-xs mt-1">Gunakan fitur kantong terpisah di bank syariah atau celengan khusus agar tidak tercampur dana belanja harian.</p>
                        </div>
                        <div class="p-3 rounded-xl bg-slate-50 border border-slate-100">
                            <h4 class="font-bold text-slate-800 text-xs uppercase tracking-wider text-emerald-700">2. Autodebet di Awal Gajian</h4>
                            <p class="text-xs mt-1">Sisihkan dana qurban di tanggal gajian sebelum membelanjakan untuk kebutuhan konsumtif lainnya.</p>
                        </div>
                        <div class="p-3 rounded-xl bg-slate-50 border border-slate-100">
                            <h4 class="font-bold text-slate-800 text-xs uppercase tracking-wider text-emerald-700">3. Patungan Keluarga (1/7 Sapi)</h4>
                            <p class="text-xs mt-1">Ajak orang tua, pasangan, atau saudara patungan 1 sapi agar lebih ringan dan terasa kebersamaannya.</p>
                        </div>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- ==================== FOOTER ==================== -->
    <footer class="bg-emerald-950 text-emerald-200 border-t border-emerald-900 mt-16 no-print">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 flex flex-col sm:flex-row items-center justify-between gap-4">
            <div class="flex items-center gap-2.5">
                <i class="fa-solid fa-kaaba text-gold-400 text-xl"></i>
                <span class="font-bold text-white text-lg">Qurban<span class="text-gold-400">Ku</span></span>
                <span class="text-xs text-emerald-400 ml-2">• Sahabat Perencana Ibadah Qurban</span>
            </div>
            <p class="text-xs text-emerald-400 text-center sm:text-right">
                Semoga Allah menerima niat & amal ibadah qurban kita semua. Aamiin.
            </p>
        </div>
    </footer>

    <!-- ==================== JAVASCRIPT LOGIC ==================== -->
    <script>
        // ==================== 1. DATA MASTER & KONFIGURASI AWAL ====================
        const STORAGE_KEY = 'qurbanku_app_data_v2';

        // Daftar 2 pilihan utama hewan qurban beserta harga bawaan
        const ANIMAL_OPTIONS = [
            {
                id: 'kambing_domba',
                name: 'Kambing / Domba',
                defaultPrice: 2900000,
                weight: 'Estimasi bobot ± 26 - 30 kg',
                icon: 'fa-award',
                badge: '1 Shohibul Qurban',
                desc: 'Untuk 1 orang shohibul qurban. Sehat, tidak cacat, dan cukup umur sesuai syariat (>1 tahun untuk kambing / >6 bulan untuk domba).'
            },
            {
                id: 'sapi_patungan',
                name: 'Sapi 1/7 (Patungan)',
                defaultPrice: 3000000,
                weight: 'Estimasi bobot ± 250 - 300 kg (dibagi 7 orang)',
                icon: 'fa-cow',
                badge: 'Patungan 1/7',
                desc: '1 bagian dari 7 orang peserta patungan 1 ekor sapi sehat & cukup umur (>2 tahun). Lebih hemat dan penuh kebersamaan.'
            }
        ];

        // State default aplikasi (disertai data demo agar langsung terlihat menarik saat pertama dibuka)
        const DEFAULT_STATE = {
            selectedAnimalId: 'sapi_patungan',
            animalPrices: {
                kambing_domba: 2900000,
                sapi_patungan: 3000000
            },
            editingAnimalId: null,
            targetDate: '2027-05-17', // Estimasi Idul Adha 1448 H
            transactions: [
                {
                    id: 'tx-1',
                    sender: 'H. Budi Santoso',
                    amount: 500000,
                    date: '2026-09-01',
                    method: 'Transfer Bank',
                    note: 'Niat awal tabungan qurban'
                },
                {
                    id: 'tx-2',
                    sender: 'Siti Aminah',
                    amount: 450000,
                    date: '2026-10-05',
                    method: 'Transfer Bank',
                    note: 'Sisihan gaji bulan Oktober'
                },
                {
                    id: 'tx-3',
                    sender: 'Rizky Pratama',
                    amount: 350000,
                    date: '2026-11-12',
                    method: 'E-Wallet / QRIS',
                    note: 'Tabungan pekanan Jumat'
                }
            ],
            patunganCowPrice: 21000000,
            patunganMembers: [
                { id: 'm-1', name: 'H. Budi Santoso bin Ahmad', paid: 3000000 },
                { id: 'm-2', name: 'Siti Aminah binti Yusuf', paid: 3000000 },
                { id: 'm-3', name: 'Rizky Pratama bin Budi', paid: 1800000 },
                { id: 'm-4', name: 'Nadia Putri binti Hasan', paid: 1000000 }
            ]
        };

        let appState = {};
        let savingsChartInstance = null;
        let countdownInterval = null;

        // ==================== 2. FUNGSI UTILITAS FORMAT & PENYIMPANAN ====================
        function formatRupiah(number) {
            return new Intl.NumberFormat('id-ID', {
                style: 'currency',
                currency: 'IDR',
                minimumFractionDigits: 0,
                maximumFractionDigits: 0
            }).format(Math.round(number));
        }

        function formatDateIndo(dateStr) {
            if (!dateStr) return '-';
            const options = { day: 'numeric', month: 'short', year: 'numeric' };
            return new Date(dateStr).toLocaleDateString('id-ID', options);
        }

        function loadState() {
            try {
                const saved = localStorage.getItem(STORAGE_KEY);
                if (saved) {
                    const parsed = JSON.parse(saved);
                    appState = {
                        ...DEFAULT_STATE,
                        ...parsed,
                        animalPrices: {
                            ...DEFAULT_STATE.animalPrices,
                            ...(parsed.animalPrices || {})
                        },
                        editingAnimalId: null
                    };
                    // Validasi pilihan hewan agar selalu salah satu dari 2 paket
                    if (!['kambing_domba', 'sapi_patungan'].includes(appState.selectedAnimalId)) {
                        appState.selectedAnimalId = 'sapi_patungan';
                    }
                    // Normalisasi data transaksi
                    appState.transactions = (appState.transactions || []).map(tx => {
                        let cleanMethod = tx.method || 'Transfer Bank';
                        if (cleanMethod.includes('Celengan')) {
                            cleanMethod = 'Setor Tunai';
                        } else if (cleanMethod.includes('Syariah') || cleanMethod.includes('Emas')) {
                            cleanMethod = 'Transfer Bank';
                        }
                        return {
                            ...tx,
                            sender: tx.sender || 'Hamba Allah',
                            method: cleanMethod
                        };
                    });
                } else {
                    appState = JSON.parse(JSON.stringify(DEFAULT_STATE));
                    saveState();
                }
            } catch (e) {
                console.error('Gagal memuat localStorage:', e);
                appState = JSON.parse(JSON.stringify(DEFAULT_STATE));
            }
        }

        function saveState() {
            try {
                const stateToPersist = { ...appState, editingAnimalId: null };
                localStorage.setItem(STORAGE_KEY, JSON.stringify(stateToPersist));
            } catch (e) {
                console.error('Gagal menyimpan ke localStorage:', e);
            }
        }

        // Notifikasi Toast Modern (Pengganti alert)
        function showToast(message, type = 'success') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            
            const colors = type === 'success' 
                ? 'bg-emerald-900 text-white border-emerald-600' 
                : type === 'warning' 
                ? 'bg-amber-600 text-white border-amber-400'
                : 'bg-rose-700 text-white border-rose-500';

            const icon = type === 'success' 
                ? 'fa-circle-check text-gold-400' 
                : type === 'warning' 
                ? 'fa-triangle-exclamation text-white'
                : 'fa-circle-xmark text-white';

            toast.className = `${colors} border px-4 py-3 rounded-2xl shadow-xl flex items-center gap-3 text-sm font-semibold transform transition-all duration-300 translate-x-full opacity-0 pointer-events-auto max-w-sm`;
            toast.innerHTML = `<i class="fa-solid ${icon} text-base"></i><span>${message}</span>`;
            
            container.appendChild(toast);

            // Animasi masuk
            requestAnimationFrame(() => {
                toast.classList.remove('translate-x-full', 'opacity-0');
            });

            // Hapus otomatis setelah 3.2 detik
            setTimeout(() => {
                toast.classList.add('translate-x-full', 'opacity-0');
                setTimeout(() => toast.remove(), 300);
            }, 3200);
        }

        // Modal Konfirmasi Kustom (Pengganti confirm)
        function showConfirmModal(title, message, onConfirmCallback) {
            const modal = document.getElementById('confirm-modal');
            const box = document.getElementById('confirm-modal-box');
            document.getElementById('confirm-title').textContent = title;
            document.getElementById('confirm-message').textContent = message;

            modal.classList.remove('hidden');
            modal.classList.add('flex');
            requestAnimationFrame(() => {
                box.classList.remove('scale-95', 'opacity-0');
                box.classList.add('scale-100', 'opacity-100');
            });

            const closeModal = () => {
                box.classList.remove('scale-100', 'opacity-100');
                box.classList.add('scale-95', 'opacity-0');
                setTimeout(() => {
                    modal.classList.add('hidden');
                    modal.classList.remove('flex');
                }, 200);
            };

            document.getElementById('confirm-cancel-btn').onclick = closeModal;
            document.getElementById('confirm-ok-btn').onclick = () => {
                closeModal();
                onConfirmCallback();
            };
        }

        // ==================== 3. COUNTDOWN TIMER IDUL ADHA ====================
        function startCountdown() {
            if (countdownInterval) clearInterval(countdownInterval);

            function updateTimer() {
                const targetTime = new Date(appState.targetDate + 'T06:00:00').getTime();
                const now = new Date().getTime();
                const diff = targetTime - now;

                if (diff <= 0) {
                    document.getElementById('cd-days').textContent = '00';
                    document.getElementById('cd-hours').textContent = '00';
                    document.getElementById('cd-minutes').textContent = '00';
                    document.getElementById('cd-seconds').textContent = '00';
                    return;
                }

                const days = Math.floor(diff / (1000 * 60 * 60 * 24));
                const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
                const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
                const seconds = Math.floor((diff % (1000 * 60)) / 1000);

                document.getElementById('cd-days').textContent = String(days).padStart(2, '0');
                document.getElementById('cd-hours').textContent = String(hours).padStart(2, '0');
                document.getElementById('cd-minutes').textContent = String(minutes).padStart(2, '0');
                document.getElementById('cd-seconds').textContent = String(seconds).padStart(2, '0');
            }

            updateTimer();
            countdownInterval = setInterval(updateTimer, 1000);
        }

        function updateTargetDate(newDate) {
            if (!newDate) return;
            appState.targetDate = newDate;
            saveState();
            startCountdown();
            updateAllCalculations();
            showToast('Tanggal estimasi Idul Adha diperbarui!');
        }

        // ==================== 4. PILIHAN TARGET HEWAN QURBAN ====================
        function getCurrentTargetInfo() {
            const found = ANIMAL_OPTIONS.find(a => a.id === appState.selectedAnimalId) || ANIMAL_OPTIONS[1];
            const currentPrice = Number(appState.animalPrices[found.id]) || found.defaultPrice;
            return {
                ...found,
                price: currentPrice
            };
        }

        function renderAnimalCards() {
            const container = document.getElementById('animal-cards-container');
            container.innerHTML = '';

            ANIMAL_OPTIONS.forEach(animal => {
                const isSelected = appState.selectedAnimalId === animal.id;
                const isEditing = appState.editingAnimalId === animal.id;
                const currentPrice = Number(appState.animalPrices[animal.id]) || animal.defaultPrice;

                const card = document.createElement('div');
                card.onclick = () => {
                    if (!isEditing && appState.selectedAnimalId !== animal.id) {
                        selectAnimal(animal.id);
                    }
                };
                card.className = `cursor-pointer rounded-3xl p-6 transition-all duration-200 flex flex-col justify-between relative ${
                    isSelected 
                    ? 'bg-emerald-900 text-white ring-4 ring-gold-400/60 shadow-xl' 
                    : 'bg-white text-slate-800 border-2 border-slate-200 hover:border-emerald-400 hover:shadow-md'
                }`;

                const editSectionHTML = isEditing ? `
                    <div onclick="event.stopPropagation()" class="mt-4 pt-4 border-t ${isSelected ? 'border-emerald-800' : 'border-slate-100'} space-y-2.5">
                        <label class="block text-[11px] font-bold uppercase tracking-wider ${isSelected ? 'text-gold-300' : 'text-emerald-700'}">
                            Masukkan Nominal Baru (Rp)
                        </label>
                        <div class="flex flex-col sm:flex-row gap-2">
                            <div class="relative flex-1">
                                <span class="absolute left-3 top-2.5 text-xs font-bold ${isSelected ? 'text-emerald-300' : 'text-slate-500'}">Rp</span>
                                <input type="number" id="inline-price-input-${animal.id}" value="${currentPrice}" min="100000" step="50000"
                                    onkeydown="if(event.key==='Enter'){ event.preventDefault(); saveInlineAnimalPrice('${animal.id}'); }"
                                    class="w-full pl-9 pr-3 py-2 rounded-xl text-sm font-extrabold focus:outline-none focus:ring-2 focus:ring-gold-400 ${
                                        isSelected ? 'bg-emerald-950 text-white border border-emerald-700' : 'bg-slate-50 text-slate-900 border border-slate-300'
                                    }">
                            </div>
                            <div class="flex gap-1.5">
                                <button type="button" onclick="saveInlineAnimalPrice('${animal.id}')" class="px-3.5 py-2 rounded-xl bg-gold-500 hover:bg-gold-400 text-emerald-950 font-extrabold text-xs transition shadow-sm">
                                    Simpan
                                </button>
                                <button type="button" onclick="cancelInlineAnimalEdit()" class="px-3 py-2 rounded-xl ${isSelected ? 'bg-emerald-800 hover:bg-emerald-700 text-emerald-200' : 'bg-slate-100 hover:bg-slate-200 text-slate-600'} font-bold text-xs transition">
                                    Batal
                                </button>
                            </div>
                        </div>
                    </div>
                ` : `
                    <div class="mt-5 pt-4 border-t ${isSelected ? 'border-emerald-800' : 'border-slate-100'} flex flex-wrap items-center justify-between gap-3">
                        <div>
                            <span class="block text-[11px] font-medium ${isSelected ? 'text-emerald-300' : 'text-slate-400'}">Estimasi Biaya Qurban</span>
                            <span class="font-extrabold text-xl sm:text-2xl ${isSelected ? 'text-gold-400' : 'text-emerald-800'}">${formatRupiah(currentPrice)}</span>
                        </div>
                        <div class="flex items-center gap-2">
                            <button type="button" onclick="event.stopPropagation(); startInlineAnimalEdit('${animal.id}')" class="px-3 py-2 rounded-xl text-xs font-bold transition flex items-center gap-1.5 ${
                                isSelected 
                                ? 'bg-emerald-800 hover:bg-emerald-700 text-gold-300 border border-emerald-700' 
                                : 'bg-slate-100 hover:bg-emerald-50 text-slate-700 hover:text-emerald-800 border border-slate-200'
                            }">
                                <i class="fa-solid fa-pen-to-square"></i>
                                Ubah Nominal
                            </button>
                            ${isSelected 
                                ? '<span class="inline-flex items-center gap-1 px-3 py-2 rounded-xl bg-gold-500 text-emerald-950 text-xs font-extrabold"><i class="fa-solid fa-circle-check"></i> Aktif</span>' 
                                : '<button type="button" onclick="event.stopPropagation(); selectAnimal(\'' + animal.id + '\')" class="px-3 py-2 rounded-xl bg-emerald-700 hover:bg-emerald-800 text-white text-xs font-bold transition">Pilih Paket</button>'
                            }
                        </div>
                    </div>
                `;

                card.innerHTML = `
                    <div>
                        <div class="flex items-center justify-between mb-3.5">
                            <span class="text-xs font-bold uppercase tracking-wider px-3 py-1 rounded-full ${
                                isSelected ? 'bg-gold-500 text-emerald-950' : 'bg-emerald-50 text-emerald-700 border border-emerald-200'
                            }">${animal.badge}</span>
                            <div class="w-11 h-11 rounded-2xl flex items-center justify-center text-lg ${
                                isSelected ? 'bg-emerald-800 text-gold-400' : 'bg-emerald-50 text-emerald-700'
                            }">
                                <i class="fa-solid ${animal.icon}"></i>
                            </div>
                        </div>
                        <h3 class="font-extrabold text-xl leading-snug">${animal.name}</h3>
                        <p class="text-xs font-semibold mt-1 flex items-center gap-1.5 ${isSelected ? 'text-gold-300' : 'text-emerald-700'}">
                            <i class="fa-solid fa-scale-balanced"></i>
                            ${animal.weight}
                        </p>
                        <p class="text-xs sm:text-sm mt-3 leading-relaxed ${isSelected ? 'text-emerald-100/90' : 'text-slate-600'}">${animal.desc}</p>
                    </div>
                    ${editSectionHTML}
                `;
                container.appendChild(card);
            });

            // Sinkronkan panel penyesuaian nominal cepat di bawah kartu
            const activeInfo = getCurrentTargetInfo();
            const quickLabel = document.getElementById('quick-active-animal-label');
            const quickInput = document.getElementById('quick-active-price-input');
            if (quickLabel) quickLabel.textContent = activeInfo.name;
            if (quickInput) quickInput.value = activeInfo.price;
        }

        function selectAnimal(animalId) {
            appState.selectedAnimalId = animalId;
            appState.editingAnimalId = null;
            saveState();
            renderAnimalCards();
            updateAllCalculations();
            const selectedInfo = getCurrentTargetInfo();
            showToast(`Target aktif diubah ke ${selectedInfo.name}.`);
        }

        function startInlineAnimalEdit(animalId) {
            appState.editingAnimalId = animalId;
            renderAnimalCards();
            setTimeout(() => {
                const input = document.getElementById(`inline-price-input-${animalId}`);
                if (input) {
                    input.focus();
                    input.select();
                }
            }, 50);
        }

        function cancelInlineAnimalEdit() {
            appState.editingAnimalId = null;
            renderAnimalCards();
        }

        function saveInlineAnimalPrice(animalId) {
            const input = document.getElementById(`inline-price-input-${animalId}`);
            if (!input) return;
            const newPrice = Number(input.value);

            if (!newPrice || newPrice < 100000) {
                showToast('Masukkan nominal target minimal Rp 100.000', 'error');
                input.focus();
                return;
            }

            appState.animalPrices[animalId] = newPrice;
            appState.selectedAnimalId = animalId;
            appState.editingAnimalId = null;

            // Jika Sapi 1/7 diubah, tawarkan sinkronisasi otomatis ke kalkulator patungan 7 orang
            if (animalId === 'sapi_patungan') {
                appState.patunganCowPrice = newPrice * 7;
                renderPatunganSection();
            }

            saveState();
            renderAnimalCards();
            updateAllCalculations();
            const targetInfo = getCurrentTargetInfo();
            showToast(`Nominal ${targetInfo.name} diperbarui menjadi ${formatRupiah(newPrice)}!`);
        }

        function applyQuickActivePrice() {
            const input = document.getElementById('quick-active-price-input');
            const newPrice = Number(input.value);

            if (!newPrice || newPrice < 100000) {
                showToast('Masukkan nominal target minimal Rp 100.000', 'error');
                input.focus();
                return;
            }

            const activeId = appState.selectedAnimalId;
            appState.animalPrices[activeId] = newPrice;
            appState.editingAnimalId = null;

            if (activeId === 'sapi_patungan') {
                appState.patunganCowPrice = newPrice * 7;
                renderPatunganSection();
            }

            saveState();
            renderAnimalCards();
            updateAllCalculations();
            const targetInfo = getCurrentTargetInfo();
            showToast(`Nominal target ${targetInfo.name} disimpan: ${formatRupiah(newPrice)}.`);
        }

        function openQuickTargetEdit() {
            const quickInput = document.getElementById('quick-active-price-input');
            if (quickInput) {
                quickInput.scrollIntoView({ behavior: 'smooth', block: 'center' });
                setTimeout(() => {
                    quickInput.focus();
                    quickInput.select();
                }, 300);
            }
        }

        // ==================== 5. KALKULATOR & UPDATE PROGRES UTAMA ====================
        let currentPlanAmounts = { daily: 0, weekly: 0, monthly: 0 };

        function updateAllCalculations() {
            const targetInfo = getCurrentTargetInfo();
            const totalSaved = appState.transactions.reduce((sum, tx) => sum + Number(tx.amount), 0);
            const targetPrice = targetInfo.price;
            const remaining = Math.max(0, targetPrice - totalSaved);
            const percentage = targetPrice > 0 ? Math.min(100, (totalSaved / targetPrice) * 100) : 0;

            // Update Hero Card
            document.getElementById('hero-animal-name').textContent = targetInfo.name;
            document.getElementById('hero-animal-icon').className = `fa-solid ${targetInfo.icon}`;
            document.getElementById('hero-total-saved').textContent = formatRupiah(totalSaved);
            document.getElementById('hero-target-price').textContent = formatRupiah(targetPrice);
            document.getElementById('hero-progress-pct').textContent = `${percentage.toFixed(1)}% Tercapai`;
            
            if (remaining === 0) {
                document.getElementById('hero-remaining-text').textContent = 'Alhamdulillah, Target Lunas!';
                document.getElementById('hero-remaining-text').className = 'text-emerald-600 font-extrabold';
            } else {
                document.getElementById('hero-remaining-text').textContent = `Kurang ${formatRupiah(remaining)}`;
                document.getElementById('hero-remaining-text').className = 'text-amber-600';
            }

            document.getElementById('hero-progress-bar').style.width = `${percentage}%`;
            document.getElementById('chart-target-label').textContent = formatRupiah(targetPrice);

            // Hitung Sisa Waktu hingga Target Date
            const now = new Date();
            const targetDateObj = new Date(appState.targetDate + 'T06:00:00');
            const diffTime = Math.max(0, targetDateObj - now);
            const daysLeft = Math.max(1, Math.ceil(diffTime / (1000 * 60 * 60 * 24)));
            const weeksLeft = Math.max(1, Math.ceil(daysLeft / 7));
            const monthsLeft = Math.max(1, Math.ceil(daysLeft / 30));

            // Update Kalkulator Rencana Setoran
            document.getElementById('calc-remaining-label').textContent = formatRupiah(remaining);
            document.getElementById('calc-days-left-label').textContent = `${daysLeft} hari`;

            const statusBadge = document.getElementById('calc-status-badge');
            if (remaining === 0) {
                statusBadge.textContent = 'Target Tercapai (100%)';
                statusBadge.className = 'font-bold text-sm text-emerald-300';
            } else {
                statusBadge.textContent = 'Sedang Menabung';
                statusBadge.className = 'font-bold text-sm text-gold-400';
            }

            const dailyAmt = remaining > 0 ? Math.ceil(remaining / daysLeft / 500) * 500 : 0;
            const weeklyAmt = remaining > 0 ? Math.ceil(remaining / weeksLeft / 1000) * 1000 : 0;
            const monthlyAmt = remaining > 0 ? Math.ceil(remaining / monthsLeft / 5000) * 5000 : 0;

            currentPlanAmounts = { daily: dailyAmt, weekly: weeklyAmt, monthly: monthlyAmt };

            document.getElementById('plan-daily').textContent = formatRupiah(dailyAmt);
            document.getElementById('plan-days-count').textContent = daysLeft;

            document.getElementById('plan-weekly').textContent = formatRupiah(weeklyAmt);
            document.getElementById('plan-weeks-count').textContent = weeksLeft;

            document.getElementById('plan-monthly').textContent = formatRupiah(monthlyAmt);
            document.getElementById('plan-months-count').textContent = monthsLeft;

            // Update Grafik
            updateChart(targetPrice);
        }

        function quickFillDeposit(type) {
            const amount = currentPlanAmounts[type];
            if (amount <= 0) {
                showToast('Target tabungan Anda sudah lunas!', 'success');
                return;
            }
            const input = document.getElementById('deposit-amount');
            input.value = amount;
            input.scrollIntoView({ behavior: 'smooth', block: 'center' });
            input.focus();
            showToast(`Nominal ${formatRupiah(amount)} dimasukkan ke form setoran.`);
        }

        function setDepositAmount(val) {
            document.getElementById('deposit-amount').value = val;
        }

        function focusDepositInput() {
            setTimeout(() => {
                document.getElementById('deposit-sender').focus();
            }, 300);
        }

        // Salin Nomor Rekening Tujuan (Kompatibel dengan iFrame)
        function copyBankAccount() {
            const accNum = document.getElementById('bank-account-number').textContent.trim();
            const tempInput = document.createElement('input');
            tempInput.value = accNum;
            document.body.appendChild(tempInput);
            tempInput.select();
            tempInput.setSelectionRange(0, 99999);
            try {
                document.execCommand('copy');
                showToast(`Nomor rekening ${accNum} berhasil disalin!`);
            } catch (err) {
                showToast('Gagal menyalin nomor rekening.', 'error');
            }
            document.body.removeChild(tempInput);
        }

        // ==================== 6. TRANSAKSI SETORAN & GRAFIK CHART.JS ====================
        function handleAddDeposit(e) {
            e.preventDefault();
            const sender = document.getElementById('deposit-sender').value.trim();
            const amount = Number(document.getElementById('deposit-amount').value);
            const date = document.getElementById('deposit-date').value;
            const method = document.getElementById('deposit-method').value;
            const note = document.getElementById('deposit-note').value.trim();

            if (!sender) {
                showToast('Nama penyetor wajib diisi!', 'error');
                document.getElementById('deposit-sender').focus();
                return;
            }

            if (!amount || amount < 1000) {
                showToast('Nominal setoran minimal Rp 1.000', 'error');
                return;
            }

            const newTx = {
                id: 'tx-' + Date.now(),
                sender: sender,
                amount: amount,
                date: date,
                method: method,
                note: note || 'Setoran tabungan qurban'
            };

            appState.transactions.push(newTx);
            // Urutkan transaksi berdasarkan tanggal terbaru
            appState.transactions.sort((a, b) => new Date(b.date) - new Date(a.date));
            
            saveState();
            renderTransactions();
            updateAllCalculations();

            // Reset form kecuali tanggal dan nama penyetor
            document.getElementById('deposit-amount').value = '';
            document.getElementById('deposit-note').value = '';
            showToast(`Alhamdulillah! Setoran dari ${sender} sebesar ${formatRupiah(amount)} berhasil dicatat.`);
        }

        function deleteTransaction(id) {
            showConfirmModal(
                'Hapus Riwayat Setoran',
                'Apakah Anda yakin ingin menghapus catatan setoran ini? Saldo tabungan akan disesuaikan kembali.',
                () => {
                    appState.transactions = appState.transactions.filter(tx => tx.id !== id);
                    saveState();
                    renderTransactions();
                    updateAllCalculations();
                    showToast('Riwayat setoran telah dihapus.', 'warning');
                }
            );
        }

        function renderTransactions() {
            const tbody = document.getElementById('transaction-list-body');
            const filterVal = document.getElementById('filter-method').value;
            const searchKeyword = (document.getElementById('search-sender').value || '').trim().toLowerCase();
            
            const filtered = appState.transactions.filter(tx => {
                const matchMethod = filterVal === 'ALL' || tx.method === filterVal;
                const senderName = (tx.sender || 'Hamba Allah').toLowerCase();
                const matchSender = !searchKeyword || senderName.includes(searchKeyword);
                return matchMethod && matchSender;
            });

            document.getElementById('tx-count').textContent = filtered.length;

            if (filtered.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="5" class="py-10 text-center text-slate-400">
                            <i class="fa-regular fa-folder-open text-3xl mb-2 block text-slate-300"></i>
                            Belum ada catatan setoran yang sesuai filter.
                        </td>
                    </tr>
                `;
                return;
            }

            tbody.innerHTML = filtered.map(tx => {
                let badgeColor = 'bg-emerald-50 text-emerald-700 border-emerald-200';
                let methodIcon = 'fa-building-columns';
                if (tx.method.includes('E-Wallet')) {
                    badgeColor = 'bg-sky-50 text-sky-700 border-sky-200';
                    methodIcon = 'fa-qrcode';
                } else if (tx.method === 'Setor Tunai') {
                    badgeColor = 'bg-amber-50 text-amber-800 border-amber-200';
                    methodIcon = 'fa-money-bill-wave';
                }

                const senderDisplay = tx.sender || 'Hamba Allah';

                return `
                    <tr class="hover:bg-slate-50/80 transition group">
                        <td class="py-3 px-3 whitespace-nowrap text-xs font-semibold text-slate-600">
                            ${formatDateIndo(tx.date)}
                        </td>
                        <td class="py-3 px-3 whitespace-nowrap">
                            <div class="flex items-center gap-2">
                                <span class="w-6 h-6 rounded-full bg-emerald-100 text-emerald-800 text-[10px] font-extrabold flex items-center justify-center shrink-0">
                                    ${senderDisplay.charAt(0).toUpperCase()}
                                </span>
                                <span class="text-xs font-bold text-slate-900">${senderDisplay}</span>
                            </div>
                        </td>
                        <td class="py-3 px-3">
                            <span class="inline-flex items-center gap-1 text-[10px] font-bold px-2 py-0.5 rounded-md border ${badgeColor} mb-0.5">
                                <i class="fa-solid ${methodIcon}"></i>
                                ${tx.method}
                            </span>
                            <p class="text-xs text-slate-600 font-medium">${tx.note}</p>
                        </td>
                        <td class="py-3 px-3 text-right whitespace-nowrap font-extrabold text-emerald-700">
                            +${formatRupiah(tx.amount)}
                        </td>
                        <td class="py-3 px-2 text-center no-print">
                            <button onclick="deleteTransaction('${tx.id}')" title="Hapus Setoran" class="w-7 h-7 rounded-lg text-slate-400 hover:text-rose-600 hover:bg-rose-50 transition">
                                <i class="fa-solid fa-trash-can text-xs"></i>
                            </button>
                        </td>
                    </tr>
                `;
            }).join('');
        }

        function updateChart(targetPrice) {
            const ctx = document.getElementById('savingsChart').getContext('2d');
            
            // Kelompokkan transaksi berdasarkan Bulan (YYYY-MM) secara kronologis
            const sortedAsc = [...appState.transactions].sort((a, b) => new Date(a.date) - new Date(b.date));
            
            const monthlyMap = {};
            sortedAsc.forEach(tx => {
                const monthKey = tx.date.substring(0, 7); // YYYY-MM
                monthlyMap[monthKey] = (monthlyMap[monthKey] || 0) + Number(tx.amount);
            });

            const monthKeys = Object.keys(monthlyMap);
            const labels = [];
            const cumulativeData = [];
            const targetLineData = [];

            let runningTotal = 0;
            if (monthKeys.length === 0) {
                labels.push('Mulai');
                cumulativeData.push(0);
                targetLineData.push(targetPrice);
            } else {
                monthKeys.forEach(key => {
                    const [year, month] = key.split('-');
                    const dateObj = new Date(year, Number(month) - 1, 1);
                    labels.push(dateObj.toLocaleDateString('id-ID', { month: 'short', year: 'numeric' }));
                    runningTotal += monthlyMap[key];
                    cumulativeData.push(runningTotal);
                    targetLineData.push(targetPrice);
                });
            }

            if (savingsChartInstance) {
                savingsChartInstance.destroy();
            }

            savingsChartInstance = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [
                        {
                            label: 'Saldo Terkumpul',
                            data: cumulativeData,
                            borderColor: '#059669',
                            backgroundColor: 'rgba(16, 185, 129, 0.15)',
                            borderWidth: 3,
                            pointBackgroundColor: '#d97706',
                            pointBorderColor: '#ffffff',
                            pointBorderWidth: 2,
                            pointRadius: 5,
                            fill: true,
                            tension: 0.35
                        },
                        {
                            label: 'Target Harga Qurban',
                            data: targetLineData,
                            borderColor: '#f59e0b',
                            borderWidth: 2,
                            borderDash: [6, 6],
                            pointRadius: 0,
                            fill: false
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'top',
                            labels: {
                                usePointStyle: true,
                                font: { family: '"Plus Jakarta Sans"', size: 11, weight: '600' }
                            }
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return context.dataset.label + ': ' + formatRupiah(context.raw);
                                }
                            }
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                font: { size: 10 },
                                callback: function(value) {
                                    if (value >= 1000000) return 'Rp ' + (value / 1000000).toFixed(1) + ' jt';
                                    if (value >= 1000) return 'Rp ' + (value / 1000) + ' rb';
                                    return 'Rp ' + value;
                                }
                            },
                            grid: { color: '#f1f5f9' }
                        },
                        x: {
                            ticks: { font: { size: 11 } },
                            grid: { display: false }
                        }
                    }
                }
            });
        }

        // ==================== 7. KELOMPOK PATUNGAN SAPI (1/7) ====================
        function updatePatunganCowPrice(val) {
            const price = Number(val);
            if (!price || price < 700000) {
                showToast('Harga sapi patungan minimal Rp 700.000', 'error');
                return;
            }
            appState.patunganCowPrice = price;
            saveState();
            renderPatunganSection();
            showToast('Harga 1 ekor sapi patungan diperbarui.');
        }

        function handleAddPatunganMember(e) {
            e.preventDefault();
            if (appState.patunganMembers.length >= 7) {
                showToast('Kuota patungan sapi sudah penuh (maksimal 7 orang)!', 'warning');
                return;
            }

            const name = document.getElementById('member-name').value.trim();
            const paid = Number(document.getElementById('member-paid').value) || 0;

            if (!name) return;

            appState.patunganMembers.push({
                id: 'm-' + Date.now(),
                name: name,
                paid: paid
            });

            saveState();
            renderPatunganSection();
            document.getElementById('member-name').value = '';
            document.getElementById('member-paid').value = '0';
            showToast(`${name} ditambahkan ke kelompok patungan.`);
        }

        function updateMemberPaid(id, deltaAmount) {
            const perPersonShare = Math.round(appState.patunganCowPrice / 7);
            const member = appState.patunganMembers.find(m => m.id === id);
            if (!member) return;

            if (deltaAmount === 'LUNAS') {
                member.paid = perPersonShare;
            } else {
                member.paid = Math.max(0, member.paid + deltaAmount);
            }

            saveState();
            renderPatunganSection();
            showToast(`Setoran ${member.name} diperbarui.`);
        }

        function removePatunganMember(id) {
            showConfirmModal(
                'Hapus Anggota Patungan',
                'Hapus peserta ini dari daftar kelompok patungan 1/7 sapi?',
                () => {
                    appState.patunganMembers = appState.patunganMembers.filter(m => m.id !== id);
                    saveState();
                    renderPatunganSection();
                    showToast('Anggota patungan dihapus.', 'warning');
                }
            );
        }

        function renderPatunganSection() {
            const cowPrice = appState.patunganCowPrice;
            const perPersonShare = Math.round(cowPrice / 7);

            document.getElementById('patungan-total-cow-price').value = cowPrice;
            document.getElementById('patungan-per-person-price').textContent = formatRupiah(perPersonShare);
            document.getElementById('patungan-target-total').textContent = formatRupiah(cowPrice);

            let totalCollected = 0;
            let paidFullCount = 0;

            appState.patunganMembers.forEach(m => {
                totalCollected += Number(m.paid);
                if (m.paid >= perPersonShare) paidFullCount++;
            });

            const pct = cowPrice > 0 ? Math.min(100, (totalCollected / cowPrice) * 100) : 0;

            document.getElementById('patungan-paid-count').textContent = paidFullCount;
            document.getElementById('patungan-collected-total').textContent = formatRupiah(totalCollected);
            document.getElementById('patungan-progress-bar').style.width = `${pct}%`;

            // Sembunyikan form tambah jika sudah 7 orang
            const addForm = document.getElementById('patungan-add-form');
            if (appState.patunganMembers.length >= 7) {
                addForm.classList.add('hidden');
            } else {
                addForm.classList.remove('hidden');
            }

            // Render 7 Slot (Terisi + Slot Kosong)
            const grid = document.getElementById('patungan-members-grid');
            grid.innerHTML = '';

            for (let i = 0; i < 7; i++) {
                const member = appState.patunganMembers[i];
                const slotNum = i + 1;

                if (member) {
                    const isLunas = member.paid >= perPersonShare;
                    const memberPct = Math.min(100, (member.paid / perPersonShare) * 100);
                    const shortfall = Math.max(0, perPersonShare - member.paid);

                    const card = document.createElement('div');
                    card.className = `rounded-2xl p-4 border transition ${
                        isLunas 
                        ? 'bg-emerald-50/60 border-emerald-300' 
                        : 'bg-white border-slate-200 shadow-sm'
                    }`;

                    card.innerHTML = `
                        <div class="flex items-start justify-between gap-2 mb-2">
                            <div class="flex items-center gap-2.5">
                                <span class="w-7 h-7 rounded-full ${isLunas ? 'bg-emerald-600 text-white' : 'bg-slate-200 text-slate-700'} text-xs font-extrabold flex items-center justify-center shrink-0">
                                    ${slotNum}
                                </span>
                                <div>
                                    <h4 class="font-bold text-sm text-slate-900 leading-tight">${member.name}</h4>
                                    <span class="inline-block mt-1 text-[10px] font-bold uppercase px-2 py-0.5 rounded-full ${
                                        isLunas ? 'bg-emerald-100 text-emerald-800' : 'bg-amber-100 text-amber-800'
                                    }">
                                        ${isLunas ? '<i class="fa-solid fa-check mr-1"></i>Lunas 1/7' : 'Cicilan Berjalan'}
                                    </span>
                                </div>
                            </div>
                            <button onclick="removePatunganMember('${member.id}')" class="text-slate-400 hover:text-rose-600 text-xs p-1 no-print" title="Hapus Anggota">
                                <i class="fa-solid fa-xmark"></i>
                            </button>
                        </div>

                        <div class="mt-3 space-y-1.5">
                            <div class="flex justify-between text-xs">
                                <span class="text-slate-500">Terkumpul: <strong class="text-slate-800">${formatRupiah(member.paid)}</strong></span>
                                <span class="font-bold ${isLunas ? 'text-emerald-700' : 'text-amber-600'}">${memberPct.toFixed(0)}%</span>
                            </div>
                            <div class="w-full h-2 bg-slate-200/70 rounded-full overflow-hidden">
                                <div class="h-full ${isLunas ? 'bg-emerald-600' : 'bg-gold-500'} rounded-full" style="width: ${memberPct}%"></div>
                            </div>
                            ${!isLunas ? `<p class="text-[11px] text-slate-400">Kurang ${formatRupiah(shortfall)}</p>` : ''}
                        </div>

                        ${!isLunas ? `
                        <div class="mt-3 pt-2.5 border-t border-slate-100 flex gap-1.5 no-print">
                            <button onclick="updateMemberPaid('${member.id}', 500000)" class="flex-1 py-1.5 px-2 rounded-lg bg-slate-100 hover:bg-emerald-100 text-slate-700 hover:text-emerald-800 text-[11px] font-bold transition">
                                +500rb
                            </button>
                            <button onclick="updateMemberPaid('${member.id}', 'LUNAS')" class="flex-1 py-1.5 px-2 rounded-lg bg-emerald-700 hover:bg-emerald-800 text-white text-[11px] font-bold transition">
                                Lunasi
                            </button>
                        </div>
                        ` : ''}
                    `;
                    grid.appendChild(card);
                } else {
                    // Slot Kosong
                    const emptyCard = document.createElement('div');
                    emptyCard.className = 'rounded-2xl p-4 border-2 border-dashed border-slate-200 bg-slate-50/50 flex flex-col items-center justify-center text-center min-h-[135px]';
                    emptyCard.innerHTML = `
                        <span class="w-7 h-7 rounded-full bg-slate-200/80 text-slate-500 text-xs font-bold flex items-center justify-center mb-1.5">${slotNum}</span>
                        <p class="text-xs font-semibold text-slate-400">Slot Peserta #${slotNum} Tersedia</p>
                        <p class="text-[11px] text-slate-400 mt-0.5">Tambahkan nama shohibul qurban di atas</p>
                    `;
                    grid.appendChild(emptyCard);
                }
            }
        }

        // ==================== 8. EKSPOR LAPORAN & RESET DEMO ====================
        function exportReport() {
            showToast('Menyiapkan tampilan cetak / PDF laporan tabungan...');
            setTimeout(() => {
                window.print();
            }, 500);
        }

        function confirmResetDemo() {
            showConfirmModal(
                'Reset Data ke Pengaturan Awal',
                'Semua catatan setoran dan anggota patungan Anda saat ini akan dikembalikan ke data contoh awal. Lanjutkan?',
                () => {
                    localStorage.removeItem(STORAGE_KEY);
                    loadState();
                    initAppUI();
                    showToast('Data berhasil di-reset ke contoh awal.');
                }
            );
        }

        // ==================== 9. INISIALISASI APLIKASI ====================
        function initAppUI() {
            // Set default tanggal form setoran ke hari ini
            const todayStr = new Date().toISOString().split('T')[0];
            document.getElementById('deposit-date').value = todayStr;
            document.getElementById('target-date-input').value = appState.targetDate;

            startCountdown();
            renderAnimalCards();
            renderTransactions();
            updateAllCalculations();
            renderPatunganSection();
        }

        window.addEventListener('DOMContentLoaded', () => {
            loadState();
            initAppUI();
        });
    </script>
</body>
</html>
