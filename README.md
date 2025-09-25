<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Akça Pro X - Kurum Değerlendirme Anketi</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Firebase App (the core Firebase SDK) -->
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <!-- Firebase Auth -->
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <!-- Firebase Realtime Database SDK eklendi -->
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .gradient-bg {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .active-tab {
            border: 3px solid #6366f1 !important;
            background-color: #6366f1 !important;
            color: white !important;
            font-weight: bold !important;
            transform: scale(1.05) !important;
            box-shadow: 0 4px 8px rgba(99, 102, 241, 0.3) !important;
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.5);
        }
        .modal.show {
            display: flex;
            align-items: center;
            justify-content: center;
        }
        @media print {
            .no-print { display: none !important; }
            body { background: white !important; }
        }
        
        /* Profesyonel Tablo Stilleri */
        .survey-table {
            border-collapse: collapse;
            width: 100%;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            font-size: 11px;
            background: white;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
        .survey-table th, .survey-table td {
            border: 1px solid #a3a3a3;
            padding: 8px 12px;
            text-align: center;
        }
        .survey-table th {
            background: linear-gradient(to bottom, #f8f9fa 0%, #e9ecef 100%);
            font-weight: 600;
            color: #495057;
            border-bottom: 2px solid #6c757d;
        }
        .main-group-row {
            background: linear-gradient(to bottom, #e3f2fd 0%, #bbdefb 100%);
            font-weight: 700;
            color: #1565c0;
        }
        .sub-category {
            background: #f8f9fa;
            text-align: left !important;
            font-weight: 500;
            padding-left: 20px;
        }
        .survey-table tbody tr:nth-child(even) {
            background: #f8f9fa;
        }
        .survey-table tbody tr:hover {
            background: #e3f2fd;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">
    <!-- Ana Navigasyon -->
    <nav class="gradient-bg text-white p-3 shadow-lg sticky top-0 z-50">
        <div class="max-w-5xl mx-auto flex flex-col md:flex-row justify-between items-center gap-2 md:gap-0">
            <div class="flex items-center gap-2">
                <!-- Gizli yönetici erişimi -->
                <div onclick="showModule('admin')" class="w-3 h-3 cursor-pointer opacity-15 hover:opacity-50 transition-opacity" title="">
                    <div class="w-3 h-3 rounded-full border border-white/30 flex items-center justify-center animate-spin" style="animation-duration: 12s;">
                        <div class="w-1 h-1 bg-white/40 rounded-full"></div>
                    </div>
                </div>
                <div>
                    <h1 class="text-lg font-bold">Akça Pro X</h1>
                    <p class="text-xs opacity-90">Kurum Değerlendirme Anketi</p>
                </div>
            </div>
            <div class="flex gap-2">
                <button onclick="showModule('survey')" class="px-3 py-1 bg-white/20 rounded text-sm hover:bg-white/30 transition-colors">📊 Anket</button>
            <button onclick="showModule('company')" class="px-3 py-1 bg-white/20 rounded text-sm hover:bg-white/30 transition-colors">🏢 Kurum Portalı</button>

            </div>
        </div>
    </nav>

    <!-- Anket Modülü -->
    <div id="surveyModule" class="max-w-5xl mx-auto p-2 md:p-4">
        <div class="bg-white shadow-xl rounded-2xl max-w-2xl mx-auto p-4 md:p-8">
            <div class="text-center mb-6">
                <h2 class="text-2xl md:text-3xl font-extrabold text-gray-800 mb-1 tracking-tight">Kurum Değerlendirme Anketi</h2>
                <p class="text-gray-600 mb-2 text-base md:text-lg">Görüşleriniz bizim için değerli</p>
                <span class="bg-green-100 text-green-800 text-xs font-semibold px-2 py-1 rounded-full">v3.0.0 - Firebase Realtime Database</span>
            </div>

            <!-- Sorumluluk Reddi -->
            <div id="disclaimerSection" class="mb-4">
                <div class="bg-yellow-50 border border-yellow-300 rounded p-3 mb-3">
                    <h3 class="font-semibold text-yellow-800 mb-2 text-sm">⚠️ Veri Koruma Beyanı</h3>
                    <div class="text-xs text-yellow-700 space-y-1">
                        <p>• Verileriniz Google Firebase Realtime Database üzerinde güvenli şekilde saklanır ve üçüncü taraflarla paylaşılmaz.</p>
                        <p>• Anket sonuçları sadece kurum yetkilileri tarafından görüntülenebilir.</p>
                        <p>• Sistem güvenliği Google Firebase altyapısı tarafından sağlanır.</p>
                        <p>• Hack, veri ihlali vb. güvenlik olaylarından kaynaklanan bilgi erişimlerinin sorumluluğu Akça Pro X'e ait değildir.</p>
                    </div>
                </div>
                <label class="flex items-center space-x-2 cursor-pointer">
                    <input type="checkbox" id="acceptDisclaimer" class="w-4 h-4 text-purple-600">
                    <span class="text-xs font-medium">Veri koruma beyanını kabul ediyorum</span>
                </label>
            </div>

            <!-- Şirket Bilgileri -->
            <div id="companyInfoSection">
                <h3 class="text-base font-semibold text-gray-700 mb-3">Kurum ve Kişisel Bilgiler</h3>
                <!-- Google ile Giriş Yap butonu -->
                <div class="mb-3 flex flex-col items-center">
                    <button id="googleSignInBtn" type="button" class="flex items-center gap-2 px-4 py-2 bg-white border border-gray-300 rounded shadow hover:bg-gray-100 text-gray-700 font-semibold mb-2">
                        <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" alt="Google" class="w-5 h-5"> Google ile Giriş Yap
                    </button>
                    <div id="googleUserInfo" class="text-xs text-green-700 font-medium hidden"></div>
                </div>
                <div class="mb-3">
                    <input type="text" id="companyName" placeholder="Kurum adınızı girin (Okul, Üniversite vb.)" 
                           class="w-full border-2 border-gray-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
                </div>
                <div class="mb-3">
                    <p class="text-xs text-gray-600 mb-2">Rolünüzü seçin:</p>
                    <div class="grid grid-cols-3 gap-2">
                        <button type="button" onclick="selectJobType('Hasta')" id="patientBtn" 
                                class="job-btn py-3 px-2 text-xs rounded border-2 border-blue-300 hover:border-blue-500 hover:bg-blue-50 transition-all duration-200 cursor-pointer font-medium bg-white text-center focus:outline-none focus:ring-2 focus:ring-blue-400">
                            <div class="text-lg mb-1">🧑‍⚕️</div>
                            <div>Hasta</div>
                        </button>
                        <button type="button" onclick="selectJobType('Doktor')" id="doctorBtn" 
                                class="job-btn py-3 px-2 text-xs rounded border-2 border-green-300 hover:border-green-500 hover:bg-green-50 transition-all duration-200 cursor-pointer font-medium bg-white text-center focus:outline-none focus:ring-2 focus:ring-green-400">
                            <div class="text-lg mb-1">👨‍⚕️</div>
                            <div>Doktor/Hemşire</div>
                        </button>
                        <button type="button" onclick="selectJobType('Yönetim')" id="managementBtn" 
                                class="job-btn py-3 px-2 text-xs rounded border-2 border-purple-300 hover:border-purple-500 hover:bg-purple-50 transition-all duration-200 cursor-pointer font-medium bg-white text-center focus:outline-none focus:ring-2 focus:ring-purple-400">
                            <div class="text-lg mb-1">👩‍🔬</div>
                            <div>Yönetim</div>
                        </button>
                    </div>
                </div>
                <div id="selectedJobDisplay" class="text-center text-sm text-gray-600 mb-3 min-h-[20px]"></div>
                <div class="grid grid-cols-2 gap-2 mb-4">
                    <input type="text" id="firstName" placeholder="Adınız" 
                        class="border-2 border-gray-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
                    <input type="text" id="lastName" placeholder="Soyadınız" 
                        class="border-2 border-gray-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
                </div>
                <button id="startSurvey" class="w-full py-3 rounded text-white font-semibold gradient-bg hover:opacity-90 transition-opacity text-sm">
                    📊 Anketi Başlat
                </button>
            </div>

            <!-- Anket Alanı -->
            <div id="surveySection" class="hidden">
                <div class="flex flex-col md:flex-row justify-between items-center mb-6 gap-2">
                    <span id="progressText" class="text-gray-600 font-medium">Anket İlerlemesi 0/50 Yanıtlandı</span>
                    <span id="timeElapsed" class="text-sm text-gray-500">Süre: 00:00</span>
                </div>
                <div class="w-full bg-gray-200 rounded-full h-3 mb-8">
                    <div id="progressBar" class="bg-purple-600 h-3 rounded-full transition-all duration-300" style="width:0%"></div>
                </div>
                <div id="questionContainer" class="space-y-6"></div>
                <button id="submitSurvey" class="hidden w-full mt-8 py-4 rounded-xl text-white font-semibold bg-green-600 hover:bg-green-700 transition-colors text-lg">
                    ✅ Anketi Tamamla
                </button>
            </div>
        </div>
    </div>

    <!-- Şirket Portalı -->
    <div id="companyModule" class="max-w-5xl mx-auto p-2 md:p-4 hidden">
        <div class="bg-white shadow-xl rounded-2xl max-w-5xl mx-auto p-4 md:p-8">
            <div id="companyLogin" class="max-w-md mx-auto">
                <h2 class="text-3xl font-bold text-center mb-8">🏫 Kurum Portalı Girişi</h2>
                <div class="space-y-6">
                    <input type="text" id="companyLoginName" placeholder="Kurum Adı" 
                           class="w-full border-2 border-gray-300 rounded-lg px-4 py-4 text-base focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    <input type="password" id="companyPassword" placeholder="12 Karakterlik Şifre" 
                           class="w-full border-2 border-gray-300 rounded-lg px-4 py-4 text-base focus:ring-2 focus:ring-blue-500 focus:border-blue-500" autocomplete="off">
                    <button onclick="loginCompany()" class="w-full py-4 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors text-lg font-semibold">
                        🔐 Giriş Yap
                    </button>
                </div>
                <div class="mt-6 p-4 bg-blue-50 rounded-lg text-sm text-blue-700">
                    <p><strong>Not:</strong> Kurum şifrenizi yöneticinizden alabilirsiniz.</p>
                </div>
            </div>

            <div id="companyDashboard" class="hidden">
                <div class="flex justify-between items-center mb-8">
                    <div>
                        <h2 class="text-3xl font-bold">🏥 Hastane Anket Raporu</h2>
                        <p class="text-gray-600 text-lg" id="companyNameDisplay"></p>
                    </div>
                    <button onclick="logoutCompany()" class="px-6 py-3 bg-red-600 text-white rounded-lg hover:bg-red-700 font-semibold">
                        🚪 Çıkış
                    </button>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4 md:gap-6 mb-8">
                    <div class="bg-gradient-to-r from-blue-500 to-blue-600 text-white p-6 rounded-lg">
                        <h3 class="text-lg font-semibold mb-2">Toplam Katılımcı</h3>
                        <p class="text-4xl font-bold" id="totalParticipants">0</p>
                    </div>
                    <div class="bg-gradient-to-r from-green-500 to-green-600 text-white p-6 rounded-lg">
                        <h3 class="text-lg font-semibold mb-2">Ortalama Puan</h3>
                        <p class="text-4xl font-bold" id="averageScore">0.0</p>
                    </div>
                    <div class="bg-gradient-to-r from-purple-500 to-purple-600 text-white p-6 rounded-lg">
                        <h3 class="text-lg font-semibold mb-2">Değerlendirme Oranı</h3>
                        <p class="text-4xl font-bold" id="satisfactionRate">0%</p>
                    </div>
                </div>

                <div class="bg-white border rounded-2xl p-4 md:p-6">
                        <div class="flex flex-col md:flex-row justify-between items-center mb-6 gap-2">
                            <h3 class="text-xl font-semibold mb-2 md:mb-0">Anket Sonuçları</h3>
                            <div class="flex flex-col md:flex-row gap-2 items-center">
                                <input type="date" id="reportStartDate" class="border rounded px-2 py-1 text-sm" />
                                <span class="mx-1">-</span>
                                <input type="date" id="reportEndDate" class="border rounded px-2 py-1 text-sm" />
                                <button onclick="filterByDateRange()" class="px-3 py-1 bg-blue-600 text-white rounded hover:bg-blue-700 text-sm">Tarihe Göre Rapor</button>
                                <button onclick="showPDFReport(true)" style="display:none" class="px-3 py-1 bg-red-600 text-white rounded hover:bg-red-700 text-sm">📄 PDF Göster (Filtreli)</button>
                                <button onclick="showPDFReport(false)" style="display:none" class="px-3 py-1 bg-gray-600 text-white rounded hover:bg-gray-700 text-sm">📄 PDF Göster (Tümü)</button>
                            </div>
                        </div>
                    <!-- Grafikler Bölümü -->
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 mb-6">
                        <div class="bg-gray-50 p-4 rounded-xl min-h-[200px] flex flex-col">
                            <h4 class="font-semibold text-gray-800 mb-3 text-sm">📊 Pozisyon</h4>
                            <div class="flex-1 flex items-center justify-center" style="min-height: 150px;">
                                <canvas id="positionChart"></canvas>
                            </div>
                        </div>
                        <div class="bg-gray-50 p-4 rounded-xl min-h-[200px] flex flex-col">
                            <h4 class="font-semibold text-gray-800 mb-3 text-sm">📈 Değerlendirme</h4>
                            <div class="flex-1 flex items-center justify-center" style="min-height: 150px;">
                                <canvas id="satisfactionChart"></canvas>
                            </div>
                        </div>
                        <div class="bg-gray-50 p-4 rounded-xl min-h-[200px] flex flex-col">
                            <h4 class="font-semibold text-gray-800 mb-3 text-sm">⏰ Süre Dağılımı</h4>
                            <div class="flex-1 flex items-center justify-center" style="min-height: 150px;">
                                <canvas id="timeChart"></canvas>
                            </div>
                        </div>
                        <div class="bg-gray-50 p-4 rounded-xl min-h-[200px] flex flex-col">
                            <h4 class="font-semibold text-gray-800 mb-3 text-sm">🎯 Puan Dağılımı</h4>
                            <div class="flex-1 flex items-center justify-center" style="min-height: 150px;">
                                <canvas id="trendChart"></canvas>
                            </div>
                        </div>
                    </div>
                    <div id="detailedFrequencyTables" class="mb-8"></div>
                    <!-- Katılımcı Detayları Bölümü -->
                    <div class="bg-white border rounded-xl p-2 md:p-4 mb-6">
                        <div class="flex flex-col md:flex-row justify-between items-center mb-4 gap-2">
                            <h4 class="font-semibold text-gray-800">👥 Katılımcı Detayları</h4>
                            <button onclick="toggleParticipantDetails()" id="toggleParticipantsBtn" class="px-4 py-2 bg-blue-600 text-white text-sm rounded-xl hover:bg-blue-700">
                                📋 Katılımcıları Görüntüle
                            </button>
                        </div>
                        <div id="participantDetails" class="hidden">
                            <div class="overflow-x-auto">
                                <table class="w-full table-auto text-sm">
                                    <thead>
                                        <tr class="bg-gray-100">
                                            <th class="px-3 py-2 text-left">İsim</th>
                                            <th class="px-3 py-2 text-left">Pozisyon</th>
                                            <th class="px-3 py-2 text-center">Ortalama Puan</th>
                                            <th class="px-3 py-2 text-center">Değerlendirme</th>
                                            <th class="px-3 py-2 text-center">Tarih</th>
                                        </tr>
                                    </thead>
                                    <tbody id="participantTableBody">
                                        <!-- Katılımcı listesi buraya yüklenecek -->
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                    <div id="detailedReport" class="space-y-4"></div>
                </div>
            </div>
        </div>
    </div>

    <!-- AI Interpretation Modal -->
    <div id="aiInterpretationModal" class="modal">
      <div class="modal-content max-w-7xl bg-white shadow-2xl" style="margin: 2% auto; padding: 40px; border-radius: 20px; max-height: 90vh; overflow-y: auto; width: 95vw;">
        <div class="modal-header flex justify-between items-center mb-6 border-b pb-4">
          <h2 class="text-2xl font-bold text-gray-800">🏥 Sağlık Hizmetleri AI Analiz</h2>
          <span class="close cursor-pointer text-4xl text-gray-500 hover:text-gray-700" onclick="document.getElementById('aiInterpretationModal').classList.remove('show')">&times;</span>
        </div>
        <div id="aiInterpretationContent" class="text-lg text-gray-800 leading-8 whitespace-pre-line"></div>
      </div>
    </div>

    <!-- Yönetici Portalı -->
    <div id="adminModule" class="max-w-5xl mx-auto p-2 md:p-4 hidden">
        <div class="bg-white shadow-xl rounded-2xl max-w-5xl mx-auto p-4 md:p-8">
            <div id="adminLogin" class="max-w-md mx-auto">
                <h2 class="text-3xl font-bold text-center mb-8">⚙️ Yönetici Portalı</h2>
                <div class="space-y-6">
                    <input type="password" id="adminPassword" placeholder="Yönetici Şifresi" 
                           class="w-full border-2 border-gray-300 rounded-lg px-4 py-4 text-base focus:ring-2 focus:ring-red-500 focus:border-red-500" autocomplete="off">
                    <button onclick="loginAdmin()" class="w-full py-4 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors text-lg font-semibold">
                        🔐 Yönetici Girişi
                    </button>
                </div>
            </div>

            <div id="adminDashboard" class="hidden">
                <div class="flex justify-between items-center mb-8">
                    <h2 class="text-3xl font-bold">Sistem Yönetimi</h2>
                    <button onclick="logoutAdmin()" class="px-6 py-3 bg-red-600 text-white rounded-lg hover:bg-red-700 font-semibold">
                        🚪 Çıkış
                    </button>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
                    <div class="bg-blue-100 p-6 rounded-lg text-center">
                        <h3 class="font-semibold text-blue-800 mb-2">Toplam Kurum</h3>
                        <p class="text-3xl font-bold text-blue-600" id="totalCompanies">0</p>
                    </div>
                    <div class="bg-green-100 p-6 rounded-lg text-center">
                        <h3 class="font-semibold text-green-800 mb-2">Aktif Anketler</h3>
                        <p class="text-3xl font-bold text-green-600" id="activeSurveys">0</p>
                    </div>
                    <div class="bg-yellow-100 p-6 rounded-lg text-center">
                        <h3 class="font-semibold text-yellow-800 mb-2">Toplam Katılımcı</h3>
                        <p class="text-3xl font-bold text-yellow-600" id="totalUsers">0</p>
                    </div>
                    <div class="bg-purple-100 p-6 rounded-lg text-center">
                        <h3 class="font-semibold text-purple-800 mb-2">Sistem Durumu</h3>
                        <p class="text-sm font-bold text-purple-600">🟢 Aktif</p>
                    </div>
                </div>

                <div class="bg-white border rounded-lg p-6">
                    <h3 class="text-xl font-semibold mb-6">Kurum Listesi ve Yönetimi</h3>
                    <div class="mb-4 flex flex-col sm:flex-row gap-2 items-center">
                        <input id="companySearchInput" type="text" placeholder="🔍 Kurum adı ile ara..." class="border border-gray-300 rounded px-3 py-2 text-sm w-full sm:w-64 focus:ring-2 focus:ring-blue-500 focus:border-blue-500" oninput="filterCompanyList()">
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full table-auto">
                            <thead>
                                <tr class="bg-gray-50">
                                    <th class="px-4 py-3 text-left">Kurum Adı</th>
                                    <th class="px-4 py-3 text-left">Şifre</th>
                                    <th class="px-4 py-3 text-left">Katılımcı</th>
                                    <th class="px-4 py-3 text-left">Durum</th>
                                    <th class="px-4 py-3 text-left">İşlemler</th>
                                </tr>
                            </thead>
                            <tbody id="companyList">
                                <!-- Şirket listesi buraya yüklenecek -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal -->
    <div id="modal" class="modal">
        <div class="bg-white rounded-lg p-6 max-w-md w-full mx-4">
            <div id="modalContent"></div>
        </div>
    </div>

        <!--
DİKKAT: Yönetici ve kurum şifreleri ile raporlama mantığı istemci tarafında tutulmamalıdır. Gerçek bir uygulamada bu kodlar backend (sunucu) tarafında olmalıdır. Bu dosya sadece demo/örnek amaçlıdır.
-->
<script>
// Temel fonksiyon iskeletleri (eksik olanlar)
function showModule(moduleName) {
    // Tüm modülleri gizle
    const modules = ['survey', 'company', 'admin'];
    modules.forEach(m => {
        const el = document.getElementById(m + 'Module');
        if (el) el.classList.add('hidden');
    });
    // Seçili modülü göster
    const showEl = document.getElementById(moduleName + 'Module');
    if (showEl) showEl.classList.remove('hidden');
    window.currentModule = moduleName;
}

function selectJobType(type) {
    // ...rol seçimi kodu...
}
function startTimer() {
    // ...zamanlayıcı başlatma kodu...
}
function displayCurrentQuestion() {
    // ...soru gösterme kodu...
}
async function submitSurvey() {
    // ...anket gönderme kodu...
}
async function loginCompany() {
    // ...kurum girişi kodu...
}
function logoutCompany() {
    // ...kurum çıkışı kodu...
}
function filterByDateRange() {
    // ...tarih filtreleme kodu...
}
function showPDFReport(isFiltered) {
    // ...PDF rapor oluşturma kodu...
}
function toggleParticipantDetails() {
    // ...katılımcı detaylarını aç/kapat kodu...
}
async function loginAdmin() {
    // ...yönetici girişi kodu...
}
function logoutAdmin() {
    // ...yönetici çıkışı kodu...
}
function filterCompanyList() {
    // ...kurum arama filtreleme kodu...
}
        // Firebase config
        const firebaseConfig = {
            apiKey: "AIzaSyDp2Yh8hamXi6OTfw03MT0S4rp5CjnlAcg",
            authDomain: "akcaprox-anket.firebaseapp.com",
            projectId: "akcaprox-anket",
            storageBucket: "akcaprox-anket.appspot.com",
            messagingSenderId: "426135179922",
            appId: "1:426135179922:web:c16b3fd6fa5f3d9224cc4b",
            measurementId: "G-CD1ET7RGX1"
        };
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();

        // Google Sign-In logic
        let googleUser = null;
        document.addEventListener('DOMContentLoaded', function() {
            // Anket başlatma butonunu startSurvey fonksiyonuna bağla
            const startBtn = document.getElementById('startSurvey');
            if (startBtn) {
                startBtn.addEventListener('click', startSurvey);
            }
            const googleBtn = document.getElementById('googleSignInBtn');
            const userInfoDiv = document.getElementById('googleUserInfo');
            if (googleBtn) {
                googleBtn.addEventListener('click', function() {
                    const provider = new firebase.auth.GoogleAuthProvider();
                    auth.signInWithPopup(provider)
                        .then((result) => {
                            const user = result.user;
                            if (user) {
                                googleUser = user;
                                document.getElementById('firstName').value = user.displayName ? user.displayName.split(' ')[0] : '';
                                document.getElementById('lastName').value = user.displayName ? user.displayName.split(' ').slice(1).join(' ') : '';
                                userInfoDiv.textContent = `Giriş yapıldı: ${user.displayName} (${user.email})`;
                                userInfoDiv.classList.remove('hidden');
                                // Make name fields editable after login
                                document.getElementById('firstName').readOnly = false;
                                document.getElementById('lastName').readOnly = false;
                            }
                        })
                        .catch((error) => {
                            alert('Google ile giriş başarısız: ' + error.message);
                        });
                });
            }
        });

        // Anket başlatma butonuna Google ile giriş kontrolü ekle
        document.addEventListener('DOMContentLoaded', function() {
            const startBtn = document.getElementById('startSurvey');
            if (startBtn) {
                startBtn.addEventListener('click', function(e) {
                    if (!googleUser) {
                        e.preventDefault();
                        alert('Ankete başlamadan önce Google ile giriş yapmalısınız.');
                    }
                }, true);
            }
        });
        // Anket başlatma fonksiyonu: Google ile giriş zorunluluğu ve erişilebilir uyarı
        function startSurvey(e) {
            const companyName = document.getElementById('companyName').value.trim();
            const disclaimerAccepted = document.getElementById('acceptDisclaimer').checked;
            const firstName = document.getElementById('firstName').value.trim();
            const lastName = document.getElementById('lastName').value.trim();
            const selectedJobType = window.selectedJobType || '';

            // Zorunlu alanlar kontrolü
            let missingFields = [];
            if (!disclaimerAccepted) missingFields.push('Veri koruma beyanı');
            if (!companyName) missingFields.push('Kurum adı');
            if (!firstName) missingFields.push('Adınız');
            if (!lastName) missingFields.push('Soyadınız');
            if (!selectedJobType) missingFields.push('Rolünüz');

            // Google Sign-In enforcement
            if (!googleUser) {
                showModal(
                    '🔒 Giriş Gerekli',
                    `<div class=\"text-2xl font-extrabold text-red-700 mb-4\">Google ile Giriş Yapmalısınız</div>
                    <div class=\"text-base text-gray-800 mb-2\">Ankete başlamadan önce kimliğinizi doğrulamanız gerekmektedir.</div>
                    <ul class=\"list-disc pl-6 text-base text-gray-700 mb-4\">
                        <li>Yukarıdaki <b>Google ile Giriş Yap</b> butonunu kullanarak hesabınızla oturum açın.</li>
                        <li>Giriş yaptıktan sonra ad ve soyad alanlarınız otomatik doldurulacak ve düzenlenebilir olacaktır.</li>
                        <li>Gizliliğiniz korunur, bilgileriniz üçüncü kişilerle paylaşılmaz.</li>
                    </ul>
                    <div class=\"text-sm text-gray-500\">Herhangi bir sorun yaşarsanız lütfen yöneticinizle iletişime geçin.</div>`
                );
                if (e) e.preventDefault();
                return;
            }

            if (missingFields.length > 0) {
                showModal(
                    '❌ Eksik Bilgi',
                    `<div class=\"text-lg text-red-700 font-bold mb-2\">Aşağıdaki alan(lar)ı doldurmalısınız:</div><ul class=\"list-disc pl-6 text-base text-gray-700 mb-4\">${missingFields.map(f=>`<li>${f}</li>`).join('')}</ul>`
                );
                if (e) e.preventDefault();
                return;
            }

            // Seçilen role göre soruları al
            currentQuestions = questions[selectedJobType];
            console.log('Seçilen rol:', selectedJobType);
            console.log('Sorular:', currentQuestions);

            if (!currentQuestions || currentQuestions.length === 0) {
                showModal('❌ Hata', 'Seçilen rol için sorular bulunamadı. Lütfen sayfayı yenileyip tekrar deneyin.');
                if (e) e.preventDefault();
                return;
            }

            // Değişkenleri sıfırla
            currentQuestionIndex = 0;
            answers = [];
            surveyStartTime = new Date();

            // Anket bölümünü göster
            document.getElementById('disclaimerSection').classList.add('hidden');
            document.getElementById('companyInfoSection').classList.add('hidden');
            document.getElementById('surveySection').classList.remove('hidden');

            startTimer();
            displayCurrentQuestion();

            console.log('Anket başarıyla başlatıldı!');
        }
        let currentModule = 'survey';
        let surveyStartTime = null;
        let timerInterval = null;
        let currentQuestions = [];
        let currentQuestionIndex = 0;
        let answers = [];
        let selectedJobType = '';
        let loggedInCompany = null;
        let isAdminLoggedIn = false;
    let filteredSurveys = null;


        // Firebase Realtime Database URL
        const FIREBASE_DB_URL = 'https://json-19344-default-rtdb.europe-west1.firebasedatabase.app/';

        // Soru setleri
        const questions = {
            "Hasta": [
                // 1.1 Randevu, Kabul ve Taburcu Süreçleri (5 Soru)
                "Randevu alma sürecim (online, telefon vb.) kolay ve hızlı gerçekleşti.",
                "Hastaneye giriş (kabul) işlemlerim ve kayıt süreçlerim hızlı ve sorunsuzdu.",
                "Hastane personeli, randevu/kabul sırasında bana doğru ve yeterli bilgi verdi.",
                "Tedavim bittiğinde taburcu olma (çıkış) süreçlerim karmaşık değildi ve hızlıydı.",
                "Taburcu olurken sonraki tedavi ve ilaç kullanımı hakkında net talimatlar aldım.",
                // 1.2 Tıbbi İletişim ve Bilgilendirme (5 Soru)
                "Doktorum, hastalığımın teşhisini ve tedavi seçeneklerini anlaşılır bir dille açıkladı.",
                "Tıbbi personel, yapılan işlemler ve testler hakkında beni her adımda bilgilendirdi.",
                "Tedavi süreciyle ilgili sorularıma her zaman sabırlı ve tatmin edici cevaplar aldım.",
                "Doktorum, tedaviye başlamadan önce riskler ve olası yan etkiler konusunda beni uyardı.",
                "Tıbbi kararlara aktif olarak katılımım teşvik edildi ve fikrim soruldu.",
                // 1.3 Doktor ve Hemşire Bakım Kalitesi (5 Soru)
                "Doktorumun mesleki bilgi ve tecrübesi bana güven verdi.",
                "Hemşireler, ihtiyaç duyduğum anda hızlı bir şekilde yanımda oldu.",
                "Hemşireler, ağrı yönetimimi etkili bir şekilde sağladı ve düzenli kontrol etti.",
                "Tıbbi personel, bana saygılı, nazik ve şefkatli davrandı.",
                "Hemşirelerin yaptığı işlemlerde (iğne, pansuman vb.) kendimi güvende hissettim.",
                // 1.4 Hastane Hijyeni ve Fiziksel Çevre (5 Soru)
                "Kaldığım oda/servis alanı temiz ve düzenliydi.",
                "Hastane genelindeki tuvalet, bekleme alanı gibi ortak yerler hijyenikti.",
                "Hastanenin genel atmosferi (sessizlik, aydınlatma) iyileşme sürecime katkı sağladı.",
                "Hastane binası içi ve dışı yönlendirme tabelaları kolay anlaşılırdı.",
                "Kullanılan tıbbi ekipmanların temiz ve güvenli olduğuna inanıyorum.",
                // 1.5 İlaç ve Tedavi Süreçleri (5 Soru)
                "İlaçlarım ve tedavilerim her zaman doğru zamanda uygulandı.",
                "İlaçların dozajı ve uygulama şekli hakkında net bilgi aldım.",
                "Tedavi sürecimde gereksiz beklemeler ve gecikmeler yaşanmadı.",
                "İlaçlarımın muhtemel yan etkileri hakkında bilgilendirildim ve izlendim.",
                "Tedavi planının beklenen sonuçları konusunda gerçekçi bir bilgilendirme yapıldı.",
                // 1.6 Yemek ve Beslenme Hizmetleri (5 Soru)
                "Hastanede sunulan yemeklerin lezzeti ve kalitesi tatmin ediciydi.",
                "Diyetime uygun beslenme gereksinimlerim (alerji, özel diyet) dikkate alındı.",
                "Yemekler doğru saatte ve hijyenik bir şekilde servis edildi.",
                "Beslenme uzmanından diyetim hakkında ek bilgi alma imkanı buldum.",
                "Yemek servis personelinin ilgisi ve nazikliği yeterliydi.",
                // 1.7 Hasta Hakları ve Etik (5 Soru)
                "Hastane personelinin gizlilik ve mahremiyet kurallarına uyduğuna inanıyorum.",
                "Hastanede inançlarıma ve kültürel değerlerime saygı gösterildi.",
                "Hastane, şikayet ve geri bildirim mekanizmalarını bana açıkça sundu.",
                "Tıbbi uygulamalar için benden onay alınması süreci şeffaftı.",
                "Hastanede uygulanan fiyatlandırma ve faturalandırma süreci anlaşılırdı.",
                // 1.8 Güvenlik ve Ağrı Yönetimi (5 Soru)
                "Hastanede düşme, yanma gibi kazalara karşı alınan önlemler yeterliydi.",
                "Bakımım boyunca kimlik doğrulama süreçleri (doğru hasta, doğru ilaç) titizlikle uygulandı.",
                "Ağrı hissettiğimde, hızlı ve etkili bir şekilde müdahale edildi.",
                "Odada/Serviste güvenlik ve çağrı zili kolay erişilebilir durumdaydı.",
                "Hastane personelinin kişisel eşyalarıma saygılı davrandığına inanıyorum.",
                // 1.9 Acil Servis ve Yoğun Bakım Deneyimi (5 Soru)
                "(Acil serviste hizmet aldıysam) Bekleme sürem makuldü ve erken müdahale sağlandı.",
                "(Acil serviste hizmet aldıysam) Acil personelinin hızı ve yetkinliği güven verdi.",
                "(Yoğun bakımda kaldıysam) Yoğun bakım ziyaret saatleri ve iletişim süreci yeterliydi.",
                "(Yoğun bakımda kaldıysam) Yoğun bakım personelinin hasta ve yakınlarına yaklaşımı destekleyiciydi.",
                "Acil durumlarda hastane ekibinin koordinasyonu başarılıydı.",
                // 1.10 Genel Memnuniyet ve Tavsiye (5 Soru)
                "Bu hastaneyi aileme ve arkadaşlarıma tavsiye ederim.",
                "Genel olarak, hastaneden aldığım tıbbi bakım kalitesi beklentilerimi karşıladı.",
                "Hastanenin online iletişim ve bilgilendirme kanalları (web sitesi vb.) yeterliydi.",
                "Hastaneden genel sağlık durumumun iyileştiği hissiyle ayrılıyorum.",
                "Toplam deneyimim, hastane personelinin üst düzeyde özen gösterdiğini gösteriyor."
            ],
            "Doktor": [
                // 2.1 Randevu, Kabul ve Taburcu Süreçleri (5 Soru)
                "Hastaların kabul ve taburcu işlemleri, benim tıbbi iş yükümü artırmayacak şekilde verimli ilerlemektedir.",
                "Hastane bilgi yönetim sistemi, hastaların geçmiş verilerine hızlı erişimimi sağlamaktadır.",
                "Ameliyat/işlem randevu sistemleri, zaman yönetimi ve planlama açısından yeterlidir.",
                "Sekreterya ve idari personel ile hasta kabul süreçlerinde koordinasyonumuz güçlüdür.",
                "Hastaların taburcu sonrası evde bakım/destek hizmetleri konusunda net protokoller mevcuttur.",
                // 2.2 Tıbbi İletişim ve Bilgilendirme (5 Soru)
                "Hastalara teşhis ve tedavi süreçlerini açıklarken kullanacağım eğitim materyalleri (broşür, model) mevcuttur.",
                "Hastane, meslektaşlarım ve uzmanlarla konsültasyon yapmamı desteklemektedir.",
                "Hastalarla duygusal ve hassas konuları konuşurken yönetimden destek hissediyorum.",
                "Hasta yakınlarına bilgi verme prosedürleri net ve tutarlıdır.",
                "Hastane, hata bildirim sistemini kullanarak iletişimi ve şeffaflığı teşvik etmektedir.",
                // 2.3 Doktor ve Hemşire Bakım Kalitesi (5 Soru)
                "Çalıştığım birimde yeterli sayıda nitelikli hemşire mevcuttur.",
                "Ekibimdeki uzmanlık seviyesi ve işbirliği, en iyi bakımı sunmamızı sağlamaktadır.",
                "İş yükü dağılımı, hastalarla yeterli zaman geçirmeme olanak verecek şekilde dengelenmiştir.",
                "Hastanenin sürekli mesleki gelişim (CME) programları bilgi ve becerilerimi güncel tutmaktadır.",
                "Hastalara yüksek kalitede bakım sağlama konusunda kendimi yetkin hissediyorum.",
                // 2.4 Hastane Hijyeni ve Fiziksel Çevre (5 Soru)
                "Çalıştığım serviste enfeksiyon kontrol prosedürleri (el hijyeni, izolasyon) titizlikle uygulanmaktadır.",
                "Hastane, steril ve hijyenik koşulları sağlamak için yeterli personel ve kaynak sağlamaktadır.",
                "Tıbbi atıkların yönetimi prosedürlere uygun ve güvenli bir şekilde yapılmaktadır.",
                "Hastanenin fiziki altyapısı ve mimarisi, iş akışımı ve hasta bakımını desteklemektedir.",
                "Çalıştığım alanların bakım ve onarımı, taleplerime hızlı yanıt vermektedir.",
                // 2.5 İlaç ve Tedavi Süreçleri (5 Soru)
                "Hastanenin ilaç yönetim sistemi (dozaj, dağıtım, stok) hatasız çalışmaktadır.",
                "Gerekli tüm tıbbi cihazlar, sarf malzemeleri ve ilaçlar her zaman stokta mevcuttur.",
                "Tıbbi cihazların kalibrasyon ve bakımları düzenli olarak yapılmaktadır.",
                "Yeni tedavi protokolleri ve ilaçlar konusunda zamanında ve eksiksiz eğitim alıyorum.",
                "Hasta güvenliği protokolleri, tıbbi hataları (ilaç hatası vb.) en aza indirmektedir.",
                // 2.6 Çalışma Koşulları ve Sosyal Haklar (5 Soru)
                "Çalıştığım hastanenin maaş ve yan hakları sektör ortalamasına göre tatmin edicidir.",
                "Vardiya ve nöbet planlamaları, dinlenmeme ve özel hayatıma saygı gösterecek şekilde yapılmaktadır.",
                "Hastanenin sosyal imkanları ve dinlenme alanları yeterli ve konforludur.",
                "Hastanede iş arkadaşlarımla ve amirlerimle güçlü ve destekleyici bir ilişkimiz var.",
                "Hastane, tükenmişlik sendromunu (burnout) önlemeye yönelik programlar sunmaktadır.",
                // 2.7 Hasta Hakları ve Etik (5 Soru)
                "Hastane, etik kurulların kararlarına ve yönlendirmelerine saygı duymaktadır.",
                "Çalışırken karşılaştığım etik ikilemleri tartışabileceğim bir mekanizma mevcuttur.",
                "Hastaların mahremiyetini ve kişisel verilerini koruma konusunda net talimatlara sahibim.",
                "Hastane, hasta hakları ve etik konularında düzenli eğitim vermektedir.",
                "Hastaların dini ve kültürel ihtiyaçlarına uygun hizmet sunma konusunda destekleniyorum.",
                // 2.8 Eğitim ve Kariyer Gelişimi (5 Soru)
                "Hastanede yükselme ve kariyer geliştirme fırsatları konusunda şeffaflık vardır.",
                "Yöneticilerim, akademik çalışmalarımı ve araştırmalarımı desteklemektedir.",
                "Hastane, özel uzmanlık alanlarımda sertifika ve kurs almam için bütçe sağlamaktadır.",
                "Koçluk ve mentorluk programları, genç meslektaşlarımın gelişimine katkı sağlamaktadır.",
                "Hastanedeki eğitimler, sektördeki en son tıbbi gelişmeleri takip etmeme yardımcı olmaktadır.",
                // 2.9 Acil Servis ve Yoğun Bakım İşleyişi (5 Soru)
                "(Acil/Yoğun Bakım personeli isem) Çalıştığım birimde ekipman ve personel sayısı gelen hasta yoğunluğuna yeterlidir.",
                "Travma ve acil müdahale odaları, uluslararası standartlara uygun donanıma sahiptir.",
                "Yoğun bakım yatak doluluk oranları ve hasta sevk süreçleri etkin yönetilmektedir.",
                "Acil servis ve yoğun bakımdaki izin/tatil planlaması adil yapılmaktadır.",
                "Acil durumlarda departmanlar arası koordinasyon (ameliyathane, laboratuvar) sorunsuz ve hızlıdır.",
                // 2.10 Kurumsal Bağlılık ve Memnuniyet (5 Soru)
                "Hastanenin kurumsal misyonu ve değerleri, benim hizmet anlayışımla örtüşmektedir.",
                "Yöneticilerim ve idarecilerim, mesleki çabalarımı takdir etmektedir.",
                "Hastane yönetiminin kararları, hasta bakımının kalitesini önceliklendirmektedir.",
                "Meslektaşlarımla birlikte çalışmaktan ve bu kurumun bir parçası olmaktan gurur duyuyorum.",
                "Genel olarak, bu hastanede çalışmaktan memnunum ve kurumuma bağlıyım."
            ],
            "Yönetim": [
                // 3.1 Randevu, Kabul ve Taburcu Süreçleri (5 Soru)
                "Hastane Randevu ve Kayıt Sistemi, bekleme sürelerini minimize etme hedefine ulaşmıştır.",
                "Hasta kabul ve taburcu personeli, süreç verimliliği ve iletişim becerileri konusunda düzenli eğitim almaktadır.",
                "Hastanenin kapasite yönetimi (yatak doluluk, ameliyathane kullanımı) verimli bir şekilde yapılmaktadır.",
                "Taburcu sonrası takip ve geri bildirim süreçleri sistematik olarak uygulanmaktadır.",
                "Hastanenin online/dijital randevu kanallarının kullanımı hedeflenen orana ulaşmıştır.",
                // 3.2 Tıbbi İletişim ve Bilgilendirme (5 Soru)
                "Hastanemiz, Hasta Memnuniyeti Skorlarında iletişim ve bilgilendirme başlıklarında sektör ortalamasının üzerindedir.",
                "Hata bildirimi ve kök neden analizi (RCA) sistemi, öğrenen bir kurum kültürünü desteklemektedir.",
                "Tıbbi personelin hasta ve hasta yakınlarıyla iletişim becerileri düzenli olarak değerlendirilmektedir.",
                "Hastaların sağlık okuryazarlığını artırmaya yönelik kurumsal programlarımız mevcuttur.",
                "Açık Kapı Politikası ile hasta ve personel şikayetleri hızlı ve şeffaf çözülmektedir.",
                // 3.3 Doktor ve Hemşire Bakım Kalitesi (5 Soru)
                "Hastanenin Hemşire-Hasta Oranı, güvenli ve kaliteli bakım standartlarına uygundur.",
                "Doktor ve hemşire personelinin maaş ve yan hakları, kurumun kalifiye personel çekme hedefine uygundur.",
                "Tıbbi hataların bildirim oranı, risk yönetimi hedeflerimize uygundur (Gizli hataların bildirilmesi teşvik edilmektedir).",
                "Yüksek riskli tıbbi işlemler için net ve güncel protokollerimiz mevcuttur.",
                "Periyodik Denetimler (Tıbbi Kalite Denetimi) objektif ve etkin bir şekilde yapılmaktadır.",
                // 3.4 Hastane Hijyeni ve Fiziksel Çevre (5 Soru)
                "Hastanenin Enfeksiyon Kontrol Oranları (örneğin hastane kaynaklı enfeksiyonlar) kabul edilebilir limitlerin altındadır.",
                "Temizlik ve hijyen hizmetleri için ayrılan bütçe, yüksek standartları korumaya yeterlidir.",
                "Hastane, fiziki altyapı ve medikal ekipman yenileme konusunda uzun vadeli yatırım planına sahiptir.",
                "Hasta odası ve ortak alan konforu, hasta memnuniyetini doğrudan etkileyecek seviyededir.",
                "Hastane, yeşil hastane ve çevre dostu sertifikasyon hedeflerini gerçekleştirmektedir.",
                // 3.5 İlaç ve Tedavi Süreçleri (5 Soru)
                "Hastanenin ilaç stok yönetimi, kritik ilaçlarda eksiklik yaşanmasını önlemektedir.",
                "Yeni medikal teknoloji ve cihaz yatırımları, kurumun stratejik büyüme hedeflerini desteklemektedir.",
                "Ameliyathane kullanım verimliliği ve doluluk oranları optimize edilmiştir.",
                            "Tedavi gecikmeleri ve aksaklıklar"
                        ]
                    };
            </script>
