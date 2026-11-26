<!doctype html>
<html lang="id" class="scroll-smooth">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>QurbanKu - Aplikasi Perencana & Tracker Tabungan Qurban</title>

    <!-- Google Fonts: Plus Jakarta Sans & Amiri -->
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap"
      rel="stylesheet"
    />

    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Font Awesome Icons -->
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css"
    />

    <!-- Chart.js untuk Grafik -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <!-- Konfigurasi Tailwind Custom Colors & Fonts -->
    <script>
      tailwind.config = {
        theme: {
          extend: {
            fontFamily: {
              sans: ['"Plus Jakarta Sans"', "sans-serif"],
              arabic: ['"Amiri"', "serif"],
            },
            colors: {
              emerald: {
                50: "#ecfdf5",
                100: "#d1fae5",
                200: "#a7f3d0",
                300: "#6ee7b7",
                400: "#34d399",
                500: "#10b981",
                600: "#059669",
                700: "#047857",
                800: "#065f46",
                900: "#064e3b",
                950: "#022c22",
              },
              gold: {
                50: "#fffbeb",
                100: "#fef3c7",
                200: "#fde68a",
                300: "#fcd34d",
                400: "#fbbf24",
                500: "#f59e0b",
                600: "#d97706",
                700: "#b45309",
              },
            },
          },
        },
      };
    </script>

    <style>
    /* Semua perangkat */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
}

.container {
    width: 90%;
    max-width: 1200px;
    margin: auto;
}

/* Tablet */
@media (max-width: 768px) {
    .container {
        width: 95%;
    }
}

/* HP */
@media (max-width: 480px) {
    .container {
        width: 100%;
        padding: 10px;
    }

    h1 {
        font-size: 24px;
    }
}

img {
    max-width: 100%;
    height: auto;
}

.container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
}

@media (max-width: 768px) {
    .container {
        grid-template-columns: repeat(2, 1fr);
    }
}

