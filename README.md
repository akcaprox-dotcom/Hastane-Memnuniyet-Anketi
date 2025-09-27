<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Akça Pro X - Hastane Değerlendirme Anketi</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Firebase App (the core Firebase SDK) - v9 compat for advanced auth -->
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <!-- Firebase Auth -->
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <!-- Firebase Database -->
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
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
        
        /* Pulse animasyonu (Google buton uyarısı için) */
        @keyframes pulse {
            0% {
                transform: scale(1);
                opacity: 1;
            }
            50% {
                transform: scale(1.05);
                opacity: 0.8;
            }
            100% {
                transform: scale(1);
                opacity: 1;
            }
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
                    <p class="text-xs opacity-90">Hastane Değerlendirme Anketi</p>
                </div>
            </div>
            <div class="flex gap-2">
                <button onclick="showModule('survey')" class="px-3 py-1 bg-white/20 rounded text-sm hover:bg-white/30 transition-colors">📊 Anket</button>
                <button onclick="showModule('company')" class="px-3 py-1 bg-white/20 rounded text-sm hover:bg-white/30 transition-colors">🏥 Hastane Portalı</button>
            </div>
        </div>
    </nav>

    <!-- Anket Modülü -->
    <div id="surveyModule" class="max-w-5xl mx-auto p-2 md:p-4">
        <div class="bg-white shadow-xl rounded-2xl max-w-2xl mx-auto p-4 md:p-8">
            <div class="text-center mb-6">
                <h2 class="text-2xl md:text-3xl font-extrabold text-gray-800 mb-1 tracking-tight">Hastane Değerlendirme Anketi</h2>
                <p class="text-gray-600 mb-2 text-base md:text-lg">Görüşleriniz bizim için değerli</p>
                <span class="bg-green-100 text-green-800 text-xs font-semibold px-2 py-1 rounded-full">v4.0.0 - Gelişmiş Firebase Entegre</span>
            </div>

            <!-- Sorumluluk Reddi -->
            <div id="disclaimerSection" class="mb-4">
                <div class="bg-yellow-50 border border-yellow-300 rounded p-3 mb-3">
                    <h3 class="font-semibold text-yellow-800 mb-2 text-sm">⚠️ Veri Koruma Beyanı</h3>
                    <div class="text-xs text-yellow-700 space-y-1">
                        <p>• Verileriniz Firebase güvenli bulut altyapısında saklanır ve üçüncü taraflarla paylaşılmaz.</p>
                        <p>• Anket sonuçları sadece hastane yetkilileri tarafından görüntülenebilir.</p>
                        <p>• Sistem güvenliği Google Firebase altyapısı sorumluluğundadır.</p>
                        <p>• Hack, veri ihlali vb. güvenlik olaylarından kaynaklanan bilgi erişimlerinin sorumluluğu Akça Pro X'e ait değildir.</p>
                    </div>
                </div>
                <label class="flex items-center space-x-2 cursor-pointer">
                    <input type="checkbox" id="acceptDisclaimer" class="w-4 h-4 text-purple-600">
                    <span class="text-xs font-medium">Veri koruma beyanını kabul ediyorum</span>
                </label>
            </div>

            <!-- Hastane Bilgileri -->
            <div id="companyInfoSection">
                <h3 class="text-base font-semibold text-gray-700 mb-3">Hastane ve Kişisel Bilgiler</h3>
                
                <!-- Google ile Giriş Yap butonu -->
                <div class="mb-3 flex flex-col items-center">
                    <button id="googleSignInBtn" type="button" class="flex items-center gap-2 px-4 py-2 bg-white border border-gray-300 rounded shadow hover:bg-gray-100 text-gray-700 font-semibold mb-2">
                        <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" alt="Google" class="w-5 h-5"> Google ile Giriş Yap
                    </button>
                    <div id="googleUserInfo" class="text-xs text-green-700 font-medium hidden"></div>
                </div>
                
                <!-- Hastane Seçim Tipi -->
                <div class="mb-3">
                    <div class="flex gap-4 mb-2">
                        <label class="flex items-center cursor-pointer">
                            <input type="radio" name="companyType" value="new" id="newCompanyRadio" class="mr-2 text-purple-600 focus:ring-purple-500" checked>
                            <span class="text-sm font-medium text-gray-700">🆕 Yeni Hastane</span>
                        </label>
                        <label class="flex items-center cursor-pointer">
                            <input type="radio" name="companyType" value="existing" id="existingCompanyRadio" class="mr-2 text-purple-600 focus:ring-purple-500">
                            <span class="text-sm font-medium text-gray-700">📋 Kayıtlı Hastane</span>
                        </label>
                    </div>
                </div>
                
                <!-- Manuel Hastane Adı Girişi -->
                <div class="mb-3" id="manualCompanyInput">
                    <input type="text" id="companyName" placeholder="Hastane adınızı girin (Devlet, Özel, Üniversite Hastanesi vb.)" 
                        class="w-full border-2 border-purple-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
                </div>
                
                <!-- Kayıtlı Hastaneler Dropdown -->
                <div class="mb-3 hidden" id="existingCompanySelect">
                    <div class="flex gap-2">
                        <select id="companyDropdown" class="flex-1 border-2 border-purple-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500 bg-white">
                            <option value="">Hastanenizi seçin...</option>
                        </select>
                        <button type="button" id="refreshCompaniesBtn" class="px-3 py-2 bg-purple-100 hover:bg-purple-200 border-2 border-purple-300 rounded text-sm font-medium text-purple-700 transition-colors" title="Hastane listesini yenile">
                            🔄
                        </button>
                    </div>
                </div>
                
                <div class="mb-3">
                    <p class="text-xs text-gray-600 mb-2">Rolünüzü seçin:</p>
                    <div class="grid grid-cols-1 sm:grid-cols-3 gap-2">
                        <button type="button" id="patientBtn" 
                            class="job-btn py-3 px-2 text-xs rounded border-2 border-blue-300 hover:border-blue-500 hover:bg-blue-50 transition-all duration-200 cursor-pointer font-medium bg-white text-center focus:outline-none focus:ring-2 focus:ring-blue-400">
                            <div class="text-lg mb-1">🧑‍⚕️</div>
                            <div>Hasta</div>
                        </button>
                        <button type="button" id="doctorBtn" 
                            class="job-btn py-3 px-2 text-xs rounded border-2 border-green-300 hover:border-green-500 hover:bg-green-50 transition-all duration-200 cursor-pointer font-medium bg-white text-center focus:outline-none focus:ring-2 focus:ring-green-400">
                            <div class="text-lg mb-1">👨‍⚕️</div>
                            <div>Doktor/Hemşire</div>
                        </button>
                        <button type="button" id="managementBtn" 
                            class="job-btn py-3 px-2 text-xs rounded border-2 border-purple-300 hover:border-purple-500 hover:bg-purple-50 transition-all duration-200 cursor-pointer font-medium bg-white text-center focus:outline-none focus:ring-2 focus:ring-purple-400">
                            <div class="text-lg mb-1">👩‍🔬</div>
                            <div>Yönetim</div>
                        </button>
                    </div>
                </div>
                <div id="selectedJobDisplay" class="text-center text-sm text-gray-600 mb-3 min-h-[20px]"></div>
                <div class="grid grid-cols-2 gap-2 mb-4">
                    <input type="text" id="firstName" placeholder="Adınız" 
                        class="border-2 border-gray-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500 bg-gray-50" readonly>
                    <input type="text" id="lastName" placeholder="Soyadınız" 
                        class="border-2 border-gray-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500 bg-gray-50" readonly>
                </div>
                <div class="text-xs text-gray-500 mb-4" id="nameFieldsInfo">
                    ℹ️ Ad ve soyad alanları Google ile giriş yaptıktan sonra düzenlenebilir olacak
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

    <!-- Hastane Portalı -->
    <div id="companyModule" class="max-w-5xl mx-auto p-2 md:p-4 hidden">
        <div class="bg-white shadow-xl rounded-2xl max-w-5xl mx-auto p-4 md:p-8">
            <div id="companyLogin" class="max-w-md mx-auto">
                <h2 class="text-3xl font-bold text-center mb-8">🏥 Hastane Portalı Girişi</h2>
                <div class="space-y-6">
                    <input type="text" id="companyLoginName" placeholder="Hastane Adı" 
                           class="w-full border-2 border-gray-300 rounded-lg px-4 py-4 text-base focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    <input type="password" id="companyPassword" placeholder="12 Karakterlik Şifre" 
                           class="w-full border-2 border-gray-300 rounded-lg px-4 py-4 text-base focus:ring-2 focus:ring-blue-500 focus:border-blue-500" autocomplete="off">
                    <button onclick="loginCompany()" class="w-full py-4 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors text-lg font-semibold">
                        🔐 Giriş Yap
                    </button>
                </div>
                <div class="mt-6 p-4 bg-blue-50 rounded-lg text-sm text-blue-700">
                    <p><strong>Not:</strong> Hastane şifrenizi yöneticinizden alabilirsiniz.</p>
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

    <!-- Modal Yapıları -->
    <div id="modalOverlay" class="modal">
        <div class="bg-white p-6 rounded-lg shadow-lg max-w-md mx-auto" style="margin-top: 10vh;">
            <h3 id="modalTitle" class="text-lg font-bold mb-4"></h3>
            <div id="modalContent" class="mb-4"></div>
            <button onclick="closeModal()" class="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700">Tamam</button>
        </div>
    </div>

    <script>
        // Firebase config
        const firebaseConfig = {
            apiKey: "AIzaSyDp2Yh8hamXi6OTfw03MT0S4rp5CjnlAcg",
            authDomain: "akcaprox-anket.firebaseapp.com",
            projectId: "akcaprox-anket",
            storageBucket: "akcaprox-anket.appspot.com",
            messagingSenderId: "426135179922",
            appId: "1:426135179922:web:c16b3fd6fa5f3d9224cc4b",
            measurementId: "G-CD1ET7RGX1",
            databaseURL: "https://isletme-76bad-default-rtdb.europe-west1.firebasedatabase.app/"
        };
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();

        // Global değişkenler
        let currentModule = 'survey';
        let surveyStartTime = null;
        let timerInterval = null;
        let currentQuestions = [];
        let currentQuestionIndex = 0;
        let answers = [];
        let selectedJobType = '';
        let loggedInCompany = null;
        let isAdminLoggedIn = false;
        let systemData = { surveyData: null };

        // Google Sign-In logic
        let googleUser = null;
        document.addEventListener('DOMContentLoaded', function() {
            // Google Sign-In setup
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
                                // Prefill name fields
                                document.getElementById('firstName').value = user.displayName ? user.displayName.split(' ')[0] : '';
                                document.getElementById('lastName').value = user.displayName ? user.displayName.split(' ').slice(1).join(' ') : '';
                                userInfoDiv.textContent = `Giriş yapıldı: ${user.displayName} (${user.email})`;
                                userInfoDiv.classList.remove('hidden');
                                
                                // Make name fields editable and update styling
                                const firstNameField = document.getElementById('firstName');
                                const lastNameField = document.getElementById('lastName');
                                const nameFieldsInfo = document.getElementById('nameFieldsInfo');
                                
                                firstNameField.readOnly = false;
                                lastNameField.readOnly = false;
                                firstNameField.classList.remove('bg-gray-50');
                                lastNameField.classList.remove('bg-gray-50');
                                firstNameField.classList.add('bg-white');
                                lastNameField.classList.add('bg-white');
                                
                                // Update info message
                                nameFieldsInfo.innerHTML = '✅ Ad ve soyad alanları artık düzenlenebilir - Lütfen doğru bilgilerinizi girin';
                                nameFieldsInfo.classList.remove('text-gray-500');
                                nameFieldsInfo.classList.add('text-green-600', 'font-medium');
                            }
                        })
                        .catch((error) => {
                            alert('Google ile giriş başarısız: ' + error.message);
                        });
                });
            }

            // Hastane türü değişim dinleyicileri
            document.getElementById('newCompanyRadio').addEventListener('change', toggleCompanyInputType);
            document.getElementById('existingCompanyRadio').addEventListener('change', toggleCompanyInputType);
            
            // Refresh butonu
            document.getElementById('refreshCompaniesBtn').addEventListener('click', loadExistingCompanies);
            
            // Job type butonları
            document.getElementById('patientBtn').addEventListener('click', () => selectJobType('Hasta'));
            document.getElementById('doctorBtn').addEventListener('click', () => selectJobType('Doktor'));
            document.getElementById('managementBtn').addEventListener('click', () => selectJobType('Yönetim'));
            
            // Start survey button
            document.getElementById('startSurvey').addEventListener('click', startSurvey);
        });

        function selectJobType(jobType) {
            selectedJobType = jobType;
            
            // Tüm butonları sıfırla
            document.querySelectorAll('#patientBtn, #doctorBtn, #managementBtn').forEach(btn => {
                btn.classList.remove('active-tab');
            });
            
            // Seçili butonu işaretle
            const buttonMap = {
                'Hasta': 'patientBtn',
                'Doktor': 'doctorBtn',
                'Yönetim': 'managementBtn'
            };
            
            document.getElementById(buttonMap[jobType]).classList.add('active-tab');
            
            // Display seçim
            document.getElementById('selectedJobDisplay').textContent = `Seçilen rol: ${jobType}`;
        }

        // Hastane giriş türü değiştirme
        function toggleCompanyInputType() {
            const isNewCompany = document.getElementById('newCompanyRadio').checked;
            const manualInput = document.getElementById('manualCompanyInput');
            const dropdownSelect = document.getElementById('existingCompanySelect');
            
            if (isNewCompany) {
                // Yeni hastane: Manuel input göster, dropdown gizle
                manualInput.classList.remove('hidden');
                dropdownSelect.classList.add('hidden');
                document.getElementById('companyName').value = '';
            } else {
                // Mevcut hastane: Dropdown göster, manuel input gizle
                manualInput.classList.add('hidden');
                dropdownSelect.classList.remove('hidden');
                loadExistingCompanies();
            }
        }

        // Mevcut hastaneleri dropdown'a yükle
        async function loadExistingCompanies() {
            try {
                console.log('Mevcut hastaneler yükleniyor...');
                // Her zaman fresh data yükle
                console.log('Firebase\'den güncel veri yükleniyor...');
                await loadSystemData();
                
                console.log('Sistem verileri:', systemData.surveyData);
                const dropdown = document.getElementById('companyDropdown');
                dropdown.innerHTML = '<option value="">Hastanenizi seçin...</option>';
                
                if (systemData.surveyData && systemData.surveyData.companies) {
                    console.log('Bulunan hastaneler:', systemData.surveyData.companies);
                    // Hastaneleri alfabetik sıraya koy
                    const companies = Object.values(systemData.surveyData.companies)
                        .sort((a, b) => a.name.localeCompare(b.name));
                    
                    console.log('Sıralanmış hastaneler:', companies);
                    companies.forEach(company => {
                        console.log('Dropdown\'a ekleniyor:', company.name);
                        const option = document.createElement('option');
                        option.value = company.name;
                        option.textContent = company.name;
                        dropdown.appendChild(option);
                    });
                } else {
                    console.log('Hiç hastane bulunamadı veya companies objesi yok');
                }
                
                // Dropdown değişikliğini dinle
                dropdown.addEventListener('change', function() {
                    // Seçilen hastaneyi companyName input'una koy (mevcut kod uyumluluğu için)
                    if (this.value) {
                        document.getElementById('companyName').value = this.value;
                    } else {
                        document.getElementById('companyName').value = '';
                    }
                });
                
            } catch (error) {
                console.error('Hastane listesi yüklenirken hata:', error);
                showModal('⚠️ Hata', 'Hastane listesi yüklenirken bir hata oluştu. Lütfen manuel giriş yapın.');
                // Hata durumunda manuel girişe geç
                document.getElementById('newCompanyRadio').checked = true;
                toggleCompanyInputType();
            }
        }

        function startSurvey() {
            // Hastane adını al (manuel veya dropdown'dan)
            let companyName = '';
            const isNewCompany = document.getElementById('newCompanyRadio').checked;
            
            if (isNewCompany) {
                companyName = document.getElementById('companyName').value.trim();
            } else {
                const dropdown = document.getElementById('companyDropdown');
                companyName = dropdown.value.trim();
                // Dropdown seçimini companyName input'una da yaz (diğer fonksiyonlar için)
                document.getElementById('companyName').value = companyName;
            }
            
            const firstName = document.getElementById('firstName').value.trim();
            const lastName = document.getElementById('lastName').value.trim();
            const disclaimerAccepted = document.getElementById('acceptDisclaimer').checked;

            // Google Sign-In enforcement - Tüm kullanıcılar için zorunlu
            if (!googleUser) {
                // Google butonunu vurgula ve dikkati çek
                const googleBtn = document.getElementById('googleSignInBtn');
                if (googleBtn) {
                    googleBtn.style.animation = 'pulse 2s infinite';
                    googleBtn.style.boxShadow = '0 0 20px #FF6B6B';
                    googleBtn.scrollIntoView({ behavior: 'smooth', block: 'center' });
                    
                    // 5 saniye sonra efekti kaldır
                    setTimeout(() => {
                        googleBtn.style.animation = '';
                        googleBtn.style.boxShadow = '';
                    }, 5000);
                }
                
                showModal(
                    '🔑 ÖNCE GİRİŞ YAPMANIZ GEREK!',
                    `<div class="text-center">
                        <div class="text-3xl font-extrabold text-red-600 mb-4">⚠️ DURDURUN! ⚠️</div>
                        <div class="text-xl font-bold text-gray-800 mb-4">Ankete başlamadan önce giriş yapmalısınız!</div>
                        <div class="bg-yellow-100 border border-yellow-400 rounded p-4 mb-4">
                            <p class="text-lg font-semibold text-yellow-800 mb-2">📍 NE YAPMANIZ GEREKİYOR:</p>
                            <ol class="text-left list-decimal pl-6 text-gray-700 space-y-2">
                                <li class="font-medium">Yukarıda bulunan <span class="bg-blue-100 px-2 py-1 rounded font-bold text-blue-800">Google ile Giriş Yap</span> düğmesine tıklayın</li>
                                <li class="font-medium">Gmail hesabınızla giriş yapın</li>
                                <li class="font-medium">Ad ve soyad alanlarınız otomatik dolacak</li>
                                <li class="font-medium">Sonra tekrar <span class="bg-green-100 px-2 py-1 rounded font-bold text-green-800">Anketi Başlat</span> düğmesine tıklayın</li>
                            </ol>
                        </div>
                        <div class="text-sm text-red-600 font-medium">
                            ⏰ Bu adımı atlayamazsınız - Güvenlik zorunluluğu!
                        </div>
                    </div>`
                );
                return;
            }

            if (!disclaimerAccepted) {
                showModal('⚠️ Uyarı', 'Devam etmek için sorumluluk reddi beyanını kabul etmelisiniz.');
                return;
            }

            if (!companyName || !selectedJobType) {
                showModal('⚠️ Eksik Bilgi', 'Lütfen hastane adını girin ve rolünüzü seçin.');
                return;
            }

            if (!firstName || !lastName) {
                showModal('⚠️ Eksik Bilgi', 'Lütfen adınızı ve soyadınızı girin.');
                return;
            }

            currentQuestions = questions[selectedJobType];
            currentQuestionIndex = 0;
            answers = [];
            surveyStartTime = new Date();

            // Anket bölümünü göster
            document.getElementById('disclaimerSection').classList.add('hidden');
            document.getElementById('companyInfoSection').classList.add('hidden');
            document.getElementById('surveySection').classList.remove('hidden');

            startTimer();
            displayCurrentQuestion();
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

        // Hastaneler için anket soruları
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

        function displayCurrentQuestion() {
            if (currentQuestionIndex >= currentQuestions.length) {
                document.getElementById('submitSurvey').classList.remove('hidden');
                return;
            }

            const question = currentQuestions[currentQuestionIndex];
            const progressPercent = Math.round(((currentQuestionIndex) / currentQuestions.length) * 100);
            
            document.getElementById('progressBar').style.width = progressPercent + '%';
            document.getElementById('progressText').textContent = 
                `Anket İlerlemesi ${currentQuestionIndex}/${currentQuestions.length} Yanıtlandı`;

            const container = document.getElementById('questionContainer');
            container.innerHTML = `
                <div class="bg-blue-50 border border-blue-200 rounded-lg p-4 mb-4">
                    <h3 class="text-lg font-semibold text-blue-800 mb-2">Soru ${currentQuestionIndex + 1}/${currentQuestions.length}</h3>
                    <p class="text-gray-700 text-base leading-relaxed">${question}</p>
                </div>
                <div class="grid grid-cols-1 sm:grid-cols-5 gap-3">
                    ${[1,2,3,4,5].map(score => `
                        <button onclick="selectAnswer(${score})" 
                            class="answer-btn p-4 border-2 border-gray-300 rounded-lg hover:border-blue-500 hover:bg-blue-50 transition-all duration-200 text-center">
                            <div class="text-2xl mb-2">${getScoreEmoji(score)}</div>
                            <div class="text-sm font-medium text-gray-700">${getScoreText(score)}</div>
                            <div class="text-xs text-gray-500 mt-1">${score}/5</div>
                        </button>
                    `).join('')}
                </div>
            `;
        }

        function getScoreEmoji(score) {
            const emojis = {1: '😞', 2: '🙁', 3: '😐', 4: '🙂', 5: '😊'};
            return emojis[score];
        }

        function getScoreText(score) {
            const texts = {
                1: 'Hiç Memnun Değilim',
                2: 'Memnun Değilim', 
                3: 'Kararsızım',
                4: 'Memnunum',
                5: 'Çok Memnunum'
            };
            return texts[score];
        }

        function selectAnswer(score) {
            answers[currentQuestionIndex] = {
                question: currentQuestions[currentQuestionIndex],
                score: score
            };

            // Seçilen butonu vurgula
            document.querySelectorAll('.answer-btn').forEach(btn => {
                btn.classList.remove('border-green-500', 'bg-green-100', 'selected');
            });
            event.target.closest('.answer-btn').classList.add('border-green-500', 'bg-green-100', 'selected');

            // 1 saniye sonra sonraki soruya geç
            setTimeout(() => {
                currentQuestionIndex++;
                displayCurrentQuestion();
            }, 800);
        }

        // Sistem verilerini yükle
        async function loadSystemData() {
            try {
                const database = firebase.database();
                const snapshot = await database.ref('/').once('value');
                systemData.surveyData = snapshot.val();
                console.log('Sistem verileri yüklendi:', systemData.surveyData);
            } catch (error) {
                console.error('Sistem verileri yüklenirken hata:', error);
                throw error;
            }
        }

        // Modal fonksiyonları
        function showModal(title, content) {
            document.getElementById('modalTitle').textContent = title;
            document.getElementById('modalContent').innerHTML = content;
            document.getElementById('modalOverlay').classList.add('show');
        }

        function closeModal() {
            document.getElementById('modalOverlay').classList.remove('show');
        }

        // Module değiştirme
        function showModule(module) {
            currentModule = module;
            document.getElementById('surveyModule').style.display = module === 'survey' ? 'block' : 'none';
            document.getElementById('companyModule').style.display = module === 'company' ? 'block' : 'none';
        }

        // Basit hastane login (demo amaçlı)
        function loginCompany() {
            const companyName = document.getElementById('companyLoginName').value.trim();
            const password = document.getElementById('companyPassword').value.trim();
            
            if (companyName && password.length >= 12) {
                loggedInCompany = { name: companyName };
                document.getElementById('companyLogin').classList.add('hidden');
                document.getElementById('companyDashboard').classList.remove('hidden');
                document.getElementById('companyNameDisplay').textContent = companyName;
                loadCompanyReports();
            } else {
                showModal('⚠️ Hata', 'Hastane adı ve 12 karakterlik şifre gerekli.');
            }
        }

        function logoutCompany() {
            loggedInCompany = null;
            document.getElementById('companyLogin').classList.remove('hidden');
            document.getElementById('companyDashboard').classList.add('hidden');
        }

        function loadCompanyReports() {
            // Demo amaçlı basit veriler
            document.getElementById('totalParticipants').textContent = '0';
            document.getElementById('averageScore').textContent = '0.0';
            document.getElementById('satisfactionRate').textContent = '0%';
        }

        function toggleParticipantDetails() {
            const details = document.getElementById('participantDetails');
            const btn = document.getElementById('toggleParticipantsBtn');
            
            if (details.classList.contains('hidden')) {
                details.classList.remove('hidden');
                btn.textContent = '📋 Katılımcıları Gizle';
            } else {
                details.classList.add('hidden');
                btn.textContent = '📋 Katılımcıları Görüntüle';
            }
        }

        function filterByDateRange() {
            showModal('ℹ️ Bilgi', 'Tarih filtreleme özelliği geliştirilme aşamasında.');
        }

        // Submit survey fonksiyonu
        document.getElementById('submitSurvey').addEventListener('click', function() {
            showModal('✅ Başarılı', 'Anket başarıyla tamamlandı! Katılımınız için teşekkür ederiz.');
            // Burada anket verilerini Firebase'e gönderebilirsiniz
        });

    </script>
</body>
</html>
