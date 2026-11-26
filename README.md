<!doctype html>
<html lang="id" class="scroll-smooth">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0" />
    <title>QurbanKu - Perencana & Tracker Tabungan Qurban Responsif</title>

    <!-- Google Fonts: Plus Jakarta Sans & Amiri (untuk sentuhan kaligrafi/Islami) -->
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400&family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap"
      rel="stylesheet"
    />

    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Font Awesome Icons CDN -->
    <link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css"
    />

    <!-- Chart.js CDN untuk Grafik Perkembangan Tabungan -->
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
      /* Pola Geometri Islami Halus untuk Kartu Utama */
      .islamic-pattern {
        background-color: #064e3b;
        background-image:
          radial-gradient(#34d399 0.75px, transparent 0.75px),
          radial-gradient(#fbbf24 0.75px, #064e3b 0.75px);
        background-size: 24px 24px;
        background-position:
          0 0,
          12px 12px;
      }

      /* Pastikan seluruh elemen interaktif responsif terhadap sentuhan & klik */
      button,
      a,
      input,
      select {
        touch-action: manipulation;
      }

      /* Custom Scrollbar */
      ::-webkit-scrollbar {
        width: 6px;
        height: 6px;
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

      /* Animasi Toast */
      @keyframes toastSlideIn {
        from {
          transform: translateY(-14px);
          opacity: 0;
        }
        to {
          transform: translateY(0);
          opacity: 1;
        }
      }
      .toast-enter {
        animation: toastSlideIn 0.22s ease-out forwards;
      }

      /* Styling khusus saat mode Cetak / Simpan PDF */
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
        .shadow-xl,
        .shadow-2xl {
          box-shadow: none !important;
          border: 1px solid #cbd5e1 !important;
        }
        header,
        footer {
          display: none !important;
        }
        section {
          break-inside: avoid;
        }
      }
    </style>
  </head>
  <body
    class="bg-slate-100/80 text-slate-800 antialiased selection:bg-emerald-600 selection:text-white min-h-screen flex flex-col"
  >
    <!-- ==================== NOTIFIKASI TOAST & MODAL KUSTOM (PENGGANTI ALERT/CONFIRM) ==================== -->
    <div
      id="toast-container"
      class="fixed top-4 left-4 right-4 sm:left-auto sm:right-5 z-50 flex flex-col gap-2 pointer-events-none sm:max-w-sm"
    ></div>

    <!-- Modal Konfirmasi Kustom -->
    <div
      id="confirm-modal"
      class="fixed inset-0 z-50 hidden items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4 no-print overflow-y-auto"
      role="dialog"
      aria-modal="true"
    >
      <div
        class="bg-white rounded-3xl max-w-md w-full p-5 sm:p-6 shadow-2xl border border-slate-100 transform transition-all scale-95 opacity-0 my-auto"
        id="confirm-modal-box"
      >
        <div class="flex items-center gap-3 text-amber-600 mb-3">
          <div
            class="w-10 h-10 rounded-2xl bg-amber-100 flex items-center justify-center text-lg shrink-0"
          >
            <i class="fa-solid fa-triangle-exclamation"></i>
          </div>
          <h3 class="text-base sm:text-lg font-bold text-slate-800" id="confirm-title">
            Konfirmasi Tindakan
          </h3>
        </div>
        <p
          class="text-slate-600 text-xs sm:text-sm mb-6 leading-relaxed"
          id="confirm-message"
        >
          Apakah Anda yakin ingin melanjutkan tindakan ini?
        </p>
        <div class="flex justify-end gap-2.5">
          <button
            type="button"
            id="confirm-cancel-btn"
            class="min-h-[42px] px-4 py-2 rounded-xl text-xs sm:text-sm font-semibold text-slate-600 hover:bg-slate-100 transition"
          >
            Batal
          </button>
          <button
            type="button"
            id="confirm-ok-btn"
            class="min-h-[42px] px-4 py-2 rounded-xl text-xs sm:text-sm font-semibold bg-rose-600 text-white hover:bg-rose-700 shadow-sm transition"
          >
            Ya, Lanjutkan
          </button>
        </div>
      </div>
    </div>

    <!-- Modal Edit Setoran -->
    <div
      id="edit-deposit-modal"
      class="fixed inset-0 z-50 hidden items-center justify-center bg-slate-900/60 backdrop-blur-sm p-4 no-print overflow-y-auto"
      role="dialog"
      aria-modal="true"
    >
      <div
        class="bg-white rounded-3xl max-w-md w-full p-5 sm:p-6 shadow-2xl border border-slate-100 my-auto"
      >
        <div class="flex items-center justify-between mb-4">
          <div class="flex items-center gap-2.5">
            <div
              class="w-9 h-9 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center"
            >
              <i class="fa-solid fa-pen-to-square"></i>
            </div>
            <h3 class="text-base sm:text-lg font-bold text-slate-900">
              Edit Setoran Tabungan
            </h3>
          </div>
          <button
            type="button"
            onclick="closeEditDepositModal()"
            class="w-9 h-9 flex items-center justify-center rounded-xl text-slate-400 hover:text-slate-600 hover:bg-slate-100 transition"
          >
            <i class="fa-solid fa-xmark text-lg"></i>
          </button>
        </div>
        <form id="edit-deposit-form" class="space-y-4">
          <input type="hidden" id="edit-deposit-id" />
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1"
              >Nominal Setoran (Rp)</label
            >
            <input
              type="number"
              id="edit-deposit-amount"
              min="1000"
              step="1000"
              required
              class="w-full min-h-[44px] px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
            />
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1"
              >Tanggal Setoran</label
            >
            <input
              type="date"
              id="edit-deposit-date"
              required
              class="w-full min-h-[44px] px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
            />
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1"
              >Metode Setoran</label
            >
            <select
              id="edit-deposit-method"
              class="w-full min-h-[44px] px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-white"
            >
              <option value="Transfer Bank (BSI)">Transfer Bank (BSI)</option>
              <option value="E-Wallet / QRIS">E-Wallet / QRIS</option>
              <option value="Setoran Tunai">Setoran Tunai</option>
              <option value="Autodebet / Gajian">Autodebet / Gajian</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1"
              >Catatan</label
            >
            <input
              type="text"
              id="edit-deposit-note"
              placeholder="Contoh: Tabungan pekan ke-4"
              class="w-full min-h-[44px] px-3.5 py-2.5 rounded-xl border border-slate-300 text-sm text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
            />
          </div>
          <div class="flex justify-end gap-2.5 pt-2">
            <button
              type="button"
              onclick="closeEditDepositModal()"
              class="min-h-[42px] px-4 py-2 rounded-xl text-xs sm:text-sm font-semibold text-slate-600 hover:bg-slate-100 transition"
            >
              Batal
            </button>
            <button
              type="submit"
              class="min-h-[42px] px-5 py-2 rounded-xl text-xs sm:text-sm font-bold bg-emerald-700 hover:bg-emerald-800 text-white shadow-sm transition"
            >
              Simpan Perubahan
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- ==================== HEADER / TOP BAR RINGKAS (SELALU TERLIHAT) ==================== -->
    <header
      class="sticky top-0 z-40 bg-white/95 backdrop-blur-md border-b border-emerald-100 shadow-sm no-print"
    >
      <div class="max-w-7xl mx-auto px-3 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center h-14 sm:h-16 gap-2">
          <!-- Brand Logo -->
          <div class="flex items-center gap-2.5 min-w-0">
            <div
              class="w-9 h-9 sm:w-10 sm:h-10 rounded-xl bg-gradient-to-br from-emerald-700 to-emerald-900 flex items-center justify-center text-gold-400 shadow-md shrink-0"
            >
              <i class="fa-solid fa-kaaba text-base sm:text-lg"></i>
            </div>
            <div class="truncate">
              <span
                class="font-extrabold text-lg sm:text-xl tracking-tight text-emerald-900"
                >Qurban<span class="text-gold-600">Ku</span></span
              >
              <span
                class="hidden sm:inline-block text-[11px] font-semibold bg-emerald-50 text-emerald-700 px-2 py-0.5 rounded-full ml-1.5 border border-emerald-200"
                >1448 H / 2027</span
              >
            </div>
          </div>

          <!-- Navigasi Cepat Desktop & Tablet (Langsung Terlihat Tanpa Menu Tersembunyi) -->
          <nav
            class="hidden lg:flex items-center gap-5 text-xs xl:text-sm font-bold text-slate-600"
          >
            <a href="#form-setoran-card" class="hover:text-emerald-700 transition"
              >Setoran</a
            >
            <a href="#pilih-hewan" class="hover:text-emerald-700 transition"
              >Target Hewan</a
            >
            <a href="#kalkulator-section" class="hover:text-emerald-700 transition"
              >Kalkulator</a
            >
            <a href="#patungan-sapi" class="hover:text-emerald-700 transition"
              >Patungan 1/7</a
            >
            <a href="#panduan-qurban" class="hover:text-emerald-700 transition"
              >Panduan</a
            >
          </nav>

          <!-- Tombol Aksi Utama Langsung Terlihat di Header -->
          <div class="flex items-center gap-1.5 sm:gap-2 shrink-0">
            <a
              href="#form-setoran-card"
              onclick="focusDepositInput()"
              class="min-h-[36px] sm:min-h-[40px] flex items-center gap-1.5 px-2.5 sm:px-3.5 py-1.5 rounded-xl bg-emerald-700 hover:bg-emerald-800 text-white text-xs sm:text-sm font-bold shadow-sm transition"
            >
              <i class="fa-solid fa-plus text-gold-300"></i>
              <span>Setor</span>
            </a>
            <button
              type="button"
              onclick="exportReport()"
              title="Cetak atau Simpan PDF Laporan"
              class="min-h-[36px] sm:min-h-[40px] flex items-center gap-1.5 px-2.5 sm:px-3 py-1.5 rounded-xl bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs sm:text-sm font-bold border border-emerald-200 transition"
            >
              <i class="fa-solid fa-print text-emerald-600"></i>
              <span class="hidden sm:inline">Laporan</span>
            </button>
            <button
              type="button"
              onclick="confirmResetDemo()"
              title="Reset Data Demo"
              class="min-h-[36px] sm:min-h-[40px] flex items-center gap-1.5 px-2.5 sm:px-3 py-1.5 rounded-xl bg-slate-100 hover:bg-rose-50 text-slate-600 hover:text-rose-600 text-xs sm:text-sm font-bold border border-slate-200 hover:border-rose-200 transition"
            >
              <i class="fa-solid fa-rotate-left"></i>
              <span class="hidden md:inline">Reset</span>
            </button>
          </div>
        </div>
      </div>
    </header>

    <!-- ==================== MAIN CONTAINER TERSTRUKTUR SEPERTI BERANDA KARTU ==================== -->
    <main class="max-w-7xl mx-auto w-full px-3 sm:px-6 lg:px-8 py-4 sm:py-6 space-y-5 sm:space-y-6 flex-1">

      <!-- ==================== 1. KARTU UTAMA COUNTDOWN & RINGKASAN SALDO (ATAS) ==================== -->
      <section class="grid grid-cols-1 lg:grid-cols-12 gap-4 sm:gap-5 items-stretch">
        <!-- Kartu Countdown Idul Adha & Target -->
        <div
          class="lg:col-span-7 relative islamic-pattern text-white rounded-3xl p-4 sm:p-6 shadow-lg overflow-hidden flex flex-col justify-between"
        >
          <div
            class="absolute inset-0 bg-gradient-to-br from-emerald-950/90 via-emerald-900/85 to-emerald-800/80"
          ></div>

          <div class="relative z-10 space-y-4">
            <!-- Baris Lokasi/Hijriyah & Tanggal Target -->
            <div class="flex flex-wrap items-center justify-between gap-2 text-xs">
              <div class="flex items-center gap-1.5 font-bold text-gold-300">
                <i class="fa-solid fa-moon"></i>
                <span>Idul Adha 1448 H • <span id="target-date-display">17 Mei 2027</span></span>
              </div>
              <div class="flex items-center gap-1.5 no-print">
                <label for="target-date-input" class="text-[11px] text-emerald-200">Tgl:</label>
                <input
                  type="date"
                  id="target-date-input"
                  value="2027-05-17"
                  onchange="updateTargetDate(this.value)"
                  class="bg-emerald-950/90 text-white text-[11px] sm:text-xs rounded-lg px-2 py-1 border border-emerald-600/70 focus:outline-none focus:border-gold-400"
                />
              </div>
            </div>

            <!-- Angka Countdown Utama + Status Singkat -->
            <div class="flex flex-wrap items-end justify-between gap-3">
              <div>
                <div class="flex items-baseline gap-2">
                  <span
                    id="cd-days"
                    class="text-3xl sm:text-4xl font-extrabold text-gold-400 tracking-tight"
                    >000</span
                  >
                  <span class="text-sm sm:text-base font-bold text-emerald-100"
                    >hari lagi menuju Idul Adha</span
                  >
                </div>
                <p class="text-xs text-emerald-200/90 mt-0.5">
                  Waktu tersisa:
                  <strong id="cd-hours" class="text-white">00</strong>j :
                  <strong id="cd-minutes" class="text-white">00</strong>m :
                  <strong id="cd-seconds" class="text-gold-300">00</strong>d
                </p>
              </div>
              <div
                class="w-11 h-11 rounded-2xl bg-gradient-to-br from-gold-300 to-amber-500 text-emerald-950 flex items-center justify-center text-xl shadow-md shrink-0"
              >
                <i class="fa-solid fa-mosque"></i>
              </div>
            </div>

            <!-- Baris Mini Rekomendasi Setoran Harian / Mingguan / Bulanan di Dalam Kartu Atas -->
            <div
              class="grid grid-cols-3 gap-2 pt-3 border-t border-emerald-700/60 text-center bg-emerald-950/55 rounded-2xl p-2.5"
            >
              <button
                type="button"
                onclick="quickFillDeposit('daily')"
                class="rounded-xl p-1.5 hover:bg-emerald-800/70 transition text-center no-print"
              >
                <span class="block text-[10px] text-emerald-300 font-semibold">Per Hari</span>
                <span id="hero-mini-daily" class="block text-xs sm:text-sm font-extrabold text-white mt-0.5">Rp 0</span>
              </button>
              <button
                type="button"
                onclick="quickFillDeposit('weekly')"
                class="rounded-xl p-1.5 bg-gold-500/15 border border-gold-400/40 hover:bg-gold-500/25 transition text-center no-print"
              >
                <span class="block text-[10px] text-gold-300 font-bold">Per Pekan</span>
                <span id="hero-mini-weekly" class="block text-xs sm:text-sm font-extrabold text-gold-300 mt-0.5">Rp 0</span>
              </button>
              <button
                type="button"
                onclick="quickFillDeposit('monthly')"
                class="rounded-xl p-1.5 hover:bg-emerald-800/70 transition text-center no-print"
              >
                <span class="block text-[10px] text-emerald-300 font-semibold">Per Bulan</span>
                <span id="hero-mini-monthly" class="block text-xs sm:text-sm font-extrabold text-white mt-0.5">Rp 0</span>
              </button>
            </div>
          </div>
        </div>

        <!-- Kartu Ringkasan Saldo & Progres Target Hewan -->
        <div
          class="lg:col-span-5 bg-white text-slate-800 rounded-3xl p-4 sm:p-6 shadow-md border border-slate-200/90 flex flex-col justify-between"
        >
          <div>
            <div class="flex items-center justify-between gap-2 mb-2">
              <span
                class="text-[11px] font-extrabold uppercase tracking-wider text-emerald-700 bg-emerald-50 px-2.5 py-1 rounded-lg border border-emerald-100"
                >Tabungan Aktif</span
              >
              <button
                type="button"
                onclick="openQuickTargetEdit()"
                class="text-[11px] font-bold text-emerald-700 hover:text-emerald-900 bg-emerald-50 hover:bg-emerald-100 px-2.5 py-1 rounded-lg border border-emerald-200 transition no-print"
              >
                <i class="fa-solid fa-pen-to-square mr-1"></i>Ubah Target
              </button>
            </div>

            <div class="flex items-center justify-between gap-2 mt-2">
              <div>
                <h2
                  id="hero-animal-name"
                  class="text-base sm:text-lg font-extrabold text-slate-900"
                >
                  Sapi 1/7 (Patungan)
                </h2>
                <p class="text-xs text-slate-500">
                  Target: <strong id="hero-target-price" class="text-slate-700">Rp 3.000.000</strong>
                </p>
              </div>
              <div
                class="w-11 h-11 rounded-2xl bg-gold-100 text-gold-600 flex items-center justify-center text-xl shrink-0"
              >
                <i id="hero-animal-icon" class="fa-solid fa-cow"></i>
              </div>
            </div>

            <div class="my-3.5">
              <p class="text-[11px] text-slate-500 font-semibold">Dana Terkumpul</p>
              <span
                id="hero-total-saved"
                class="text-2xl sm:text-3xl font-extrabold text-emerald-800 tracking-tight"
                >Rp 0</span
              >
            </div>
          </div>

          <div class="space-y-2">
            <div class="flex justify-between text-xs font-bold">
              <span class="text-emerald-700" id="hero-progress-pct">0% Tercapai</span>
              <span class="text-amber-600" id="hero-remaining-text">Kurang Rp 3.000.000</span>
            </div>
            <div
              class="w-full h-3 bg-slate-100 rounded-full overflow-hidden p-0.5 border border-slate-200"
            >
              <div
                id="hero-progress-bar"
                class="h-full bg-gradient-to-r from-emerald-600 via-emerald-500 to-gold-500 rounded-full transition-all duration-700 ease-out"
                style="width: 0%"
              ></div>
            </div>
          </div>
        </div>
      </section>

      <!-- ==================== 2. BAR PENGINGAT / STATUS TARGET TIPIS ==================== -->
      <a
        href="#kalkulator-section"
        id="status-reminder-banner"
        class="flex items-center justify-between gap-3 bg-white hover:bg-amber-50/50 border border-slate-200/90 rounded-2xl px-4 py-3 shadow-sm transition group no-print"
      >
        <div class="flex items-center gap-3 min-w-0">
          <span
            class="w-6 h-6 rounded-full bg-amber-500 text-white text-xs font-extrabold flex items-center justify-center shrink-0"
            >!</span
          >
          <p class="text-xs sm:text-sm font-bold text-slate-800 truncate" id="reminder-banner-text">
            Sisa kekurangan tabungan qurban sedang dihitung
          </p>
        </div>
        <i
          class="fa-solid fa-chevron-right text-slate-400 group-hover:translate-x-0.5 transition text-xs shrink-0"
        ></i>
      </a>

      <!-- ==================== 3. BARIS TOMBOL PINTASAN LANGSUNG TERLIHAT (GRID IKON + LABEL) ==================== -->
      <section aria-label="Pintasan Fitur Utama" class="no-print">
        <div
          class="grid grid-cols-4 sm:grid-cols-4 md:grid-cols-8 gap-2.5 sm:gap-3"
        >
          <a
            href="#form-setoran-card"
            onclick="focusDepositInput()"
            class="bg-white hover:bg-emerald-50/70 border border-slate-200/80 hover:border-emerald-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-piggy-bank"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Setoran</span
            >
          </a>

          <a
            href="#pilih-hewan"
            class="bg-white hover:bg-emerald-50/70 border border-slate-200/80 hover:border-emerald-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-gold-100 text-gold-700 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-cow"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Hewan</span
            >
          </a>

          <a
            href="#kalkulator-section"
            class="bg-white hover:bg-emerald-50/70 border border-slate-200/80 hover:border-emerald-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-emerald-50 text-emerald-700 border border-emerald-200 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-calculator"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Rencana</span
            >
          </a>

          <a
            href="#patungan-sapi"
            class="bg-white hover:bg-emerald-50/70 border border-slate-200/80 hover:border-emerald-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-amber-100 text-amber-700 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-users-viewfinder"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Patungan</span
            >
          </a>

          <a
            href="#grafik-tabungan-card"
            class="bg-white hover:bg-emerald-50/70 border border-slate-200/80 hover:border-emerald-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-teal-100 text-teal-700 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-chart-line"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Grafik</span
            >
          </a>

          <a
            href="#riwayat-setoran-card"
            class="bg-white hover:bg-emerald-50/70 border border-slate-200/80 hover:border-emerald-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-slate-100 text-slate-700 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-clock-rotate-left"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Riwayat</span
            >
          </a>

          <a
            href="#panduan-qurban"
            class="bg-white hover:bg-emerald-50/70 border border-slate-200/80 hover:border-emerald-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-emerald-100 text-emerald-800 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-book-open"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Panduan</span
            >
          </a>

          <button
            type="button"
            onclick="exportReport()"
            class="bg-white hover:bg-gold-50/70 border border-slate-200/80 hover:border-gold-300 rounded-2xl p-2.5 sm:p-3 shadow-sm flex flex-col items-center justify-center text-center gap-1.5 transition"
          >
            <div
              class="w-10 h-10 rounded-xl bg-gold-100 text-gold-700 flex items-center justify-center text-base sm:text-lg"
            >
              <i class="fa-solid fa-print"></i>
            </div>
            <span class="text-[11px] sm:text-xs font-bold text-slate-700 leading-tight"
              >Cetak</span
            >
          </button>
        </div>
      </section>

      <!-- ==================== 4. KARTU PESAN / AYAT QURBAN HARI INI ==================== -->
      <section aria-label="Pesan Qurban Hari Ini">
        <div
          class="bg-white rounded-3xl p-4 sm:p-6 shadow-sm border border-slate-200/90"
        >
          <div class="flex items-center justify-between gap-2 mb-3">
            <h2 class="text-sm sm:text-base font-extrabold text-slate-900">
              Pesan Qurban Hari Ini
            </h2>
            <button
              type="button"
              onclick="cycleDailyQuote()"
              class="text-xs font-bold text-emerald-700 hover:text-emerald-900 bg-emerald-50 px-2.5 py-1 rounded-lg border border-emerald-200 transition no-print"
            >
              <i class="fa-solid fa-shuffle mr-1"></i>Ganti Kutipan
            </button>
          </div>

          <div
            class="bg-slate-50/90 border border-slate-200/70 rounded-2xl p-4 sm:p-5"
          >
            <p
              id="daily-quote-arabic"
              dir="rtl"
              class="font-arabic text-xl sm:text-2xl text-slate-900 leading-relaxed mb-2"
            >
              فَصَلِّ لِرَبِّكَ وَانْحَرْ
            </p>
            <p
              id="daily-quote-translation"
              class="text-xs sm:text-sm text-slate-600 italic leading-relaxed"
            >
              "Maka laksanakanlah shalat karena Tuhanmu, dan berkurbanlah (sebagai ibadah dan mendekatkan diri kepada Allah)."
            </p>

            <div
              class="flex items-center justify-between gap-3 mt-4 pt-3 border-t border-slate-200/70"
            >
              <span
                id="daily-quote-source"
                class="text-xs sm:text-sm font-extrabold text-slate-800"
                >QS. Al-Kautsar ayat 2</span
              >
              <button
                type="button"
                onclick="shareOrCopyQuote()"
                class="inline-flex items-center gap-1.5 text-xs sm:text-sm font-bold text-slate-700 hover:text-emerald-700 transition no-print"
              >
                <i class="fa-solid fa-arrow-up-from-bracket"></i>
                <span>Bagikan</span>
              </button>
            </div>
          </div>
        </div>
      </section>

      <!-- ==================== 5. PILIHAN TARGET HEWAN QURBAN ==================== -->
      <section id="pilih-hewan" class="scroll-mt-20">
        <div
          class="flex flex-col sm:flex-row sm:items-end justify-between mb-4 sm:mb-5 gap-2"
        >
          <div>
            <span
              class="text-xs font-bold uppercase tracking-wider text-gold-600"
              >Target Hewan Qurban</span
            >
            <h2 class="text-xl sm:text-2xl font-extrabold text-slate-900 mt-0.5">
              Pilih Paket Hewan Qurban
            </h2>
          </div>
          <p class="text-xs sm:text-sm text-slate-500">
            Klik kartu untuk mengganti target aktif atau sesuaikan nominal harga di bawah.
          </p>
        </div>

        <!-- Grid 2 Kartu Pilihan Hewan Simetris -->
        <div
          class="grid grid-cols-1 md:grid-cols-2 gap-4 sm:gap-5"
          id="animal-cards-container"
        >
          <!-- Di-render secara dinamis via JavaScript -->
        </div>

        <!-- Panel Sesuaikan Nominal Target Aktif (Cepat) -->
        <div
          id="quick-target-panel"
          class="mt-4 bg-white border border-emerald-200 rounded-2xl p-4 sm:p-5 shadow-sm flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3 sm:gap-4 no-print"
        >
          <div class="flex items-center gap-3">
            <div
              class="w-10 h-10 rounded-xl bg-gold-50 text-gold-600 border border-gold-200 flex items-center justify-center text-base shrink-0"
            >
              <i class="fa-solid fa-pen-ruler"></i>
            </div>
            <div>
              <h3 class="font-bold text-xs sm:text-sm text-slate-900">
                Sesuaikan Nominal Target Aktif (<span
                  id="quick-active-animal-label"
                  class="text-emerald-700"
                  >Sapi 1/7</span
                >)
              </h3>
              <p class="text-[11px] sm:text-xs text-slate-500">
                Ubah nominal target paket aktif sesuai harga hewan di daerah Anda.
              </p>
            </div>
          </div>
          <div class="flex items-center gap-2 w-full sm:w-auto">
            <div class="relative flex-1 sm:w-44">
              <span
                class="absolute left-3 top-2.5 text-xs font-bold text-emerald-700"
                >Rp</span
              >
              <input
                type="number"
                id="quick-active-price-input"
                min="100000"
                step="50000"
                placeholder="3000000"
                class="w-full min-h-[42px] pl-8 pr-3 py-2 rounded-xl border border-slate-300 text-xs sm:text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
              />
            </div>
            <button
              type="button"
              onclick="applyQuickActivePrice()"
              class="min-h-[42px] px-4 py-2 rounded-xl bg-emerald-700 hover:bg-emerald-800 text-white font-bold text-xs sm:text-sm transition shadow-sm shrink-0"
            >
              Simpan
            </button>
          </div>
        </div>
      </section>

      <!-- ==================== 6. KALKULATOR & PERENCANA SETORAN OTOMATIS ==================== -->
      <section id="kalkulator-section" class="scroll-mt-20">
        <div
          class="bg-gradient-to-br from-emerald-900 to-emerald-950 rounded-3xl p-5 sm:p-7 text-white shadow-lg relative overflow-hidden"
        >
          <div class="relative z-10">
            <div
              class="flex flex-col md:flex-row md:items-center justify-between gap-3 pb-4 border-b border-emerald-800"
            >
              <div>
                <span
                  class="text-xs font-bold uppercase tracking-wider text-gold-400"
                  >Kalkulator Setoran</span
                >
                <h2 class="text-lg sm:text-2xl font-extrabold mt-0.5">
                  Rencana Setoran Otomatis Menuju Idul Adha
                </h2>
                <p class="text-xs sm:text-sm text-emerald-200 mt-1">
                  Sisa kekurangan <span id="calc-remaining-label" class="font-bold text-white">Rp 0</span> dibagi sisa <span id="calc-days-left-label" class="font-bold text-gold-300">0 hari</span>.
                </p>
              </div>
              <div
                class="bg-emerald-800/80 border border-emerald-700 px-3.5 py-2 rounded-2xl text-xs w-fit"
              >
                <span class="text-emerald-300 block text-[10px]">Status Target:</span>
                <span id="calc-status-badge" class="font-bold text-xs sm:text-sm text-gold-400">Sedang Berjalan</span>
              </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mt-5">
              <!-- Harian -->
              <div
                class="bg-white/10 backdrop-blur-sm border border-emerald-700/70 rounded-2xl p-4 sm:p-5 flex flex-col justify-between"
              >
                <div>
                  <div class="flex items-center justify-between mb-2">
                    <span class="text-xs font-bold uppercase tracking-wider text-emerald-300"
                      >Setoran Harian</span
                    >
                    <span
                      class="w-7 h-7 rounded-lg bg-emerald-800 text-gold-400 flex items-center justify-center text-xs"
                    >
                      <i class="fa-solid fa-sun"></i>
                    </span>
                  </div>
                  <div class="text-xl sm:text-2xl font-extrabold text-white" id="plan-daily">
                    Rp 0
                  </div>
                  <p class="text-xs text-emerald-200 mt-1">
                    Per hari selama <span id="plan-days-count" class="font-semibold text-white">0</span> hari
                  </p>
                </div>
                <button
                  type="button"
                  onclick="quickFillDeposit('daily')"
                  class="mt-4 min-h-[38px] w-full py-2 rounded-xl bg-emerald-700/80 hover:bg-emerald-600 text-xs font-semibold text-white border border-emerald-600 transition no-print"
                >
                  Gunakan Nominal Ini
                </button>
              </div>

              <!-- Mingguan -->
              <div
                class="bg-white/10 backdrop-blur-sm border-2 border-gold-400/70 rounded-2xl p-4 sm:p-5 relative flex flex-col justify-between"
              >
                <span
                  class="absolute -top-2.5 right-4 bg-gold-500 text-emerald-950 text-[10px] font-extrabold uppercase px-2.5 py-0.5 rounded-full"
                  >Paling Populer</span
                >
                <div>
                  <div class="flex items-center justify-between mb-2">
                    <span class="text-xs font-bold uppercase tracking-wider text-gold-300"
                      >Setoran Mingguan</span
                    >
                    <span
                      class="w-7 h-7 rounded-lg bg-gold-500/20 text-gold-400 flex items-center justify-center text-xs"
                    >
                      <i class="fa-solid fa-calendar-week"></i>
                    </span>
                  </div>
                  <div class="text-xl sm:text-2xl font-extrabold text-gold-400" id="plan-weekly">
                    Rp 0
                  </div>
                  <p class="text-xs text-emerald-200 mt-1">
                    Per pekan selama <span id="plan-weeks-count" class="font-semibold text-white">0</span> pekan
                  </p>
                </div>
                <button
                  type="button"
                  onclick="quickFillDeposit('weekly')"
                  class="mt-4 min-h-[38px] w-full py-2 rounded-xl bg-gold-500 hover:bg-gold-400 text-xs font-bold text-emerald-950 transition shadow-sm no-print"
                >
                  Gunakan Nominal Ini
                </button>
              </div>

              <!-- Bulanan -->
              <div
                class="bg-white/10 backdrop-blur-sm border border-emerald-700/70 rounded-2xl p-4 sm:p-5 flex flex-col justify-between"
              >
                <div>
                  <div class="flex items-center justify-between mb-2">
                    <span class="text-xs font-bold uppercase tracking-wider text-emerald-300"
                      >Setoran Bulanan</span
                    >
                    <span
                      class="w-7 h-7 rounded-lg bg-emerald-800 text-gold-400 flex items-center justify-center text-xs"
                    >
                      <i class="fa-solid fa-calendar-days"></i>
                    </span>
                  </div>
                  <div class="text-xl sm:text-2xl font-extrabold text-white" id="plan-monthly">
                    Rp 0
                  </div>
                  <p class="text-xs text-emerald-200 mt-1">
                    Per bulan selama <span id="plan-months-count" class="font-semibold text-white">0</span> bulan
                  </p>
                </div>
                <button
                  type="button"
                  onclick="quickFillDeposit('monthly')"
                  class="mt-4 min-h-[38px] w-full py-2 rounded-xl bg-emerald-700/80 hover:bg-emerald-600 text-xs font-semibold text-white border border-emerald-600 transition no-print"
                >
                  Gunakan Nominal Ini
                </button>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- ==================== 7. TRACKER SETORAN, REKENING BSI, GRAFIK & RIWAYAT ==================== -->
      <section id="tracker-tabungan" class="scroll-mt-20 space-y-5">
        <div>
          <span class="text-xs font-bold uppercase tracking-wider text-gold-600"
            >Catat & Pantau</span
          >
          <h2 class="text-xl sm:text-2xl font-extrabold text-slate-900 mt-0.5">
            Form Setoran, Grafik & Riwayat Tabungan
          </h2>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-5">
          <!-- Form Tambah Setoran & Rekening BSI -->
          <div class="lg:col-span-5 no-print" id="form-setoran-card">
            <div
              class="bg-white rounded-3xl p-5 sm:p-6 shadow-sm border border-slate-200/90"
            >
              <div class="flex items-center gap-3 mb-4">
                <div
                  class="w-10 h-10 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center text-lg shrink-0"
                >
                  <i class="fa-solid fa-piggy-bank"></i>
                </div>
                <div>
                  <h3 class="font-bold text-base sm:text-lg text-slate-900">
                    Tambah Setoran Baru
                  </h3>
                  <p class="text-xs text-slate-500">
                    Data tersimpan otomatis di browser Anda
                  </p>
                </div>
              </div>

              <!-- Kartu Rekening BSI -->
              <div
                class="mb-4 p-3.5 rounded-2xl bg-gradient-to-br from-emerald-50 to-gold-50/60 border border-emerald-200/90"
              >
                <div class="flex items-center justify-between gap-2 mb-1.5">
                  <span
                    class="text-[11px] font-extrabold uppercase tracking-wider text-emerald-800 flex items-center gap-1.5"
                  >
                    <i class="fa-solid fa-building-columns text-gold-600"></i>
                    Rekening Transfer
                  </span>
                  <span
                    class="text-[10px] font-bold px-2 py-0.5 rounded-full bg-white text-emerald-700 border border-emerald-200"
                    >BSI (Kode: 451)</span
                  >
                </div>
                <div
                  class="flex items-center justify-between gap-2 bg-white px-3 py-2 rounded-xl border border-emerald-100"
                >
                  <div>
                    <p
                      id="bank-account-number"
                      class="font-mono font-extrabold text-sm sm:text-base text-slate-900 tracking-wide"
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
                    class="min-h-[38px] px-3 py-1.5 rounded-xl bg-emerald-700 hover:bg-emerald-800 active:scale-95 text-white text-xs font-bold transition flex items-center gap-1.5 shadow-sm shrink-0"
                  >
                    <i class="fa-regular fa-copy"></i>
                    <span id="copy-rekening-text">Salin</span>
                  </button>
                </div>
              </div>

              <!-- Form Setoran -->
              <form id="deposit-form" class="space-y-3.5">
                <div>
                  <label
                    for="deposit-amount"
                    class="block text-xs font-bold text-slate-700 mb-1"
                    >Nominal Setoran (Rp) *</label
                  >
                  <div class="relative">
                    <span
                      class="absolute left-3.5 top-2.5 text-xs font-extrabold text-emerald-700"
                      >Rp</span
                    >
                    <input
                      type="number"
                      id="deposit-amount"
                      min="1000"
                      step="1000"
                      required
                      placeholder="100000"
                      class="w-full min-h-[42px] pl-10 pr-3 py-2 rounded-xl border border-slate-300 text-sm font-bold text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500"
                    />
                  </div>
                  <div class="flex flex-wrap gap-1.5 mt-2">
                    <button
                      type="button"
                      onclick="setPresetAmount(50000)"
                      class="px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-50 text-[11px] font-bold text-slate-600 hover:text-emerald-700 border border-slate-200 transition"
                    >
                      +50rb
                    </button>
                    <button
                      type="button"
                      onclick="setPresetAmount(100000)"
                      class="px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-50 text-[11px] font-bold text-slate-600 hover:text-emerald-700 border border-slate-200 transition"
                    >
                      +100rb
                    </button>
                    <button
                      type="button"
                      onclick="setPresetAmount(250000)"
                      class="px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-50 text-[11px] font-bold text-slate-600 hover:text-emerald-700 border border-slate-200 transition"
                    >
                      +250rb
                    </button>
                    <button
                      type="button"
                      onclick="setPresetAmount(500000)"
                      class="px-2.5 py-1 rounded-lg bg-slate-100 hover:bg-emerald-50 text-[11px] font-bold text-slate-600 hover:text-emerald-700 border border-slate-200 transition"
                    >
                      +500rb
                    </button>
                  </div>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                  <div>
                    <label
                      for="deposit-date"
                      class="block text-xs font-bold text-slate-700 mb-1"
                      >Tanggal *</label
                    >
                    <input
                      type="date"
                      id="deposit-date"
                      required
                      class="w-full min-h-[42px] px-3 py-2 rounded-xl border border-slate-300 text-xs sm:text-sm text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
                    />
                  </div>
                  <div>
                    <label
                      for="deposit-method"
                      class="block text-xs font-bold text-slate-700 mb-1"
                      >Metode</label
                    >
                    <select
                      id="deposit-method"
                      class="w-full min-h-[42px] px-3 py-2 rounded-xl border border-slate-300 text-xs sm:text-sm text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500 bg-white"
                    >
                      <option value="Transfer Bank (BSI)">Transfer Bank (BSI)</option>
                      <option value="E-Wallet / QRIS">E-Wallet / QRIS</option>
                      <option value="Setoran Tunai">Setoran Tunai</option>
                      <option value="Autodebet / Gajian">Autodebet / Gajian</option>
                    </select>
                  </div>
                </div>

                <div>
                  <label
                    for="deposit-note"
                    class="block text-xs font-bold text-slate-700 mb-1"
                    >Catatan</label
                  >
                  <input
                    type="text"
                    id="deposit-note"
                    placeholder="Contoh: Tabungan pekan ke-1"
                    class="w-full min-h-[42px] px-3 py-2 rounded-xl border border-slate-300 text-xs sm:text-sm text-slate-900 focus:outline-none focus:ring-2 focus:ring-emerald-500"
                  />
                </div>

                <button
                  type="submit"
                  class="w-full min-h-[44px] py-2.5 px-4 rounded-xl bg-gradient-to-r from-emerald-700 to-emerald-800 hover:from-emerald-800 hover:to-emerald-900 text-white font-bold text-xs sm:text-sm shadow-md transition flex items-center justify-center gap-2"
                >
                  <i class="fa-solid fa-check-circle text-gold-400"></i>
                  Simpan Setoran
                </button>
              </form>
            </div>
          </div>

          <!-- Grafik & Riwayat Setoran -->
          <div class="lg:col-span-7 space-y-5">
            <div
              id="grafik-tabungan-card"
              class="bg-white rounded-3xl p-5 sm:p-6 shadow-sm border border-slate-200/90 scroll-mt-20"
            >
              <div class="flex flex-wrap items-center justify-between gap-2 mb-3">
                <div>
                  <h3 class="font-bold text-base sm:text-lg text-slate-900">
                    Grafik Akumulasi Tabungan
                  </h3>
                  <p class="text-xs text-slate-500">
                    Perkembangan saldo kumulatif dibanding target hewan qurban
                  </p>
                </div>
                <span
                  id="chart-summary-badge"
                  class="text-[11px] font-bold px-2.5 py-1 rounded-full bg-emerald-50 text-emerald-700 border border-emerald-200"
                  >0 Transaksi</span
                >
              </div>
              <div class="relative h-56 sm:h-64 w-full">
                <canvas id="savingsChart"></canvas>
              </div>
            </div>

            <div
              id="riwayat-setoran-card"
              class="bg-white rounded-3xl p-5 sm:p-6 shadow-sm border border-slate-200/90 scroll-mt-20"
            >
              <div
                class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 mb-4"
              >
                <div>
                  <h3 class="font-bold text-base sm:text-lg text-slate-900">
                    Riwayat Setoran
                  </h3>
                  <p class="text-xs text-slate-500">
                    Edit atau hapus catatan setoran kapan saja
                  </p>
                </div>
                <div class="flex items-center gap-2 no-print">
                  <input
                    type="text"
                    id="deposit-search"
                    oninput="renderDeposits()"
                    placeholder="Cari setoran..."
                    class="px-3 py-1.5 rounded-xl border border-slate-200 text-xs focus:outline-none focus:ring-2 focus:ring-emerald-500 w-full sm:w-40"
                  />
                  <button
                    type="button"
                    onclick="confirmClearAllDeposits()"
                    title="Hapus Semua Setoran"
                    class="min-h-[34px] px-3 py-1.5 rounded-xl bg-rose-50 hover:bg-rose-100 text-rose-600 text-xs font-bold border border-rose-200 transition shrink-0"
                  >
                    <i class="fa-solid fa-trash-can"></i>
                  </button>
                </div>
              </div>

              <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse min-w-[460px]">
                  <thead>
                    <tr
                      class="border-b border-slate-200 text-[11px] font-bold uppercase tracking-wider text-slate-400"
                    >
                      <th class="py-2.5 px-2">Tanggal</th>
                      <th class="py-2.5 px-2">Keterangan & Metode</th>
                      <th class="py-2.5 px-2 text-right">Nominal</th>
                      <th class="py-2.5 px-2 text-center no-print">Aksi</th>
                    </tr>
                  </thead>
                  <tbody
                    id="deposits-table-body"
                    class="divide-y divide-slate-100 text-xs sm:text-sm"
                  >
                    <!-- Di-render via JS -->
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- ==================== 8. TRACKER PATUNGAN SAPI 1/7 ==================== -->
      <section id="patungan-sapi" class="scroll-mt-20 space-y-4">
        <div
          class="flex flex-col sm:flex-row sm:items-end justify-between gap-3"
        >
          <div>
            <span
              class="text-xs font-bold uppercase tracking-wider text-gold-600"
              >Patungan Sapi 1/7</span
            >
            <h2 class="text-xl sm:text-2xl font-extrabold text-slate-900 mt-0.5">
              Tracker Patungan Sapi (7 Shohibul Qurban)
            </h2>
          </div>

          <div class="flex items-center gap-2 no-print">
            <button
              type="button"
              onclick="addPatunganMember()"
              class="min-h-[38px] px-3.5 py-2 rounded-xl bg-emerald-700 hover:bg-emerald-800 text-white text-xs font-bold shadow-sm transition flex items-center gap-1.5"
            >
              <i class="fa-solid fa-user-plus text-gold-400"></i>
              <span>Tambah Slot</span>
            </button>
            <button
              type="button"
              onclick="confirmResetPatungan()"
              class="min-h-[38px] px-3 py-2 rounded-xl bg-white hover:bg-slate-50 text-slate-700 text-xs font-bold border border-slate-200 transition"
            >
              Reset Grup
            </button>
          </div>
        </div>

        <div
          class="bg-white rounded-3xl p-5 sm:p-6 shadow-sm border border-slate-200/90"
        >
          <div class="grid grid-cols-2 lg:grid-cols-4 gap-3 sm:gap-4 mb-5">
            <div class="p-3.5 sm:p-4 rounded-2xl bg-emerald-50/80 border border-emerald-100">
              <span class="text-[11px] sm:text-xs font-semibold text-emerald-700"
                >Target Sapi Utuh</span
              >
              <div
                id="patungan-full-cow-target"
                class="text-base sm:text-xl font-extrabold text-slate-900 mt-0.5"
              >
                Rp 21.000.000
              </div>
              <span class="text-[11px] text-slate-500"
                >7 x <span id="patungan-per-share-target">Rp 3.000.000</span></span
              >
            </div>

            <div class="p-3.5 sm:p-4 rounded-2xl bg-gold-50/80 border border-gold-200/80">
              <span class="text-[11px] sm:text-xs font-semibold text-gold-700"
                >Total Terkumpul</span
              >
              <div
                id="patungan-total-collected"
                class="text-base sm:text-xl font-extrabold text-emerald-800 mt-0.5"
              >
                Rp 0
              </div>
              <span id="patungan-pct-label" class="text-[11px] text-slate-600"
                >0% dari sapi</span
              >
            </div>

            <div class="p-3.5 sm:p-4 rounded-2xl bg-slate-50 border border-slate-200">
              <span class="text-[11px] sm:text-xs font-semibold text-slate-600"
                >Slot Terisi / Lunas</span
              >
              <div
                id="patungan-slot-summary"
                class="text-base sm:text-xl font-extrabold text-slate-900 mt-0.5"
              >
                0 / 7 Slot
              </div>
              <span
                id="patungan-paid-count"
                class="text-[11px] text-emerald-700 font-semibold"
                >0 lunas penuh</span
              >
            </div>

            <div class="p-3.5 sm:p-4 rounded-2xl bg-slate-50 border border-slate-200">
              <span class="text-[11px] sm:text-xs font-semibold text-slate-600"
                >Sisa Kekurangan</span
              >
              <div
                id="patungan-remaining"
                class="text-base sm:text-xl font-extrabold text-amber-600 mt-0.5"
              >
                Rp 21.000.000
              </div>
              <span class="text-[11px] text-slate-500">Akumulasi grup</span>
            </div>
          </div>

          <div class="mb-5">
            <div class="flex justify-between text-xs font-bold mb-1.5">
              <span class="text-emerald-800">Progres Pendanaan 1 Ekor Sapi</span>
              <span id="patungan-bar-pct" class="text-gold-600">0%</span>
            </div>
            <div
              class="w-full h-3 bg-slate-100 rounded-full overflow-hidden border border-slate-200"
            >
              <div
                id="patungan-progress-bar"
                class="h-full bg-gradient-to-r from-emerald-600 to-gold-500 rounded-full transition-all duration-500"
                style="width: 0%"
              ></div>
            </div>
          </div>

          <div
            id="patungan-slots-grid"
            class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4"
          >
            <!-- Di-render via JS -->
          </div>
        </div>
      </section>

      <!-- ==================== 9. PANDUAN & FIQIH QURBAN ==================== -->
      <section id="panduan-qurban" class="scroll-mt-20 space-y-4">
        <div>
          <span class="text-xs font-bold uppercase tracking-wider text-gold-600"
            >Panduan Qurban</span
          >
          <h2 class="text-xl sm:text-2xl font-extrabold text-slate-900 mt-0.5">
            Ketentuan & Fiqih Qurban Singkat
          </h2>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
          <div
            class="bg-white rounded-3xl p-5 sm:p-6 shadow-sm border border-slate-200/90 space-y-3"
          >
            <div class="flex items-center gap-3">
              <div
                class="w-10 h-10 rounded-xl bg-emerald-100 text-emerald-700 flex items-center justify-center text-lg shrink-0"
              >
                <i class="fa-solid fa-scale-balanced"></i>
              </div>
              <h3 class="font-bold text-base sm:text-lg text-slate-900">
                Ketentuan Hewan & Patungan
              </h3>
            </div>
            <ul class="space-y-2 text-xs sm:text-sm text-slate-600 leading-relaxed">
              <li class="flex items-start gap-2">
                <i class="fa-solid fa-check text-emerald-600 mt-1 shrink-0"></i>
                <span
                  ><strong>Kambing / Domba:</strong> Minimal umur 1 tahun dan sah untuk <strong>1 orang</strong>.</span
                >
              </li>
              <li class="flex items-start gap-2">
                <i class="fa-solid fa-check text-emerald-600 mt-1 shrink-0"></i>
                <span
                  ><strong>Sapi / Kerbau:</strong> Minimal umur 2 tahun dan sah untuk <strong>patungan hingga 7 orang</strong>.</span
                >
              </li>
              <li class="flex items-start gap-2">
                <i class="fa-solid fa-check text-emerald-600 mt-1 shrink-0"></i>
                <span
                  ><strong>Kondisi Hewan:</strong> Sehat, tidak pincang, tidak buta sebelah, dan bebas cacat berat.</span
                >
              </li>
            </ul>
          </div>

          <div
            class="bg-white rounded-3xl p-5 sm:p-6 shadow-sm border border-slate-200/90 space-y-3"
          >
            <div class="flex items-center gap-3">
              <div
                class="w-10 h-10 rounded-xl bg-gold-100 text-gold-600 flex items-center justify-center text-lg shrink-0"
              >
                <i class="fa-solid fa-hand-holding-heart"></i>
              </div>
              <h3 class="font-bold text-base sm:text-lg text-slate-900">
                Anjuran Pembagian Daging Qurban
              </h3>
            </div>
            <p class="text-xs sm:text-sm text-slate-600 leading-relaxed">
              Anjuran pembagian sepertiga proporsional bagi shohibul qurban, kerabat/tetangga, dan fakir miskin:
            </p>
            <div class="grid grid-cols-3 gap-2 pt-1 text-center">
              <div class="p-2.5 sm:p-3 rounded-2xl bg-emerald-50 border border-emerald-100">
                <span class="block text-base sm:text-lg font-extrabold text-emerald-800">1/3</span>
                <span class="text-[10px] sm:text-xs font-semibold text-slate-600">Keluarga</span>
              </div>
              <div class="p-2.5 sm:p-3 rounded-2xl bg-gold-50 border border-gold-200">
                <span class="block text-base sm:text-lg font-extrabold text-gold-700">1/3</span>
                <span class="text-[10px] sm:text-xs font-semibold text-slate-600">Tetangga</span>
              </div>
              <div class="p-2.5 sm:p-3 rounded-2xl bg-slate-50 border border-slate-200">
                <span class="block text-base sm:text-lg font-extrabold text-slate-800">1/3</span>
                <span class="text-[10px] sm:text-xs font-semibold text-slate-600">Dhuafa</span>
              </div>
            </div>
          </div>
        </div>
      </section>
    </main>

    <!-- ==================== FOOTER ==================== -->
    <footer
      class="bg-emerald-950 text-emerald-100 border-t border-emerald-900 mt-10 no-print shrink-0"
    >
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <div
          class="flex flex-col sm:flex-row items-center justify-between gap-4 pb-6 border-b border-emerald-900 text-center sm:text-left"
        >
          <div class="flex items-center gap-3">
            <div
              class="w-9 h-9 rounded-xl bg-emerald-900 border border-emerald-700 flex items-center justify-center text-gold-400 shrink-0"
            >
              <i class="fa-solid fa-kaaba"></i>
            </div>
            <div>
              <span class="font-extrabold text-base text-white"
                >Qurban<span class="text-gold-400">Ku</span></span
              >
              <p class="text-xs text-emerald-300">
                Aplikasi Perencana & Tracker Tabungan Qurban Idul Adha 1448 H
              </p>
            </div>
          </div>
          <p class="text-xs text-emerald-400">
            Penyimpanan lokal aman di browser Anda.
          </p>
        </div>
        <div class="pt-4 text-center text-xs text-emerald-500">
          QurbanKu &copy; 1448 H / 2027 • Dibuat untuk memudahkan ibadah qurban.
        </div>
      </div>
    </footer>

    <!-- ==================== JAVASCRIPT APLIKASI ==================== -->
    <script>
      const STORAGE_KEY = "qurbanku_state_v1";

      // Kutipan / Ayat Qurban Harian
      const DAILY_QUOTES = [
        {
          arabic: "فَصَلِّ لِرَبِّكَ وَانْحَرْ",
          translation:
            '"Maka laksanakanlah shalat karena Tuhanmu, dan berkurbanlah (sebagai ibadah dan mendekatkan diri kepada Allah)."',
          source: "QS. Al-Kautsar ayat 2",
        },
        {
          arabic: "لَنْ يَنَالَ اللَّهَ لُحُومُهَا وَلَا دِمَاؤُهَا وَلَكِنْ يَنَالُهُ التَّقْوَىٰ مِنْكُمْ",
          translation:
            '"Daging-daging (hewan kurban) dan darahnya itu sekali-kali tidak akan sampai kepada Allah, tetapi yang sampai kepada-Nya adalah ketakwaan kamu."',
          source: "QS. Al-Hajj ayat 37",
        },
        {
          arabic: "مَا عَمِلَ آدَمِيٌّ مِنْ عَمَلٍ يَوْمَ النَّحْرِ أَحَبَّ إِلَى اللَّهِ مِنْ إِهْرَاقِ الدَّمِ",
          translation:
            '"Tidak ada suatu amalan yang dikerjakan anak Adam pada hari Nahr yang lebih dicintai Allah selain menyembelih hewan qurban."',
          source: "HR. Tirmidzi & Ibnu Majah",
        },
      ];

      // Katalog Pilihan Hewan Qurban Default
      const DEFAULT_ANIMALS = [
        {
          id: "sapi-1-7",
          name: "Sapi 1/7 (Patungan)",
          subtitle: "1 bagian dari 7 orang • Bobot & kualitas terstandar",
          icon: "fa-cow",
          badge: "Patungan 1/7",
          targetPrice: 3000000,
        },
        {
          id: "kambing-std",
          name: "Kambing / Domba Standard",
          subtitle: "Qurban mandiri 1 orang • Usia ≥ 1 tahun, sehat & layak",
          icon: "fa-horse-head",
          badge: "Mandiri Standard",
          targetPrice: 2500000,
        },
        {
          id: "kambing-prem",
          name: "Kambing / Domba Premium",
          subtitle: "Qurban mandiri bobot besar (35–42 kg) • Daging lebih banyak",
          icon: "fa-horse-head",
          badge: "Mandiri Premium",
          targetPrice: 3500000,
        },
        {
          id: "sapi-utuh",
          name: "Sapi Utuh (1 Ekor)",
          subtitle: "Qurban 1 ekor penuh • Keluarga besar atau lembaga",
          icon: "fa-cow",
          badge: "1 Ekor Penuh",
          targetPrice: 21000000,
        },
      ];

      function createDefaultPatungan(sharePrice = 3000000) {
        return [
          {
            id: 1,
            name: "Ahmad Fauzi (Anda)",
            paidAmount: 1800000,
            targetAmount: sharePrice,
            note: "Cicilan 3x",
          },
          {
            id: 2,
            name: "Siti Rahmawati",
            paidAmount: sharePrice,
            targetAmount: sharePrice,
            note: "Lunas transfer",
          },
          {
            id: 3,
            name: "Budi Santoso",
            paidAmount: 2250000,
            targetAmount: sharePrice,
            note: "Kurang 750rb",
          },
          {
            id: 4,
            name: "Hendra Wijaya",
            paidAmount: 1500000,
            targetAmount: sharePrice,
            note: "Setoran awal",
          },
          {
            id: 5,
            name: "Nurul Hidayah",
            paidAmount: sharePrice,
            targetAmount: sharePrice,
            note: "Lunas",
          },
          {
            id: 6,
            name: "Rizky Pratama",
            paidAmount: 900000,
            targetAmount: sharePrice,
            note: "Cicilan pekan 1",
          },
          {
            id: 7,
            name: "Slot Terbuka #7",
            paidAmount: 0,
            targetAmount: sharePrice,
            note: "Menunggu peserta",
          },
        ];
      }

      function createDemoState() {
        const today = new Date();
        const fmt = (daysAgo) => {
          const d = new Date(today.getTime() - daysAgo * 86400000);
          return d.toISOString().split("T")[0];
        };

        return {
          targetDate: "2027-05-17",
          selectedAnimalId: "sapi-1-7",
          quoteIndex: 0,
          animals: DEFAULT_ANIMALS.map((a) => ({ ...a })),
          deposits: [
            {
              id: "dep-1",
              amount: 500000,
              date: fmt(45),
              method: "Transfer Bank (BSI)",
              note: "Niat setoran perdana qurban",
            },
            {
              id: "dep-2",
              amount: 350000,
              date: fmt(30),
              method: "Autodebet / Gajian",
              note: "Alokasi gajian bulan lalu",
            },
            {
              id: "dep-3",
              amount: 450000,
              date: fmt(15),
              method: "E-Wallet / QRIS",
              note: "Setoran tengah bulan",
            },
            {
              id: "dep-4",
              amount: 400000,
              date: fmt(3),
              method: "Transfer Bank (BSI)",
              note: "Setoran pekan ini",
            },
          ],
          patungan: createDefaultPatungan(3000000),
        };
      }

      let appState = loadState();
      let savingsChartInstance = null;
      let countdownInterval = null;

      function loadState() {
        try {
          const raw = localStorage.getItem(STORAGE_KEY);
          if (!raw) return createDemoState();
          const parsed = JSON.parse(raw);
          if (
            !parsed ||
            !Array.isArray(parsed.animals) ||
            !Array.isArray(parsed.deposits)
          ) {
            return createDemoState();
          }
          if (!Array.isArray(parsed.patungan) || parsed.patungan.length === 0) {
            parsed.patungan = createDefaultPatungan(3000000);
          }
          if (typeof parsed.quoteIndex !== "number") {
            parsed.quoteIndex = 0;
          }
          return parsed;
        } catch (err) {
          console.error("Gagal memuat state:", err);
          return createDemoState();
        }
      }

      function saveState() {
        try {
          localStorage.setItem(STORAGE_KEY, JSON.stringify(appState));
        } catch (err) {
          console.error("Gagal menyimpan state:", err);
          showToast("Gagal menyimpan data ke browser.", "error");
        }
      }

      function formatRupiah(num) {
        const val = Number.isFinite(Number(num)) ? Math.round(Number(num)) : 0;
        return "Rp " + val.toLocaleString("id-ID");
      }

      function formatDateID(dateStr) {
        if (!dateStr) return "-";
        const parts = dateStr.split("-");
        if (parts.length !== 3) return dateStr;
        const d = new Date(Number(parts[0]), Number(parts[1]) - 1, Number(parts[2]));
        if (Number.isNaN(d.getTime())) return dateStr;
        return d.toLocaleDateString("id-ID", {
          day: "numeric",
          month: "short",
          year: "numeric",
        });
      }

      function escapeHTML(str) {
        return String(str || "")
          .replace(/&/g, "&amp;")
          .replace(/</g, "&lt;")
          .replace(/>/g, "&gt;")
          .replace(/"/g, "&quot;")
          .replace(/'/g, "&#39;");
      }

      function showToast(message, type = "success") {
        const container = document.getElementById("toast-container");
        if (!container) return;

        const styles = {
          success: "bg-emerald-900 text-white border-emerald-600",
          error: "bg-rose-800 text-white border-rose-600",
          info: "bg-slate-900 text-white border-slate-700",
        };
        const icons = {
          success: "fa-circle-check text-gold-400",
          error: "fa-circle-xmark text-rose-300",
          info: "fa-circle-info text-emerald-300",
        };

        const toast = document.createElement("div");
        toast.className = `toast-enter pointer-events-auto flex items-center gap-3 px-4 py-3 rounded-2xl border shadow-xl text-xs sm:text-sm font-semibold ${styles[type] || styles.success}`;
        toast.innerHTML = `
          <i class="fa-solid ${icons[type] || icons.success} text-base shrink-0"></i>
          <span class="flex-1">${escapeHTML(message)}</span>
        `;

        container.appendChild(toast);

        setTimeout(() => {
          toast.style.opacity = "0";
          toast.style.transform = "translateY(-10px)";
          toast.style.transition = "all 0.2s ease";
          setTimeout(() => toast.remove(), 220);
        }, 3000);
      }

      function showConfirmModal({
        title = "Konfirmasi Tindakan",
        message = "Apakah Anda yakin ingin melanjutkan?",
        okText = "Ya, Lanjutkan",
        onConfirm = () => {},
      }) {
        const modal = document.getElementById("confirm-modal");
        const box = document.getElementById("confirm-modal-box");
        const titleEl = document.getElementById("confirm-title");
        const msgEl = document.getElementById("confirm-message");
        const okBtn = document.getElementById("confirm-ok-btn");
        const cancelBtn = document.getElementById("confirm-cancel-btn");

        titleEl.textContent = title;
        msgEl.textContent = message;
        okBtn.textContent = okText;

        modal.classList.remove("hidden");
        modal.classList.add("flex");
        requestAnimationFrame(() => {
          box.classList.remove("scale-95", "opacity-0");
          box.classList.add("scale-100", "opacity-100");
        });

        const cleanup = () => {
          box.classList.remove("scale-100", "opacity-100");
          box.classList.add("scale-95", "opacity-0");
          setTimeout(() => {
            modal.classList.add("hidden");
            modal.classList.remove("flex");
          }, 160);
          okBtn.removeEventListener("click", handleOk);
          cancelBtn.removeEventListener("click", handleCancel);
        };

        const handleOk = () => {
          cleanup();
          onConfirm();
        };
        const handleCancel = () => cleanup();

        okBtn.addEventListener("click", handleOk);
        cancelBtn.addEventListener("click", handleCancel);
      }

      function getActiveAnimal() {
        return (
          appState.animals.find((a) => a.id === appState.selectedAnimalId) ||
          appState.animals[0]
        );
      }

      function getTotalSaved() {
        return appState.deposits.reduce(
          (sum, d) => sum + (Number(d.amount) || 0),
          0
        );
      }

      function getDaysRemaining() {
        const target = new Date(appState.targetDate + "T00:00:00");
        const now = new Date();
        const diffMs = target.getTime() - now.getTime();
        if (Number.isNaN(diffMs) || diffMs <= 0) return 1;
        return Math.max(1, Math.ceil(diffMs / (1000 * 60 * 60 * 24)));
      }

      function updateCountdown() {
        const inputEl = document.getElementById("target-date-input");
        const displayEl = document.getElementById("target-date-display");
        if (inputEl && inputEl.value !== appState.targetDate) {
          inputEl.value = appState.targetDate;
        }
        if (displayEl) {
          displayEl.textContent = formatDateID(appState.targetDate);
        }

        const target = new Date(appState.targetDate + "T00:00:00").getTime();
        const dist = Math.max(0, target - Date.now());

        const days = Math.floor(dist / (1000 * 60 * 60 * 24));
        const hours = Math.floor((dist % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
        const minutes = Math.floor((dist % (1000 * 60 * 60)) / (1000 * 60));
        const seconds = Math.floor((dist % (1000 * 60)) / 1000);

        const setText = (id, val) => {
          const el = document.getElementById(id);
          if (el) el.textContent = val;
        };
        setText("cd-days", String(days).padStart(3, "0"));
        setText("cd-hours", String(hours).padStart(2, "0"));
        setText("cd-minutes", String(minutes).padStart(2, "0"));
        setText("cd-seconds", String(seconds).padStart(2, "0"));
      }

      function updateTargetDate(val) {
        if (!val) return;
        appState.targetDate = val;
        saveState();
        updateCountdown();
        renderCalculator();
        showToast(`Tanggal target diubah ke ${formatDateID(val)}`, "info");
      }

      function renderDailyQuote() {
        const idx = Math.abs(Number(appState.quoteIndex) || 0) % DAILY_QUOTES.length;
        const q = DAILY_QUOTES[idx];
        document.getElementById("daily-quote-arabic").textContent = q.arabic;
        document.getElementById("daily-quote-translation").textContent = q.translation;
        document.getElementById("daily-quote-source").textContent = q.source;
      }

      function cycleDailyQuote() {
        appState.quoteIndex = ((Number(appState.quoteIndex) || 0) + 1) % DAILY_QUOTES.length;
        saveState();
        renderDailyQuote();
        showToast("Kutipan qurban diperbarui.", "info");
      }

      function shareOrCopyQuote() {
        const idx = Math.abs(Number(appState.quoteIndex) || 0) % DAILY_QUOTES.length;
        const q = DAILY_QUOTES[idx];
        const shareText = `${q.arabic}\n${q.translation}\n— ${q.source} (QurbanKu)`;

        if (navigator.share) {
          navigator
            .share({ title: "Pesan Qurban Hari Ini - QurbanKu", text: shareText })
            .catch(() => fallbackCopy(shareText, () => showToast("Kutipan disalin ke papan klip!", "success")));
        } else {
          fallbackCopy(shareText, () => showToast("Kutipan disalin ke papan klip!", "success"));
        }
      }

      function renderHeroAndSummary() {
        const animal = getActiveAnimal();
        const totalSaved = getTotalSaved();
        const targetPrice = Math.max(1, Number(animal.targetPrice) || 1);
        const pct = Math.min(100, Math.round((totalSaved / targetPrice) * 100));
        const remaining = Math.max(0, targetPrice - totalSaved);

        document.getElementById("hero-animal-name").textContent = animal.name;
        const iconEl = document.getElementById("hero-animal-icon");
        if (iconEl) iconEl.className = `fa-solid ${animal.icon}`;

        document.getElementById("hero-total-saved").textContent = formatRupiah(totalSaved);
        document.getElementById("hero-target-price").textContent = formatRupiah(targetPrice);
        document.getElementById("hero-progress-pct").textContent = `${pct}% Tercapai`;
        document.getElementById("hero-remaining-text").textContent =
          remaining === 0 ? "Target Terpenuhi!" : `Kurang ${formatRupiah(remaining)}`;
        document.getElementById("hero-progress-bar").style.width = `${pct}%`;

        // Banner pengingat tipis di bawah hero
        const bannerText = document.getElementById("reminder-banner-text");
        if (bannerText) {
          bannerText.textContent =
            remaining === 0
              ? `Target ${animal.name} sudah 100% tercapai! Siap berqurban.`
              : `Sisa kekurangan ${animal.name}: ${formatRupiah(remaining)} (${pct}% tercapai)`;
        }

        // Sinkron panel ubah nominal target aktif
        document.getElementById("quick-active-animal-label").textContent = animal.name;
        const quickInput = document.getElementById("quick-active-price-input");
        if (quickInput && document.activeElement !== quickInput) {
          quickInput.value = animal.targetPrice;
        }
      }

      function renderAnimalCards() {
        const container = document.getElementById("animal-cards-container");
        if (!container) return;

        container.innerHTML = appState.animals
          .map((animal) => {
            const isSelected = animal.id === appState.selectedAnimalId;
            return `
              <div
                onclick="selectAnimal('${animal.id}')"
                class="cursor-pointer rounded-2xl p-4 sm:p-5 transition-all border-2 ${
                  isSelected
                    ? "bg-white border-emerald-600 shadow-md ring-2 ring-emerald-500/20"
                    : "bg-white border-slate-200 hover:border-emerald-300 shadow-sm"
                }"
              >
                <div class="flex items-start justify-between gap-2 mb-2.5">
                  <div class="flex items-center gap-3">
                    <div
                      class="w-10 h-10 sm:w-11 sm:h-11 rounded-2xl flex items-center justify-center text-lg shrink-0 ${
                        isSelected
                          ? "bg-emerald-700 text-gold-400 shadow-sm"
                          : "bg-emerald-50 text-emerald-700"
                      }"
                    >
                      <i class="fa-solid ${animal.icon}"></i>
                    </div>
                    <div>
                      <span
                        class="text-[10px] font-bold uppercase tracking-wider px-2 py-0.5 rounded-full ${
                          isSelected
                            ? "bg-emerald-100 text-emerald-800"
                            : "bg-slate-100 text-slate-600"
                        }"
                        >${escapeHTML(animal.badge)}</span
                      >
                      <h3 class="font-bold text-sm sm:text-base text-slate-900 mt-0.5">
                        ${escapeHTML(animal.name)}
                      </h3>
                    </div>
                  </div>
                  ${
                    isSelected
                      ? `<span class="w-6 h-6 rounded-full bg-emerald-600 text-white flex items-center justify-center text-xs shrink-0"><i class="fa-solid fa-check"></i></span>`
                      : `<span class="w-6 h-6 rounded-full border border-slate-300 shrink-0"></span>`
                  }
                </div>

                <p class="text-xs text-slate-500 mb-3 leading-relaxed">
                  ${escapeHTML(animal.subtitle)}
                </p>

                <div class="pt-2.5 border-t border-slate-100 flex items-center justify-between">
                  <span class="text-xs font-medium text-slate-500">Target Dana</span>
                  <span class="text-sm sm:text-base font-extrabold text-emerald-800"
                    >${formatRupiah(animal.targetPrice)}</span
                  >
                </div>
              </div>
            `;
          })
          .join("");
      }

      function selectAnimal(id) {
        if (!appState.animals.some((a) => a.id === id)) return;
        appState.selectedAnimalId = id;
        saveState();
        renderAll();
        const chosen = getActiveAnimal();
        showToast(`Target aktif diubah ke ${chosen.name}`, "info");
      }

      function openQuickTargetEdit() {
        const panel = document.getElementById("quick-target-panel");
        const input = document.getElementById("quick-active-price-input");
        if (panel) panel.scrollIntoView({ behavior: "smooth", block: "center" });
        if (input) {
          setTimeout(() => {
            input.focus();
            input.select();
          }, 250);
        }
      }

      function applyQuickActivePrice() {
        const input = document.getElementById("quick-active-price-input");
        const newPrice = Number(input.value);
        if (!newPrice || newPrice < 100000) {
          showToast("Masukkan nominal target minimal Rp 100.000.", "error");
          return;
        }
        const animal = getActiveAnimal();
        animal.targetPrice = Math.round(newPrice);
        saveState();
        renderAll();
        showToast(
          `Nominal target ${animal.name} disimpan: ${formatRupiah(animal.targetPrice)}`,
          "success"
        );
      }

      function renderCalculator() {
        const animal = getActiveAnimal();
        const totalSaved = getTotalSaved();
        const remaining = Math.max(0, (Number(animal.targetPrice) || 0) - totalSaved);
        const daysLeft = getDaysRemaining();
        const weeksLeft = Math.max(1, Math.ceil(daysLeft / 7));
        const monthsLeft = Math.max(1, Math.ceil(daysLeft / 30));

        const daily = remaining > 0 ? Math.ceil(remaining / daysLeft) : 0;
        const weekly = remaining > 0 ? Math.ceil(remaining / weeksLeft) : 0;
        const monthly = remaining > 0 ? Math.ceil(remaining / monthsLeft) : 0;

        document.getElementById("calc-remaining-label").textContent = formatRupiah(remaining);
        document.getElementById("calc-days-left-label").textContent = `${daysLeft} hari`;

        const badge = document.getElementById("calc-status-badge");
        if (remaining === 0) {
          badge.textContent = "Target Tercapai (100%)";
          badge.className = "font-bold text-xs sm:text-sm text-emerald-300";
        } else {
          badge.textContent = `Kurang ${formatRupiah(remaining)}`;
          badge.className = "font-bold text-xs sm:text-sm text-gold-400";
        }

        document.getElementById("plan-daily").textContent = formatRupiah(daily);
        document.getElementById("plan-weekly").textContent = formatRupiah(weekly);
        document.getElementById("plan-monthly").textContent = formatRupiah(monthly);

        document.getElementById("hero-mini-daily").textContent = formatRupiah(daily);
        document.getElementById("hero-mini-weekly").textContent = formatRupiah(weekly);
        document.getElementById("hero-mini-monthly").textContent = formatRupiah(monthly);

        document.getElementById("plan-days-count").textContent = daysLeft;
        document.getElementById("plan-weeks-count").textContent = weeksLeft;
        document.getElementById("plan-months-count").textContent = monthsLeft;

        window.__qurbanPlanAmounts = { daily, weekly, monthly };
      }

      function quickFillDeposit(type) {
        const plans = window.__qurbanPlanAmounts || {
          daily: 10000,
          weekly: 70000,
          monthly: 300000,
        };
        const amt = plans[type] || 50000;
        const input = document.getElementById("deposit-amount");
        if (input) {
          input.value = amt > 0 ? amt : 50000;
          input.scrollIntoView({ behavior: "smooth", block: "center" });
          input.focus();
          showToast(`Nominal ${formatRupiah(input.value)} diisikan ke form setoran.`, "info");
        }
      }

      function focusDepositInput() {
        const input = document.getElementById("deposit-amount");
        if (input) setTimeout(() => input.focus(), 250);
      }

      function setPresetAmount(val) {
        const input = document.getElementById("deposit-amount");
        if (!input) return;
        const current = Number(input.value) || 0;
        input.value = current + val;
        input.focus();
      }

      function copyBankAccount() {
        const numEl = document.getElementById("bank-account-number");
        const btnText = document.getElementById("copy-rekening-text");
        if (!numEl) return;
        const accountNo = numEl.textContent.trim();

        const onCopied = () => {
          showToast(`Nomor rekening BSI ${accountNo} berhasil disalin!`, "success");
          if (btnText) {
            const prev = btnText.textContent;
            btnText.textContent = "Tersalin";
            setTimeout(() => {
              btnText.textContent = prev;
            }, 1800);
          }
        };

        if (navigator.clipboard && window.isSecureContext) {
          navigator.clipboard
            .writeText(accountNo)
            .then(onCopied)
            .catch(() => fallbackCopy(accountNo, onCopied));
        } else {
          fallbackCopy(accountNo, onCopied);
        }
      }

      function fallbackCopy(text, cb) {
        const ta = document.createElement("textarea");
        ta.value = text;
        ta.style.position = "fixed";
        ta.style.opacity = "0";
        document.body.appendChild(ta);
        ta.select();
        try {
          document.execCommand("copy");
          if (cb) cb();
        } catch (e) {
          showToast("Salin manual teks berikut: " + text, "info");
        }
        document.body.removeChild(ta);
      }

      document.getElementById("deposit-form").addEventListener("submit", function (e) {
        e.preventDefault();
        const amountInput = document.getElementById("deposit-amount");
        const dateInput = document.getElementById("deposit-date");
        const methodInput = document.getElementById("deposit-method");
        const noteInput = document.getElementById("deposit-note");

        const amount = Number(amountInput.value);
        const date = dateInput.value;
        const method = methodInput.value;
        const note = noteInput.value.trim();

        if (!amount || amount < 1000) {
          showToast("Masukkan nominal setoran minimal Rp 1.000.", "error");
          return;
        }
        if (!date) {
          showToast("Pilih tanggal setoran terlebih dahulu.", "error");
          return;
        }

        const newDeposit = {
          id: "dep-" + Date.now() + "-" + Math.random().toString(36).slice(2, 6),
          amount: Math.round(amount),
          date,
          method,
          note: note || method,
        };

        appState.deposits.push(newDeposit);
        saveState();

        amountInput.value = "";
        noteInput.value = "";
        renderAll();
        showToast(`Setoran ${formatRupiah(newDeposit.amount)} berhasil dicatat!`, "success");
      });

      function renderDeposits() {
        const tbody = document.getElementById("deposits-table-body");
        const searchEl = document.getElementById("deposit-search");
        const badgeEl = document.getElementById("chart-summary-badge");
        if (!tbody) return;

        const query = (searchEl ? searchEl.value : "").toLowerCase().trim();
        const sorted = [...appState.deposits].sort((a, b) => b.date.localeCompare(a.date));

        const filtered = query
          ? sorted.filter(
              (d) =>
                (d.note || "").toLowerCase().includes(query) ||
                (d.method || "").toLowerCase().includes(query) ||
                String(d.amount).includes(query)
            )
          : sorted;

        if (badgeEl) {
          badgeEl.textContent = `${appState.deposits.length} Transaksi`;
        }

        if (filtered.length === 0) {
          tbody.innerHTML = `
            <tr>
              <td colspan="4" class="py-8 text-center text-slate-400 text-xs sm:text-sm">
                <i class="fa-solid fa-inbox text-2xl mb-2 block opacity-60"></i>
                Belum ada catatan setoran yang ditampilkan.
              </td>
            </tr>
          `;
          return;
        }

        tbody.innerHTML = filtered
          .map(
            (dep) => `
            <tr class="hover:bg-emerald-50/40 transition">
              <td class="py-3 px-2 whitespace-nowrap text-xs font-semibold text-slate-600">
                ${formatDateID(dep.date)}
              </td>
              <td class="py-3 px-2">
                <p class="font-semibold text-slate-800">${escapeHTML(dep.note)}</p>
                <span class="inline-block text-[10px] font-bold text-emerald-700 bg-emerald-50 px-2 py-0.5 rounded-md mt-0.5 border border-emerald-100">${escapeHTML(dep.method)}</span>
              </td>
              <td class="py-3 px-2 text-right whitespace-nowrap font-extrabold text-emerald-700">
                +${formatRupiah(dep.amount)}
              </td>
              <td class="py-3 px-2 text-center whitespace-nowrap no-print">
                <button
                  type="button"
                  onclick="openEditDepositModal('${dep.id}')"
                  title="Edit Setoran"
                  class="p-1.5 rounded-lg text-slate-500 hover:text-emerald-700 hover:bg-emerald-50 transition"
                >
                  <i class="fa-solid fa-pen"></i>
                </button>
                <button
                  type="button"
                  onclick="confirmDeleteDeposit('${dep.id}')"
                  title="Hapus Setoran"
                  class="p-1.5 rounded-lg text-slate-500 hover:text-rose-600 hover:bg-rose-50 transition"
                >
                  <i class="fa-solid fa-trash"></i>
                </button>
              </td>
            </tr>
          `
          )
          .join("");
      }

      function openEditDepositModal(id) {
        const dep = appState.deposits.find((d) => d.id === id);
        if (!dep) return;

        document.getElementById("edit-deposit-id").value = dep.id;
        document.getElementById("edit-deposit-amount").value = dep.amount;
        document.getElementById("edit-deposit-date").value = dep.date;
        document.getElementById("edit-deposit-method").value =
          dep.method || "Transfer Bank (BSI)";
        document.getElementById("edit-deposit-note").value = dep.note || "";

        const modal = document.getElementById("edit-deposit-modal");
        modal.classList.remove("hidden");
        modal.classList.add("flex");
      }

      function closeEditDepositModal() {
        const modal = document.getElementById("edit-deposit-modal");
        modal.classList.add("hidden");
        modal.classList.remove("flex");
      }

      document.getElementById("edit-deposit-form").addEventListener("submit", function (e) {
        e.preventDefault();
        const id = document.getElementById("edit-deposit-id").value;
        const amount = Number(document.getElementById("edit-deposit-amount").value);
        const date = document.getElementById("edit-deposit-date").value;
        const method = document.getElementById("edit-deposit-method").value;
        const note = document.getElementById("edit-deposit-note").value.trim();

        if (!amount || amount < 1000 || !date) {
          showToast("Pastikan nominal dan tanggal setoran valid.", "error");
          return;
        }

        const idx = appState.deposits.findIndex((d) => d.id === id);
        if (idx !== -1) {
          appState.deposits[idx] = {
            ...appState.deposits[idx],
            amount: Math.round(amount),
            date,
            method,
            note: note || method,
          };
          saveState();
          closeEditDepositModal();
          renderAll();
          showToast("Perubahan setoran berhasil disimpan.", "success");
        }
      });

      function confirmDeleteDeposit(id) {
        const dep = appState.deposits.find((d) => d.id === id);
        if (!dep) return;
        showConfirmModal({
          title: "Hapus Setoran",
          message: `Hapus catatan setoran sebesar ${formatRupiah(dep.amount)} (${formatDateID(dep.date)})?`,
          okText: "Hapus",
          onConfirm: () => {
            appState.deposits = appState.deposits.filter((d) => d.id !== id);
            saveState();
            renderAll();
            showToast("Catatan setoran telah dihapus.", "info");
          },
        });
      }

      function confirmClearAllDeposits() {
        if (appState.deposits.length === 0) {
          showToast("Riwayat setoran sudah kosong.", "info");
          return;
        }
        showConfirmModal({
          title: "Hapus Semua Riwayat Setoran",
          message:
            "Seluruh riwayat setoran akan dihapus dan saldo akumulasi menjadi Rp 0. Lanjutkan?",
          okText: "Hapus Semua",
          onConfirm: () => {
            appState.deposits = [];
            saveState();
            renderAll();
            showToast("Seluruh riwayat setoran dihapus.", "info");
          },
        });
      }

      function renderChart() {
        const canvas = document.getElementById("savingsChart");
        if (!canvas || typeof Chart === "undefined") return;

        const animal = getActiveAnimal();
        const targetVal = Number(animal.targetPrice) || 0;

        const sorted = [...appState.deposits].sort((a, b) => a.date.localeCompare(b.date));

        let runningTotal = 0;
        const labels = ["Awal"];
        const cumulativeData = [0];

        sorted.forEach((dep) => {
          runningTotal += Number(dep.amount) || 0;
          labels.push(formatDateID(dep.date));
          cumulativeData.push(runningTotal);
        });

        const targetLine = labels.map(() => targetVal);

        if (savingsChartInstance) {
          savingsChartInstance.destroy();
        }

        const ctx = canvas.getContext("2d");
        savingsChartInstance = new Chart(ctx, {
          type: "line",
          data: {
            labels,
            datasets: [
              {
                label: "Akumulasi Tabungan (Rp)",
                data: cumulativeData,
                borderColor: "#059669",
                backgroundColor: "rgba(16, 185, 129, 0.14)",
                borderWidth: 3,
                pointBackgroundColor: "#047857",
                pointBorderColor: "#ffffff",
                pointBorderWidth: 2,
                pointRadius: 4,
                fill: true,
                tension: 0.3,
              },
              {
                label: "Target Qurban (Rp)",
                data: targetLine,
                borderColor: "#d97706",
                borderDash: [6, 6],
                borderWidth: 2,
                pointRadius: 0,
                fill: false,
              },
            ],
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            interaction: { mode: "index", intersect: false },
            plugins: {
              legend: {
                position: "bottom",
                labels: {
                  usePointStyle: true,
                  boxWidth: 8,
                  font: { family: '"Plus Jakarta Sans", sans-serif', size: 11, weight: "600" },
                },
              },
              tooltip: {
                callbacks: {
                  label: (ctx) => `${ctx.dataset.label}: ${formatRupiah(ctx.parsed.y)}`,
                },
              },
            },
            scales: {
              y: {
                beginAtZero: true,
                ticks: {
                  font: { family: '"Plus Jakarta Sans", sans-serif', size: 10 },
                  callback: (val) =>
                    val >= 1000000
                      ? "Rp " + (val / 1000000).toFixed(1) + "jt"
                      : "Rp " + val,
                },
                grid: { color: "rgba(148, 163, 184, 0.15)" },
              },
              x: {
                ticks: { font: { family: '"Plus Jakarta Sans", sans-serif', size: 10 } },
                grid: { display: false },
              },
            },
          },
        });
      }

      function renderPatungan() {
        const grid = document.getElementById("patungan-slots-grid");
        if (!grid) return;

        const members = appState.patungan || [];
        const totalCollected = members.reduce(
          (sum, m) => sum + (Number(m.paidAmount) || 0),
          0
        );
        const totalTarget = members.reduce(
          (sum, m) => sum + (Number(m.targetAmount) || 3000000),
          0
        );
        const remaining = Math.max(0, totalTarget - totalCollected);
        const pct =
          totalTarget > 0
            ? Math.min(100, Math.round((totalCollected / totalTarget) * 100))
            : 0;
        const paidFullCount = members.filter(
          (m) => (Number(m.paidAmount) || 0) >= (Number(m.targetAmount) || 3000000)
        ).length;
        const activeSlotsCount = members.filter(
          (m) =>
            (Number(m.paidAmount) || 0) > 0 ||
            !(m.name || "").toLowerCase().includes("slot terbuka")
        ).length;

        const sampleShare = members[0]?.targetAmount || 3000000;
        document.getElementById("patungan-full-cow-target").textContent =
          formatRupiah(totalTarget || sampleShare * 7);
        document.getElementById("patungan-per-share-target").textContent =
          formatRupiah(sampleShare);
        document.getElementById("patungan-total-collected").textContent =
          formatRupiah(totalCollected);
        document.getElementById("patungan-pct-label").textContent = `${pct}% dari target sapi`;
        document.getElementById("patungan-slot-summary").textContent =
          `${Math.min(7, activeSlotsCount)} / 7 Slot`;
        document.getElementById("patungan-paid-count").textContent =
          `${paidFullCount} lunas penuh`;
        document.getElementById("patungan-remaining").textContent = formatRupiah(remaining);
        document.getElementById("patungan-bar-pct").textContent = `${pct}%`;
        document.getElementById("patungan-progress-bar").style.width = `${pct}%`;

        grid.innerHTML = members
          .map((m, idx) => {
            const paid = Math.max(0, Number(m.paidAmount) || 0);
            const target = Math.max(1, Number(m.targetAmount) || 3000000);
            const mPct = Math.min(100, Math.round((paid / target) * 100));
            const isLunas = paid >= target;

            return `
              <div class="rounded-2xl border p-4 flex flex-col justify-between transition ${
                isLunas
                  ? "bg-emerald-50/60 border-emerald-300"
                  : "bg-white border-slate-200 hover:border-emerald-300"
              }">
                <div>
                  <div class="flex items-center justify-between gap-2 mb-2">
                    <span class="text-[11px] font-extrabold uppercase tracking-wider text-slate-400">Bagian #${idx + 1}</span>
                    <span class="text-[10px] font-bold px-2 py-0.5 rounded-full ${
                      isLunas
                        ? "bg-emerald-600 text-white"
                        : paid > 0
                        ? "bg-gold-100 text-gold-700 border border-gold-300"
                        : "bg-slate-100 text-slate-500"
                    }">${isLunas ? "LUNAS" : paid > 0 ? "MENYICIL" : "KOSONG"}</span>
                  </div>

                  <div class="mb-1.5">
                    <input
                      type="text"
                      value="${escapeHTML(m.name)}"
                      onchange="updatePatunganMember(${m.id}, 'name', this.value)"
                      class="w-full font-bold text-sm text-slate-900 bg-transparent border-b border-transparent hover:border-slate-300 focus:border-emerald-600 focus:outline-none py-0.5"
                    />
                  </div>

                  <p class="text-xs text-slate-500 mb-3 truncate" title="${escapeHTML(m.note || "")}">
                    ${escapeHTML(m.note || "Catatan patungan")}
                  </p>
                </div>

                <div class="space-y-2 pt-2 border-t border-slate-100">
                  <div class="flex justify-between text-xs font-bold">
                    <span class="text-emerald-800">${formatRupiah(paid)}</span>
                    <span class="text-slate-400">/ ${formatRupiah(target)}</span>
                  </div>

                  <div class="w-full h-2 bg-slate-100 rounded-full overflow-hidden">
                    <div
                      class="h-full rounded-full transition-all duration-300 ${isLunas ? "bg-emerald-600" : "bg-gold-500"}"
                      style="width: ${mPct}%"
                    ></div>
                  </div>

                  <div class="flex items-center gap-1.5 pt-1 no-print">
                    <button
                      type="button"
                      onclick="addPatunganDeposit(${m.id}, 250000)"
                      class="flex-1 min-h-[34px] py-1 px-2 rounded-lg bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-[11px] font-bold border border-emerald-200 transition"
                    >
                      +250rb
                    </button>
                    <button
                      type="button"
                      onclick="markPatunganLunas(${m.id})"
                      class="flex-1 min-h-[34px] py-1 px-2 rounded-lg bg-gold-50 hover:bg-gold-100 text-gold-800 text-[11px] font-bold border border-gold-200 transition"
                    >
                      Pelunasan
                    </button>
                  </div>
                </div>
              </div>
            `;
          })
          .join("");
      }

      function updatePatunganMember(id, field, value) {
        const member = appState.patungan.find((m) => m.id === id);
        if (!member) return;
        member[field] = value;
        saveState();
        renderPatungan();
      }

      function addPatunganDeposit(id, amount) {
        const member = appState.patungan.find((m) => m.id === id);
        if (!member) return;
        member.paidAmount = Math.min(
          Number(member.targetAmount) || 3000000,
          (Number(member.paidAmount) || 0) + amount
        );
        member.note = `Setoran +${formatRupiah(amount)}`;
        saveState();
        renderPatungan();
        showToast(`Setoran ${formatRupiah(amount)} untuk ${member.name} dicatat.`, "success");
      }

      function markPatunganLunas(id) {
        const member = appState.patungan.find((m) => m.id === id);
        if (!member) return;
        member.paidAmount = Number(member.targetAmount) || 3000000;
        member.note = "Lunas 100%";
        saveState();
        renderPatungan();
        showToast(`${member.name} ditandai LUNAS!`, "success");
      }

      function addPatunganMember() {
        if (appState.patungan.length >= 7) {
          showToast("Kuota patungan sapi 1/7 maksimal 7 orang.", "info");
          return;
        }
        const sharePrice = appState.patungan[0]?.targetAmount || 3000000;
        const nextNum = appState.patungan.length + 1;
        appState.patungan.push({
          id: Date.now(),
          name: `Peserta Patungan #${nextNum}`,
          paidAmount: 0,
          targetAmount: sharePrice,
          note: "Baru ditambahkan",
        });
        saveState();
        renderPatungan();
        showToast("Slot peserta patungan baru ditambahkan.", "success");
      }

      function confirmResetPatungan() {
        showConfirmModal({
          title: "Reset Grup Patungan",
          message:
            "Kembalikan 7 slot peserta patungan ke data sampel awal?",
          okText: "Reset Grup",
          onConfirm: () => {
            const sharePrice =
              appState.animals.find((a) => a.id === "sapi-1-7")?.targetPrice || 3000000;
            appState.patungan = createDefaultPatungan(sharePrice);
            saveState();
            renderPatungan();
            showToast("Data grup patungan direset ke awal.", "info");
          },
        });
      }

      function exportReport() {
        showToast("Menyiapkan tampilan cetak laporan...", "info");
        setTimeout(() => {
          window.print();
        }, 250);
      }

      function confirmResetDemo() {
        showConfirmModal({
          title: "Reset Data Demo",
          message:
            "Seluruh setoran, target, dan grup patungan akan dikembalikan ke data awal. Lanjutkan?",
          okText: "Reset Demo",
          onConfirm: () => {
            appState = createDemoState();
            saveState();
            renderAll();
            updateCountdown();
            showToast("Data aplikasi dikembalikan ke demo awal.", "success");
          },
        });
      }

      function renderAll() {
        renderHeroAndSummary();
        renderDailyQuote();
        renderAnimalCards();
        renderCalculator();
        renderDeposits();
        renderPatungan();
        renderChart();
      }

      document.addEventListener("DOMContentLoaded", function () {
        const dateInput = document.getElementById("deposit-date");
        if (dateInput && !dateInput.value) {
          dateInput.value = new Date().toISOString().split("T")[0];
        }

        renderAll();
        updateCountdown();

        if (countdownInterval) clearInterval(countdownInterval);
        countdownInterval = setInterval(updateCountdown, 1000);
      });
    </script>
  </body>
</html>