@media (max-width: 480px) {
    .container {
        grid-template-columns: 1fr;
    }
}

      .islamic-pattern {
        background-color: #064e3b;
        background-image:
          radial-gradient(#10b981 0.75px, transparent 0.75px),
          radial-gradient(#d97706 0.75px, #064e3b 0.75px);
        background-size: 30px 30px;
        background-position:
          0 0,
          15px 15px;
      }

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
        .shadow-lg,
        .shadow-md,
        .shadow-xl {
          box-shadow: none !important;
          border: 1px solid #e2e8f0 !important;
        }
        header,
        footer,
        nav {
          display: none !important;
        }
      }
    </style>
  </head>
  <body
    class="bg-slate-50 text-slate-800 antialiased selection:bg-emerald-600 selection:text-white pb-24 md:pb-12"
  >
    <!-- ==================== NOTIFIKASI TOAST & MODAL KUSTOM ==================== -->
    <div
      id="toast-container"
      class="fixed top-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"
    ></div>

    <!-- Modal Konfirmasi Kustom -->
    <div
      id="confirm-modal"
      class="fixed inset-0 z-50 hidden items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4 no-print"
    >
      <div
        class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl border border-slate-100 transform transition-all scale-95 opacity-0"
        id="confirm-modal-box"
      >
        <div class="flex items-center gap-3 text-amber-600 mb-3">
          <div
            class="w-10 h-10 rounded-full bg-amber-100 flex items-center justify-center text-lg"
          >
            <i class="fa-solid fa-triangle-exclamation"></i>
          </div>
          <h3 class="text-lg font-bold text-slate-800" id="confirm-title">
            Konfirmasi Tindakan
          </h3>
        </div>
        <p
          class="text-slate-600 text-sm mb-6 leading-relaxed"
          id="confirm-message"
        >
          Apakah Anda yakin ingin melanjutkan tindakan ini?
        </p>
        <div class="flex justify-end gap-3">
          <button
            id="confirm-cancel-btn"
            class="px-4 py-2.5 rounded-xl text-sm font-semibold text-slate-600 hover:bg-slate-100 transition"
          >
            Batal
          </button>
          <button
            id="confirm-ok-btn"
            class="px-4 py-2.5 rounded-xl text-sm font-semibold bg-rose-600 text-white hover:bg-rose-700 shadow-sm transition"
          >
            Ya, Lanjutkan
          </button>
        </div>
      </div>
    </div>

    <!-- ==================== HEADER / NAVBAR ==================== -->
    <header
      class="sticky top-0 z-40 bg-white/95 backdrop-blur-md border-b border-emerald-100 shadow-sm no-print"
    >
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center h-16">
          <div class="flex items-center gap-3">
            <div
              class="w-10 h-10 rounded-xl bg-gradient-to-br from-emerald-700 to-emerald-900 flex items-center justify-center text-gold-400 shadow-md"
            >
              <i class="fa-solid fa-kaaba text-lg"></i>
            </div>
            <div>
              <span
                class="font-extrabold text-xl tracking-tight text-emerald-900"
                >Qurban<span class="text-gold-600">Ku</span></span
              >
              <span
                class="hidden sm:inline-block text-[11px] font-medium bg-emerald-50 text-emerald-700 px-2 py-0.5 rounded-full ml-2 border border-emerald-200"
                >1448 H / 2027</span
              >
            </div>
          </div>

          <nav
            class="hidden md:flex items-center gap-6 text-sm font-semibold text-slate-600"
          >
            <a href="#pilih-hewan" class="hover:text-emerald-700 transition"
              >Target Hewan</a
            >
            <a
              href="#tracker-tabungan"
              class="hover:text-emerald-700 transition"
              >Tracker & Grafik</a
            >
            <a href="#patungan-sapi" class="hover:text-emerald-700 transition"
              >Patungan 1/7</a
            >
            <a href="#panduan-qurban" class="hover:text-emerald-700 transition"
              >Panduan Qurban</a
            >
          </nav>

          <div class="flex items-center gap-2">
            <button
              onclick="exportReport()"
              title="Cetak atau Simpan PDF Laporan"
              class="flex items-center gap-2 px-3.5 py-2 rounded-xl bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs sm:text-sm font-semibold border border-emerald-200 transition"
            >
              <i class="fa-solid fa-print text-emerald-600"></i>
              <span class="hidden sm:inline">Laporan</span>
            </button>
            <button
              onclick="confirmResetDemo()"
              title="Reset Data Demo"
              class="flex items-center gap-2 px-3 py-2 rounded-xl bg-slate-100 hover:bg-rose-50 text-slate-600 hover:text-rose-600 text-xs sm:text-sm font-semibold border border-slate-200 hover:border-rose-200 transition"
            >
              <i class="fa-solid fa-rotate-left"></i>
              <span class="hidden lg:inline">Reset Demo</span>
            </button>
          </div>
        </div>
      </div>
    </header>

    <!-- Bottom Navigation Bar untuk HP (Responsif) -->
    <div
      class="fixed bottom-0 left-0 right-0 z-40 bg-white/95 backdrop-blur-md border-t border-slate-200 px-4 py-2 flex justify-around items-center md:hidden no-print shadow-lg"
    >
      <a
        href="#pilih-hewan"
        class="flex flex-col items-center text-slate-600 hover:text-emerald-700 text-[10px] font-bold"
      >
        <i class="fa-solid fa-bullseye text-lg mb-0.5"></i> Target
      </a>
      <a
        href="#form-setoran-card"
        onclick="focusDepositInput()"
        class="flex flex-col items-center text-emerald-700 font-bold text-[10px]"
      >
        <div
          class="w-10 h-10 -mt-5 rounded-full bg-emerald-700 text-white flex items-center justify-center shadow-lg border-2 border-white"
        >
          <i class="fa-solid fa-plus text-base"></i>
        </div>
        <span>Setor</span>
      </a>
      <a
        href="#tracker-tabungan"
        class="flex flex-col items-center text-slate-600 hover:text-emerald-700 text-[10px] font-bold"
      >
        <i class="fa-solid fa-chart-line text-lg mb-0.5"></i> Tracker
      </a>
      <a
        href="#patungan-sapi"
        class="flex flex-col items-center text-slate-600 hover:text-emerald-700 text-[10px] font-bold"
      >
        <i class="fa-solid fa-users text-lg mb-0.5"></i> Patungan
      </a>
    </div>

    <!-- ==================== 1. HERO SECTION & COUNTDOWN ==================== -->
    <section class="relative islamic-pattern text-white overflow-hidden">
      <div
        class="absolute inset-0 bg-gradient-to-br from-emerald-950/95 via-emerald-900/90 to-emerald-800/85"
      ></div>
      <div
        class="absolute -top-24 -right-24 w-96 h-96 bg-gold-500/10 rounded-full blur-3xl pointer-events-none"
      ></div>
      <div
        class="absolute -bottom-24 -left-24 w-96 h-96 bg-emerald-400/10 rounded-full blur-3xl pointer-events-none"
      ></div>

      <div
        class="relative max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-10 lg:py-14"
      >
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-center">
          <div class="lg:col-span-7 space-y-6">
            <div
              class="inline-flex items-center gap-2 px-3.5 py-1.5 rounded-full bg-emerald-800/80 border border-emerald-600/50 text-gold-300 text-xs sm:text-sm font-medium"
            >
              <i class="fa-solid fa-moon"></i>
              <span>Persiapan Ibadah Qurban Idul Adha 1448 H</span>
            </div>

            <div>
              <p class="font-arabic text-2xl sm:text-3xl text-gold-400 mb-1">
                بِسْمِ اللَّهِ الرَّحْمَنِ الرَّحِيم
              </p>
              <h1
                class="text-3xl sm:text-4xl lg:text-5xl font-extrabold tracking-tight leading-tight"
              >
                Wujudkan Niat Suci
                <span
                  class="text-transparent bg-clip-text bg-gradient-to-r from-gold-300 via-gold-400 to-amber-300"
                  >Berqurban Tahun Ini</span
                >
              </h1>
              <p
                class="mt-3 text-emerald-100/90 text-sm sm:text-base max-w-2xl leading-relaxed"
              >
                Rencanakan tabungan qurban Anda secara terukur, pantau setoran
                harian hingga bulanan, dan kelola patungan qurban sapi bersama
                keluarga atau sahabat secara transparan.
              </p>
            </div>

            <!-- Countdown Timer Box -->
            <div
              class="bg-emerald-950/70 backdrop-blur-md border border-emerald-700/60 rounded-2xl p-4 sm:p-5 max-w-xl"
            >
              <div
                class="flex flex-wrap items-center justify-between gap-2 mb-3"
              >
                <span
                  class="text-xs font-semibold uppercase tracking-wider text-emerald-300 flex items-center gap-1.5"
                >
                  <i class="fa-regular fa-calendar-check text-gold-400"></i>
                  Estimasi Idul Adha (17 Mei 2027)
                </span>
                <div class="flex items-center gap-1.5 no-print">
                  <label
                    for="target-date-input"
                    class="text-[11px] text-emerald-300"
                    >Ubah Tgl:</label
                  >
                  <input
                    type="date"
                    id="target-date-input"
                    value="2027-05-17"
                    onchange="updateTargetDate(this.value)"
                    class="bg-emerald-950 text-white text-xs rounded-lg px-2 py-1 border border-emerald-700 focus:outline-none focus:border-gold-400"
                  />
                </div>
              </div>

              <div class="grid grid-cols-4 gap-2 sm:gap-4 text-center">
                <div
                  class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2"
                >
                  <span
                    id="cd-days"
                    class="block text-2xl sm:text-3xl font-extrabold text-gold-400"
                    >000</span
                  >
                  <span
                    class="text-[11px] sm:text-xs text-emerald-200 font-medium"
                    >Hari</span
                  >
                </div>
                <div
                  class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2"
                >
                  <span
                    id="cd-hours"
                    class="block text-2xl sm:text-3xl font-extrabold text-white"
                    >00</span
                  >
                  <span
                    class="text-[11px] sm:text-xs text-emerald-200 font-medium"
                    >Jam</span
                  >
                </div>
                <div
                  class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2"
                >
                  <span
                    id="cd-minutes"
                    class="block text-2xl sm:text-3xl font-extrabold text-white"
                    >00</span
                  >
                  <span
                    class="text-[11px] sm:text-xs text-emerald-200 font-medium"
                    >Menit</span
                  >
                </div>
                <div
                  class="bg-emerald-900/90 border border-emerald-700/70 rounded-xl py-2.5 px-2"
                >
                  <span
                    id="cd-seconds"
                    class="block text-2xl sm:text-3xl font-extrabold text-emerald-300"
                    >00</span
                  >
                  <span
                    class="text-[11px] sm:text-xs text-emerald-200 font-medium"
                    >Detik</span
                  >
                </div>
              </div>
            </div>
          </div>

          <div class="lg:col-span-5">
            <div
              class="bg-white text-slate-800 rounded-3xl p-6 sm:p-7 shadow-2xl border-2 border-gold-400/50 relative overflow-hidden"
            >
              <div
                class="absolute top-0 right-0 w-32 h-32 bg-gradient-to-bl from-gold-100 to-transparent rounded-bl-full opacity-70 pointer-events-none"
              ></div>

              <div class="flex items-center justify-between mb-4">
                <div>
                  <span
                    class="text-xs font-bold uppercase tracking-wider text-emerald-700 bg-emerald-50 px-2.5 py-1 rounded-lg"
                    >Ringkasan Tabungan</span
                  >
                  <h2
                    id="hero-animal-name"
                    class="text-lg font-bold text-slate-900 mt-2"
                  >
                    Sapi 1/7 (Patungan)
                  </h2>
                </div>
                <div
                  id="hero-animal-icon-wrapper"
                  class="w-12 h-12 rounded-2xl bg-gold-100 text-gold-600 flex items-center justify-center text-2xl shadow-inner"
                >
                  <i id="hero-animal-icon" class="fa-solid fa-cow"></i>
                </div>
              </div>

              <div class="my-5">
                <p class="text-xs text-slate-500 font-medium">
                  Total Dana Terkumpul
                </p>
                <div class="flex items-baseline gap-2 mt-1">
                  <span
                    id="hero-total-saved"
                    class="text-3xl sm:text-4xl font-extrabold text-emerald-800 tracking-tight"
                    >Rp 0</span
                  >
                </div>
                <div class="flex flex-wrap items-center gap-2 mt-1">
                  <p class="text-xs text-slate-500">
                    dari target
                    <span
                      id="hero-target-price"
                      class="font-bold text-slate-700"
                      >Rp 3.000.000</span
                    >
                  </p>
                  <button
                    type="button"
                    onclick="openQuickTargetEdit()"
                    class="text-[11px] font-bold text-emerald-700 hover:text-emerald-900 bg-emerald-50 hover:bg-emerald-100 px-2 py-0.5 rounded-md border border-emerald-200 transition no-print"
                  >
                    <i class="fa-solid fa-pen-to-square mr-1"></i>Ubah Target
                  </button>
                </div>
              </div>

              <div class="space-y-2 mb-5">
                <div class="flex justify-between text-xs font-bold">
                  <span class="text-emerald-700" id="hero-progress-pct"
                    >0% Tercapai</span
                  >
                  <span class="text-amber-600" id="hero-remaining-text"
                    >Kurang Rp 3.000.000</span
                  >
                </div>
                <div
                  class="w-full h-3.5 bg-slate-100 rounded-full overflow-hidden p-0.5 border border-slate-200"
                >
                  <div
                    id="hero-progress-bar"
                    class="h-full bg-gradient-to-r from-emerald-600 via-emerald-500 to-gold-500 rounded-full transition-all duration-700 ease-out"
                    style="width: 0%"
                  ></div>
                </div>
              </div>

              <div class="flex gap-3 no-print">
                <a
                  href="#form-setoran-card"
                  onclick="focusDepositInput()"
                  class="flex-1 bg-gradient-to-r from-emerald-700 to-emerald-800 hover:from-emerald-800 hover:to-emerald-900 text-white font-bold py-3 px-4 rounded-xl text-sm text-center shadow-md shadow-emerald-900/20 transition flex items-center justify-center gap-2"
                >
                  <i class="fa-solid fa-circle-plus text-gold-400"></i>
                  Catat Setoran
                </a>
                <a
                  href="#kalkulator-section"
                  class="px-4 py-3 rounded-xl bg-gold-50 hover:bg-gold-100 text-gold-700 font-bold text-sm border border-gold-200 transition flex items-center justify-center"
                  title="Lihat Rencana Nabung"
                >
                  <i class="fa-solid fa-calculator"></i>
                </a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- ==================== MAIN CONTENT ==================== -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-10 space-y-12">
      <!-- ==================== 2. PILIHAN TARGET HEWAN QURBAN (2 PILIHAN) ==================== -->
      <section id="pilih-hewan" class="scroll-mt-20">
        <div
          class="flex flex-col sm:flex-row sm:items-end justify-between mb-6 gap-2"
        >
          <div>
            <span
              class="text-xs font-bold uppercase tracking-wider text-gold-600"
              >Langkah 1 • Tentukan Niat & Target</span
            >
            <h2 class="text-2xl sm:text-3xl font-extrabold text-slate-900 mt-1">
              Pilih Target Hewan Qurban
            </h2>
          </div>
          <p class="text-sm text-slate-500">
            Pilih salah satu dari 2 opsi di bawah atau ubah nominal sesuai
            kebutuhan Anda.
          </p>
        </div>

        <!-- Grid 2 Kartu Pilihan Hewan -->
        <div
          class="grid grid-cols-1 md:grid-cols-2 gap-6"
          id="animal-cards-container"
        >
          <!-- Diisi via JavaScript -->
        </div>

        <!-- Panel Cepat Ubah Nominal Target Aktif -->
        <div
          id="quick-target-panel"
          class="mt-5 bg-white border border-emerald-200 rounded-2xl p-4 sm:p-5 shadow-sm flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 no-print"
        >
          <div class="flex items-center gap-3">
            <div
              class="w-10 h-10 rounded-xl bg-gold-50 text-gold-600 border border-gold-200 flex items-center justify-center text-base shrink-0"
            >
              <i class="fa-solid fa-pen-ruler"></i>
            </div>
            <div>
              <h3 class="font-bold text-sm text-slate-900">
                Ubah Nominal Target Aktif (<span
                  id="quick-active-animal-label"
                  class="text-emerald-700"
                  >Sapi 1/7</span
                >)
              </h3>
              <p class="text-xs text-slate-500">
                Sesuaikan harga paket qurban kapan saja secara langsung.
              </p>
            </div>
          </div>
          <div class="flex items-center gap-2.5 w-full sm:w-auto">
            <div class="relative flex-1 sm:w-48">
              <span
                class="absolute left-3.5 top-2.5 text-xs font-bold text-emerald-700"
                >Rp</span
              >
              <input
                type="number"
                id="quick-active-price-input"
                min="100000"
                step="50000"
                placeholder="3000000"
                class="w-full pl-9 pr-3 py-2 rounded-xl border border-slate-300 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
              />
            </div>
            <button
              type="button"
              onclick="applyQuickActivePrice()"
              class="px-4 py-2 rounded-xl bg-emerald-700 hover:bg-emerald-800 text-white font-bold text-xs sm:text-sm transition shadow-sm shrink-0"
            >
              Simpan Nominal
            </button>
          </div>
        </div>
      </section>

      <!-- ==================== 3. KALKULATOR & PERENCANA SETORAN ==================== -->
      <section id="kalkulator-section" class="scroll-mt-20">
        <div
          class="bg-gradient-to-br from-emerald-900 to-emerald-950 rounded-3xl p-6 sm:p-8 text-white shadow-xl relative overflow-hidden"
        >
          <div
            class="absolute right-0 top-0 opacity-10 text-9xl pointer-events-none -mr-6 -mt-6"
          >
            <i class="fa-solid fa-mosque"></i>
          </div>

          <div class="relative z-10">
            <div
              class="flex flex-col md:flex-row md:items-center justify-between gap-4 pb-6 border-b border-emerald-800"
            >
              <div>
                <span
                  class="text-xs font-bold uppercase tracking-wider text-gold-400"
                  >Langkah 2 • Strategi Menabung</span
                >
                <h2 class="text-2xl font-extrabold mt-1">
                  Rencana Setoran Otomatis Menuju Idul Adha
                </h2>
                <p class="text-sm text-emerald-200 mt-1">
                  Dihitung dari sisa kekurangan (<span
                    id="calc-remaining-label"
                    class="font-bold text-white"
                    >Rp 0</span
                  >) dibagi sisa waktu (<span
                    id="calc-days-left-label"
                    class="font-bold text-gold-300"
                    >0 hari</span
                  >).
                </p>
              </div>
              <div
                class="bg-emerald-800/80 border border-emerald-700 px-4 py-2.5 rounded-2xl text-xs"
              >
                <span class="text-emerald-300 block">Status Target:</span>
                <span
                  id="calc-status-badge"
                  class="font-bold text-sm text-gold-400"
                  >Sedang Berjalan</span
                >
              </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-5 mt-6">
              <div
                class="bg-white/10 backdrop-blur-sm border border-emerald-700/70 rounded-2xl p-5 hover:bg-white/15 transition"
              >
                <div class="flex items-center justify-between mb-3">
                  <span
                    class="text-xs font-bold uppercase tracking-wider text-emerald-300"
                    >Opsi 1 • Setoran Harian</span
                  >
                  <span
                    class="w-8 h-8 rounded-lg bg-emerald-800 text-gold-400 flex items-center justify-center text-sm"
                  >
                    <i class="fa-solid fa-sun"></i>
                  </span>
                </div>
                <div
                  class="text-2xl sm:text-3xl font-extrabold text-white"
                  id="plan-daily"
                >
                  Rp 0
                </div>
                <p class="text-xs text-emerald-200 mt-1.5">
                  Per hari selama
                  <span id="plan-days-count" class="font-semibold text-white"
                    >0</span
                  >
                  hari ke depan
                </p>
                <button
                  onclick="quickFillDeposit('daily')"
                  class="mt-4 w-full py-2 rounded-xl bg-emerald-700/80 hover:bg-emerald-600 text-xs font-semibold text-white border border-emerald-600 transition no-print"
                >
                  Gunakan Nominal Ini
                </button>
              </div>

              <div
                class="bg-white/10 backdrop-blur-sm border-2 border-gold-400/70 rounded-2xl p-5 relative hover:bg-white/15 transition"
              >
                <span
                  class="absolute -top-3 right-4 bg-gold-500 text-emerald-950 text-[10px] font-extrabold uppercase px-2.5 py-0.5 rounded-full"
                  >Paling Populer</span
                >
                <div class="flex items-center justify-between mb-3">
                  <span
                    class="text-xs font-bold uppercase tracking-wider text-gold-300"
                    >Opsi 2 • Setoran Mingguan</span
                  >
                  <span
                    class="w-8 h-8 rounded-lg bg-gold-500/20 text-gold-400 flex items-center justify-center text-sm"
                  >
                    <i class="fa-solid fa-calendar-week"></i>
                  </span>
                </div>
                <div
                  class="text-2xl sm:text-3xl font-extrabold text-gold-400"
                  id="plan-weekly"
                >
                  Rp 0
                </div>
                <p class="text-xs text-emerald-200 mt-1.5">
                  Per pekan (tiap Jumat) selama
                  <span id="plan-weeks-count" class="font-semibold text-white"
                    >0</span
                  >
                  pekan
                </p>
                <button
                  onclick="quickFillDeposit('weekly')"
                  class="mt-4 w-full py-2 rounded-xl bg-gold-500 hover:bg-gold-400 text-xs font-bold text-emerald-950 transition shadow-sm no-print"
                >
                  Gunakan Nominal Ini
                </button>
              </div>

              <div
                class="bg-white/10 backdrop-blur-sm border border-emerald-700/70 rounded-2xl p-5 hover:bg-white/15 transition"
              >
                <div class="flex items-center justify-between mb-3">
                  <span
                    class="text-xs font-bold uppercase tracking-wider text-emerald-300"
                    >Opsi 3 • Setoran Bulanan</span
                  >
                  <span
                    class="w-8 h-8 rounded-lg bg-emerald-800 text-gold-400 flex items-center justify-center text-sm"
                  >
                    <i class="fa-solid fa-calendar-days"></i>
                  </span>
                </div>
                <div
                  class="text-2xl sm:text-3xl font-extrabold text-white"
                  id="plan-monthly"
                >
                  Rp 0
                </div>
                <p class="text-xs text-emerald-200 mt-1.5">
                  Per bulan (saat gajian) selama
                  <span id="plan-months-count" class="font-semibold text-white"
                    >0</span
                  >
                  bulan
                </p>
                <button
                  onclick="quickFillDeposit('monthly')"
                  class="mt-4 w-full py-2 rounded-xl bg-emerald-700/80 hover:bg-emerald-600 text-xs font-semibold text-white border border-emerald-600 transition no-print"
                >
                  Gunakan Nominal Ini
                </button>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- ==================== 4. TRACKER & GRAFIK (STOCKBIT STYLE) ==================== -->
      <section id="tracker-tabungan" class="scroll-mt-20 space-y-6">
        <div>
          <span class="text-xs font-bold uppercase tracking-wider text-gold-600"
            >Langkah 3 • Catat & Pantau</span
          >
          <h2 class="text-2xl sm:text-3xl font-extrabold text-slate-900 mt-1">
            Tracker Progres & Grafik Stockbit-Style
          </h2>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
          <!-- Form Tambah Setoran -->
          <div class="lg:col-span-5 space-y-6 no-print" id="form-setoran-card">
            <div
              class="bg-white rounded-3xl p-6 shadow-md border border-slate-200/80"
            >
              <div class="flex items-center gap-3 mb-5">
                <div
                  class="w-10 h-10 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center text-lg"
                >
                  <i class="fa-solid fa-piggy-bank"></i>
                </div>
                <div>
                  <h3 class="font-bold text-lg text-slate-900">
                    Tambah Setoran Baru
                  </h3>
                  <p class="text-xs text-slate-500">
                    Catat setoran dengan nama penyetor & metode pembayaran
                  </p>
                </div>
              </div>

              <!-- Rekening Tujuan Transfer -->
              <div
                class="mb-5 p-4 rounded-2xl bg-gradient-to-br from-emerald-50 to-gold-50/60 border border-emerald-200/90 relative overflow-hidden"
              >
                <div class="flex items-center justify-between gap-2 mb-2">
                  <span
                    class="text-[11px] font-extrabold uppercase tracking-wider text-emerald-800 flex items-center gap-1.5"
                  >
                    <i class="fa-solid fa-building-columns text-gold-600"></i>
                    Rekening Tujuan Transfer
                  </span>
                  <span
                    class="text-[10px] font-bold px-2 py-0.5 rounded-full bg-white text-emerald-700 border border-emerald-200"
                    >BSI (451)</span
                  >
                </div>
                <div
                  class="flex items-center justify-between gap-2 bg-white/90 px-3.5 py-2.5 rounded-xl border border-emerald-100"
                >
                  <div>
                    <p
                      id="bank-account-number"
                      class="font-mono font-extrabold text-base sm:text-lg text-slate-900 tracking-wide"
                    >
                      7123456789
                    </p>
                    <p class="text-xs text-slate-600 font-medium">
                      a.n.
                      <strong class="text-emerald-900">Panitia QurbanKu</strong>
                    </p>
                  </div>
                  <button
                    type="button"
                    onclick="copyBankAccount()"
                    class="px-3 py-2 rounded-xl bg-emerald-700 hover:bg-emerald-800 active:scale-95 text-white text-xs font-bold transition flex items-center gap-1.5 shadow-sm shrink-0"
                  >
                    <i class="fa-regular fa-copy"></i> Salin
                  </button>
                </div>
              </div>

              <!-- Form Input -->
              <form id="deposit-form" onsubmit="handleAddDeposit(event)" class="space-y-4">
                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1.5"
                    >Nama Penyetor <span class="text-rose-500">*</span></label
                  >
                  <div class="relative">
                    <span class="absolute left-3.5 top-3 text-slate-400 text-xs">
                      <i class="fa-solid fa-user"></i>
                    </span>
                    <input
                      type="text"
                      id="deposit-name"
                      required
                      placeholder="Contoh: Budi Santoso"
                      class="w-full pl-10 pr-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
                    />
                  </div>
                </div>

                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1.5"
                    >Nominal Setoran (Rp)
                    <span class="text-rose-500">*</span></label
                  >
                  <div class="relative">
                    <span
                      class="absolute left-3.5 top-2.5 text-xs font-extrabold text-emerald-700"
                      >Rp</span
                    >
                    <input
                      type="number"
                      id="deposit-amount"
                      required
                      min="10000"
                      step="10000"
                      placeholder="100000"
                      class="w-full pl-9 pr-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
                    />
                  </div>
                  <!-- Tombol Nominal Cepat -->
                  <div class="grid grid-cols-2 sm:grid-cols-4 gap-1.5 mt-2">
                    <button
                      type="button"
                      onclick="addQuickNominal(50000)"
                      class="py-1.5 rounded-lg bg-slate-100 hover:bg-emerald-50 hover:text-emerald-800 text-slate-700 text-xs font-bold border border-slate-200 transition"
                    >
                      +50rb
                    </button>
                    <button
                      type="button"
                      onclick="addQuickNominal(100000)"
                      class="py-1.5 rounded-lg bg-slate-100 hover:bg-emerald-50 hover:text-emerald-800 text-slate-700 text-xs font-bold border border-slate-200 transition"
                    >
                      +100rb
                    </button>
                    <button
                      type="button"
                      onclick="addQuickNominal(250000)"
                      class="py-1.5 rounded-lg bg-slate-100 hover:bg-emerald-50 hover:text-emerald-800 text-slate-700 text-xs font-bold border border-slate-200 transition"
                    >
                      +250rb
                    </button>
                    <button
                      type="button"
                      onclick="addQuickNominal(500000)"
                      class="py-1.5 rounded-lg bg-slate-100 hover:bg-emerald-50 hover:text-emerald-800 text-slate-700 text-xs font-bold border border-slate-200 transition"
                    >
                      +500rb
                    </button>
                  </div>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1.5"
                      >Metode Pembayaran</label
                    >
                    <select
                      id="deposit-method"
                      class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 bg-white"
                    >
                      <option value="Transfer Bank">Transfer Bank</option>
                      <option value="E-Wallet / QRIS">E-Wallet / QRIS</option>
                      <option value="Setor Tunai">Setor Tunai</option>
                    </select>
                  </div>
                  <div>
                    <label class="block text-xs font-bold text-slate-700 mb-1.5"
                      >Tanggal Setoran</label
                    >
                    <input
                      type="date"
                      id="deposit-date"
                      required
                      class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 bg-white"
                    />
                  </div>
                </div>

                <div>
                  <label class="block text-xs font-bold text-slate-700 mb-1.5"
                    >Catatan / Keterangan (Opsional)</label
                  >
                  <input
                    type="text"
                    id="deposit-note"
                    placeholder="Contoh: Cicilan ke-1"
                    class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
                  />
                </div>

                <button
                  type="submit"
                  class="w-full bg-gradient-to-r from-emerald-700 to-emerald-800 hover:from-emerald-800 hover:to-emerald-900 text-white font-bold py-3 px-4 rounded-xl text-sm shadow-md transition flex items-center justify-center gap-2"
                >
                  <i class="fa-solid fa-floppy-disk text-gold-400"></i> Simpan
                  Setoran
                </button>
              </form>
            </div>
          </div>

          <!-- Kolom Kanan (7 Kolom): Grafik Stockbit-Style & Riwayat -->
          <div class="lg:col-span-7 space-y-6">
            <!-- Grafik Stockbit Style -->
            <div
              class="bg-white rounded-3xl p-6 shadow-md border border-slate-200/80 flex flex-col justify-between"
            >
              <!-- Stockbit Ticker Header -->
              <div
                class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 pb-4 border-b border-slate-100"
              >
                <div>
                  <div class="flex items-center gap-2">
                    <span
                      class="text-[11px] font-extrabold uppercase tracking-wider bg-emerald-100 text-emerald-800 px-2.5 py-0.5 rounded-md"
                      >QURBANKU TIKER</span
                    >
                    <span
                      id="stockbit-date-label"
                      class="text-xs text-slate-400 font-medium"
                      >Menampilkan Saldo</span
                    >
                  </div>
                  <div class="flex items-baseline gap-2.5 mt-1">
                    <h3
                      id="stockbit-price-display"
                      class="text-2xl sm:text-3xl font-extrabold text-slate-900 tracking-tight"
                    >
                      Rp 0
                    </h3>
                  </div>
                  <p
                    id="stockbit-desc-display"
                    class="text-xs text-slate-500 mt-0.5 font-medium truncate max-w-xs sm:max-w-md"
                  >
                    Arahkan kursor atau sentuh grafik untuk melihat riwayat
                    transaksi
                  </p>
                </div>

                <!-- Kontrol Navigasi & Tab Grafik -->
                <div class="flex flex-wrap items-center gap-2 no-print">
                  <!-- Mode Tab: Per Pemasukan vs Per Hari -->
                  <div
                    class="bg-slate-100 p-1 rounded-xl flex items-center gap-1 border border-slate-200"
                  >
                    <button
                      type="button"
                      id="mode-tx-btn"
                      onclick="setChartMode('tx')"
                      class="px-2.5 py-1 rounded-lg text-xs font-bold bg-white text-emerald-800 shadow-sm transition"
                    >
                      Per Pemasukan
                    </button>
                    <button
                      type="button"
                      id="mode-day-btn"
                      onclick="setChartMode('day')"
                      class="px-2.5 py-1 rounded-lg text-xs font-bold text-slate-600 hover:text-slate-900 transition"
                    >
                      Per Hari
                    </button>
                  </div>

                  <!-- Tombol Geser Horizontal & Zoom -->
                  <div class="flex items-center gap-1">
                    <button
                      type="button"
                      onclick="scrollChart('left')"
                      title="Geser Kiri"
                      class="w-8 h-8 rounded-xl bg-slate-100 hover:bg-emerald-50 hover:text-emerald-700 text-slate-600 flex items-center justify-center text-xs border border-slate-200 transition"
                    >
                      <i class="fa-solid fa-chevron-left"></i>
                    </button>
                    <button
                      type="button"
                      onclick="scrollChart('right')"
                      title="Geser Kanan"
                      class="w-8 h-8 rounded-xl bg-slate-100 hover:bg-emerald-50 hover:text-emerald-700 text-slate-600 flex items-center justify-center text-xs border border-slate-200 transition"
                    >
                      <i class="fa-solid fa-chevron-right"></i>
                    </button>
                    <button
                      type="button"
                      onclick="toggleChartZoom()"
                      id="zoom-btn"
                      title="Perlebar Grafik (Scrollable)"
                      class="px-2.5 h-8 rounded-xl bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs font-bold border border-emerald-200 transition"
                    >
                      Geser ↔
                    </button>
                  </div>
                </div>
              </div>

              <!-- Container Canvas dengan Wrapper Scrollable -->
              <div class="mt-4 relative">
                <div
                  id="chart-scroll-wrapper"
                  class="overflow-x-auto overflow-y-hidden pb-2"
                >
                  <div
                    id="chart-inner-container"
                    class="relative h-64 sm:h-72 w-full min-w-[500px]"
                  >
                    <canvas id="savingsChart"></canvas>
                  </div>
                </div>
                <p
                  class="text-[10px] text-slate-400 text-center mt-1 sm:hidden no-print"
                >
                  <i class="fa-solid fa-arrows-left-right mr-1"></i> Geser
                  grafik ke samping untuk melihat data lainnya
                </p>
              </div>
            </div>
          </div>
        </div>

        <!-- Tabel Riwayat Setoran -->
        <div
          class="bg-white rounded-3xl p-6 shadow-md border border-slate-200/80"
        >
          <div
            class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 mb-6"
          >
            <div>
              <h3 class="font-bold text-lg text-slate-900">
                Riwayat Setoran Tabungan
              </h3>
              <p class="text-xs text-slate-500">
                Daftar lengkap transaksi setoran yang telah dicatat
              </p>
            </div>
            <!-- Filter & Search Riwayat -->
            <div
              class="flex flex-wrap items-center gap-2.5 w-full sm:w-auto no-print"
            >
              <div class="relative flex-1 sm:w-56">
                <span class="absolute left-3 top-2.5 text-slate-400 text-xs">
                  <i class="fa-solid fa-search"></i>
                </span>
                <input
                  type="text"
                  id="search-deposit"
                  oninput="renderDepositTable()"
                  placeholder="Cari nama penyetor..."
                  class="w-full pl-9 pr-3 py-2 rounded-xl border border-slate-300 text-xs font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
                />
              </div>
              <select
                id="filter-method"
                onchange="renderDepositTable()"
                class="px-3 py-2 rounded-xl border border-slate-300 text-xs font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-white"
              >
                <option value="all">Semua Metode</option>
                <option value="Transfer Bank">Transfer Bank</option>
                <option value="E-Wallet / QRIS">E-Wallet / QRIS</option>
                <option value="Setor Tunai">Setor Tunai</option>
              </select>
            </div>
          </div>

          <div
            class="overflow-x-auto rounded-2xl border border-slate-200"
            id="deposit-table-wrapper"
          >
            <table class="w-full text-left border-collapse text-sm">
              <thead>
                <tr
                  class="bg-emerald-900 text-white text-xs uppercase tracking-wider"
                >
                  <th class="py-3 px-4 font-semibold">Tanggal</th>
                  <th class="py-3 px-4 font-semibold">Nama Penyetor</th>
                  <th class="py-3 px-4 font-semibold">Nominal</th>
                  <th class="py-3 px-4 font-semibold">Metode</th>
                  <th class="py-3 px-4 font-semibold">Keterangan</th>
                  <th class="py-3 px-4 font-semibold text-center no-print">
                    Aksi
                  </th>
                </tr>
              </thead>
              <tbody id="deposit-table-body" class="divide-y divide-slate-100">
                <!-- Diisi via JavaScript -->
              </tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- ==================== 5. KELOMPOK PATUNGAN SAPI (1/7) ==================== -->
      <section id="patungan-sapi" class="scroll-mt-20 space-y-6">
        <div
          class="flex flex-col sm:flex-row sm:items-end justify-between gap-4"
        >
          <div>
            <span class="text-xs font-bold uppercase tracking-wider text-gold-600"
              >Kolaborasi Jamaah / Keluarga</span
            >
            <h2 class="text-2xl sm:text-3xl font-extrabold text-slate-900 mt-1">
              Kelompok Patungan Sapi (1/7 Porsi)
            </h2>
            <p class="text-sm text-slate-500 mt-1">
              Sesuai syariat, 1 ekor sapi dapat dipatungan oleh maksimal 7 orang.
              Pantau kelayakan dan setoran tiap peserta.
            </p>
          </div>
          <button
            onclick="openAddParticipantModal()"
            class="bg-emerald-700 hover:bg-emerald-800 text-white font-bold px-4 py-2.5 rounded-xl text-sm shadow-sm transition flex items-center gap-2 shrink-0 no-print"
          >
            <i class="fa-solid fa-user-plus text-gold-400"></i> Tambah Anggota
            Patungan
          </button>
        </div>

        <!-- Progress Kelompok Patungan Sapi -->
        <div
          class="bg-white rounded-3xl p-6 shadow-md border border-slate-200/80"
        >
          <div
            class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-6 pb-6 border-b border-slate-100"
          >
            <div>
              <span
                class="text-xs font-bold uppercase tracking-wider text-emerald-700 bg-emerald-50 px-2.5 py-1 rounded-lg"
                >Ringkasan Kelompok Sapi #1</span
              >
              <h3
                id="group-summary-title"
                class="text-xl font-extrabold text-slate-900 mt-2"
              >
                Kolektif Qurban Sapi Keluarga Besar
              </h3>
            </div>
            <div class="flex items-center gap-4">
              <div class="text-right">
                <span class="text-xs text-slate-500 block"
                  >Total Terkumpul Sapi</span
                >
                <span
                  id="group-total-saved"
                  class="text-xl font-extrabold text-emerald-800"
                  >Rp 0</span
                >
              </div>
              <div class="h-10 w-px bg-slate-200"></div>
              <div class="text-left">
                <span class="text-xs text-slate-500 block">Total Target Sapi (7x)</span>
                <span
                  id="group-total-target"
                  class="text-xl font-extrabold text-slate-900"
                  >Rp 21.000.000</span
                >
              </div>
            </div>
          </div>

          <!-- Grid Daftar 7 Slot Patungan Sapi -->
          <div
            class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4"
            id="participants-grid"
          >
            <!-- Diisi via JavaScript -->
          </div>
        </div>
      </section>

      <!-- ==================== 6. PANDUAN QURBAN ==================== -->
      <section id="panduan-qurban" class="scroll-mt-20">
        <div
          class="bg-gradient-to-br from-slate-900 to-slate-950 rounded-3xl p-6 sm:p-8 text-white shadow-xl"
        >
          <div class="flex items-center gap-3 mb-6">
            <div
              class="w-10 h-10 rounded-xl bg-gold-500/20 text-gold-400 flex items-center justify-center text-lg"
            >
              <i class="fa-solid fa-book-open"></i>
            </div>
            <div>
              <span
                class="text-xs font-bold uppercase tracking-wider text-gold-400"
                >Edukasi & Syariat</span
              >
              <h2 class="text-xl sm:text-2xl font-extrabold">
                Panduan Praktis Ibadah Qurban
              </h2>
            </div>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div
              class="bg-white/5 border border-slate-800 rounded-2xl p-5 hover:bg-white/10 transition"
            >
              <div class="text-gold-400 text-lg mb-3 font-bold flex items-center gap-2">
                <i class="fa-solid fa-shield-halved"></i> 1. Syarat Hewan Sah
              </div>
              <p class="text-xs text-slate-300 leading-relaxed">
                Hewan harus sehat, tidak cacat (buta sebelah, pincang parah,
                sangat kurus), dan telah cukup umur: Kambing/domba minimal 1
                tahun (atau poel), sedangkan Sapi/kerbau minimal 2 tahun.
              </p>
            </div>
            <div
              class="bg-white/5 border border-slate-800 rounded-2xl p-5 hover:bg-white/10 transition"
            >
              <div class="text-gold-400 text-lg mb-3 font-bold flex items-center gap-2">
                <i class="fa-solid fa-handshake"></i> 2. Ketentuan Patungan
              </div>
              <p class="text-xs text-slate-300 leading-relaxed">
                1 ekor sapi atau unta dapat dibeli secara patungan oleh maksimal
                7 orang (1/7 bagian per orang). Sementara kambing atau domba
                hanya sah untuk 1 orang per ekor.
              </p>
            </div>
            <div
              class="bg-white/5 border border-slate-800 rounded-2xl p-5 hover:bg-white/10 transition"
            >
              <div class="text-gold-400 text-lg mb-3 font-bold flex items-center gap-2">
                <i class="fa-solid fa-clock"></i> 3. Waktu Penyembelihan
              </div>
              <p class="text-xs text-slate-300 leading-relaxed">
                Penyembelihan qurban dilaksanakan mulai setelah selesai shalat
                Idul Adha (10 Dzulhijjah) hingga hari Tasyrik berakhir (tanggal
                11, 12, dan 13 Dzulhijjah) sebelum matahari terbenam.
              </p>
            </div>
          </div>
        </div>
      </section>
    </main>

    <!-- ==================== MODAL TAMBAH / EDIT ANGGOTA PATUNGAN ==================== -->
    <div
      id="participant-modal"
      class="fixed inset-0 z-50 hidden items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4 no-print"
    >
      <div
        class="bg-white rounded-3xl max-w-md w-full p-6 shadow-2xl border border-slate-100 transform transition-all scale-95 opacity-0"
        id="participant-modal-box"
      >
        <div class="flex items-center justify-between mb-4 pb-3 border-b border-slate-100">
          <h3 id="participant-modal-title" class="text-lg font-bold text-slate-900">
            Tambah Anggota Patungan Sapi
          </h3>
          <button
            onclick="closeParticipantModal()"
            class="w-8 h-8 rounded-full bg-slate-100 hover:bg-slate-200 text-slate-600 flex items-center justify-center transition"
          >
            <i class="fa-solid fa-xmark"></i>
          </button>
        </div>

        <form id="participant-form" onsubmit="handleSaveParticipant(event)" class="space-y-4">
          <input type="hidden" id="participant-id" />
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1.5"
              >Nama Peserta / Anggota <span class="text-rose-500">*</span></label
            >
            <input
              type="text"
              id="participant-name"
              required
              placeholder="Contoh: Ahmad Fauzi"
              class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
            />
          </div>

          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1.5"
              >Jumlah Setoran Terkumpul (Rp)
              <span class="text-rose-500">*</span></label
            >
            <input
              type="number"
              id="participant-paid"
              required
              min="0"
              step="50000"
              value="0"
              class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
            />
          </div>

          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1.5"
              >Target Porsi Per Orang (Rp)</label
            >
            <input
              type="number"
              id="participant-target"
              required
              min="100000"
              step="50000"
              value="3000000"
              class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-slate-50"
              readonly
            />
          </div>

          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1.5"
              >Kontak / Catatan</label
            >
            <input
              type="text"
              id="participant-note"
              placeholder="Contoh: Keluarga Bp. Ahmad"
              class="w-full px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-semibold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
            />
          </div>

          <div class="flex justify-end gap-3 pt-2">
            <button
              type="button"
              onclick="closeParticipantModal()"
              class="px-4 py-2.5 rounded-xl text-sm font-semibold text-slate-600 hover:bg-slate-100 transition"
            >
              Batal
            </button>
            <button
              type="submit"
              class="px-5 py-2.5 rounded-xl text-sm font-bold bg-emerald-700 text-white hover:bg-emerald-800 shadow-sm transition"
            >
              Simpan Peserta
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- ==================== FOOTER ==================== -->
    <footer class="bg-emerald-950 text-emerald-200/80 py-12 border-t border-emerald-900 no-print">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="flex flex-col md:flex-row items-center justify-between gap-6 pb-8 border-b border-emerald-900"
        >
          <div class="flex items-center gap-3">
            <div
              class="w-10 h-10 rounded-xl bg-emerald-800 flex items-center justify-center text-gold-400"
            >
              <i class="fa-solid fa-kaaba text-lg"></i>
            </div>
            <div>
              <span class="font-extrabold text-xl text-white"
                >Qurban<span class="text-gold-400">Ku</span></span
              >
              <p class="text-xs text-emerald-300">
                Aplikasi Perencana & Tracker Tabungan Qurban Indonesia
              </p>
            </div>
          </div>
          <div class="flex items-center gap-6 text-sm">
            <a href="#pilih-hewan" class="hover:text-white transition"
              >Target Hewan</a
            >
            <a href="#tracker-tabungan" class="hover:text-white transition"
              >Tracker</a
            >
            <a href="#patungan-sapi" class="hover:text-white transition"
              >Patungan Sapi</a
            >
            <a href="#panduan-qurban" class="hover:text-white transition"
              >Panduan</a
            >
          </div>
        </div>
        <div
          class="flex flex-col sm:flex-row items-center justify-between gap-4 pt-8 text-xs text-emerald-400"
        >
          <p>
            © 2027 QurbanKu. Niatkan ibadah ikhlas karena Allah SWT.
          </p>
          <p class="flex items-center gap-1">
            Dibuat dengan <i class="fa-solid fa-heart text-rose-500"></i> untuk
            Keluarga & Jamaah Indonesia
          </p>
        </div>
      </div>
    </footer>

    <!-- ==================== JAVASCRIPT APLIKASI ==================== -->
    <script>
      let appData = {
        targetDate: "2027-05-17",
        activeAnimalId: "sapi-1-7",
        animals: [
          {
            id: "kambing",
            name: "Kambing / Domba",
            price: 2900000,
            icon: "fa-sheep",
            badge: "Pilihan Mandiri",
            desc: "Estimasi bobot ± 26–30 kg. Cocok untuk qurban 1 orang.",
          },
          {
            id: "sapi-1-7",
            name: "Sapi 1/7 (Patungan)",
            price: 3000000,
            icon: "fa-cow",
            badge: "Paling Diminati",
            desc: "Estimasi bobot ± 250–300 kg dibagi 7 orang jamaah.",
          },
        ],
        deposits: [
          {
            id: "dep-1",
            date: "2026-06-01",
            name: "Ahmad Fauzi",
            amount: 500000,
            method: "Transfer Bank",
            note: "Setoran Awal Bulan",
          },
          {
            id: "dep-2",
            date: "2026-07-01",
            name: "Siti Aminah",
            amount: 750000,
            method: "E-Wallet / QRIS",
            note: "Cicilan ke-2",
          },
        ],
        participants: [
          {
            id: "p-1",
            name: "Ahmad Fauzi",
            paid: 3000000,
            target: 3000000,
            note: "Lunas",
          },
          {
            id: "p-2",
            name: "Siti Aminah",
            paid: 2500000,
            target: 3000000,
            note: "Kurang 500rb",
          },
          {
            id: "p-3",
            name: "Budi Santoso",
            paid: 1500000,
            target: 3000000,
            note: "Cicilan 3",
          },
          {
            id: "p-4",
            name: "Dewi Lestari",
            paid: 3000000,
            target: 3000000,
            note: "Lunas",
          },
          {
            id: "p-5",
            name: "Eko Prasetyo",
            paid: 1000000,
            target: 3000000,
            note: "Cicilan 2",
          },
          {
            id: "p-6",
            name: "Fatimah Zahra",
            paid: 2000000,
            target: 3000000,
            note: "Cicilan 4",
          },
          {
            id: "p-7",
            name: "Hendra Gunawan",
            paid: 500000,
            target: 3000000,
            note: "Cicilan 1",
          },
        ],
      };

      let savingsChartInstance = null;
      let chartMode = "tx"; // 'tx' atau 'day'
      let isChartZoomed = false;

      document.addEventListener("DOMContentLoaded", () => {
        loadFromLocalStorage();
        document.getElementById("target-date-input").value = appData.targetDate;
        document.getElementById("deposit-date").valueAsDate = new Date();
        initCountdown();
        renderAll();
      });

      function saveToLocalStorage() {
        localStorage.setItem("qurbanku_data_v4", JSON.stringify(appData));
      }

      function loadFromLocalStorage() {
        const saved = localStorage.getItem("qurbanku_data_v4");
        if (saved) {
          try {
            appData = JSON.parse(saved);
          } catch (e) {
            console.error("Gagal parse localStorage", e);
          }
        }
      }

      function formatRupiah(num) {
        return "Rp " + Number(num).toLocaleString("id-ID");
      }

      function showToast(message, type = "success") {
        const container = document.getElementById("toast-container");
        const toast = document.createElement("div");
        const bg = type === "success" ? "bg-emerald-800 text-white" : "bg-rose-700 text-white";
        const icon = type === "success" ? "fa-circle-check text-gold-400" : "fa-circle-exclamation text-white";

        toast.className = `pointer-events-auto flex items-center gap-3 px-4 py-3 rounded-2xl shadow-xl border border-white/10 text-sm font-semibold transition-all transform translate-y-2 opacity-0 ${bg}`;
        toast.innerHTML = `<i class="fa-solid ${icon} text-lg"></i><span>${message}</span>`;
        container.appendChild(toast);

        setTimeout(() => {
          toast.classList.remove("translate-y-2", "opacity-0");
        }, 10);

        setTimeout(() => {
          toast.classList.add("translate-y-2", "opacity-0");
          setTimeout(() => toast.remove(), 300);
        }, 3500);
      }

      let confirmCallback = null;
      function showConfirm(title, message, callback) {
        document.getElementById("confirm-title").innerText = title;
        document.getElementById("confirm-message").innerText = message;
        confirmCallback = callback;
        const modal = document.getElementById("confirm-modal");
        const box = document.getElementById("confirm-modal-box");
        modal.classList.remove("hidden");
        modal.classList.add("flex");
        setTimeout(() => {
          box.classList.remove("scale-95", "opacity-0");
          box.classList.add("scale-100", "opacity-100");
        }, 10);
      }

      document.getElementById("confirm-cancel-btn").addEventListener("click", () => {
        const modal = document.getElementById("confirm-modal");
        const box = document.getElementById("confirm-modal-box");
        box.classList.remove("scale-100", "opacity-100");
        box.classList.add("scale-95", "opacity-0");
        setTimeout(() => {
          modal.classList.remove("flex");
          modal.classList.add("hidden");
          confirmCallback = null;
        }, 200);
      });

      document.getElementById("confirm-ok-btn").addEventListener("click", () => {
        if (confirmCallback) confirmCallback();
        document.getElementById("confirm-cancel-btn").click();
      });

      let countdownInterval = null;
      function initCountdown() {
        if (countdownInterval) clearInterval(countdownInterval);
        
        function updateTimer() {
          const targetDate = new Date(appData.targetDate + "T00:00:00").getTime();
          const now = new Date().getTime();
          const distance = targetDate - now;

          if (distance < 0) {
            document.getElementById("cd-days").innerText = "000";
            document.getElementById("cd-hours").innerText = "00";
            document.getElementById("cd-minutes").innerText = "00";
            document.getElementById("cd-seconds").innerText = "00";
            return;
          }

          const days = Math.floor(distance / (1000 * 60 * 60 * 24));
          const hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
          const minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
          const seconds = Math.floor((distance % (1000 * 60)) / 1000);

          document.getElementById("cd-days").innerText = String(days).padStart(3, "0");
          document.getElementById("cd-hours").innerText = String(hours).padStart(2, "0");
          document.getElementById("cd-minutes").innerText = String(minutes).padStart(2, "0");
          document.getElementById("cd-seconds").innerText = String(seconds).padStart(2, "0");
        }

        updateTimer();
        countdownInterval = setInterval(updateTimer, 1000);
      }

      function updateTargetDate(val) {
        appData.targetDate = val;
        saveToLocalStorage();
        initCountdown();
        renderAll();
        showToast("Estimasi tanggal Idul Adha diperbarui!");
      }

      function renderAll() {
        renderAnimalCards();
        renderHeroSummary();
        renderCalculator();
        renderDepositTable();
        renderParticipants();
        renderStockbitChart();
      }

      function renderAnimalCards() {
        const container = document.getElementById("animal-cards-container");
        container.innerHTML = "";

        appData.animals.forEach((animal) => {
          const isActive = animal.id === appData.activeAnimalId;
          const borderClass = isActive
            ? "border-2 border-emerald-600 bg-emerald-50/40 shadow-lg ring-2 ring-emerald-500/20"
            : "border border-slate-200 bg-white hover:border-emerald-300 shadow-sm";

          container.innerHTML += `
            <div class="rounded-3xl p-6 transition-all duration-300 relative flex flex-col justify-between ${borderClass}">
              <div>
                <div class="flex items-center justify-between mb-4">
                  <span class="text-xs font-bold uppercase tracking-wider px-3 py-1 rounded-full ${isActive ? 'bg-emerald-700 text-white' : 'bg-slate-100 text-slate-600'}">
                    ${animal.badge}
                  </span>
                  <div class="w-12 h-12 rounded-2xl ${isActive ? 'bg-emerald-700 text-gold-300' : 'bg-gold-100 text-gold-600'} flex items-center justify-center text-xl shadow-inner">
                    <i class="fa-solid ${animal.icon}"></i>
                  </div>
                </div>
                <h3 class="text-xl font-extrabold text-slate-900">${animal.name}</h3>
                <p class="text-xs text-slate-500 mt-1.5 leading-relaxed">${animal.desc}</p>
              </div>

              <div class="mt-6 pt-4 border-t border-slate-200/60 flex items-center justify-between gap-3">
                <div>
                  <span class="text-[11px] text-slate-400 font-semibold block">Target Harga</span>
                  <span class="text-xl font-extrabold text-emerald-800">${formatRupiah(animal.price)}</span>
                </div>
                <div class="flex items-center gap-2">
                  <button type="button" onclick="promptEditAnimalPrice('${animal.id}')" class="px-3 py-2 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-bold transition">
                    <i class="fa-solid fa-pen mr-1"></i> Ubah
                  </button>
                  ${
                    isActive
                      ? `<span class="px-3 py-2 rounded-xl bg-emerald-700 text-white text-xs font-bold flex items-center gap-1"><i class="fa-solid fa-check"></i> Aktif</span>`
                      : `<button type="button" onclick="setActiveAnimal('${animal.id}')" class="px-3 py-2 rounded-xl bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs font-bold transition">Pilih</button>`
                  }
                </div>
              </div>
            </div>
          `;
        });
      }

      function setActiveAnimal(id) {
        appData.activeAnimalId = id;
        saveToLocalStorage();
        renderAll();
        const active = appData.animals.find((a) => a.id === id);
        showToast(`Target aktif diubah ke: ${active.name}`);
      }

      function promptEditAnimalPrice(id) {
        const animal = appData.animals.find((a) => a.id === id);
        const newPriceStr = prompt(`Masukkan target harga baru untuk ${animal.name}:`, animal.price);
        if (newPriceStr !== null) {
          const newPrice = parseInt(newPriceStr);
          if (!isNaN(newPrice) && newPrice > 0) {
            animal.price = newPrice;
            saveToLocalStorage();
            renderAll();
            showToast(`Harga ${animal.name} berhasil diperbarui!`);
          } else {
            showToast("Nominal harga tidak valid.", "error");
          }
        }
      }

      function openQuickTargetEdit() {
        const active = appData.animals.find((a) => a.id === appData.activeAnimalId);
        document.getElementById("quick-active-animal-label").innerText = active.name;
        document.getElementById("quick-active-price-input").value = active.price;
        document.getElementById("quick-target-panel").scrollIntoView({ behavior: "smooth", block: "center" });
        document.getElementById("quick-active-price-input").focus();
      }

      function applyQuickActivePrice() {
        const val = parseInt(document.getElementById("quick-active-price-input").value);
        if (isNaN(val) || val <= 0) {
          showToast("Masukkan nominal harga yang valid.", "error");
          return;
        }
        const active = appData.animals.find((a) => a.id === appData.activeAnimalId);
        active.price = val;
        saveToLocalStorage();
        renderAll();
        showToast(`Target harga ${active.name} diperbarui ke ${formatRupiah(val)}`);
      }

      function getTotalSaved() {
        return appData.deposits.reduce((acc, curr) => acc + curr.amount, 0);
      }

      function getActiveAnimal() {
        return appData.animals.find((a) => a.id === appData.activeAnimalId) || appData.animals[0];
      }

      function renderHeroSummary() {
        const active = getActiveAnimal();
        const total = getTotalSaved();
        const target = active.price;
        const pct = target > 0 ? Math.min(100, Math.round((total / target) * 100)) : 0;
        const remaining = Math.max(0, target - total);

        document.getElementById("hero-animal-name").innerText = active.name;
        document.getElementById("hero-animal-icon").className = `fa-solid ${active.icon}`;
        document.getElementById("hero-total-saved").innerText = formatRupiah(total);
        document.getElementById("hero-target-price").innerText = formatRupiah(target);
        document.getElementById("hero-progress-pct").innerText = `${pct}% Tercapai`;
        document.getElementById("hero-remaining-text").innerText = remaining > 0 ? `Kurang ${formatRupiah(remaining)}` : "Target Lunas! Alhamdulillah 🎉";
        document.getElementById("hero-progress-bar").style.width = `${pct}%`;
      }

      function renderCalculator() {
        const active = getActiveAnimal();
        const total = getTotalSaved();
        const target = active.price;
        const remaining = Math.max(0, target - total);

        const targetDate = new Date(appData.targetDate + "T00:00:00").getTime();
        const now = new Date().getTime();
        const diffDays = Math.max(1, Math.ceil((targetDate - now) / (1000 * 60 * 60 * 24)));
        const diffWeeks = Math.max(1, Math.ceil(diffDays / 7));
        const diffMonths = Math.max(1, Math.ceil(diffDays / 30));

        const dailyPlan = Math.ceil(remaining / diffDays);
        const weeklyPlan = Math.ceil(remaining / diffWeeks);
        const monthlyPlan = Math.ceil(remaining / diffMonths);

        document.getElementById("calc-remaining-label").innerText = formatRupiah(remaining);
        document.getElementById("calc-days-left-label").innerText = `${diffDays} hari`;
        document.getElementById("calc-status-badge").innerText = remaining === 0 ? "Target Selesai" : "Sedang Berjalan";

        document.getElementById("plan-daily").innerText = formatRupiah(dailyPlan);
        document.getElementById("plan-days-count").innerText = diffDays;

        document.getElementById("plan-weekly").innerText = formatRupiah(weeklyPlan);
        document.getElementById("plan-weeks-count").innerText = diffWeeks;

        document.getElementById("plan-monthly").innerText = formatRupiah(monthlyPlan);
        document.getElementById("plan-months-count").innerText = diffMonths;
      }

      function quickFillDeposit(type) {
        const active = getActiveAnimal();
        const total = getTotalSaved();
        const remaining = Math.max(0, active.price - total);
        const targetDate = new Date(appData.targetDate + "T00:00:00").getTime();
        const now = new Date().getTime();
        const diffDays = Math.max(1, Math.ceil((targetDate - now) / (1000 * 60 * 60 * 24)));
        const diffWeeks = Math.max(1, Math.ceil(diffDays / 7));
        const diffMonths = Math.max(1, Math.ceil(diffDays / 30));

        let val = 50000;
        if (type === "daily") val = Math.ceil(remaining / diffDays);
        if (type === "weekly") val = Math.ceil(remaining / diffWeeks);
        if (type === "monthly") val = Math.ceil(remaining / diffMonths);

        document.getElementById("deposit-amount").value = val;
        document.getElementById("deposit-form").scrollIntoView({ behavior: "smooth", block: "center" });
        showToast(`Nominal ${formatRupiah(val)} dimasukkan ke form setoran!`);
      }

      function focusDepositInput() {
        document.getElementById("deposit-name").focus();
      }

      function addQuickNominal(amt) {
        const input = document.getElementById("deposit-amount");
        const current = parseInt(input.value) || 0;
        input.value = current + amt;
      }

      function handleAddDeposit(e) {
        e.preventDefault();
        const name = document.getElementById("deposit-name").value.trim();
        const amount = parseInt(document.getElementById("deposit-amount").value);
        const method = document.getElementById("deposit-method").value;
        const date = document.getElementById("deposit-date").value;
        const note = document.getElementById("deposit-note").value.trim();

        if (!name || isNaN(amount) || amount <= 0 || !date) {
          showToast("Mohon isi nama dan nominal setoran dengan benar.", "error");
          return;
        }

        const newDep = {
          id: "dep-" + Date.now(),
          date,
          name,
          amount,
          method,
          note: note || "Setoran Tabungan",
        };

        appData.deposits.push(newDep);
        appData.deposits.sort((a, b) => new Date(a.date) - new Date(b.date));
        saveToLocalStorage();

        document.getElementById("deposit-form").reset();
        document.getElementById("deposit-date").valueAsDate = new Date();

        renderAll();
        showToast(`Setoran dari ${name} sebesar ${formatRupiah(amount)} berhasil dicatat!`);
      }

      function deleteDeposit(id) {
        showConfirm("Hapus Setoran", "Apakah Anda yakin ingin menghapus catatan setoran ini?", () => {
          appData.deposits = appData.deposits.filter((d) => d.id !== id);
          saveToLocalStorage();
          renderAll();
          showToast("Catatan setoran dihapus.");
        });
      }

      function renderDepositTable() {
        const tbody = document.getElementById("deposit-table-body");
        const search = document.getElementById("search-deposit").value.toLowerCase();
        const methodFilter = document.getElementById("filter-method").value;

        const filtered = appData.deposits.filter((d) => {
          const matchSearch = d.name.toLowerCase().includes(search) || d.note.toLowerCase().includes(search);
          const matchMethod = methodFilter === "all" || d.method === methodFilter;
          return matchSearch && matchMethod;
        });

        if (filtered.length === 0) {
          tbody.innerHTML = `
            <tr>
              <td colspan="6" class="text-center py-8 text-slate-400 text-sm">
                <i class="fa-solid fa-folder-open text-2xl mb-2 block"></i> Belum ada riwayat setoran yang cocok.
              </td>
            </tr>
          `;
          return;
        }

        tbody.innerHTML = "";
        const sortedDesc = [...filtered].sort((a, b) => new Date(b.date) - new Date(a.date));

        sortedDesc.forEach((d) => {
          let badgeColor = "bg-emerald-50 text-emerald-700 border-emerald-200";
          let iconMethod = "fa-building-columns";
          if (d.method === "E-Wallet / QRIS") {
            badgeColor = "bg-amber-50 text-amber-700 border-amber-200";
            iconMethod = "fa-qrcode";
          } else if (d.method === "Setor Tunai") {
            badgeColor = "bg-blue-50 text-blue-700 border-blue-200";
            iconMethod = "fa-money-bill-wave";
          }

          tbody.innerHTML += `
            <tr class="hover:bg-slate-50/80 transition">
              <td class="py-3 px-4 text-xs font-semibold text-slate-600 whitespace-nowrap">${d.date}</td>
              <td class="py-3 px-4 font-bold text-slate-900">${d.name}</td>
              <td class="py-3 px-4 font-extrabold text-emerald-800">${formatRupiah(d.amount)}</td>
              <td class="py-3 px-4">
                <span class="inline-flex items-center gap-1.5 text-xs font-semibold px-2.5 py-1 rounded-full border ${badgeColor}">
                  <i class="fa-solid ${iconMethod}"></i> ${d.method}
                </span>
              </td>
              <td class="py-3 px-4 text-xs text-slate-500">${d.note}</td>
              <td class="py-3 px-4 text-center no-print">
                <button onclick="deleteDeposit('${d.id}')" class="w-8 h-8 rounded-xl bg-slate-100 hover:bg-rose-50 text-slate-500 hover:text-rose-600 transition" title="Hapus">
                  <i class="fa-solid fa-trash-can text-xs"></i>
                </button>
              </td>
            </tr>
          `;
        });
      }

      function copyBankAccount() {
        const accNum = document.getElementById("bank-account-number").innerText;
        navigator.clipboard.writeText(accNum).then(() => {
          showToast("Nomor rekening berhasil disalin!");
        });
      }

      function setChartMode(mode) {
        chartMode = mode;
        document.getElementById("mode-tx-btn").className = mode === "tx" ? "px-2.5 py-1 rounded-lg text-xs font-bold bg-white text-emerald-800 shadow-sm transition" : "px-2.5 py-1 rounded-lg text-xs font-bold text-slate-600 hover:text-slate-900 transition";
        document.getElementById("mode-day-btn").className = mode === "day" ? "px-2.5 py-1 rounded-lg text-xs font-bold bg-white text-emerald-800 shadow-sm transition" : "px-2.5 py-1 rounded-lg text-xs font-bold text-slate-600 hover:text-slate-900 transition";
        renderStockbitChart();
      }

      function toggleChartZoom() {
        isChartZoomed = !isChartZoomed;
        const inner = document.getElementById("chart-inner-container");
        const btn = document.getElementById("zoom-btn");
        if (isChartZoomed) {
          inner.style.minWidth = "850px";
          btn.innerText = "Fit ⊡";
          btn.className = "px-2.5 h-8 rounded-xl bg-emerald-700 text-white text-xs font-bold border border-emerald-800 transition";
          showToast("Grafik diperlebar secara horizontal.");
        } else {
          inner.style.minWidth = "500px";
          btn.innerText = "Geser ↔";
          btn.className = "px-2.5 h-8 rounded-xl bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs font-bold border border-emerald-200 transition";
          showToast("Grafik disesuaikan ke ukuran standar.");
        }
        renderStockbitChart();
      }

      function scrollChart(dir) {
        const wrapper = document.getElementById("chart-scroll-wrapper");
        const amount = 200;
        if (dir === "left") {
          wrapper.scrollBy({ left: -amount, behavior: "smooth" });
        } else {
          wrapper.scrollBy({ left: amount, behavior: "smooth" });
        }
      }

      function renderStockbitChart() {
        const ctx = document.getElementById("savingsChart").getContext("2d");
        const sortedDeps = [...appData.deposits].sort((a, b) => new Date(a.date) - new Date(b.date));

        let labels = [];
        let dataPoints = [];
        let subtitles = [];

        if (sortedDeps.length === 0) {
          labels = ["Mulai"];
          dataPoints = [0];
          subtitles = ["Belum ada transaksi"];
        } else if (chartMode === "tx") {
          sortedDeps.forEach((d, idx) => {
            labels.push(` #${idx + 1} (${d.date})`);
            dataPoints.push(d.amount);
            subtitles.push(`${d.name} (${formatRupiah(d.amount)})`);
          });
        } else {
          const dayMap = {};
          sortedDeps.forEach((d) => {
            dayMap[d.date] = (dayMap[d.date] || 0) + d.amount;
          });
          const sortedDays = Object.keys(dayMap).sort((a, b) => new Date(a) - new Date(b));
          sortedDays.forEach((date) => {
            const amt = dayMap[date];
            labels.push(date);
            dataPoints.push(amt);
            subtitles.push(`Total harian (${formatRupiah(amt)})`);
          });
        }

        const lastAmount = dataPoints[dataPoints.length - 1];
        document.getElementById("stockbit-price-display").innerText = formatRupiah(lastAmount);

        if (sortedDeps.length > 0 && chartMode === "tx") {
          const lastDep = sortedDeps[sortedDeps.length - 1];
          document.getElementById("stockbit-date-label").innerText = lastDep.date;
          document.getElementById("stockbit-desc-display").innerText = `Setoran terakhir: ${lastDep.name} (${lastDep.method}) • ${formatRupiah(lastDep.amount)}`;
        } else {
          document.getElementById("stockbit-date-label").innerText = "Periode";
          document.getElementById("stockbit-desc-display").innerText = "Grafik nominal setoran qurban";
        }

        if (savingsChartInstance) {
          savingsChartInstance.destroy();
        }

        const crosshairPlugin = {
          id: 'stockbitCrosshair',
          afterDraw: (chart) => {
            if (chart.tooltip._active && chart.tooltip._active.length) {
              const ctx = chart.ctx;
              const activePoint = chart.tooltip._active[0];
              const x = activePoint.element.x;
              const topY = chart.scales.y.top;
              const bottomY = chart.scales.y.bottom;

              ctx.save();
              ctx.beginPath();
              ctx.moveTo(x, topY);
              ctx.lineTo(x, bottomY);
              ctx.lineWidth = 1.5;
              ctx.strokeStyle = '#059669';
              ctx.setLineDash([4, 4]);
              ctx.stroke();
              ctx.restore();
            }
          }
        };

        savingsChartInstance = new Chart(ctx, {
          type: "bar",
          data: {
            labels: labels,
            datasets: [
              {
                label: "Nominal Setoran",
                data: dataPoints,
                backgroundColor: "#059669",
                borderRadius: 8,
                barThickness: 'flex',
                maxBarThickness: 40,
              },
            ],
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            interaction: {
              mode: "index",
              intersect: false,
            },
            plugins: {
              legend: { display: false },
              tooltip: {
                backgroundColor: "rgba(6, 78, 59, 0.95)",
                titleFont: { size: 13, weight: "bold", family: "'Plus Jakarta Sans', sans-serif" },
                bodyFont: { size: 12, family: "'Plus Jakarta Sans', sans-serif" },
                padding: 12,
                cornerRadius: 12,
                displayColors: false,
                callbacks: {
                  title: (items) => {
                    const idx = items[0].dataIndex;
                    return subtitles[idx] || labels[idx];
                  },
                  label: (context) => {
                    const val = context.raw;
                    return `Nominal: ${formatRupiah(val)}`;
                  },
                  footer: () => "Klik atau geser untuk melihat riwayat lain",
                },
              },
            },
            scales: {
              x: {
                grid: { display: false },
                ticks: { font: { size: 11, family: "'Plus Jakarta Sans'" }, color: "#64748b" },
              },
              y: {
                grid: { color: "rgba(226, 232, 240, 0.6)" },
                ticks: {
                  font: { size: 11, family: "'Plus Jakarta Sans'" },
                  color: "#64748b",
                  callback: (value) => {
                    if (value >= 1000000) return (value / 1000000).toFixed(1) + "jt";
                    if (value >= 1000) return (value / 1000) + "rb";
                    return value;
                  },
                },
              },
            },
          },
          plugins: [crosshairPlugin],
        });
      }

      function renderParticipants() {
        const grid = document.getElementById("participants-grid");
        grid.innerHTML = "";

        let totalGroupSaved = 0;
        let totalGroupTarget = 0;

        appData.participants.forEach((p, idx) => {
          totalGroupSaved += p.paid;
          totalGroupTarget += p.target;
          const pct = p.target > 0 ? Math.min(100, Math.round((p.paid / p.target) * 100)) : 0;
          const isLunas = p.paid >= p.target;

          grid.innerHTML += `
            <div class="bg-slate-50/80 border ${isLunas ? 'border-emerald-300 bg-emerald-50/30' : 'border-slate-200'} rounded-2xl p-4 flex flex-col justify-between">
              <div>
                <div class="flex items-center justify-between mb-2">
                  <span class="text-[11px] font-extrabold uppercase tracking-wider px-2 py-0.5 rounded-md ${isLunas ? 'bg-emerald-700 text-white' : 'bg-slate-200 text-slate-700'}">
                    Slot #${idx + 1}
                  </span>
                  <div class="flex items-center gap-1 no-print">
                    <button onclick="openEditParticipantModal('${p.id}')" class="w-7 h-7 rounded-lg bg-white hover:bg-emerald-50 text-slate-600 hover:text-emerald-700 border border-slate-200 flex items-center justify-center text-xs transition" title="Edit">
                      <i class="fa-solid fa-pen"></i>
                    </button>
                    <button onclick="deleteParticipant('${p.id}')" class="w-7 h-7 rounded-lg bg-white hover:bg-rose-50 text-slate-500 hover:text-rose-600 border border-slate-200 flex items-center justify-center text-xs transition" title="Hapus">
                      <i class="fa-solid fa-trash-can"></i>
                    </button>
                  </div>
                </div>
                <h4 class="font-bold text-slate-900 text-sm truncate">${p.name}</h4>
                <p class="text-[11px] text-slate-500 mt-0.5 truncate">${p.note || 'Anggota Patungan'}</p>
              </div>

              <div class="mt-4 pt-3 border-t border-slate-200/60 space-y-2">
                <div class="flex justify-between text-xs">
                  <span class="font-bold text-emerald-800">${formatRupiah(p.paid)}</span>
                  <span class="font-semibold text-slate-400">${pct}%</span>
                </div>
                <div class="w-full h-2 bg-slate-200 rounded-full overflow-hidden">
                  <div class="h-full bg-emerald-600 rounded-full" style="width: ${pct}%"></div>
                </div>
                <div class="text-[10px] text-slate-500 text-right">Target Porsi: ${formatRupiah(p.target)}</div>
              </div>
            </div>
          `;
        });

        const sapiPack = appData.animals.find((a) => a.id === "sapi-1-7");
        const singlePortionTarget = sapiPack ? sapiPack.price : 3000000;
        const totalOverallSapiTarget = singlePortionTarget * 7;

        document.getElementById("group-total-saved").innerText = formatRupiah(totalGroupSaved);
        document.getElementById("group-total-target").innerText = formatRupiah(totalOverallSapiTarget);
      }

      function openAddParticipantModal() {
        if (appData.participants.length >= 7) {
          showToast("Slot patungan sapi sudah penuh (maksimal 7 orang).", "error");
          return;
        }
        document.getElementById("participant-modal-title").innerText = "Tambah Anggota Patungan Sapi";
        document.getElementById("participant-form").reset();
        document.getElementById("participant-id").value = "";
        
        const sapiPack = appData.animals.find((a) => a.id === "sapi-1-7");
        const defaultTarget = sapiPack ? sapiPack.price : 3000000;
        document.getElementById("participant-target").value = defaultTarget;

        const modal = document.getElementById("participant-modal");
        const box = document.getElementById("participant-modal-box");
        modal.classList.remove("hidden");
        modal.classList.add("flex");
        setTimeout(() => {
          box.classList.remove("scale-95", "opacity-0");
          box.classList.add("scale-100", "opacity-100");
        }, 10);
      }

      function openEditParticipantModal(id) {
        const p = appData.participants.find((item) => item.id === id);
        if (!p) return;

        document.getElementById("participant-modal-title").innerText = "Edit Anggota Patungan Sapi";
        document.getElementById("participant-id").value = p.id;
        document.getElementById("participant-name").value = p.name;
        document.getElementById("participant-paid").value = p.paid;
        document.getElementById("participant-target").value = p.target;
        document.getElementById("participant-note").value = p.note;

        const modal = document.getElementById("participant-modal");
        const box = document.getElementById("participant-modal-box");
        modal.classList.remove("hidden");
        modal.classList.add("flex");
        setTimeout(() => {
          box.classList.remove("scale-95", "opacity-0");
          box.classList.add("scale-100", "opacity-100");
        }, 10);
      }

      function closeParticipantModal() {
        const modal = document.getElementById("participant-modal");
        const box = document.getElementById("participant-modal-box");
        box.classList.remove("scale-100", "opacity-100");
        box.classList.add("scale-95", "opacity-0");
        setTimeout(() => {
          modal.classList.remove("flex");
          modal.classList.add("hidden");
        }, 200);
      }

      function handleSaveParticipant(e) {
        e.preventDefault();
        const id = document.getElementById("participant-id").value;
        const name = document.getElementById("participant-name").value.trim();
        const paid = parseInt(document.getElementById("participant-paid").value) || 0;
        const target = parseInt(document.getElementById("participant-target").value) || 3000000;
        const note = document.getElementById("participant-note").value.trim();

        if (!name) {
          showToast("Nama peserta wajib diisi.", "error");
          return;
        }

        if (id) {
          const p = appData.participants.find((item) => item.id === id);
          if (p) {
            p.name = name;
            p.paid = paid;
            p.target = target;
            p.note = note;
          }
          showToast("Data anggota patungan diperbarui!");
        } else {
          if (appData.participants.length >= 7) {
            showToast("Slot maksimal 7 orang.", "error");
            return;
          }
          const newP = {
            id: "p-" + Date.now(),
            name,
            paid,
            target,
            note: note || "Anggota Patungan",
          };
          appData.participants.push(newP);
          showToast("Anggota patungan berhasil ditambahkan!");
        }

        saveToLocalStorage();
        closeParticipantModal();
        renderParticipants();
      }

      function deleteParticipant(id) {
        showConfirm("Hapus Anggota", "Apakah Anda yakin ingin menghapus peserta ini dari kelompok patungan?", () => {
          appData.participants = appData.participants.filter((p) => p.id !== id);
          saveToLocalStorage();
          renderParticipants();
          showToast("Anggota patungan dihapus.");
        });
      }

      function exportReport() {
        window.print();
      }

      function confirmResetDemo() {
        showConfirm("Reset Data Demo", "Semua data setoran dan peserta akan dikembalikan ke pengaturan awal demo. Lanjutkan?", () => {
          localStorage.removeItem("qurbanku_data_v4");
          location.reload();
        });
      }
    </script>
  </body>
</html>
