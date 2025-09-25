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

        <script>
// Modal açma ve kapama fonksiyonları (sadece eksik olanlar eklendi)
function showModal(title, content) {
    const modal = document.getElementById('modal');
    const modalContent = document.getElementById('modalContent');
    modalContent.innerHTML = `
        <h3 class="text-xl font-semibold mb-4">${title}</h3>
        <div class="mb-6 text-base">${content}</div>
        <button onclick="closeModal()" class="w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 font-semibold">
            Tamam
        </button>
    `;
    modal.classList.add('show');
}

function closeModal() {
    document.getElementById('modal').classList.remove('show');
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
    // ...existing code...
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
                // Tıbbi Hizmet Kalitesi (10 Soru)
                "Doktorunuzun teşhis ve tedavi sürecine ne kadar güveniyorsunuz?",
                "Aldığınız tıbbi tedavinin açıklayıcı ve anlaşılır olduğunu düşünüyor musunuz?",
                "Doktorunuzun sorularınıza yeterli zaman ayırdığına inanıyor musunuz?",
                "Tedavi sürecinde ağrı veya rahatsızlığınızın yönetilmesinden memnun musunuz?",
                "Hastanenin tıbbi cihaz ve ekipmanlarının yeterli ve güncel olduğunu düşünüyor musunuz?",
                "İlaçlarınız ve tedaviniz hakkında yeterli bilgi aldığınıza inanıyor musunuz?",
                "Hastanenin laboratuvar ve görüntüleme hizmetlerinin hızından memnun musunuz?",
                "Aldığınız tedavinin beklediğiniz faydayı sağladığını düşünüyor musunuz?",
                "Doktorunuzun sizi tedavi planı konusunda karar sürecine dahil ettiğine inanıyor musunuz?",
                "Tıbbi hizmetlerin genel kalitesini nasıl değerlendiriyorsunuz?",
                // Personel Davranışları ve İletişim (10 Soru)
                "Hemşire ve diğer sağlık personelinin size karşı nazik ve saygılı davrandığına inanıyor musunuz?",
                "Personelin, ihtiyaç duyduğunuzda size hızlı bir şekilde yanıt verdiğini düşünüyor musunuz?",
                "Sağlık personelinin, sizi bilgilendirme konusunda yeterli çaba gösterdiğine inanıyor musunuz?",
                "Personelin, mahremiyetinize ve kişisel alanınıza saygı duyduğunu düşünüyor musunuz?",
                "Hemşirenizin veya sağlık ekibinizin size karşı sabırlı ve anlayışlı davrandığına inanıyor musunuz?",
                "Hasta bakımı sırasında size yeterli ilginin gösterildiğine inanıyor musunuz?",
                "Personel ile iletişim kurarken kendinizi rahat ve güvende hissettiniz mi?",
                "Sağlık personelinin size güvence ve moral verdiğini düşünüyor musunuz?",
                "Tedaviniz sırasında duygusal olarak desteklendiğinize inanıyor musunuz?",
                "Personel ile iletişiminizin genel kalitesini nasıl değerlendiriyorsunuz?",
                // Hastane Ortamı ve İmkanlar (10 Soru)
                "Hastane odasının temizliğinden ve konforundan memnun musunuz?",
                "Genel hastane ortamının (koridorlar, bekleme alanları) temiz ve düzenli olduğunu düşünüyor musunuz?",
                "Hastanenin genel gürültü seviyesinin kabul edilebilir olduğunu düşünüyor musunuz?",
                "Hastanenin yemek hizmetlerinin kalitesinden ve çeşitliliğinden memnun musunuz?",
                "Hastanenin otopark ve ulaşım imkanlarının yeterli olduğunu düşünüyor musunuz?",
                "Ziyaret saatlerinin ve kurallarının makul olduğunu düşünüyor musunuz?",
                "Hastane içinde yol bulmanın kolay olduğuna inanıyor musunuz?",
                "Tuvaletlerin ve banyo imkanlarının hijyenik olduğunu düşünüyor musunuz?",
                "Hastanenin güvenlik önlemlerinin yeterli olduğuna inanıyor musunuz?",
                "Hastane ortamının genel kalitesini nasıl değerlendiriyorsunuz?",
                // Yönlendirme ve Bilgilendirme (10 Soru)
                "Hastaneye yatış sürecinin kolay ve anlaşılır olduğunu düşünüyor musunuz?",
                "Hastane personeli tarafından randevu ve kayıt işlemlerinde yeterince yönlendirildiğinize inanıyor musunuz?",
                "Tıbbi prosedürler ve riskler hakkında size yeterli bilgi verildiğini düşünüyor musunuz?",
                "Hastanenin, size özel bilgilerinizi koruduğuna ve gizliliğe önem verdiğine inanıyor musunuz?",
                "Taburcu sürecinin düzenli ve anlaşılır bir şekilde yönetildiğini düşünüyor musunuz?",
                "Taburcu sonrası bakım ve takip süreci hakkında yeterli bilgi aldığınıza inanıyor musunuz?",
                "Hastanenin web sitesi veya bilgilendirme materyallerinin anlaşılır ve faydalı olduğunu düşünüyor musunuz?",
                "Hastane çalışanlarının sizi doğru servislere ve birimlere yönlendirmesinden memnun musunuz?",
                "Hasta haklarınız konusunda yeterli bilgiye sahip olduğunuza inanıyor musunuz?",
                "Hastaneye yatış sürecinin genel kalitesini nasıl değerlendiriyorsunuz?",
                // Genel Deneyim ve Tavsiye (10 Soru)
                "Hastanede yaşadığınız genel deneyimden memnun musunuz?",
                "Hastaneyi, yakınlarınıza veya arkadaşlarınıza tavsiye eder misiniz?",
                "Acil durumlar için bu hastaneyi tekrar tercih eder misiniz?",
                "Hastane personelinin, beklentilerinizi aştığını düşünüyor musunuz?",
                "Hastanede aldığınız hizmetin, ödediğiniz ücrete değdiğini düşünüyor musunuz?",
                "Şikayet veya önerileriniz için kolayca iletişim kurabildiğinize inanıyor musunuz?",
                "Hastanenin, hasta geri bildirimlerine önem verdiğini düşünüyor musunuz?",
                "Hastanenin, sunduğu hizmetlerin kalitesini sürekli iyileştirdiğine inanıyor musunuz?",
                "Hastanenin, hasta memnuniyetini önceliklendirdiğini düşünüyor musunuz?",
                "Hastanede geçirdiğiniz süre boyunca kendinizi değerli hissettiniz mi?"
            ],
            "Doktor": [
                // Çalışma Ortamı ve Kaynaklar (10 Soru)
                "Hastanenin tıbbi cihaz ve ekipmanlarının yeterli ve güncel olduğunu düşünüyor musunuz?",
                "Çalışma saatlerinizin makul ve yönetilebilir olduğunu düşünüyor musunuz?",
                "Hastanenin fiziksel ortamının (muayene odası, ameliyathane) verimli çalışmaya uygun olduğuna inanıyor musunuz?",
                "İdari personel ve destek birimlerinin (laboratuvar, radyoloji) iş birliğinden memnun musunuz?",
                "Acil durumlar için gerekli kaynaklara ve protokollere kolayca erişebildiğinizi düşünüyor musunuz?",
                "Hastanenin hasta kayıt sistemi ve dijital altyapısının işinizi kolaylaştırdığına inanıyor musunuz?",
                "Hastanenin, mesleki güvenliğinizi ve sağlığınızı önemsediğini düşünüyor musunuz?",
                "Tıbbi malzeme ve sarf ürünlerine kolayca erişebildiğinize inanıyor musunuz?",
                "Hastanenin temizlik ve hijyen standartlarının yeterli olduğuna inanıyor musunuz?",
                "Hastanedeki çalışma ortamınızdan genel olarak memnun musunuz?",
                // Yönetim ve İletişim (10 Soru)
                "Hastane yönetiminin aldığı kararların şeffaf ve anlaşılır olduğuna inanıyor musunuz?",
                "Yönetimin, doktorların fikirlerine ve önerilerine değer verdiğini düşünüyor musunuz?",
                "Yönetimle iletişim kanallarının açık ve etkili olduğunu düşünüyor musunuz?",
                "Hastane yönetiminin, hasta memnuniyetini önceliklendirdiğine inanıyor musunuz?",
                "Yönetimin, doktorlar arasında iş birliğini ve takım çalışmasını teşvik ettiğini düşünüyor musunuz?",
                "Maaş ve yan haklarınızın adil ve rekabetçi olduğunu düşünüyor musunuz?",
                "Yönetimin, akademik ve bilimsel çalışmalarınıza destek verdiğine inanıyor musunuz?",
                "Yöneticilerinizin, hasta bakımı süreçlerinde size yeterli özerkliği tanıdığına inanıyor musunuz?",
                "Yönetimle olan ilişkinizin genel olarak güvene dayalı olduğunu düşünüyor musunuz?",
                "Hastane yönetiminin genel performansını nasıl değerlendiriyorsunuz?",
                // Hasta Bakım ve İş Birliği (10 Soru)
                "Hasta bakımının kalitesini nasıl değerlendiriyorsunuz?",
                "Diğer birimlerdeki sağlık profesyonelleriyle (hemşireler, terapistler) iş birliğinizden memnun musunuz?",
                "Hastane genelinde hasta güvenliğine verilen önemin yeterli olduğunu düşünüyor musunuz?",
                "Hastalarla iletişim kurmak ve onları tedavi süreçleri hakkında bilgilendirmek için yeterli zamana sahip olduğunuza inanıyor musunuz?",
                "Hastane personelinin, hasta beklentilerini karşılama konusunda yeterli çaba gösterdiğine inanıyor musunuz?",
                "Hasta ve yakınlarının geri bildirimlerinin, hizmet kalitesini artırdığını düşünüyor musunuz?",
                "Hasta bakımı konusunda etik ve ahlaki değerlere yeterli önemin verildiğine inanıyor musunuz?",
                "Hasta kayıt sisteminin, hasta bilgilerine hızlı ve güvenli erişim sağladığına inanıyor musunuz?",
                "Meslektaşlarınızla olan iş birliğinizin verimli ve yapıcı olduğunu düşünüyor musunuz?",
                "Hastaların tedavi süreçlerine katılımlarını teşvik eden bir ortam olduğuna inanıyor musunuz?",
                // Mesleki Gelişim ve Eğitim (10 Soru)
                "Hastanenin, mesleki gelişiminize yönelik yeterli fırsatlar sunduğunu düşünüyor musunuz?",
                "Konferans, seminer ve eğitimlere katılma konusunda desteklendiğinize inanıyor musunuz?",
                "Hastanenin araştırma ve bilimsel çalışmalara yeterli bütçe ayırdığını düşünüyor musunuz?",
                "Mesleki literatürü takip etme konusunda hastanenin size destek olduğunu düşünüyor musunuz?",
                "Hastanenin, yeni ve güncel tıbbi yöntemleri uygulamaya açık olduğuna inanıyor musunuz?",
                "Deneyimli doktorların, genç meslektaşlarına mentorluk yaptığını düşünüyor musunuz?",
                "Hastanenin, uzmanlık alanınıza yönelik özel eğitimler sağladığına inanıyor musunuz?",
                "Kariyerinizde yükselme olanaklarının adil ve liyakate dayalı olduğunu düşünüyor musunuz?",
                "Hastanenin, çalışan memnuniyetini artırmaya yönelik çabalarını yeterli buluyor musunuz?",
                "Hastanede çalışmaktan genel olarak memnun musunuz?",
                // Çalışan Refahı ve Sosyal Denge (10 Soru)
                "Hastanenin, çalışanların fiziksel ve ruhsal sağlığını önemsediğine inanıyor musunuz?",
                "Çalışanlar için sosyal ve sportif faaliyetlerin düzenlendiğini düşünüyor musunuz?",
                "Hastanenin, iş-özel yaşam dengesini korumanıza yardımcı olduğuna inanıyor musunuz?",
                "Çalışanlara yönelik psikolojik destek hizmetlerinin yeterli olduğuna inanıyor musunuz?",
                "Hastanenin, çalışanlar arasında eşitlik ve adalet sağladığına inanıyor musunuz?",
                "İşten duyduğunuz tatminin, yaptığınız işin toplumsal faydasından kaynaklandığını düşünüyor musunuz?",
                "Hastane içinde kendinizi değerli ve takdir edilmiş hissettiğinize inanıyor musunuz?",
                "Hastanenin, çalışanların şikayetlerine karşı duyarlı bir yaklaşım sergilediğini düşünüyor musunuz?",
                "Hastanenin, iş güvenliği ve sağlığına yönelik önlemlerini yeterli buluyor musunuz?",
                "Hastanedeki genel çalışan refahı seviyesini nasıl değerlendiriyorsunuz?"
            ],
            "Personel": [
            ],
            "Yönetim": [
                // Finansal Performans ve Operasyonel Verimlilik (10 Soru)
                "Hastanenin genel finansal performansından ne kadar memnunsunuz?",
                "Bütçe planlama ve yönetim süreçlerinin verimliliğinden ne kadar memnunsunuz?",
                "Gelir ve gider takibinin doğruluğundan ne kadar memnunsunuz?",
                "Hasta yatış süreçlerinin ne kadar verimli işlediğinden ne kadar memnunsunuz?",
                "Fatura ve ödeme süreçlerinin hasta ve kurum için kolaylığından ne kadar memnunsunuz?",
                "Tıbbi malzeme ve ilaç tedarik süreçlerinin yönetiminden ne kadar memnunsunuz?",
                "Hastanenin genel karlılığından ne kadar memnunsunuz?",
                "Yatırım geri dönüşlerinin (ROI) beklenen seviyede olmasından ne kadar memnunsunuz?",
                "Finansal risklerin yönetilme şeklinden ne kadar memnunsunuz?",
                "Hastanenin genel operasyonel verimliliğinden ne kadar memnunsunuz?",
                // Pazarlama ve Marka Yönetimi (10 Soru)
                "Pazarlama stratejilerinizin hastane marka bilinirliğine katkısından ne kadar memnunsunuz?",
                "Dijital pazarlama kampanyalarının (reklamlar, sosyal medya) etkinliğinden ne kadar memnunsunuz?",
                "Hastanenin web sitesi ve dijital varlıklarının profesyonelliğinden ne kadar memnunsunuz?",
                "Markanızın sektördeki itibarından ve algısından ne kadar memnunsunuz?",
                "Halkla ilişkiler faaliyetlerinin kurum imajına katkısından ne kadar memnunsunuz?",
                "Hastanenin sunduğu hizmetlerin hedef kitleye ulaşma başarısından ne kadar memnunsunuz?",
                "Medya ilişkilerinin yönetiminden ne kadar memnunsunuz?",
                "Hasta referanslarının pazarlama aracı olarak kullanılmasından ne kadar memnunsunuz?",
                "Kurumsal kimlik çalışmalarının tutarlılığından ne kadar memnunsunuz?",
                "Pazarlama bütçesinin etkin bir şekilde kullanılmasından ne kadar memnunsunuz?",
                // İnsan Kaynakları Yönetimi (10 Soru)
                "Çalışan işe alım süreçlerinin etkinliğinden ne kadar memnunsunuz?",
                "Çalışanların performans değerlendirme sisteminin adilliğinden ne kadar memnunsunuz?",
                "Çalışan memnuniyeti ve motivasyonunun yönetilme şeklinden ne kadar memnunsunuz?",
                "Personel sirkülasyon oranlarının yönetiminden ne kadar memnunsunuz?",
                "Çalışanların eğitim ve gelişim planlamasının etkinliğinden ne kadar memnunsunuz?",
                "Liderlik ve yöneticilik becerilerinin geliştirilmesine yönelik programlardan ne kadar memnunsunuz?",
                "Çalışanlara sunulan ücret ve yan hakların sektör ortalamasına uygunluğundan ne kadar memnunsunuz?",
                "İş yerinde sağlıklı ve güvenli bir ortamın sağlanmasından ne kadar memnunsunuz?",
                "Çalışanların kariyer planlamasına sağlanan destekten ne kadar memnunsunuz?",
                "İnsan kaynakları süreçlerinin genel şeffaflığından ne kadar memnunsunuz?",
                // Hasta İlişkileri ve Kalite Kontrol (10 Soru)
                "Hasta şikayet ve geri bildirimlerinin yönetiminden ne kadar memnunsunuz?",
                "Hastalara sunulan hizmetlerin genel kalitesinden ne kadar memnunsunuz?",
                "Hastaların beklentilerinin ne kadar karşılandığından ne kadar memnunsunuz?",
                "Tedavi sonuçlarının izlenme ve değerlendirilme şeklinden ne kadar memnunsunuz?",
                "Hasta memnuniyeti anketlerinin düzenliliğinden ve sonuçlarının analizinden ne kadar memnunsunuz?",
                "Hastane içinde kalite kontrol standartlarının uygulanma şeklinden ne kadar memnunsunuz?",
                "Hastaların kişisel verilerinin korunmasından ne kadar memnunsunuz?",
                "Hasta mahremiyetine verilen önemden ne kadar memnunsunuz?",
                "Hasta ilişkileri departmanının etkinliğinden ne kadar memnunsunuz?",
                "Hastane içinde hasta güvenliğine verilen önemden ne kadar memnunsunuz?",
                // Teknolojik Altyapı ve Gelecek Vizyonu (10 Soru)
                "Hastanenin kullandığı tıbbi cihaz ve ekipmanların güncelliğinden ne kadar memnunsunuz?",
                "Hastane yönetiminin uyguladığı bilişim ve teknoloji stratejisinden ne kadar memnunsunuz?",
                "Yeni teknolojilere yapılan yatırımların etkinliğinden ne kadar memnunsunuz?",
                "Hastanenin dijitalleşme sürecinden ve otomasyon seviyesinden ne kadar memnunsunuz?",
                "E-Sağlık ve dijital hizmetlerin (uzaktan hasta takibi, online randevu vb.) geliştirilme hızından ne kadar memnunsunuz?",
                "Hastane yönetiminin, geleceğe yönelik stratejik planlamasından ne kadar memnunsunuz?",
                "Hastanenin bölgesel ve ulusal düzeyde rekabet gücünden ne kadar memnunsunuz?",
                "Hastanenin kriz ve acil durum planlarının ne kadar etkin olduğundan ne kadar memnunsunuz?",
                "Hastanenin sürdürülebilirlik ve çevresel etki hedeflerinden ne kadar memnunsunuz?",
                "Hastanenin genel olarak geleceğe ne kadar hazır olduğundan ne kadar memnunsunuz?"
            ]
        };

        // Sistem verileri
        let systemData = {
            adminPassword: '030714',
            surveyData: null
        };

        // Sayfa yüklendiğinde
        document.addEventListener('DOMContentLoaded', function() {
            setupEventListeners();
            showModule('survey');
        });

        function setupEventListeners() {
            // Anket başlatma
            document.getElementById('startSurvey').addEventListener('click', startSurvey);
            
            // Anket tamamlama
            document.getElementById('submitSurvey').addEventListener('click', submitSurvey);

            // Enter tuşu ile giriş
            document.getElementById('companyPassword').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') loginCompany();
            });
            
            document.getElementById('adminPassword').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') loginAdmin();
            });
        }

        function showModule(module) {
            // Tüm modülleri gizle
            document.getElementById('surveyModule').classList.add('hidden');
            document.getElementById('companyModule').classList.add('hidden');
            document.getElementById('adminModule').classList.add('hidden');
            
            // Seçili modülü göster
            document.getElementById(module + 'Module').classList.remove('hidden');
            currentModule = module;
        }

        // Firebase Realtime Database API fonksiyonları (GLOBAL SCOPE)
        async function loadFromFirebase() {
            try {
                const response = await fetch(FIREBASE_DB_URL + 'surveyData.json');
                if (response.ok) {
                    const data = await response.json();
                    systemData.surveyData = data || { companies: {}, responses: [], statistics: {} };
                    return systemData.surveyData;
                } else {
                    throw new Error('Firebase veri yükleme hatası');
                }
            } catch (error) {
                console.error('Firebase yükleme hatası:', error);
                const defaultData = { companies: {}, responses: [], statistics: {} };
                systemData.surveyData = defaultData;
                return defaultData;
            }
        }

        async function saveToFirebase(data) {
            try {
                const response = await fetch(FIREBASE_DB_URL + 'surveyData.json', {
                    method: 'PUT',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(data)
                });
                if (response.ok) {
                    return { success: true };
                } else {
                    return { success: false, error: 'Firebase veri kaydetme hatası' };
                }
            } catch (error) {
                console.error('Firebase bağlantı hatası:', error);
                return { success: false, error: `Bağlantı Hatası: ${error.message}` };
            }
        }

        async function createCompanyIfNotExistsFirebase(companyName) {
            if (!systemData.surveyData) await loadFromFirebase();
            if (!systemData.surveyData.companies) systemData.surveyData.companies = {};
            const normalizedName = companyName.trim().toLowerCase();
            let companyKey = Object.keys(systemData.surveyData.companies).find(key => (systemData.surveyData.companies[key].name || '').trim().toLowerCase() === normalizedName);
            if (!companyKey) {
                // Yeni şifre üret
                const password = generateCompanyPassword();
                companyKey = Date.now().toString();
                systemData.surveyData.companies[companyKey] = { name: companyName.trim(), password, createdAt: new Date().toISOString(), status: 'Aktif' };
                const saveResult = await saveToFirebase(systemData.surveyData);
                if (!saveResult.success) {
                    return { success: false, error: saveResult.error };
                }
            }
            // Eğer eski kurum ise ve status yoksa, Aktif olarak ekle
            if (!systemData.surveyData.companies[companyKey].status) {
                systemData.surveyData.companies[companyKey].status = 'Aktif';
                await saveToFirebase(systemData.surveyData);
            }
            return { success: true, key: companyKey };
        }

        function selectJobType(jobType) {
            // Sadece rol seçimi yap, anket başlatma
            window.selectedJobType = jobType;
            selectedJobType = jobType;
            console.log('Seçilen rol:', jobType);
            
            // Tüm butonları sıfırla
            const allButtons = document.querySelectorAll('.job-btn');
            allButtons.forEach(btn => {
                btn.classList.remove('selected-job');
                btn.style.border = '';
                btn.style.backgroundColor = '';
                btn.style.color = '';
                btn.style.fontWeight = '';
                btn.style.transform = '';
                btn.style.boxShadow = '';
            });

            // Seçili butonu vurgula
            const selectedBtn = document.getElementById(jobType === 'Hasta' ? 'patientBtn' : 
                                                      jobType === 'Doktor' ? 'doctorBtn' : 'managementBtn');
            if (selectedBtn) {
                selectedBtn.classList.add('selected-job');
                selectedBtn.style.border = '3px solid #6366f1';
                selectedBtn.style.backgroundColor = '#6366f1';
                selectedBtn.style.color = 'white';
                selectedBtn.style.fontWeight = 'bold';
                selectedBtn.style.transform = 'scale(1.05)';
                selectedBtn.style.boxShadow = '0 4px 8px rgba(99, 102, 241, 0.3)';
            }

            console.log('Rol seçildi:', jobType);
        }

        function startTimer() {
            timerInterval = setInterval(() => {
                const elapsed = Math.floor((new Date() - surveyStartTime) / 1000);
                const minutes = Math.floor(elapsed / 60);
                const seconds = elapsed % 60;
                document.getElementById('timeElapsed').textContent = 
                    `Süre: ${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
            }, 1000);
        }

        function displayCurrentQuestion() {
            const container = document.getElementById('questionContainer');
            const question = currentQuestions[currentQuestionIndex];
            
            container.innerHTML = `
                <div class="bg-gray-50 p-8 rounded-lg border-l-4 border-purple-500">
                    <h3 class="text-xl font-semibold mb-6">${question}</h3>
                    <div class="grid grid-cols-1 md:grid-cols-5 gap-4">
                        <button onclick="selectAnswer(1)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-red-200 hover:border-red-400 hover:bg-red-50 transition-all duration-200 text-center">
                            <div class="text-3xl font-bold mb-2 text-red-600">1</div>
                            <div class="text-sm font-medium text-gray-700">Hiç Memnun Değilim</div>
                        </button>
                        <button onclick="selectAnswer(2)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-orange-200 hover:border-orange-400 hover:bg-orange-50 transition-all duration-200 text-center">
                            <div class="text-3xl font-bold mb-2 text-orange-600">2</div>
                            <div class="text-sm font-medium text-gray-700">Memnun Değilim</div>
                        </button>
                        <button onclick="selectAnswer(3)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-yellow-200 hover:border-yellow-400 hover:bg-yellow-50 transition-all duration-200 text-center">
                            <div class="text-3xl font-bold mb-2 text-yellow-600">3</div>
                            <div class="text-sm font-medium text-gray-700">Kararsızım</div>
                        </button>
                        <button onclick="selectAnswer(4)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-green-200 hover:border-green-400 hover:bg-green-50 transition-all duration-200 text-center">
                            <div class="text-3xl font-bold mb-2 text-green-600">4</div>
                            <div class="text-sm font-medium text-gray-700">Memnunum</div>
                        </button>
                        <button onclick="selectAnswer(5)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-blue-200 hover:border-blue-400 hover:bg-blue-50 transition-all duration-200 text-center">
                            <div class="text-3xl font-bold mb-2 text-blue-600">5</div>
                            <div class="text-sm font-medium text-gray-700">Çok Memnunum</div>
                        </button>
                    </div>
                </div>
            `;
            
            updateProgress();
        }

        function selectAnswer(score) {
            answers.push({
                question: currentQuestions[currentQuestionIndex],
                score: score,
                timestamp: new Date().toISOString()
            });
            
            currentQuestionIndex++;
            
            if (currentQuestionIndex < currentQuestions.length) {
                displayCurrentQuestion();
            } else {
                showSubmitButton();
            }
        }

        function updateProgress() {
            const progress = (currentQuestionIndex / currentQuestions.length) * 100;
            document.getElementById('progressBar').style.width = progress + '%';
            document.getElementById('progressText').textContent = 
                `Anket İlerlemesi ${currentQuestionIndex}/${currentQuestions.length} Yanıtlandı`;
        }

        function showSubmitButton() {
            clearInterval(timerInterval);
            document.getElementById('questionContainer').innerHTML = `
                <div class="text-center bg-green-50 p-10 rounded-lg border-2 border-green-200">
                    <div class="text-8xl mb-6">🎉</div>
                    <h3 class="text-2xl font-semibold text-green-800 mb-4">Tebrikler!</h3>
                    <p class="text-green-700 mb-6 text-lg">Tüm soruları yanıtladınız. Anketi tamamlamak için aşağıdaki butona tıklayın.</p>
                    <div class="text-base text-green-600">
                        <p>Toplam süre: ${document.getElementById('timeElapsed').textContent.split(': ')[1]}</p>
                    </div>
                </div>
            `;
            document.getElementById('submitSurvey').classList.remove('hidden');
            updateProgress();
        }

        // JSONBin.io API fonksiyonları
        async function createNewBin() {
            throw new Error('Sabit binId ile çalışıyor, yeni bin oluşturulamaz.');
        }

        async function loadFromJSONBin() {
            try {
                if (!JSONBIN_CONFIG.binId) {
                    throw new Error('Sabit binId tanımlı değil!');
                }
                console.log('JSONBin\'den veri yükleniyor... Bin ID:', JSONBIN_CONFIG.binId);
                const response = await fetch(`${JSONBIN_CONFIG.baseUrl}/b/${JSONBIN_CONFIG.binId}/latest`, {
                    headers: {
                        'X-Master-Key': JSONBIN_CONFIG.apiKey,
                        'X-Access-Key': JSONBIN_CONFIG.accessKey,
                        'X-Bin-Meta': 'false'
                    }
                });
                if (response.ok) {
                    const data = await response.json();
                    systemData.surveyData = data.record || data;
                    return systemData.surveyData;
                } else {
                    throw new Error(`API Hatası: ${response.status}`);
                }
            } catch (error) {
                console.error('JSONBin yükleme hatası:', error);
                const defaultData = {
                    surveyName: "Kurum Değerlendirme Anketi - Sürüm 12",
                    createdAt: new Date().toISOString(),
                    responses: [],
                    statistics: {
                        totalResponses: 0,
                        averageScore: 0,
                        lastUpdated: new Date().toISOString()
                    },
                    companies: {}
                };
                systemData.surveyData = defaultData;
                return defaultData;
            }
        }

        async function saveToJSONBin(data, retryCount = 0) {
            try {
                if (!JSONBIN_CONFIG.binId) {
                    throw new Error('Sabit binId tanımlı değil!');
                }
                console.log(`JSONBin'e veri kaydediliyor... Bin ID: ${JSONBIN_CONFIG.binId}`);
                const response = await fetch(`${JSONBIN_CONFIG.baseUrl}/b/${JSONBIN_CONFIG.binId}`, {
                    method: 'PUT',
                    headers: {
                        'Content-Type': 'application/json',
                        'X-Master-Key': JSONBIN_CONFIG.apiKey,
                        'X-Access-Key': JSONBIN_CONFIG.accessKey,
                        'X-Bin-Versioning': 'false'
                    },
                    body: JSON.stringify(data)
                });
                if (response.ok) {
                    const result = await response.json();
                    console.log('JSONBin kaydetme başarılı:', result);
                    return { success: true, data: result };
                } else {
                    const errorText = await response.text();
                    console.error('JSONBin API hatası:', response.status, errorText);
                    if (retryCount < JSONBIN_CONFIG.maxRetries && (response.status >= 500 || response.status === 429)) {
                        console.log(`${JSONBIN_CONFIG.retryDelay}ms sonra yeniden denenecek...`);
                        await new Promise(resolve => setTimeout(resolve, JSONBIN_CONFIG.retryDelay * (retryCount + 1)));
                        return await saveToJSONBin(data, retryCount + 1);
                    }
                    return { success: false, error: `API Hatası: ${response.status} - ${errorText}` };
                }
            } catch (error) {
                console.error('JSONBin bağlantı hatası:', error);
                if (retryCount < JSONBIN_CONFIG.maxRetries) {
                    console.log(`Ağ hatası - ${JSONBIN_CONFIG.retryDelay}ms sonra yeniden denenecek...`);
                    await new Promise(resolve => setTimeout(resolve, JSONBIN_CONFIG.retryDelay * (retryCount + 1)));
                    return await saveToJSONBin(data, retryCount + 1);
                }
                return { success: false, error: `Bağlantı Hatası: ${error.message}` };
            }
        }

        // Eski fonksiyonun yerine Firebase fonksiyonunu kullan
        const createCompanyIfNotExists = createCompanyIfNotExistsFirebase;

        function generateCompanyPassword() {
            const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
            let password = '';
            for (let i = 0; i < 12; i++) {
                password += chars.charAt(Math.floor(Math.random() * chars.length));
            }
            return password;
        }

        async function submitSurvey() {
            try {
                console.log('Anket gönderiliyor...');
                
                const companyName = document.getElementById('companyName').value.trim();
                const firstName = document.getElementById('firstName').value.trim() || 'Anonim';
                const lastName = document.getElementById('lastName').value.trim() || 'Kullanıcı';
                
                if (!companyName || !selectedJobType || !answers || answers.length === 0) {
                    throw new Error('Eksik bilgi: Kurum adı, iş türü ve anket yanıtları gerekli');
                }
                
                const companyResult = await createCompanyIfNotExistsFirebase(companyName);
                
                if (!companyResult.success) {
                    throw new Error(`Kurum işlemi başarısız: ${companyResult.error}`);
                }
                
                systemData.surveyData = await loadFromFirebase();
                
                const surveyResponse = {
                    id: 'survey_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9),
                    companyName: companyName,
                    firstName: firstName,
                    lastName: lastName,
                    jobType: selectedJobType,
                    answers: answers,
                    submittedAt: new Date().toISOString(),
                    totalScore: answers.reduce((sum, answer) => sum + answer.score, 0),
                    averageScore: (answers.reduce((sum, answer) => sum + answer.score, 0) / answers.length).toFixed(2),
                    duration: document.getElementById('timeElapsed').textContent.split(': ')[1] || '00:00'
                };
                
                if (!systemData.surveyData.responses) {
                    systemData.surveyData.responses = [];
                }
                systemData.surveyData.responses.push(surveyResponse);
                
                if (!systemData.surveyData.statistics) {
                    systemData.surveyData.statistics = {
                        totalResponses: 0,
                        averageScore: 0,
                        lastUpdated: new Date().toISOString()
                    };
                }
                
                systemData.surveyData.statistics.totalResponses = systemData.surveyData.responses.length;
                systemData.surveyData.statistics.averageScore = (
                    systemData.surveyData.responses.reduce((sum, r) => sum + parseFloat(r.averageScore), 0) / 
                    systemData.surveyData.responses.length
                ).toFixed(2);
                systemData.surveyData.statistics.lastUpdated = new Date().toISOString();
                
                if (companyResult && systemData.surveyData.companies[companyResult.key]) {
                    systemData.surveyData.companies[companyResult.key].totalResponses = 
                        systemData.surveyData.responses.filter(r => 
                            r.companyName.toLowerCase() === companyName.toLowerCase()
                        ).length;
                }
                
                const saveResult = await saveToFirebase(systemData.surveyData);
                
                if (saveResult.success) {
                    document.getElementById('surveySection').innerHTML = `
                        <div class="text-center bg-green-50 p-10 rounded-lg border-2 border-green-200">
                            <div class="text-8xl mb-6">✅</div>
                            <h2 class="text-3xl font-bold text-green-800 mb-6">Anketiniz Başarıyla Kaydedildi!</h2>
                            <p class="text-green-700 mb-6 text-lg">
                                Değerli görüşleriniz için teşekkür ederiz. Anket yanıtlarınız güvenli bir şekilde JSONBin.io sisteminde saklandı.
                            </p>
                            <div class="bg-blue-50 p-6 rounded-lg border border-blue-200 mb-6">
                                <p class="text-base text-blue-700">
                                    <strong>📊 Raporlama Bilgisi:</strong> Anket sonuçlarınız güvenli bir şekilde kaydedildi. 
                                    Kurum yöneticiniz raporları görüntüleyebilir ve analiz edebilir.
                                </p>
                            </div>
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                <button onclick="showModule('company')" class="bg-blue-600 text-white px-6 py-3 rounded-lg hover:bg-blue-700 transition-colors text-lg font-semibold">
                                    🏫 Kurum Portalına Git
                                </button>
                                <button onclick="location.reload()" class="bg-purple-600 text-white px-6 py-3 rounded-lg hover:bg-purple-700 transition-colors text-lg font-semibold">
                                    🔄 Yeni Anket Başlat
                                </button>
                            </div>
                        </div>
                    `;
                } else {
                    throw new Error(`Anket kaydedilemedi: ${saveResult.error}`);
                }
                
            } catch (error) {
                console.error('Anket gönderme hatası:', error);
                showModal('❌ Hata', `Anket gönderilirken bir hata oluştu:<br><br><strong>Hata:</strong> ${error.message}<br><br>Lütfen sayfayı yenileyip tekrar deneyin.`);
            }
        }

        async function loginAdmin() {
            const input = document.getElementById('adminPassword').value.trim();
            if (input === systemData.adminPassword) {
                isAdminLoggedIn = true;
                document.getElementById('adminLogin').classList.add('hidden');
                document.getElementById('adminDashboard').classList.remove('hidden');
                loadAdminDashboard();
            } else {
                showModal('❌ Hatalı Şifre', 'Yönetici şifresi yanlış. Lütfen tekrar deneyin.');
            }
        }

        function logoutAdmin() {
            isAdminLoggedIn = false;
            document.getElementById('adminDashboard').classList.add('hidden');
            document.getElementById('adminLogin').classList.remove('hidden');
            document.getElementById('adminPassword').value = '';
        }

        function loadAdminDashboard() {
            loadFromFirebase().then(() => {
                // Toplam kurum
                const totalCompanies = systemData.surveyData.companies ? Object.keys(systemData.surveyData.companies).length : 0;
                document.getElementById('totalCompanies').textContent = totalCompanies;
                // Toplam katılımcı
                const totalUsers = systemData.surveyData.responses ? systemData.surveyData.responses.length : 0;
                document.getElementById('totalUsers').textContent = totalUsers;
                // Aktif anketler (örnek: toplam kurum sayısı)
                document.getElementById('activeSurveys').textContent = totalCompanies;
                // Şirket tablosu
                const tbody = document.getElementById('companyList');
                if (tbody) {
                    // Arama ve alfabetik sıralama
                    const companies = systemData.surveyData.companies || {};
                    const responses = systemData.surveyData.responses || [];
                    let search = '';
                    const searchInput = document.getElementById('companySearchInput');
                    if (searchInput) search = searchInput.value.trim().toLowerCase();
                    const sortedCompanies = Object.entries(companies).sort((a, b) => {
                        const nameA = a[1].name.toLowerCase();
                        const nameB = b[1].name.toLowerCase();
                        return nameA.localeCompare(nameB, 'tr');
                    });
                    const filtered = sortedCompanies.filter(([_, c]) =>
                        !search || c.name.toLowerCase().includes(search)
                    );
                    if (filtered.length === 0) {
                        tbody.innerHTML = `<tr><td colspan="5" class="text-center py-4 text-gray-500">Aramanıza uygun kurum bulunamadı.</td></tr>`;
                        return;
                    }
                    tbody.innerHTML = filtered.map(([key, c]) => {
                        const count = responses.filter(r => r.companyName === c.name).length;
                        const status = c.status === 'Pasif' ? 'Pasif' : 'Aktif';
                        const statusColor = status === 'Aktif' ? 'text-green-600' : 'text-red-600';
                        return `<tr>
                            <td class='px-4 py-2'>${c.name}</td>
                            <td class='px-4 py-2'>${c.password}</td>
                            <td class='px-4 py-2'>${count}</td>
                            <td class='px-4 py-2 ${statusColor} font-semibold'>${status}</td>
                            <td class='px-4 py-2'>
                                <button onclick="toggleCompanyStatus('${key}')" class="px-3 py-1 rounded ${status === 'Aktif' ? 'bg-red-500 hover:bg-red-600 text-white' : 'bg-green-500 hover:bg-green-600 text-white'} text-xs font-bold">${status === 'Aktif' ? 'Askıya Al' : 'Aktif Et'}</button>
                            </td>
                        </tr>`;
                    }).join('');
                }
            });
        }

        // Canlı filtreleme için
        function filterCompanyList() {
            loadAdminDashboard();
        }

        // Kurum portalı giriş fonksiyonu ve dashboard yükleyici (sadece bir kez ve doğru yerde)
        async function loginCompany() {
            const companyName = document.getElementById('companyLoginName').value.trim();
            const password = document.getElementById('companyPassword').value.trim();
            if (!companyName || !password) {
                showModal('❌ Eksik Bilgi', 'Kurum adı ve şifre gereklidir.');
                return;
            }
            await loadFromFirebase();
            const companies = systemData.surveyData.companies || {};
            const normalizedName = companyName.trim().toLowerCase();
            const companyKey = Object.keys(companies).find(key => (companies[key].name || '').trim().toLowerCase() === normalizedName);
            if (!companyKey) {
                showModal('❌ Kurum Bulunamadı', 'Girilen kurum adı ile eşleşen bir kurum bulunamadı.');
                return;
            }
            if (companies[companyKey].password !== password) {
                showModal('❌ Hatalı Şifre', 'Girilen şifre yanlış.');
                return;
            }
            if (companies[companyKey].status === 'Pasif') {
                showModal('⛔ Askıya Alındı', 'Bu kurum şu anda askıya alınmış/dondurulmuş. Lütfen yöneticinizle iletişime geçin.');
                return;
            }
            loggedInCompany = companies[companyKey];
            document.getElementById('companyLogin').classList.add('hidden');
            document.getElementById('companyDashboard').classList.remove('hidden');
            loadCompanyDashboard();
        }

        // Admin: Kurum durumunu değiştir (Aktif/Pasif)
        async function toggleCompanyStatus(companyKey) {
            await loadFromFirebase();
            const companies = systemData.surveyData.companies || {};
            if (!companies[companyKey]) return;
            companies[companyKey].status = companies[companyKey].status === 'Aktif' ? 'Pasif' : 'Aktif';
            await saveToFirebase(systemData.surveyData);
            loadAdminDashboard();
        }

        // Kurum portalı çıkış fonksiyonu
        function logoutCompany() {
            loggedInCompany = null;
            document.getElementById('companyDashboard').classList.add('hidden');
            document.getElementById('companyLogin').classList.remove('hidden');
            document.getElementById('companyLoginName').value = '';
            document.getElementById('companyPassword').value = '';
        }

        function loadCompanyDashboard() {
            if (!loggedInCompany || !systemData.surveyData) return;
            const companySurveys = systemData.surveyData.responses.filter(s => 
                s.companyName.toLowerCase() === loggedInCompany.name.toLowerCase()
            );
            filteredSurveys = null;
            updateDashboardData(companySurveys);
        }

        function filterByDateRange() {
            if (!loggedInCompany || !systemData.surveyData) return;
            const start = document.getElementById('reportStartDate').value;
            const end = document.getElementById('reportEndDate').value;
            const allSurveys = systemData.surveyData.responses.filter(s => 
                s.companyName.toLowerCase() === loggedInCompany.name.toLowerCase()
            );
            if (!start && !end) {
                filteredSurveys = null;
                updateDashboardData(allSurveys);
                return;
            }
            const startDate = start ? new Date(start) : null;
            const endDate = end ? new Date(end) : null;
            const filtered = allSurveys.filter(s => {
                const d = new Date(s.submittedAt);
                if (startDate && d < startDate) return false;
                if (endDate) {
                    // Bitiş gününü de dahil et
                    const endOfDay = new Date(endDate);
                    endOfDay.setHours(23,59,59,999);
                    if (d > endOfDay) return false;
                }
                return true;
            });
            filteredSurveys = filtered;
            updateDashboardData(filtered);
        }
        
        function updateDashboardData(surveys) {
            document.getElementById('companyNameDisplay').textContent = loggedInCompany.name;
            document.getElementById('totalParticipants').textContent = surveys.length;
            let totalScore = 0;
            let totalAnswers = 0;
            surveys.forEach(s => {
                totalScore += s.totalScore;
                totalAnswers += s.answers.length;
            });
            const avgScore = totalAnswers > 0 ? (totalScore / totalAnswers).toFixed(1) : '0.0';
            document.getElementById('averageScore').textContent = avgScore;
            let highSatisfactionAnswers = 0;
            surveys.forEach(s => {
                s.answers.forEach(answer => {
                    if (answer.score >= 4) highSatisfactionAnswers++;
                });
            });
            const overallSatisfactionPercent = totalAnswers > 0 ? 
                Math.round((highSatisfactionAnswers / totalAnswers) * 100) : 0;
            document.getElementById('satisfactionRate').textContent = overallSatisfactionPercent + '%';
            generateSimpleReport(surveys);
            generateCharts(surveys);
            renderSummaryFrequencyTable(surveys); // Özet tabloyu güncelle
            
            // Eğer katılımcı tablosu açıksa onu da güncelle
            const participantDetails = document.getElementById('participantDetails');
            if (participantDetails && !participantDetails.classList.contains('hidden')) {
                loadParticipantTable(surveys);
            }
        }
        // HASTANE PDF RAPORU OLUŞTURMA
        // showPDFReport(true) => filtreli, showPDFReport(false) => tümü
        function showPDFReport(filtered) {
            const companyName = loggedInCompany ? loggedInCompany.name : '';
            let surveys;
            let dateInfo = '';
            if (filtered && filteredSurveys !== null) {
                surveys = filteredSurveys;
                const start = document.getElementById('reportStartDate').value;
                const end = document.getElementById('reportEndDate').value;
                if (start && end) dateInfo = ` - ${start} / ${end}`;
                else if (start) dateInfo = ` - ${start} sonrası`;
                else if (end) dateInfo = ` - ${end} öncesi`;
            } else {
                surveys = systemData.surveyData.responses.filter(s => s.companyName.toLowerCase() === companyName.toLowerCase());
            }
            const win = window.open('', '_blank');
            win.document.write(generateHospitalPDFContent(companyName, surveys, dateInfo));
            setTimeout(() => win.print(), 500);
        }

        function generateHospitalPDFContent(companyName, surveys, dateInfo = '') {
            const now = new Date();
            const dateStr = now.toLocaleDateString('tr-TR');
            const timeStr = now.toLocaleTimeString('tr-TR');
            const totalParticipants = surveys.length;
            let totalScore = 0;
            let totalAnswers = 0;
            surveys.forEach(s => {
                totalScore += s.totalScore;
                totalAnswers += s.answers.length;
            });
            const avgScore = totalAnswers > 0 ? (totalScore / totalAnswers).toFixed(1) : '0.0';
            const minPossibleScore = totalAnswers * 1;
            const maxPossibleScore = totalAnswers * 5;
            const satisfactionPercent = totalAnswers > 0 ? Math.round(((totalScore - minPossibleScore) / (maxPossibleScore - minPossibleScore)) * 100) : 0;
            // Genel durum kutusu
            let statusBox = '';
            if (satisfactionPercent < 50) {
                statusBox = `<div style='background:#fee2e2;padding:16px;border-radius:8px;margin-bottom:12px;'><b>Düşük Memnuniyet (%0-50) - Acil Müdahale Gerekli</b></div>`;
            } else if (satisfactionPercent < 80) {
                statusBox = `<div style='background:#fef9c3;padding:16px;border-radius:8px;margin-bottom:12px;'><b>Orta Memnuniyet (%51-80) - İyileştirme Gerekli</b></div>`;
            } else {
                statusBox = `<div style='background:#dcfce7;padding:16px;border-radius:8px;margin-bottom:12px;'><b>Yüksek Memnuniyet (%81-100)</b></div>`;
            }
            // Pozisyon analizi
            const positionData = {};
            surveys.forEach(s => {
                positionData[s.jobType] = (positionData[s.jobType] || 0) + 1;
            });
            // Değerlendirme dağılımı
            const satisfactionCounts = [0, 0, 0];
            surveys.forEach(s => {
                const avg = parseFloat(s.averageScore);
                if (avg < 2.5) satisfactionCounts[0]++;
                else if (avg < 3.5) satisfactionCounts[1]++;
                else satisfactionCounts[2]++;
            });
            // Yanıt dağılımı
            const answerLevels = ['Düşük Memnuniyet (1-2)', 'Orta Memnuniyet (3)', 'Yüksek Memnuniyet (4-5)'];
            const answerCounts = [0, 0, 0];
            surveys.forEach(s => {
                s.answers.forEach(a => {
                    if (a.score < 2.5) answerCounts[0]++;
                    else if (a.score < 3.5) answerCounts[1]++;
                    else answerCounts[2]++;
                });
            });
            // Kategori analizleri (örnek başlıklar)
            const hospitalCategories = [
                { title: '1. Tıbbi Hizmet Kalitesi', desc: 'Hastanede sunulan tıbbi hizmetlerin güvenilirliği, tedavi süreçlerinin şeffaflığı ve hasta güvenliği.' },
                { title: '2. Personel Davranışları ve İletişim', desc: 'Sağlık personelinin iletişimi, yaklaşımı ve hasta ile etkileşimi.' },
                { title: '3. Hastane Ortamı ve İmkanlar', desc: 'Fiziksel ortam, temizlik, konfor ve hastane imkanlarının kalitesi.' },
                { title: '4. Yönlendirme ve Bilgilendirme', desc: 'Hastane içi yönlendirme, bilgilendirme süreçleri ve hasta hakları.' },
                { title: '5. Genel Deneyim ve Tavsiye', desc: 'Genel memnuniyet, tekrar tercih etme ve tavsiye etme eğilimleri.' }
            ];
            // PDF HTML (grafiksel özet ve SWOT tablosu eklendi)
            return `
            <html><head><title>${companyName} - Kurum Değerlendirme Raporu</title>
            <style>
                body { font-family: Arial, sans-serif; margin: 0; padding: 0; }
                .header { text-align: center; margin-top: 24px; }
                .summary-grid { display: flex; justify-content: center; gap: 32px; margin: 24px 0; }
                .summary-box { background: #f8fafc; border-radius: 12px; padding: 24px 32px; min-width: 180px; text-align: center; font-size: 1.5rem; }
                .section { margin: 24px 0; }
                .section-title { font-size: 1.2rem; font-weight: bold; margin-bottom: 8px; }
                .table { width: 100%; border-collapse: collapse; margin-bottom: 16px; }
                .table th, .table td { border: 1px solid #e5e7eb; padding: 8px 12px; text-align: left; }
                .table th { background: #f1f5f9; }
                .highlight { font-weight: bold; color: #dc2626; }
                .info-box { background: #f1f5f9; border-radius: 8px; padding: 16px; margin-bottom: 12px; }
                .category-box { background: #fef2f2; border-radius: 8px; padding: 16px; margin-bottom: 12px; }
                .advice-box { background: #fef9c3; border-radius: 8px; padding: 16px; margin-bottom: 12px; }
                .date-info { background: #dbeafe; border-radius: 8px; padding: 12px; margin-bottom: 16px; text-align: center; font-weight: bold; color: #1e40af; }
                .swot-table { width: 100%; border-collapse: collapse; margin: 24px 0; }
                .swot-table th, .swot-table td { border: 1px solid #a3a3a3; padding: 10px; text-align: center; font-size: 1rem; }
                .swot-table th { background: #f3f4f6; font-size: 1.1rem; }
                .swot-strength { background: #d1fae5; }
                .swot-weakness { background: #fee2e2; }
                .swot-opportunity { background: #dbeafe; }
                .swot-threat { background: #fef9c3; }
            </style></head><body>
                <div class='header'>
                    <div style='font-size:2.2rem;font-weight:bold;margin-bottom:8px;'>🏥 ${companyName}</div>
                    <div style='font-size:1.3rem;font-weight:bold;'>Kurum Değerlendirme Raporu${dateInfo}</div>
                    <div style='font-size:1rem;margin-top:4px;'>Rapor Tarihi: ${dateStr}</div>
                </div>
                ${dateInfo ? `<div class='date-info'>📅 Filtrelenmiş Rapor${dateInfo}</div>` : ''}
                <div class='summary-grid'>
                    <div class='summary-box'><div style='font-size:1.1rem;'>${totalParticipants}</div>Toplam Katılımcı</div>
                    <div class='summary-box'><div style='font-size:1.1rem;'>${avgScore}</div>Ortalama Puan</div>
                    <div class='summary-box'><div style='font-size:1.1rem;'>${satisfactionPercent}%</div>Genel Memnuniyet</div>
                </div>
                <div class='section info-box'>
                    <div class='section-title'>☑️ Genel Durum Değerlendirmesi</div>
                    ${statusBox}
                    <div>Memnuniyet Hesaplama Formülü: ((Alınan Puan - Minimum Puan) / (Maksimum Puan - Minimum Puan)) × 100 = ${satisfactionPercent}%</div>
                    <div style='margin-top:8px;'>Kurumunuzun ${dateInfo ? 'seçilen tarih için' : 'tüm paydaş gruplarında'} genel memnuniyet düzeyi yukarıda gösterilmiştir.</div>
                </div>
                <div class='section'>
                    <div class='section-title'>📈 Grafiksel Özet Tablosu</div>
                    <table class='table'>
                        <tr><th>Pozisyon</th>${Object.keys(positionData).map(pos=>`<th>${pos}</th>`).join('')}</tr>
                        <tr><td>Katılımcı</td>${Object.values(positionData).map(count=>`<td>${count}</td>`).join('')}</tr>
                        <tr><td>Ortalama Skor</td><td colspan='${Object.keys(positionData).length}'>${avgScore}</td></tr>
                    </table>
                    <table class='table'>
                        <tr><th>Skor Aralığı</th><th>Yanıt Sayısı</th></tr>
                        <tr><td>1.0-2.0</td><td>${surveys.filter(s=>parseFloat(s.averageScore)<=2.0).length}</td></tr>
                        <tr><td>2.1-3.0</td><td>${surveys.filter(s=>parseFloat(s.averageScore)>2.0&&parseFloat(s.averageScore)<=3.0).length}</td></tr>
                        <tr><td>3.1-4.0</td><td>${surveys.filter(s=>parseFloat(s.averageScore)>3.0&&parseFloat(s.averageScore)<=4.0).length}</td></tr>
                        <tr><td>4.1-5.0</td><td>${surveys.filter(s=>parseFloat(s.averageScore)>4.0).length}</td></tr>
                    </table>
                </div>
                <div class='section'>
                    <div class='section-title'>👥 Paydaş Grupları Analizi</div>
                    <table class='table'>
                        <tr><th>Paydaş Grubu</th><th>Katılımcı</th></tr>
                        ${Object.entries(positionData).map(([pos, count]) => `<tr><td>${pos}</td><td>${count}</td></tr>`).join('')}
                    </table>
                </div>
                <div class='section'>
                    <div class='section-title'>☑️ Yanıt Dağılımı</div>
                    <table class='table'>
                        <tr><th>Değerlendirme Seviyesi</th><th>Yanıt Sayısı</th></tr>
                        ${answerLevels.map((level, i) => `<tr><td>${level}</td><td>${answerCounts[i]}</td></tr>`).join('')}
                    </table>
                </div>
                <div class='section'>
                    <div class='section-title'>📊 Detaylı Kategori Analizleri</div>
                    ${hospitalCategories.map(cat => `
                        <div class='category-box'>
                            <b>${cat.title}</b><br>
                            <span style='font-size:0.95rem;'>${cat.desc}</span>
                            <div style='margin-top:8px;background:#fee2e2;padding:8px;border-radius:6px;'><b>Puan Aralığı: Düşük (%0-50)</b> - Bu kategoride ciddi iyileştirme gereklidir.</div>
                        </div>
                    `).join('')}
                </div>
                <div class='section advice-box'>
                    <b>💡 Öneriler ve Eylem Planı</b><br>
                    <b>Öncelikli Aksiyonlar:</b> Acil bir eylem planı oluşturulmalı. Hastanenin fiziki koşulları, tıbbi hizmet süreçleri ve iletişim kanalları gözden geçirilmelidir.<br>
                    <b>Takip:</b> Bu rapor sonuçlarını 3-6 ay sonra tekrar değerlendirmek için yeni anket düzenleyiniz.
                </div>
                <div style='text-align:right;font-size:0.9rem;color:#888;margin-top:32px;'>Akça Pro X - Profesyonel Kurum Değerlendirme Sistemi | ${dateStr} ${timeStr}<br>Bu rapor ${totalAnswers} adet soru yanıtı analiz edilerek oluşturulmuştur.${dateInfo ? `<br>Filtre: ${dateInfo}` : ''}</div>
            </body>
</html>
            `;
        }

        // Chart.js grafiklerini oluşturan fonksiyon
        let positionChartObj, satisfactionChartObj, timeChartObj, trendChartObj;
        function generateCharts(surveys) {
            // Pozisyon grafiği
            const positionData = {};
            surveys.forEach(s => {
                positionData[s.jobType] = (positionData[s.jobType] || 0) + 1;
            });
            if (positionChartObj) positionChartObj.destroy();
            const positionCtx = document.getElementById('positionChart').getContext('2d');
            positionChartObj = new Chart(positionCtx, {
                type: 'doughnut',
                data: {
                    labels: Object.keys(positionData),
                    datasets: [{
                        data: Object.values(positionData),
                        backgroundColor: ['#3b82f6', '#10b981', '#f59e0b']
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
            // Değerlendirme grafiği
            const satisfactionCounts = [0, 0, 0];
            surveys.forEach(s => {
                const avgScore = parseFloat(s.averageScore);
                if (avgScore < 2.5) satisfactionCounts[0]++;
                else if (avgScore < 3.5) satisfactionCounts[1]++;
                else satisfactionCounts[2]++;
            });
            if (satisfactionChartObj) satisfactionChartObj.destroy();
            const satisfactionCtx = document.getElementById('satisfactionChart').getContext('2d');
            satisfactionChartObj = new Chart(satisfactionCtx, {
                type: 'bar',
                data: {
                    labels: ['Düşük', 'Orta', 'Yüksek'],
                    datasets: [{
                        data: satisfactionCounts,
                        backgroundColor: ['#ef4444', '#f59e0b', '#10b981']
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true } } }
            });
            // Süre dağılımı grafiği
            const timeCounts = { '0-5dk': 0, '5-10dk': 0, '10dk+': 0 };
            surveys.forEach(s => {
                const duration = s.duration || '00:00';
                const minutes = parseInt(duration.split(':')[0]) || 0;
                if (minutes <= 5) timeCounts['0-5dk']++;
                else if (minutes <= 10) timeCounts['5-10dk']++;
                else timeCounts['10dk+']++;
            });
            if (timeChartObj) timeChartObj.destroy();
            const timeCtx = document.getElementById('timeChart').getContext('2d');
            timeChartObj = new Chart(timeCtx, {
                type: 'pie',
                data: {
                    labels: Object.keys(timeCounts),
                    datasets: [{
                        data: Object.values(timeCounts),
                        backgroundColor: ['#8b5cf6', '#06b6d4', '#f97316']
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } } }
            });
            // Puan dağılımı grafiği
            const scoreRanges = { '1-2': 0, '2-3': 0, '3-4': 0, '4-5': 0 };
            surveys.forEach(s => {
                const avgScore = parseFloat(s.averageScore);
                if (avgScore < 2) scoreRanges['1-2']++;
                else if (avgScore < 3) scoreRanges['2-3']++;
                else if (avgScore < 4) scoreRanges['3-4']++;
                else scoreRanges['4-5']++;
            });
            if (trendChartObj) trendChartObj.destroy();
            const trendCtx = document.getElementById('trendChart').getContext('2d');
            trendChartObj = new Chart(trendCtx, {
                type: 'line',
                data: {
                    labels: Object.keys(scoreRanges),
                    datasets: [{
                        data: Object.values(scoreRanges),
                        borderColor: '#6366f1',
                        backgroundColor: 'rgba(99, 102, 241, 0.1)',
                        fill: true
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true } } }
            });
        }

        // Katılımcı detay tablosunu dolduran fonksiyon
        function generateSimpleReport(surveys) {
            loadParticipantTable(surveys);
            
            // AI butonunu detailedReport alanına ekle
            if (surveys && surveys.length > 0) {
                const aiButtonHTML = `
                    <div class="mt-6 bg-white rounded-lg border p-4">
                        <div class="text-center">
                            <h4 class="text-lg font-semibold text-gray-800 mb-4">🤖 Yapay Zeka ile Sağlık Hizmetleri Analizi</h4>
                            <button id="aiInterpretBtn" class="bg-gradient-to-r from-red-600 to-blue-600 text-white px-6 py-3 rounded-lg font-bold text-sm hover:from-red-700 hover:to-blue-700 transition-all duration-300 transform hover:scale-105 shadow-lg">
                                🏥 Hastane Değerlendirmesini AI ile Analiz Et
                            </button>
                        </div>
                    </div>
                `;
                document.getElementById('detailedReport').innerHTML = aiButtonHTML;
                
                // AI buton eventini ekle
                setTimeout(() => {
                    const btn = document.getElementById('aiInterpretBtn');
                    if (btn) btn.onclick = async function() {
                        const apiKey = 'AIzaSyDD9lwyo2viTAbHRy2nFpKZ0fUMGggS_ao';
                        btn.disabled = true;
                        btn.textContent = '🔄 AI sağlık analizi yapıyor...';
                        try {
                            // Hastane anket verilerini hazırla
                            const totalParticipants = surveys.length;
                            const avgScore = surveys.reduce((sum, s) => sum + parseFloat(s.averageScore), 0) / surveys.length;
                            const highSatisfaction = surveys.filter(s => parseFloat(s.averageScore) >= 4).length;
                            const lowSatisfaction = surveys.filter(s => parseFloat(s.averageScore) < 3).length;
                            
                            const summary = `Hastane Değerlendirme Raporu:
                            - Toplam Katılımcı: ${totalParticipants}
                            - Ortalama Memnuniyet Puanı: ${avgScore.toFixed(2)}/5
                            - Yüksek Memnuniyet (4+ puan): ${highSatisfaction} kişi (${Math.round((highSatisfaction/totalParticipants)*100)}%)
                            - Düşük Memnuniyet (3- puan): ${lowSatisfaction} kişi (${Math.round((lowSatisfaction/totalParticipants)*100)}%)`;
                            
                            const prompt = `Bir sağlık hizmetleri uzmanı ve hastane yöneticisi gibi aşağıdaki hastane değerlendirme anket raporunu analiz et.\n\nRapor Özeti:\n${summary}\n\nAşağıdaki başlıklarla detaylı, profesyonel ve sağlık hizmetleri odaklı bir analiz yaz:\n\n1. Mevcut Sağlık Hizmetleri Durumu\n2. Hastane Hizmet Kalitesinde Nelerin İyileştirilmesi Gerekiyor\n3. Bu Durumun Devam Etmesi Halinde Hasta Memnuniyeti ve Hastane İtibarına Etkileri\n\nHer başlık için en az 3-4 cümlelik, sağlık hizmetleri kalitesine uygun, özgün ve uygulanabilir öneriler içeren bir metin oluştur. Hasta güvenliği ve hizmet kalitesi odaklı yaklaşım benimse.\n`;
                            const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent`, {
                                method: 'POST',
                                headers: { 
                                    'Content-Type': 'application/json',
                                    'x-goog-api-key': apiKey
                                },
                                body: JSON.stringify({
                                    contents: [{ parts: [{ text: prompt }] }]
                                })
                            });
                            if (!response.ok) throw new Error('API Hatası: ' + response.status);
                            const result = await response.json();
                            let text = (result.candidates && result.candidates[0] && result.candidates[0].content && result.candidates[0].content.parts[0].text) || 'AI yanıtı alınamadı.';
                            document.getElementById('aiInterpretationContent').innerHTML = `<pre class="whitespace-pre-wrap bg-gray-50 p-4 rounded text-sm border">${text}</pre>`;
                            document.getElementById('aiInterpretationModal').classList.add('show');
                        } catch (e) {
                            alert('AI yorumlama hatası: ' + e.message);
                        } finally {
                            btn.disabled = false;
                            btn.textContent = '🏥 Hastane Değerlendirmesini AI ile Analiz Et';
                        }
                    }
                }, 500);
            } else {
                document.getElementById('detailedReport').innerHTML = '<p class="text-gray-500 text-center py-8">Henüz değerlendirme verisi bulunmuyor.</p>';
            }
        }

        function getParticipantCount(surveys) {
            return surveys ? surveys.length : 0;
        }

        function loadParticipantTable(surveys) {
            const tbody = document.getElementById('participantTableBody');
            if (!tbody) return;
            
            if (!surveys || surveys.length === 0) {
                tbody.innerHTML = '<tr><td colspan="5" class="text-center py-4 text-gray-500">Henüz katılımcı bulunmuyor.</td></tr>';
                return;
            }
            
            // Puana göre yüksekten düşüğe sırala
            const sortedSurveys = [...surveys].sort((a, b) => 
                parseFloat(b.averageScore) - parseFloat(a.averageScore)
            );
            
            tbody.innerHTML = sortedSurveys.map(survey => {
                const displayName = (survey.firstName && survey.lastName) ? 
                    `${survey.firstName} ${survey.lastName}` : 
                    (survey.firstName || survey.lastName || 'İsimsiz');
                
                const avgScore = parseFloat(survey.averageScore);
                let evaluation = '';
                let evaluationColor = '';
                let evaluationIcon = '';
                
                if (avgScore >= 4.5) {
                    evaluation = 'Çok Memnun';
                    evaluationColor = 'text-green-600';
                    evaluationIcon = '5';
                } else if (avgScore >= 3.5) {
                    evaluation = 'Memnun';
                    evaluationColor = 'text-green-500';
                    evaluationIcon = '4';
                } else if (avgScore >= 2.5) {
                    evaluation = 'Orta';
                    evaluationColor = 'text-yellow-600';
                    evaluationIcon = '3';
                } else if (avgScore >= 1.5) {
                    evaluation = 'Düşük';
                    evaluationColor = 'text-orange-600';
                    evaluationIcon = '2';
                } else {
                    evaluation = 'Çok Düşük';
                    evaluationColor = 'text-red-600';
                    evaluationIcon = '1';
                }
                
                return `
                    <tr class="hover:bg-gray-50">
                        <td class="px-3 py-2 font-medium">${displayName}</td>
                        <td class="px-3 py-2">
                            <span class="inline-block px-2 py-1 text-xs rounded-full bg-blue-100 text-blue-800">
                                ${survey.jobType}
                            </span>
                        </td>
                        <td class="px-3 py-2 text-center font-semibold">${avgScore.toFixed(1)}</td>
                        <td class="px-3 py-2 text-center ${evaluationColor} font-semibold">
                            <span class="inline-flex items-center gap-1">
                                <span class="inline-block w-6 h-6 rounded-full bg-gray-100 text-gray-700 text-sm font-bold flex items-center justify-center">${evaluationIcon}</span>
                                ${evaluation}
                            </span>
                        </td>
                        <td class="px-3 py-2 text-center text-sm text-gray-600">${new Date(survey.submittedAt).toLocaleDateString('tr-TR')}</td>
                    </tr>
                `;
            }).join('');
        }

        // Katılımcı detaylarını aç/kapat
        function toggleParticipantDetails() {
            const details = document.getElementById('participantDetails');
            if (!details) return;
            
            const btn = document.getElementById('toggleParticipantsBtn');
            
            if (details.classList.contains('hidden')) {
                details.classList.remove('hidden');
                // Mevcut verileri kullan (filtrelenmiş veya tüm veriler)
                let surveys = (typeof filteredSurveys !== 'undefined' && filteredSurveys !== null) ? 
                    filteredSurveys : 
                    (systemData.surveyData ? systemData.surveyData.responses.filter(s => 
                        s.companyName && s.companyName.toLowerCase() === loggedInCompany.name.toLowerCase()
                    ) : []);
                
                loadParticipantTable(surveys);
                // Buton metnini katılımcı sayısıyla güncelle
                const participantCount = getParticipantCount(surveys);
                btn.textContent = `📋 Katılımcıları Gizle (${participantCount})`;
            } else {
                details.classList.add('hidden');
                btn.textContent = '📋 Katılımcıları Görüntüle';
            }
        }
    // Detaylı frekans tablosu fonksiyonu (Türkçe etiketlerle)
    function renderDetailedFrequencyTables(surveys) {
        const container = document.getElementById('detailedFrequencyTables');
        if (!container) return;
        if (!surveys || surveys.length === 0) {
            container.innerHTML = `<div class="text-gray-400 text-center py-4">Frekans verisi yok.</div>`;
            return;
        }
        // Soru başlıklarını al
        const allQuestions = [];
        surveys.forEach(s => {
            s.answers.forEach((a, idx) => {
                if (!allQuestions[idx]) allQuestions[idx] = a.question;
            });
        });
        // Türkçe puan etiketleri
        const scoreLabels = {
            5: 'Çok Memnunum',
            4: 'Memnunuyum',
            3: 'Kararsızım',
            2: 'Memnun Değilim',
            1: 'Hiç Memnun Değilim'
        };
        let html = '';
        allQuestions.forEach((question, qIdx) => {
            // Her soru için frekansları hesapla
            const freq = { 1: 0, 2: 0, 3: 0, 4: 0, 5: 0 };
            let total = 0;
            surveys.forEach(s => {
                const a = s.answers[qIdx];
                if (a && a.score) {
                    freq[a.score] = (freq[a.score] || 0) + 1;
                    total++;
                }
            });
            // Sonuç cümlesi
            const memnun = freq[4] + freq[5];
            const memnunYuzde = total > 0 ? Math.round((memnun/total)*100) : 0;
            let resultText = '';
            if (memnunYuzde >= 80) resultText = `Katılımcıların %${memnunYuzde}'i bu konuda memnun veya çok memnun.`;
            else if (memnunYuzde >= 60) resultText = `Katılımcıların çoğu bu konuda memnun.`;
            else if (memnunYuzde >= 40) resultText = `Memnuniyet oranı orta seviyede.`;
            else resultText = `Katılımcıların çoğu bu konuda memnun değil.`;
            html += `<div class="mb-8">
                <div class="font-semibold text-gray-800 mb-2">${qIdx+1}. ${question}</div>
                <div class="mb-2 text-sm text-blue-700 font-medium">Sonuç: ${resultText}</div>
                <div class="overflow-x-auto">
                <table class="min-w-full text-sm text-center border border-gray-300 rounded-lg">
                    <thead>
                        <tr>
                            <th class="bg-gray-100 border p-2">Seçenek</th>
                            <th class="bg-gray-100 border p-2">Frekans</th>
                            <th class="bg-gray-100 border p-2">Yüzde</th>
                        </tr>
                    </thead>
                    <tbody>
                        ${[5,4,3,2,1].map(score => `
                            <tr>
                                <td class="border p-2">${scoreLabels[score]}</td>
                                <td class="border p-2">${freq[score]}</td>
                                <td class="border p-2">${total > 0 ? ((freq[score]/total)*100).toFixed(1) : '0.0'}%</td>
                            </tr>
                        `).join('')}
                    </tbody>
                </table>
                </div>
            </div>`;
        });
        container.innerHTML = html;
    }
    // Excel tarzı özet frekans tablosu fonksiyonu (10 soru x 5 puan, grup başlığında yüzde ortalama)
    function renderSummaryFrequencyTable(surveys) {
        const container = document.getElementById('detailedFrequencyTables');
        if (!container) return;
        if (!surveys || surveys.length === 0) {
            container.innerHTML = `<div class="text-gray-400 text-center py-4">Frekans verisi yok.</div>`;
            return;
        }
        // Gruplar ve başlıklar
        const groupTitles = {
            'Hasta': [
                'Hizmet Kalitesi',
                'İletişim',
                'Tedavi Süreci',
                'Fiziksel Koşullar',
                'Personel Davranışları',
                'Randevu ve Kabul',
                'Dijital Hizmetler',
                'Bekleme Süreleri',
                'Güven ve İtibar',
                'Genel Memnuniyet'
            ],
            'Doktor': [
                'Çalışma Ortamı',
                'Yönetim ve Liderlik',
                'İş Yükü ve Dengesi',
                'İç İletişim',
                'Kariyer Gelişimi',
                'Ücret ve Yan Haklar',
                'Takdir ve Geri Bildirim',
                'İş Süreçleri',
                'Kurum Kültürü',
                'Genel İş Memnuniyeti'
            ],
            'Yönetim': [
                'Finansal Performans ve Operasyonel Verimlilik',
                'Pazarlama ve Marka Yönetimi',
                'İnsan Kaynakları Yönetimi',
                'Hasta İlişkileri ve Kalite Kontrol',
                'Teknolojik Altyapı ve Gelecek Vizyonu'
            ]
        };
        const groups = Object.keys(groupTitles);
        // Puan etiketleri
        const scoreLabels = {
            5: 'Çok Memnunum',
            4: 'Memnun',
            3: 'Kararsızım',
            2: 'Memnun Değilim',
            1: 'Hiç Memnun Değilim'
        };
        let html = '<div class="mb-6"><div class="font-semibold text-gray-800 mb-2">Özet Frekans Tablosu</div>';
        html += '<div class="overflow-x-auto"><table class="survey-table">';
        html += '<thead><tr><th style="text-align: left; width: 250px;">Grup / Kategori</th>';
        [5,4,3,2,1].forEach(score => {
            html += `<th style="width: 120px;">${scoreLabels[score]}</th>`;
        });
        html += '</tr></thead><tbody>';
        groups.forEach(group => {
            // Grup başlık satırı: ortalama yüzdeler
            let groupTotal = 0;
            const groupScoreTotals = { 1: 0, 2: 0, 3: 0, 4: 0, 5: 0 };
            let groupAnswerCount = 0;
            // Soru bazında frekanslar
            const questionFreqs = [];
            for (let catIdx = 0; catIdx < groupTitles[group].length; catIdx++) {
                const freq = { 1: 0, 2: 0, 3: 0, 4: 0, 5: 0 };
                
                // Her kategori için soru aralığını hesapla
                let questionsPerCategory = (group === 'Yönetim') ? 10 : 5;
                let startQuestionIdx = catIdx * questionsPerCategory;
                
                // Bu kategoriye ait tüm soruları topla
                for (let qIdx = startQuestionIdx; qIdx < startQuestionIdx + questionsPerCategory; qIdx++) {
                    surveys.forEach(s => {
                        if (s.jobType === group && s.answers[qIdx]) {
                            const score = s.answers[qIdx].score;
                            if (score) {
                                freq[score] = (freq[score] || 0) + 1;
                                groupScoreTotals[score]++;
                                groupTotal++;
                                groupAnswerCount++;
                            }
                        }
                    });
                }
                questionFreqs.push(freq);
            }
            // Grup başlık satırı (yüzdeler)
            html += `<tr class="main-group-row">
                <td style="text-align: left; font-weight: 700;">${group}</td>`;
            [5,4,3,2,1].forEach(score => {
                const percent = groupAnswerCount > 0 ? ((groupScoreTotals[score]/groupAnswerCount)*100).toFixed(1) : '0.0';
                html += `<td style="font-weight: 600;">${percent}%</td>`;
            });
            html += '</tr>';
            // Kategori satırları
            groupTitles[group].forEach((title, qIdx) => {
                html += `<tr>
                    <td class="sub-category">${title}</td>`;
                [5,4,3,2,1].forEach(score => {
                    // Her puan için gerçek frekansı (kaç kişi o puanı verdi) göster
                    html += `<td style="text-align: center;">${questionFreqs[qIdx][score] || 0}</td>`;
                });
                html += '</tr>';
            });
        });
        html += '</tbody></table></div></div>';
        
        // Grafik alanı ekle
        const totalResponses = surveys.length;
        const chartTitle = totalResponses > 0 ? 
            `📊 Grup Bazlı Memnuniyet Dağılımı (${totalResponses} Katılımcı)` : 
            '📊 Grup Bazlı Memnuniyet Dağılımı (Veri Yok)';
            
        const chartSection = `
            <div class="mt-8 bg-white border rounded-lg p-6" style="box-shadow: 0 4px 8px rgba(0,0,0,0.1);">
                <h3 class="text-lg font-semibold mb-4 text-gray-800">${chartTitle}</h3>
                <div style="width: 100%; height: 400px;">
                    <canvas id="hospitalChart"></canvas>
                </div>
                ${totalResponses === 0 ? 
                    '<p class="text-gray-500 text-center mt-4">Henüz anket verisi bulunmuyor. Grafik veriler geldiğinde otomatik olarak güncellenecektir.</p>' : 
                    '<p class="text-gray-600 text-sm text-center mt-4">Grafik tüm gruplardan gelen cevapları birleştirerek genel memnuniyet dağılımını gösterir.</p>'
                }
            </div>
        `;
        
        container.innerHTML = html + chartSection;
        
        // Grafik oluştur
        generateHospitalChart(surveys);
    }

    let hospitalChartInstance = null;

    function generateHospitalChart(surveys) {
        try {
            console.log('generateHospitalChart çalışıyor, survey sayısı:', surveys ? surveys.length : 0);
            
            // Önce survey verilerinin yapısını inceleyelim
            if (surveys && surveys.length > 0) {
                console.log('İlk hastane survey örneği:', surveys[0]);
            }
            
            // Mevcut grafiği temizle
            if (hospitalChartInstance) {
                hospitalChartInstance.destroy();
                hospitalChartInstance = null;
            }

            const canvas = document.getElementById('hospitalChart');
            if (!canvas) {
                console.log('hospitalChart canvas bulunamadı');
                return;
            }

            // Memnuniyet verilerini hazırla - Basit yaklaşım
            const satisfactionData = [0, 0, 0, 0, 0]; // [Çok Memnun, Memnun, Kararsız, Memnun Değil, Hiç Memnun Değil]

            // Survey verilerinden memnuniyet hesapla
            if (surveys && surveys.length > 0) {
                console.log('Hastane grafiği için işlenen survey sayısı:', surveys.length);
                
                // Her survey için ortalama puan üzerinden memnuniyet hesapla
                surveys.forEach((survey, surveyIndex) => {
                    const avgScore = parseFloat(survey.averageScore) || 0;
                    
                    if (avgScore > 0) {
                        // Ortalama puana göre memnuniyet seviyesi belirle
                        let satisfactionIndex;
                        if (avgScore >= 4.5) satisfactionIndex = 0; // Çok Memnun
                        else if (avgScore >= 3.5) satisfactionIndex = 1; // Memnun
                        else if (avgScore >= 2.5) satisfactionIndex = 2; // Kararsız
                        else if (avgScore >= 1.5) satisfactionIndex = 3; // Memnun Değil
                        else satisfactionIndex = 4; // Hiç Memnun Değil
                        
                        satisfactionData[satisfactionIndex]++;
                        
                        console.log(`Hastane Survey ${surveyIndex}: avgScore=${avgScore}, satisfactionIndex=${satisfactionIndex}`);
                    }
                });
            }

            console.log('Hastane memnuniyet dağılımı:', satisfactionData);

            // Grafik verilerini hazırla
            const chartData = {
                labels: ['Çok Memnun', 'Memnun', 'Kararsız', 'Memnun Değil', 'Hiç Memnun Değil'],
                datasets: [{
                    label: 'Hastane Memnuniyet Dağılımı',
                    data: satisfactionData,
                    backgroundColor: [
                        '#22C55E', // Çok Memnun - Yeşil
                        '#84CC16', // Memnun - Açık Yeşil  
                        '#EAB308', // Kararsız - Sarı
                        '#F97316', // Memnun Değil - Turuncu
                        '#EF4444'  // Hiç Memnun Değil - Kırmızı
                    ],
                    borderColor: [
                        '#16A34A',
                        '#65A30D', 
                        '#CA8A04',
                        '#EA580C',
                        '#DC2626'
                    ],
                    borderWidth: 2
                }]
            };

            // Grafik ayarları (eğitim anketindeki gibi)
            const config = {
                type: 'bar',
                data: chartData,
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: {
                            beginAtZero: true,
                            title: {
                                display: true,
                                text: 'Katılımcı Sayısı'
                            }
                        }
                    },
                    plugins: {
                        legend: {
                            display: false
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    const total = satisfactionData.reduce((a, b) => a + b, 0);
                                    const percentage = total > 0 ? ((context.raw / total) * 100).toFixed(1) : 0;
                                    return context.dataset.label + ': ' + context.raw + ' (' + percentage + '%)';
                                }
                            }
                        }
                    }
                }
            };

            // Grafiği oluştur
            const ctx = canvas.getContext('2d');
            hospitalChartInstance = new Chart(ctx, config);
            
        } catch (error) {
            console.error('Hastane grafiği oluşturma hatası:', error);
        }
    }
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'981af265f22bd620',t:'MTc1ODMwNDQ1MS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
