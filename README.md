<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Akça Pro X - Kurum Değerlendirme Anketi</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');
        body {
            font-family: 'Inter', sans-serif;
        }
        .gradient-bg {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        /* Aktif Rol Seçimi */
        .active-tab {
            border: 3px solid #6366f1 !important;
            background-color: #6366f1 !important;
            color: white !important;
            font-weight: bold !important;
            transform: scale(1.05) !important;
            box-shadow: 0 4px 8px rgba(99, 102, 241, 0.3) !important;
        }
        /* Modal Stili */
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0, 0, 0, 0.6);
            backdrop-filter: blur(5px);
            padding-top: 50px;
        }
        .modal-content {
            background-color: #fefefe;
            margin: 5% auto;
            padding: 20px;
            border-radius: 12px;
            width: 90%;
            max-width: 500px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
            animation: fadeIn 0.3s;
        }
        .modal.show {
            display: block;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .chart-container {
            position: relative;
            height: 350px;
            width: 100%;
        }
        /* Yönetici Gizli Butonu */
        .admin-icon {
            opacity: 0;
        }
        .admin-icon:hover {
            opacity: 100;
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen flex flex-col">

    <div onclick="showModule('adminLogin')" class="fixed top-0 left-0 p-2 cursor-pointer z-[1001] transition-opacity duration-300 admin-icon">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-500 hover:text-red-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 7a2 2 0 012 2v5a2 2 0 01-2 2H9a2 2 0 01-2-2V9a2 2 0 012-2h6zM15 7V4a2 2 0 00-2-2h-2a2 2 0 00-2 2v3m0 6h.01M10 11h4v4h-4v-4z" />
        </svg>
    </div>


    <header class="gradient-bg text-white shadow-lg p-4 sticky top-0 z-50">
        <div class="max-w-7xl mx-auto flex justify-between items-center">
            <h1 class="text-3xl font-extrabold tracking-tight">Akça Pro X</h1>
            <nav>
                <button onclick="showModule('companyLogin')" class="bg-purple-700 hover:bg-purple-800 px-4 py-2 rounded-lg font-semibold transition-colors ml-2 shadow-md">
                    Kurum Rapor
                </button>
                <button onclick="showModule('disclaimerSection')" class="bg-white text-purple-600 hover:bg-gray-100 px-4 py-2 rounded-lg font-semibold transition-colors ml-2 shadow-md">
                    Anket Başlat
                </button>
            </nav>
        </div>
    </header>

    <main class="flex-grow max-w-7xl mx-auto w-full p-4 sm:p-6 lg:p-8">
        
        <section id="disclaimerSection" class="bg-white p-8 rounded-xl shadow-2xl transition-opacity duration-500">
            <h2 class="text-3xl font-bold text-gray-800 mb-6 border-b pb-3">Hoş Geldiniz</h2>
            
            <p class="text-gray-700 mb-6">
                Bu Kurum Değerlendirme Anketi, hizmet kalitemizi anlamak ve iyileştirmek amacıyla tasarlanmıştır. Görüşleriniz tamamen gizli tutulacak ve sadece istatistiksel analizler için kullanılacaktır. Katılımınız için teşekkür ederiz.
            </p>

            <div class="mb-8 p-4 bg-yellow-50 border-l-4 border-yellow-500 text-yellow-800 rounded-lg">
                <p class="font-semibold">Lütfen Dikkat:</p>
                <ul class="list-disc pl-5 mt-1 text-sm">
                    <li>Anket yaklaşık 5-10 dakikanızı alacaktır.</li>
                    <li>Tüm soruları dürüstçe yanıtlamanız, raporumuzun doğruluğu için kritiktir.</li>
                </ul>
            </div>
            
            <div class="mb-8 p-4 bg-blue-50 border-l-4 border-blue-500 text-blue-800 rounded-lg">
                 <p class="font-semibold">Veri Güvencesi:</p>
                 <p class="text-sm">Tüm anket verileri, <b class="text-blue-700">Google Firebase</b> altyapısı üzerinde yüksek güvenlik ve gizlilik standartları ile saklanmaktadır. Verilerinizin sorumluluğu ve güvencesi Google'ın küresel altyapısı altındadır.</p>
            </div>
            
            <div class="flex items-center mb-8">
                <input type="checkbox" id="acceptDisclaimer" class="h-5 w-5 text-purple-600 border-gray-300 rounded focus:ring-purple-500">
                <label for="acceptDisclaimer" class="ml-3 text-gray-700 select-none">
                    Veri koruma beyanını okudum ve anket sonuçlarımın anonim olarak analiz edilmesini kabul ediyorum.
                </label>
            </div>

            <button onclick="showModule('companyInfoSection')" class="w-full bg-purple-600 text-white py-3 rounded-lg text-lg font-semibold hover:bg-purple-700 transition-colors shadow-lg disabled:opacity-50">
                Devam Et
            </button>
        </section>

        <section id="companyInfoSection" class="hidden bg-white p-8 rounded-xl shadow-2xl transition-opacity duration-500">
            <h2 class="text-3xl font-bold text-gray-800 mb-6 border-b pb-3">Kurum ve Rol Bilgileri</h2>
            
            <div class="mb-8 p-4 bg-purple-50 rounded-lg border-l-4 border-purple-400">
                <h3 class="text-xl font-semibold text-gray-800 mb-4">Kurum Giriş Şeklini Seçin</h3>
                <div class="flex space-x-6 mb-4">
                    <label class="flex items-center text-lg font-medium text-gray-700 cursor-pointer">
                        <input type="radio" name="companyType" value="registered" checked onclick="toggleCompanyInput('registered')" class="h-4 w-4 text-purple-600 focus:ring-purple-500">
                        <span class="ml-2">Kayıtlı Kurum Seç</span>
                    </label>
                    <label class="flex items-center text-lg font-medium text-gray-700 cursor-pointer">
                        <input type="radio" name="companyType" value="new" onclick="toggleCompanyInput('new')" class="h-4 w-4 text-purple-600 focus:ring-purple-500">
                        <span class="ml-2">Yeni Kurum Adı Gir</span>
                    </label>
                </div>
                
                <div id="registeredCompanyInput" class="space-y-3">
                    <label for="registeredCompanyName" class="block text-sm font-medium text-gray-700">Kayıtlı Kurumu Seçiniz</label>
                    <select id="registeredCompanyName" class="w-full border border-gray-300 p-3 rounded-lg focus:ring-purple-500 focus:border-purple-500">
                        <option value="">-- Bir Kurum Seçiniz --</option>
                        </select>
                </div>
                
                <div id="newCompanyInput" class="space-y-3 hidden">
                    <label for="newCompanyName" class="block text-sm font-medium text-gray-700">Kurumunuzun Adı</label>
                    <input type="text" id="newCompanyName" class="w-full border border-gray-300 p-3 rounded-lg focus:ring-purple-500 focus:border-purple-500" placeholder="Örn: Yeni Hastane Adı">
                </div>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">Adınız & Soyadınız (Google Girişi Zorunlu)</label>
                    <div class="flex space-x-3">
                        <input type="text" id="firstName" class="w-1/2 border border-gray-300 p-3 rounded-lg focus:ring-purple-500 focus:border-purple-500" placeholder="Adınız" readonly>
                        <input type="text" id="lastName" class="w-1/2 border border-gray-300 p-3 rounded-lg focus:ring-purple-500 focus:border-purple-500" placeholder="Soyadınız" readonly>
                    </div>
                    <p id="googleUserInfo" class="text-xs text-green-600 mt-2 hidden"></p>
                </div>
            </div>
            
            <div class="mb-8">
                <h3 class="text-xl font-semibold text-gray-800 mb-4">Rolünüzü Seçin</h3>
                <div class="grid grid-cols-3 gap-4">
                    <button id="hastaBtn" onclick="selectJobType('Hasta')" class="job-btn border-2 border-gray-300 p-4 rounded-xl hover:shadow-lg transition-all duration-200 text-gray-700 bg-gray-50">
                        <span class="text-3xl block mb-2">👨‍🦱</span> Hasta / Yakını
                    </button>
                    <button id="doktorBtn" onclick="selectJobType('Doktor')" class="job-btn border-2 border-gray-300 p-4 rounded-xl hover:shadow-lg transition-all duration-200 text-gray-700 bg-gray-50">
                        <span class="text-3xl block mb-2">🩺</span> Doktor / Tıbbi Personel
                    </button>
                    <button id="yönetimBtn" onclick="selectJobType('Yönetim')" class="job-btn border-2 border-gray-300 p-4 rounded-xl hover:shadow-lg transition-all duration-200 text-gray-700 bg-gray-50">
                        <span class="text-3xl block mb-2">💼</span> İdari Personel / Yönetim
                    </button>
                </div>
                <p id="selectedJobDisplay" class="mt-4 text-center font-bold text-purple-600">Seçilen Rol: Henüz Seçilmedi</p>
            </div>
            
            <div class="mb-6 p-4 bg-blue-50 border-l-4 border-blue-500 rounded-lg flex justify-between items-center">
                 <p class="font-semibold text-blue-800">Kimliğinizi Doğrulayın</p>
                 <button id="googleSignInBtn" class="bg-blue-600 text-white hover:bg-blue-700 px-6 py-2 rounded-lg font-semibold transition-colors flex items-center shadow-md">
                    <img src="https://upload.wikimedia.org/wikipedia/commons/4/4a/Logo_2013_Google.png" class="w-5 h-5 mr-2 bg-white rounded-full p-0.5" alt="Google Logo">
                    Google ile Giriş Yap
                 </button>
            </div>

            <button id="startSurvey" onclick="startSurvey()" class="w-full bg-green-600 text-white py-3 rounded-lg text-lg font-semibold hover:bg-green-700 transition-colors shadow-lg">
                Anketi Başlat
            </button>
        </section>

        <section id="surveySection" class="hidden max-w-3xl mx-auto">
            <div class="flex justify-between items-center bg-purple-50 p-4 rounded-t-xl border-b-2 border-purple-200 shadow-md">
                <div id="timeElapsed" class="font-semibold text-purple-700">Süre: 00:00</div>
                <div id="progressText" class="font-semibold text-purple-700">Anket İlerlemesi 0/50 Yanıtlandı</div>
            </div>
            
            <div class="w-full bg-gray-200 rounded-b-xl h-2 mb-8">
                <div id="progressBar" class="bg-purple-600 h-2 rounded-b-xl transition-all duration-500" style="width: 0%"></div>
            </div>
            
            <div id="questionContainer" class="mb-8">
                </div>

            <button id="submitSurvey" class="hidden w-full bg-red-600 text-white py-4 rounded-lg text-xl font-semibold hover:bg-red-700 transition-colors shadow-lg mb-10">
                Anketi Tamamla ve Gönder
            </button>
        </section>
        
        <section id="companyLogin" class="hidden max-w-md mx-auto bg-white p-8 rounded-xl shadow-2xl transition-opacity duration-500">
            <h2 class="text-3xl font-bold text-gray-800 mb-6 border-b pb-3 text-center">Kurum Raporlama Girişi</h2>
            
            <div class="space-y-4">
                <div>
                    <label for="companyLoginName" class="block text-sm font-medium text-gray-700 mb-2">Kurum Adı</label>
                    <input type="text" id="companyLoginName" class="w-full border border-gray-300 p-3 rounded-lg focus:ring-purple-500 focus:border-purple-500" placeholder="Örn: Akça Hastanesi">
                </div>
                <div>
                    <label for="companyPassword" class="block text-sm font-medium text-gray-700 mb-2">Şifre</label>
                    <input type="password" id="companyPassword" class="w-full border border-gray-300 p-3 rounded-lg focus:ring-purple-500 focus:border-purple-500" placeholder="Kurum Şifresi">
                </div>
            </div>
            <button onclick="loginCompany()" class="w-full bg-purple-600 text-white py-3 rounded-lg text-lg font-semibold hover:bg-purple-700 transition-colors shadow-lg mt-6">
                Giriş Yap
            </button>
        </section>
        
        <section id="companyDashboard" class="hidden">
            <header class="bg-purple-600 text-white p-4 rounded-xl shadow-lg mb-6 flex justify-between items-center">
                <h2 id="companyNameDisplay" class="text-3xl font-bold">Rapor Paneli</h2>
                <button onclick="logoutCompany()" class="bg-red-500 hover:bg-red-600 px-4 py-2 rounded-lg font-semibold transition-colors">
                    Çıkış Yap
                </button>
            </header>
            
            <div class="bg-white p-6 rounded-xl shadow-md mb-6 flex items-center space-x-4">
                <h3 class="font-semibold text-lg text-gray-700">Raporu Filtrele:</h3>
                <input type="date" id="reportStartDate" class="border border-gray-300 p-2 rounded-lg focus:ring-purple-500 focus:border-purple-500">
                <span class="text-gray-500"> - </span>
                <input type="date" id="reportEndDate" class="border border-gray-300 p-2 rounded-lg focus:ring-purple-500 focus:border-purple-500">
                <button onclick="filterByDateRange()" class="bg-blue-600 text-white hover:bg-blue-700 px-4 py-2 rounded-lg font-semibold transition-colors">
                    Uygula
                </button>
                <button onclick="loadCompanyData(loggedInCompany)" class="bg-gray-400 text-white hover:bg-gray-500 px-4 py-2 rounded-lg font-semibold transition-colors">
                    Filtreyi Temizle
                </button>
            </div>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                <div class="bg-white p-6 rounded-xl shadow-md border-b-4 border-purple-500">
                    <p class="text-sm font-medium text-gray-500">Toplam Katılımcı</p>
                    <p id="totalParticipants" class="mt-1 text-3xl font-bold text-gray-900">0</p>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-md border-b-4 border-green-500">
                    <p class="text-sm font-medium text-gray-500">Ortalama Skor (1-5)</p>
                    <p id="averageScore" class="mt-1 text-3xl font-bold text-gray-900">0.0</p>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-md border-b-4 border-yellow-500">
                    <p class="text-sm font-medium text-gray-500">Memnuniyet Oranı (≥4.0)</p>
                    <p id="satisfactionRate" class="mt-1 text-3xl font-bold text-gray-900">0%</p>
                </div>
            </div>
            
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-8">
                <div class="bg-white p-6 rounded-xl shadow-md">
                    <h3 class="text-lg font-semibold text-gray-700 mb-4 border-b pb-2">Katılımcı Pozisyon Dağılımı</h3>
                    <div class="chart-container">
                        <canvas id="positionChart"></canvas>
                    </div>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-md">
                    <h3 class="text-lg font-semibold text-gray-700 mb-4 border-b pb-2">Genel Memnuniyet Durumu</h3>
                    <div class="chart-container">
                        <canvas id="satisfactionChart"></canvas>
                    </div>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-md">
                    <h3 class="text-lg font-semibold text-gray-700 mb-4 border-b pb-2">Ortalama Puan Frekansı (1.0 - 5.0)</h3>
                    <div class="chart-container">
                        <canvas id="trendChart"></canvas>
                    </div>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-md">
                    <h3 class="text-lg font-semibold text-gray-700 mb-4 border-b pb-2">Anket Tamamlama Süreleri</h3>
                    <div class="chart-container">
                        <canvas id="timeChart"></canvas>
                    </div>
                </div>
            </div>
            
            <div id="detailedFrequencyTables" class="bg-white p-6 rounded-xl shadow-md mb-8">
                </div>

            <div class="bg-white p-6 rounded-xl shadow-md mb-8">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-xl font-semibold text-gray-700">Katılımcı Detayları</h3>
                    <button id="toggleParticipantsBtn" onclick="toggleParticipantDetails()" class="bg-indigo-600 text-white hover:bg-indigo-700 px-4 py-2 rounded-lg font-semibold transition-colors">
                        📋 Katılımcıları Gizle
                    </button>
                </div>
                
                <div id="participantDetails" class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-3 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Katılımcı</th>
                                <th class="px-3 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Pozisyon</th>
                                <th class="px-3 py-3 text-center text-xs font-medium text-gray-500 uppercase tracking-wider">Ort. Puan</th>
                                <th class="px-3 py-3 text-center text-xs font-medium text-gray-500 uppercase tracking-wider">Değerlendirme</th>
                                <th class="px-3 py-3 text-center text-xs font-medium text-gray-500 uppercase tracking-wider">Tarih</th>
                            </tr>
                        </thead>
                        <tbody id="participantTableBody" class="bg-white divide-y divide-gray-200">
                            <tr><td colspan="5" class="py-4 text-center text-gray-500">Lütfen verileri yükleyin.</td></tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <section id="adminLogin" class="hidden max-w-md mx-auto bg-white p-8 rounded-xl shadow-2xl transition-opacity duration-500">
            <h2 class="text-3xl font-bold text-gray-800 mb-6 border-b pb-3 text-center">Yönetici Girişi</h2>
            
            <div class="space-y-4">
                <div>
                    <label for="adminPassword" class="block text-sm font-medium text-gray-700 mb-2">Yönetici Şifresi</label>
                    <input type="password" id="adminPassword" class="w-full border border-gray-300 p-3 rounded-lg focus:ring-purple-500 focus:border-purple-500" placeholder="Şifrenizi Girin">
                </div>
            </div>
            <button onclick="loginAdmin()" class="w-full bg-red-600 text-white py-3 rounded-lg text-lg font-semibold hover:bg-red-700 transition-colors shadow-lg mt-6">
                Yönetici Girişi
            </button>
        </section>

        <section id="adminDashboard" class="hidden">
            <header class="bg-red-600 text-white p-4 rounded-xl shadow-lg mb-6 flex justify-between items-center">
                <h2 class="text-3xl font-bold">Yönetici Genel Bakış</h2>
                <button onclick="logoutAdmin()" class="bg-red-800 hover:bg-red-900 px-4 py-2 rounded-lg font-semibold transition-colors">
                    Çıkış Yap
                </button>
            </header>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
                <div class="bg-white p-6 rounded-xl shadow-md border-b-4 border-red-500">
                    <p class="text-sm font-medium text-gray-500">Toplam Kurum Sayısı (Simülasyon)</p>
                    <p id="totalCompanies" class="mt-1 text-3xl font-bold text-gray-900">0</p>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-md border-b-4 border-red-500">
                    <p class="text-sm font-medium text-gray-500">Toplam Aktif Anket (Simülasyon)</p>
                    <p id="activeSurveys" class="mt-1 text-3xl font-bold text-gray-900">0</p>
                </div>
                <div class="bg-white p-6 rounded-xl shadow-md border-b-4 border-red-500">
                    <p class="text-sm font-medium text-gray-500">Toplam Katılımcı (Simülasyon)</p>
                    <p id="totalUsers" class="mt-1 text-3xl font-bold text-gray-900">0</p>
                </div>
            </div>
            
            <div class="p-6 bg-yellow-100 rounded-xl shadow-md border-l-4 border-yellow-500 text-yellow-800">
                <p class="font-semibold">Uyarı:</p>
                <p class="text-sm">Bu yönetici paneli, tüm kurumların verilerini toplu olarak görüntülemek ve yönetmek için tasarlanmıştır. Şu anki sürümde sadece "Kurum Raporlama" bölümündeki filtreli veriler gösterilmektedir.</p>
                <button onclick="showModule('companyDashboard')" class="bg-yellow-600 text-white hover:bg-yellow-700 px-4 py-2 rounded-lg font-semibold transition-colors mt-3 text-sm">
                    Kurum Raporlama Paneline Git
                </button>
            </div>
        </section>

    </main>
    
    <div id="modal" class="modal" onclick="if(event.target.id === 'modal') closeModal()">
        <div id="modalContent" class="modal-content">
            </div>
    </div>
    
    <script>
        // Modül değiştirme fonksiyonu
        function showModule(moduleId) {
            document.getElementById('disclaimerSection').classList.add('hidden');
            document.getElementById('companyInfoSection').classList.add('hidden');
            document.getElementById('surveySection').classList.add('hidden');
            document.getElementById('companyLogin').classList.add('hidden');
            document.getElementById('companyDashboard').classList.add('hidden');
            document.getElementById('adminLogin').classList.add('hidden');
            document.getElementById('adminDashboard').classList.add('hidden');
            
            const targetElement = document.getElementById(moduleId);
            if (targetElement) {
                 targetElement.classList.remove('hidden');
            }
        }
        
        // Modal açma ve kapama fonksiyonları
        function showModal(title, content) {
            const modal = document.getElementById('modal');
            const modalContent = document.getElementById('modalContent');
            
            if (typeof content === 'string') {
                modalContent.innerHTML = `
                    <h3 class="text-xl font-semibold mb-4">${title}</h3>
                    <div class="mb-6 text-base">${content}</div>
                    <button onclick="closeModal()" class="w-full bg-blue-600 text-white py-3 px-4 rounded-lg hover:bg-blue-700 font-semibold">
                        Tamam
                    </button>
                `;
            }

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
        
        // ** YÖNETİCİ GİRİŞ MANTIĞI BAŞLANGIÇ **
        const ADMIN_PASSWORD = "030714"; 

        function loginAdmin() {
            const adminPasswordInput = document.getElementById('adminPassword');
            const enteredPassword = adminPasswordInput ? adminPasswordInput.value : '';

            if (enteredPassword === ADMIN_PASSWORD) {
                document.getElementById('adminLogin').classList.add('hidden');
                document.getElementById('adminDashboard').classList.remove('hidden');
                
                console.log('✅ Yönetici Girişi Başarılı!');
                document.getElementById('totalCompanies').textContent = registeredCompanies.length; // Kayıtlı kurum sayısını kullan
                document.getElementById('activeSurveys').textContent = window.allSurveys ? window.allSurveys.length : 0;
                document.getElementById('totalUsers').textContent = window.allSurveys ? window.allSurveys.length : 0;

            } else {
                adminPasswordInput.value = '';
                showModal('❌ Giriş Hatası', 'Girdiğiniz Yönetici Şifresi yanlış.');
            }
        }
        
        function logoutAdmin() {
            document.getElementById('adminLogin').classList.remove('hidden');
            document.getElementById('adminDashboard').classList.add('hidden');
            document.getElementById('adminPassword').value = '';
            console.log('👋 Yönetici Çıkışı Yapıldı.');
        }
        // ** YÖNETİCİ GİRİŞ MANTIĞI BİTİŞ **

        // ** KURUM GİRİŞ TİPİ MANTIĞI **
        const registeredCompanies = ["Akça Hastanesi", "Deneme Kurumu A", "Örnek Poliklinik"]; // SIMULASYON verisi
        
        function toggleCompanyInput(type) {
            const registeredDiv = document.getElementById('registeredCompanyInput');
            const newDiv = document.getElementById('newCompanyInput');

            if (type === 'registered') {
                registeredDiv.classList.remove('hidden');
                newDiv.classList.add('hidden');
            } else {
                registeredDiv.classList.add('hidden');
                newDiv.classList.remove('hidden');
            }
        }

        function populateRegisteredCompanies() {
            const select = document.getElementById('registeredCompanyName');
            select.innerHTML = '<option value="">-- Bir Kurum Seçiniz --</option>'; 
            
            registeredCompanies.forEach(company => {
                const option = document.createElement('option');
                option.value = company;
                option.textContent = company;
                select.appendChild(option);
            });
        }
        
        // Google Sign-In logic
        let googleUser = null;
        document.addEventListener('DOMContentLoaded', function() {
            // Kurum listesini doldur
            populateRegisteredCompanies();
            
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

        // Anket başlatma fonksiyonu: Kurum Adı kontrolü güncellendi
        function startSurvey(e) {
            const disclaimerAccepted = document.getElementById('acceptDisclaimer').checked;
            const firstName = document.getElementById('firstName').value.trim();
            const lastName = document.getElementById('lastName').value.trim();
            const selectedJobType = window.selectedJobType || '';
            
            // Kurum Adı alımı
            const isRegistered = document.querySelector('input[name="companyType"]:checked').value === 'registered';
            let companyName = '';
            
            if (isRegistered) {
                companyName = document.getElementById('registeredCompanyName').value.trim();
            } else {
                companyName = document.getElementById('newCompanyName').value.trim();
            }


            // Zorunlu alanlar kontrolü
            let missingFields = [];
            if (!disclaimerAccepted) missingFields.push('Veri koruma beyanı');
            if (!companyName) missingFields.push('Kurum adı (Seçim veya Giriş)');
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
                     </ul>`
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

            currentQuestions = questions[selectedJobType];

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
            document.getElementById('companyInfoSection').classList.add('hidden');
            document.getElementById('surveySection').classList.remove('hidden');
            startTimer();
            displayCurrentQuestion();
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
        
        function selectJobType(type) {
            document.querySelectorAll('.job-btn').forEach(btn => {
                btn.classList.remove('active-tab');
            });

            const selectedBtn = document.getElementById(type.toLowerCase() + 'Btn');
            if (selectedBtn) {
                selectedBtn.classList.add('active-tab');
            }

            selectedJobType = type;
            window.selectedJobType = type;
            document.getElementById('selectedJobDisplay').textContent = `Seçilen Rol: ${type}`;
        }

        function updateProgress() {
            const totalQuestions = currentQuestions.length;
            const answeredCount = answers.length;
            const progress = (answeredCount / totalQuestions) * 100;

            document.getElementById('progressText').textContent = `Anket İlerlemesi ${answeredCount}/${totalQuestions} Yanıtlandı`;
            document.getElementById('progressBar').style.width = `${progress}%`;

            if (answeredCount === totalQuestions) {
                document.getElementById('submitSurvey').classList.remove('hidden');
            } else {
                document.getElementById('submitSurvey').classList.add('hidden');
            }
        }

        function displayCurrentQuestion() {
            const container = document.getElementById('questionContainer');
            container.innerHTML = '';

            if (currentQuestionIndex < currentQuestions.length) {
                const questionText = currentQuestions[currentQuestionIndex];
                const questionElement = document.createElement('div');
                questionElement.classList.add('bg-white', 'p-6', 'rounded-xl', 'shadow-md', 'border', 'border-gray-200');
                
                const questionNumber = currentQuestionIndex + 1;
                
                let groupTitle = '';
                if (selectedJobType === 'Hasta') {
                    if (questionNumber >= 1 && questionNumber <= 10) groupTitle = 'Tıbbi Hizmet Kalitesi';
                    else if (questionNumber >= 11 && questionNumber <= 20) groupTitle = 'Personel Davranışları ve İletişim';
                    else if (questionNumber >= 21 && questionNumber <= 30) groupTitle = 'Hastane Ortamı ve İmkanlar';
                    else if (questionNumber >= 31 && questionNumber <= 40) groupTitle = 'Yönlendirme ve Bilgilendirme';
                    else if (questionNumber >= 41 && questionNumber <= 50) groupTitle = 'Genel Deneyim ve Tavsiye';
                }
                
                let titleHTML = '';
                if (groupTitle) {
                     titleHTML = `<div class="text-sm font-semibold text-purple-600 mb-2">${groupTitle}</div>`;
                }

                questionElement.innerHTML = `
                    ${titleHTML}
                    <h4 class="text-lg font-bold text-gray-800 mb-4">${questionNumber}. ${questionText}</h4>
                    <div class="space-y-3" id="answerOptions">
                        ${generateRatingOptions(questionNumber)}
                    </div>
                `;
                container.appendChild(questionElement);
            }
        }

        function generateRatingOptions(questionNumber) {
            const options = [
                { value: 1, label: "Kesinlikle Katılmıyorum (Çok Kötü)", color: "red" },
                { value: 2, label: "Katılmıyorum (Kötü)", color: "orange" },
                { value: 3, label: "Ne Katılıyorum Ne Katılmıyorum (Orta)", color: "yellow" },
                { value: 4, label: "Katılıyorum (İyi)", color: "lime" },
                { value: 5, label: "Kesinlikle Katılıyorum (Çok İyi)", color: "green" }
            ];

            return options.map(option => `
                <label class="flex items-center p-3 rounded-lg border-2 border-gray-300 hover:border-${option.color}-500 transition-all duration-200 cursor-pointer has-[:checked]:bg-${option.color}-50 has-[:checked]:border-${option.color}-600">
                    <input type="radio" name="question-${questionNumber}" value="${option.value}" class="w-5 h-5 text-${option.color}-600 focus:ring-${option.color}-500" onclick="selectAnswer(${option.value})">
                    <span class="ml-3 text-gray-700 font-medium">${option.label}</span>
                </label>
            `).join('');
        }
        
        function selectAnswer(score) {
            if (score >= 1 && score <= 5) {
                 answers[currentQuestionIndex] = score;
            }

            currentQuestionIndex++;
            
            updateProgress();
            
            if (currentQuestionIndex < currentQuestions.length) {
                displayCurrentQuestion();
            } else {
                 const container = document.getElementById('questionContainer');
                 container.innerHTML = `<div class="text-center p-8 bg-green-50 rounded-xl shadow-lg">
                                            <div class="text-4xl mb-4">🎉</div>
                                            <h4 class="text-2xl font-bold text-green-800 mb-2">Anket Tamamlandı!</h4>
                                            <p class="text-gray-600">Lütfen sonuçları kaydetmek için aşağıdaki butona tıklayın.</p>
                                        </div>`;
                 document.getElementById('submitSurvey').classList.remove('hidden');
            }
        }

        function startTimer() {
            if (timerInterval) clearInterval(timerInterval);
            
            timerInterval = setInterval(() => {
                const elapsed = Math.floor((new Date() - surveyStartTime) / 1000);
                const minutes = String(Math.floor(elapsed / 60)).padStart(2, '0');
                const seconds = String(elapsed % 60).padStart(2, '0');
                document.getElementById('timeElapsed').textContent = `Süre: ${minutes}:${seconds}`;
            }, 1000);
        }

        function stopTimer() {
             if (timerInterval) clearInterval(timerInterval);
             timerInterval = null;
        }

        document.getElementById('submitSurvey').addEventListener('click', submitSurvey);

        function submitSurvey() {
            stopTimer();

            if (answers.length !== currentQuestions.length) {
                showModal('❗ Eksik Cevap', 'Lütfen tüm soruları yanıtladığınızdan emin olun.');
                startTimer();
                return;
            }

            // Kullanıcı oturum kontrolü
            if (typeof googleUser === 'undefined' || !googleUser || !googleUser.email) {
                showModal('❌ Giriş Hatası', 'Anketi göndermek için önce Google ile giriş yapmalısınız.');
                startTimer();
                return;
            }

            // Kurum Adı alımı
            const isRegistered = document.querySelector('input[name="companyType"]:checked').value === 'registered';
            let companyName = '';

            if (isRegistered) {
                companyName = document.getElementById('registeredCompanyName').value.trim();
            } else {
                companyName = document.getElementById('newCompanyName').value.trim();
            }

            const totalScore = answers.reduce((sum, score) => sum + score, 0);
            const averageScore = (totalScore / currentQuestions.length).toFixed(2);

            const groupScores = {};
            const groupMap = {
                'Tıbbi Hizmet Kalitesi': [0, 9],
                'Personel Davranışları ve İletişim': [10, 19],
                'Hastane Ortamı ve İmkanlar': [20, 29],
                'Yönlendirme ve Bilgilendirme': [30, 39],
                'Genel Deneyim ve Tavsiye': [40, 49]
            };

            if (selectedJobType === 'Hasta') {
                for (const groupName in groupMap) {
                    const [start, end] = groupMap[groupName];
                    const groupAnswers = answers.slice(start, end + 1);
                    const groupTotal = groupAnswers.reduce((sum, score) => sum + score, 0);
                    const groupAverage = groupAnswers.length > 0 ? (groupTotal / groupAnswers.length).toFixed(2) : 0;
                    groupScores[groupName] = parseFloat(groupAverage);
                }
            }

            const surveyData = {
                companyName: companyName, // KESİNLEŞEN KURUM ADI
                userEmail: googleUser.email,
                userName: `${document.getElementById('firstName').value.trim()} ${document.getElementById('lastName').value.trim()}`,
                jobType: selectedJobType,
                answers: answers,
                questions: currentQuestions,
                totalScore: totalScore,
                averageScore: parseFloat(averageScore),
                groupScores: groupScores,
                durationSeconds: Math.floor((new Date() - surveyStartTime) / 1000),
                submittedAt: new Date().getTime()
            };

            // Firebase Realtime Database URL ile bağlantı
            let db;
            try {
                db = firebase.database(FIREBASE_DB_URL);
            } catch (err) {
                showModal('❌ Bağlantı Hatası', 'Firebase veritabanı bağlantısı kurulamadı. Lütfen internet bağlantınızı ve yapılandırmayı kontrol edin.');
                startTimer();
                return;
            }
            const companyRef = db.ref('surveys').child(surveyData.companyName.replace(/[.#$/[\]]/g, "_"));

            companyRef.push(surveyData)
                .then((ref) => {
                    if (!ref || !ref.key) {
                        showModal('❌ Kayıt Hatası', 'Anket gönderildi gibi görünüyor fakat kayıt doğrulanamadı. Lütfen tekrar deneyin veya yöneticinize başvurun.');
                        startTimer();
                        return;
                    }
                    showModal(
                        '✨ Başarılı!', 
                        `<div class="text-xl font-bold text-green-700 mb-2">Anketiniz başarıyla tamamlanıp kaydedilmiştir.</div>
                         <p class="text-base">Ortalama Puanınız: <b>${averageScore} / 5.00</b></p>
                         <p class="text-sm text-gray-500 mt-4">Katılımınız için teşekkür ederiz.</p>`
                    );
                    setTimeout(() => {
                        window.location.reload(); 
                    }, 3000);
                })
                .catch((error) => {
                    console.error("Firebase'e veri gönderme hatası:", error);
                    showModal('❌ Kayıt Hatası', `Anket kaydedilirken bir sorun oluştu: ${error && error.message ? error.message : error}`);
                    startTimer();
                });
        }
        
        // ** KURUM PORTALI KISMI **
        
        let allSurveys = [];
        let myChartInstances = {};

        function loginCompany() {
            const name = document.getElementById('companyLoginName').value.trim();
            const password = document.getElementById('companyPassword').value.trim();

            if (!name || !password) {
                showModal('❌ Giriş Hatası', 'Lütfen kurum adı ve şifrenizi girin.');
                return;
            }

            const expectedPassword = "AKCA_2025" + name.substring(0, 4).toUpperCase();

            if (password === expectedPassword) {
                 loggedInCompany = name;
                 document.getElementById('companyLogin').classList.add('hidden');
                 document.getElementById('companyDashboard').classList.remove('hidden');
                 document.getElementById('companyNameDisplay').textContent = loggedInCompany + ' Raporu';
                 
                 loadCompanyData(name);
                 
            } else {
                 showModal('❌ Giriş Hatası', 'Hatalı kurum adı veya şifre.');
            }
        }
        
        function logoutCompany() {
             loggedInCompany = null;
             document.getElementById('companyLoginName').value = '';
             document.getElementById('companyPassword').value = '';
             document.getElementById('companyLogin').classList.remove('hidden');
             document.getElementById('companyDashboard').classList.add('hidden');
             allSurveys = [];
             filteredSurveys = null;
             
             for (let key in myChartInstances) {
                if (myChartInstances[key]) {
                    myChartInstances[key].destroy();
                    myChartInstances[key] = null;
                }
             }
        }
        
        function loadCompanyData(companyName) {
            const db = firebase.database();
            const companyRef = db.ref('surveys').child(companyName.replace(/[.#$/[\]]/g, "_"));
            
            companyRef.once('value', (snapshot) => {
                const surveysObject = snapshot.val();
                allSurveys = [];
                if (surveysObject) {
                    for (const key in surveysObject) {
                        allSurveys.push(surveysObject[key]);
                    }
                }
                
                filteredSurveys = null;
                renderDashboard(allSurveys);
            }).catch(error => {
                showModal('❌ Veri Hatası', 'Kurum verileri yüklenirken bir hata oluştu: ' + error.message);
                console.error("Kurum verisi yükleme hatası:", error);
            });
        }
        
        function renderDashboard(surveys) {
             if (surveys.length === 0) {
                 document.getElementById('totalParticipants').textContent = 0;
                 document.getElementById('averageScore').textContent = '0.0';
                 document.getElementById('satisfactionRate').textContent = '0%';
                 document.getElementById('detailedFrequencyTables').innerHTML = '<h3 class="text-xl font-semibold mb-4 border-b pb-2">Detaylı Soru Frekans Analizi</h3><p class="text-gray-500">Analiz için yeterli anket sonucu yok.</p>';
                 document.getElementById('participantTableBody').innerHTML = '<tr><td colspan="5" class="py-4 text-center text-gray-500">Bu kurum için henüz anket sonucu yok.</td></tr>';
                 
                 for (let key in myChartInstances) {
                    if (myChartInstances[key]) {
                        myChartInstances[key].destroy();
                        myChartInstances[key] = null;
                    }
                 }
                 
                 createPositionChart([], 'positionChart');
                 createSatisfactionChart([], 'satisfactionChart');
                 createTimeChart([], 'timeChart');
                 createTrendChart([], 'trendChart');

                 return;
             }
             
             const totalParticipants = surveys.length;
             const totalScoreSum = surveys.reduce((sum, s) => sum + s.averageScore, 0);
             const overallAverageScore = (totalScoreSum / totalParticipants).toFixed(2);
             
             const satisfiedParticipants = surveys.filter(s => s.averageScore >= 4.0).length;
             const satisfactionRate = ((satisfiedParticipants / totalParticipants) * 100).toFixed(0);

             document.getElementById('totalParticipants').textContent = totalParticipants;
             document.getElementById('averageScore').textContent = overallAverageScore;
             document.getElementById('satisfactionRate').textContent = satisfactionRate + '%';
             
             const positionData = calculatePositionData(surveys);
             const satisfactionData = calculateSatisfactionData(surveys);
             const timeData = calculateTimeData(surveys);
             const trendData = calculateTrendData(surveys);
             
             for (let key in myChartInstances) {
                if (myChartInstances[key]) {
                    myChartInstances[key].destroy();
                    myChartInstances[key] = null;
                }
             }

             createPositionChart(positionData, 'positionChart');
             createSatisfactionChart(satisfactionData, 'satisfactionChart');
             createTimeChart(timeData, 'timeChart');
             createTrendChart(trendData, 'trendChart');

             renderDetailedFrequencyTables(surveys);
             
             renderParticipantTable(surveys);

        }
        
        function filterByDateRange() {
            const startDateStr = document.getElementById('reportStartDate').value;
            const endDateStr = document.getElementById('reportEndDate').value;

            if (!startDateStr || !endDateStr) {
                showModal('❗ Eksik Tarih', 'Lütfen başlangıç ve bitiş tarihlerini seçin.');
                return;
            }

            const startDate = new Date(startDateStr).getTime();
            const endDate = new Date(endDateStr);
            endDate.setHours(23, 59, 59, 999); 
            const endDateMs = endDate.getTime();

            if (startDate > endDateMs) {
                 showModal('❗ Geçersiz Aralık', 'Başlangıç tarihi, bitiş tarihinden sonra olamaz.');
                 return;
            }

            filteredSurveys = allSurveys.filter(survey => {
                return survey.submittedAt >= startDate && survey.submittedAt <= endDateMs;
            });

            renderDashboard(filteredSurveys);
            
            if (filteredSurveys.length === 0) {
                 showModal('🔍 Sonuç Bulunamadı', 'Seçilen tarih aralığında anket sonucu bulunamadı.');
            } else {
                 showModal('✅ Filtre Uygulandı', `${filteredSurveys.length} adet anket sonucu listeleniyor.`);
            }
        }
        
        function calculatePositionData(surveys) {
            const data = {};
            surveys.forEach(s => {
                data[s.jobType] = (data[s.jobType] || 0) + 1;
            });
            return data;
        }
        
        function calculateSatisfactionData(surveys) {
             const data = { 'Memnun (≥4.0)': 0, 'Nötr (3.0-3.9)': 0, 'Memnun Değil (<3.0)': 0 };
             surveys.forEach(s => {
                 if (s.averageScore >= 4.0) {
                     data['Memnun (≥4.0)']++;
                 } else if (s.averageScore >= 3.0) {
                     data['Nötr (3.0-3.9)']++;
                 } else {
                     data['Memnun Değil (<3.0)']++;
                 }
             });
             return data;
        }
        
        function calculateTimeData(surveys) {
             const data = { 'Hızlı (<1 dk)': 0, 'Orta (1-3 dk)': 0, 'Uzun (>3 dk)': 0 };
             surveys.forEach(s => {
                 if (s.durationSeconds < 60) {
                     data['Hızlı (<1 dk)']++;
                 } else if (s.durationSeconds <= 180) {
                     data['Orta (1-3 dk)']++;
                 } else {
                     data['Uzun (>3 dk)']++;
                 }
             });
             return data;
        }

        function calculateTrendData(surveys) {
            const scores = {};
            surveys.forEach(s => {
                const scoreGroup = Math.floor(s.averageScore);
                scores[scoreGroup] = (scores[scoreGroup] || 0) + 1;
            });
            
            const fullScores = {};
            for (let i = 1; i <= 5; i++) {
                 fullScores[i.toString()] = scores[i] || 0;
            }
            return fullScores;
        }

        function createChart(canvasId, type, data, labels, colors) {
            if (myChartInstances[canvasId]) {
                myChartInstances[canvasId].destroy();
            }

            const ctx = document.getElementById(canvasId).getContext('2d');
            const isPieOrDoughnut = (type === 'doughnut' || type === 'pie');
            
            const config = {
                type: type,
                data: {
                    labels: labels,
                    datasets: [{
                        data: data,
                        backgroundColor: isPieOrDoughnut ? colors : colors.map(c => c.replace('d0', '')),
                        borderColor: isPieOrDoughnut ? colors.map(c => c.replace('d0', '1')) : colors.map(c => c.replace('d0', '')),
                        borderWidth: isPieOrDoughnut ? 1 : 0
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            display: isPieOrDoughnut,
                            position: 'right',
                            labels: {
                                padding: 10,
                                boxWidth: 10
                            }
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    let label = context.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    if (context.parsed !== null) {
                                        label += context.parsed + ' kişi';
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: isPieOrDoughnut ? {} : {
                         y: {
                             beginAtZero: true,
                             title: {
                                 display: true,
                                 text: 'Katılımcı Sayısı'
                             }
                         }
                    }
                }
            };
            
            myChartInstances[canvasId] = new Chart(ctx, config);
        }
        
        function createPositionChart(data, canvasId) {
            const labels = Object.keys(data);
            const values = Object.values(data);
            const colors = labels.map(label => {
                if (label === 'Hasta') return '#6366f1d0';
                if (label === 'Doktor') return '#10b981d0';
                if (label === 'Yönetim') return '#f59e0bd0';
                return '#94a3b8d0';
            }); 
            
            createChart(canvasId, 'doughnut', values, labels, colors);
        }

        function createSatisfactionChart(data, canvasId) {
            const labels = Object.keys(data);
            const values = Object.values(data);
            const colors = ['#10b981d0', '#f59e0bd0', '#ef4444d0'];
            
            createChart(canvasId, 'pie', values, labels, colors);
        }

        function createTimeChart(data, canvasId) {
            const labels = Object.keys(data);
            const values = Object.values(data);
            const colors = ['#3b82f6d0', '#f59e0bd0', '#ef4444d0'];
            
            createChart(canvasId, 'bar', values, labels, colors);
        }
        
        function createTrendChart(data, canvasId) {
             const labels = Object.keys(data);
             const values = Object.values(data);
             const colors = labels.map(label => {
                 if (label >= 4) return '#10b981d0';
                 if (label >= 3) return '#f59e0bd0';
                 return '#ef4444d0';
             });
             
             createChart(canvasId, 'bar', values, labels, colors);
        }

        function renderParticipantTable(surveys) {
            const tbody = document.getElementById('participantTableBody');
            tbody.innerHTML = surveys.map(survey => {
                 const avgScore = parseFloat(survey.averageScore);
                 let evaluation = 'Nötr';
                 let evaluationIcon = '🟡';
                 
                 if (avgScore >= 4.0) {
                     evaluation = 'Memnun';
                     evaluationIcon = '🟢';
                 } else if (avgScore < 3.0) {
                     evaluation = 'Memnun Değil';
                     evaluationIcon = '🔴';
                 }

                 return `
                    <tr class="hover:bg-gray-50 cursor-pointer" onclick="showParticipantDetail('${survey.userName}', '${survey.submittedAt}')">
                        <td class="px-3 py-2 text-left font-medium text-gray-700">${survey.userName}</td>
                        <td class="px-3 py-2 text-left text-sm text-gray-600">${survey.jobType}</td>
                        <td class="px-3 py-2 text-center font-bold">${survey.averageScore}</td>
                        <td class="px-3 py-2 text-center">
                            <span class="inline-flex items-center gap-1 text-sm font-semibold">
                                <span class="w-4 h-4 text-gray-700 text-sm font-bold flex items-center justify-center">${evaluationIcon}</span>
                                ${evaluation}
                            </span>
                        </td>
                        <td class="px-3 py-2 text-center text-sm text-gray-600">${new Date(survey.submittedAt).toLocaleDateString('tr-TR')}</td>
                    </tr>
                `;
            }).join('');
        }
        
        function showParticipantDetail(userName, submittedAt) {
             const targetTime = parseInt(submittedAt);
             const participant = allSurveys.find(s => s.userName === userName && s.submittedAt === targetTime);

             if (!participant) {
                 showModal('Hata', 'Katılımcı detayı bulunamadı.');
                 return;
             }

             let detailHTML = `
                <h3 class="text-xl font-bold text-gray-800 border-b pb-2 mb-4">Katılımcı: ${participant.userName}</h3>
                <div class="grid grid-cols-2 gap-4 text-sm mb-4">
                    <div><b>E-Posta:</b> ${participant.userEmail}</div>
                    <div><b>Pozisyon:</b> ${participant.jobType}</div>
                    <div><b>Kurum Adı:</b> ${participant.companyName}</div>
                    <div><b>Tarih:</b> ${new Date(participant.submittedAt).toLocaleDateString('tr-TR')}</div>
                    <div><b>Süre:</b> ${Math.floor(participant.durationSeconds / 60)} dk ${participant.durationSeconds % 60} sn</div>
                    <div><b>Ortalama Puan:</b> <span class="text-lg font-bold text-blue-600">${participant.averageScore}</span></div>
                </div>
                
                <h4 class="text-lg font-semibold text-gray-700 mt-6 mb-3 border-t pt-4">Cevaplar ve Skorlar</h4>
                <div class="space-y-3 max-h-96 overflow-y-auto p-2 bg-gray-50 rounded-lg">
             `;
             
             if (participant.groupScores) {
                  detailHTML += `<div class="p-3 bg-indigo-100 rounded-lg mb-4">
                                     <h5 class="font-bold text-indigo-800 mb-2">Grup Ortalamaları</h5>
                                     <ul class="list-disc pl-5 text-sm text-indigo-700">`;
                  for (const group in participant.groupScores) {
                       detailHTML += `<li><b>${group}:</b> ${participant.groupScores[group]}</li>`;
                  }
                  detailHTML += `</ul></div>`;
             }


             participant.questions.forEach((q, index) => {
                 const score = participant.answers[index] || 'Cevaplanmadı';
                 const scoreColor = score >= 4 ? 'text-green-600' : score >= 3 ? 'text-yellow-600' : 'text-red-600';
                 
                 detailHTML += `
                    <div class="p-3 border-b border-gray-200">
                        <p class="font-medium text-gray-700">${index + 1}. ${q}</p>
                        <p class="text-sm mt-1">Puan: <span class="font-bold ${scoreColor}">${score} / 5</span></p>
                    </div>
                 `;
             });
             
             detailHTML += `</div>`;
             
             showModal(`Katılımcı Detayı: ${userName}`, detailHTML);
        }
        
        function toggleParticipantDetails() {
            const detailsDiv = document.getElementById('participantDetails');
            const btn = document.getElementById('toggleParticipantsBtn');

            if (detailsDiv.classList.contains('hidden')) {
                detailsDiv.classList.remove('hidden');
                btn.textContent = '📋 Katılımcıları Gizle';
            } else {
                detailsDiv.classList.add('hidden');
                btn.textContent = '📋 Katılımcıları Görüntüle';
            }
        }
        
        function renderDetailedFrequencyTables(surveys) {
            const container = document.getElementById('detailedFrequencyTables');
            container.innerHTML = `<h3 class="text-xl font-semibold mb-4 border-b pb-2">Detaylı Soru Frekans Analizi</h3>`;
            
            if (surveys.length === 0) {
                 container.innerHTML += '<p class="text-gray-500">Analiz için yeterli anket sonucu yok.</p>';
                 return;
            }

            const questionsList = surveys[0].questions;
            
            const questionData = questionsList.map((q, qIndex) => {
                 const scores = { 1: 0, 2: 0, 3: 0, 4: 0, 5: 0 };
                 surveys.forEach(s => {
                     const answer = s.answers[qIndex];
                     if (answer >= 1 && answer <= 5) {
                          scores[answer]++;
                     }
                 });
                 return { question: q, scores: scores };
            });

            const scoreLabels = {
                 1: 'Çok Kötü', 2: 'Kötü', 3: 'Orta', 4: 'İyi', 5: 'Çok İyi'
            };

            const tablesHTML = questionData.map((data, qIndex) => {
                 const scores = data.scores;
                 const totalAnswers = Object.values(scores).reduce((sum, count) => sum + count, 0);
                 
                 let maxScore = 0;
                 let maxScoreLabel = 'Yok';
                 
                 for (const score in scores) {
                     if (scores[score] > maxScore) {
                          maxScore = scores[score];
                          maxScoreLabel = scoreLabels[score];
                     }
                 }

                 return `
                    <div class="bg-white p-4 rounded-xl shadow-sm border mb-4">
                        <h4 class="font-semibold text-gray-700 mb-3">${qIndex + 1}. ${data.question}</h4>
                        <div class="grid grid-cols-5 gap-2 text-center border-t pt-2">
                             ${Object.keys(scores).map(score => {
                                 const count = scores[score];
                                 const percentage = totalAnswers > 0 ? Math.round((count / totalAnswers) * 100) : 0;
                                 const isMax = count === maxScore && count > 0;
                                 return `
                                     <div class="text-center p-2 rounded ${isMax ? 'bg-blue-100 font-bold' : 'bg-white'}">
                                         <div class="text-xs text-gray-600">${scoreLabels[score]}</div>
                                         <div class="font-semibold ${isMax ? 'text-blue-600' : 'text-gray-800'}">${count}</div>
                                         <div class="text-xs text-gray-500">${percentage}%</div>
                                     </div>
                                 `;
                             }).join('')}
                        </div>
                        ${totalAnswers > 0 ? `<div class="mt-2 text-sm text-gray-600">En çok verilen cevap: <span class="font-semibold">${maxScoreLabel}</span> (${maxScore} kişi)</div>` : '<div class="text-sm text-gray-500">Bu soru için henüz cevap yok</div>'}
                    </div>
                 `;
            }).join('');
            
            container.innerHTML += tablesHTML;
        }

        // Soru setleri (Soruları bu objede güncelleyeceğiz)
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
                "Hastanenin, hasta geri bildirimlerini dikkate alıp hizmetlerini geliştirdiğini düşünüyor musunuz?",
                "Hastanenin genel sağlık hizmetleri konusunda bölgenizdeki en iyi seçenek olduğunu düşünüyor musunuz?",
                "Gelecekteki tıbbi ihtiyaçlarınız için bu hastaneyi tercih etme olasılığınız nedir?",
                "Genel olarak hastane deneyiminizi kaç puan üzerinden değerlendirirsiniz? (1-5)"
            ],
            "Doktor": [
                "Hastanenin tıbbi cihaz ve ekipmanlarının yeterli ve güncel olduğunu düşünüyor musunuz?",
                "Laboratuvar ve görüntüleme hizmetlerinin tanı süreçlerini hızlandırdığına inanıyor musunuz?",
                "Diğer birimlerle (hemşirelik, idari, vs.) iletişim ve koordinasyonun verimli olduğunu düşünüyor musunuz?",
                "Kendi alanınızda sürekli eğitim ve gelişim imkanlarının yeterli olduğunu düşünüyor musunuz?",
                "Çalışma saatlerinizin hasta bakımı kalitesini olumsuz etkilediğine inanıyor musunuz?",
                "Hastanenin idari personelinin tıbbi kararlara saygı gösterdiğine inanıyor musunuz?",
                "Hastane yönetiminin geri bildirimlerinizi dikkate aldığına inanıyor musunuz?",
                "Hasta mahremiyeti ve veri güvenliği protokollerinin yeterli olduğunu düşünüyor musunuz?",
                "Hastanede aldığınız ücretin ve sosyal hakların beklentilerinizi karşıladığını düşünüyor musunuz?",
                "Genel olarak hastanenizin çalışma ortamını meslektaşlarınıza tavsiye eder misiniz?"
            ],
            "Yönetim": [
                "Personel (doktor, hemşire, idari) arasındaki iş birliği ve uyum seviyesini nasıl değerlendiriyorsunuz?",
                "Bütçe ve kaynak dağılımının hastane hizmetlerinin kalitesini desteklediğine inanıyor musunuz?",
                "Hastanenin teknolojik altyapısının (HBYS, otomasyon) ihtiyaçları karşıladığını düşünüyor musunuz?",
                "Risk yönetimi ve acil durum planlarının güncel ve etkili olduğunu düşünüyor musunuz?",
                "Hasta şikayetlerinin ele alınma ve çözülme sürecinin verimli olduğuna inanıyor musunuz?",
                "Hastanenin pazarlama ve halkla ilişkiler faaliyetlerinin kurum imajını güçlendirdiğini düşünüyor musunuz?",
                "Sürdürülebilirlik ve çevre dostu uygulamalara yeterince önem verildiğini düşünüyor musunuz?",
                "Yeni personel alım ve oryantasyon sürecinin yeterli olduğunu düşünüyor musunuz?",
                "Hastanenin uzun vadeli stratejik hedeflerine ulaşma potansiyelini nasıl değerlendiriyorsunuz?",
                "Genel olarak hastanenin performans yönetim sisteminden memnun musunuz?"
            ]
        };

        </script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

</body>
</html>
