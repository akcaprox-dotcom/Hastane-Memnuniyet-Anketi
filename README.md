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
        /* Yönetici Gizli Butonu: Artık Hafifçe Görünür */
        .admin-icon {
            opacity: 0.2; /* Hafifçe görünür */
            transition: opacity 0.3s, color 0.3s; /* Geçişleri etkinleştir */
        }
        .admin-icon:hover {
            opacity: 1; /* Üzerine gelince tam görünür */
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen flex flex-col">

    <div onclick="showModule('adminLogin')" class="fixed top-0 left-0 p-2 cursor-pointer z-[1001] admin-icon">
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
                [cite_start]Bu Kurum Değerlendirme Anketi, hizmet kalitemizi anlamak ve iyileştirmek amacıyla tasarlanmıştır. [cite: 13] [cite_start]Görüşleriniz tamamen gizli tutulacak ve sadece istatistiksel analizler için kullanılacaktır. [cite: 14] Katılımınız için teşekkür ederiz.
            </p>

            <div class="mb-8 p-4 bg-yellow-50 border-l-4 border-yellow-500 text-yellow-800 rounded-lg">
                <p class="font-semibold">Lütfen Dikkat:</p>
                <ul class="list-disc pl-5 mt-1 text-sm">
                    [cite_start]<li>Anket yaklaşık 5-10 dakikanızı alacaktır. [cite: 15]</li>
                    [cite_start]<li>Tüm soruları dürüstçe yanıtlamanız, raporumuzun doğruluğu için kritiktir. [cite: 15]</li>
                </ul>
            </div>
            
            <div class="mb-8 p-4 bg-blue-50 border-l-4 border-blue-500 text-blue-800 rounded-lg">
                 <p class="font-semibold">Veri Güvencesi:</p>
                 [cite_start]<p class="text-sm">Tüm anket verileri, <b class="text-blue-700">Google Firebase</b> altyapısı üzerinde yüksek güvenlik ve gizlilik standartları ile saklanmaktadır. [cite: 16] [cite_start]Verilerinizin sorumluluğu ve güvencesi Google'ın küresel altyapısı altındadır. [cite: 16]</p>
            </div>
            
            <div class="flex items-center mb-8">
                <input type="checkbox" id="acceptDisclaimer" class="h-5 w-5 text-purple-600 border-gray-300 rounded focus:ring-purple-500">
                <label for="acceptDisclaimer" class="ml-3 text-gray-700 select-none">
                    [cite_start]Veri koruma beyanını okudum ve anket sonuçlarımın anonim olarak analiz edilmesini kabul ediyorum. [cite: 17]
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
                    <button onclick="closeModal()" class="w-full bg-blue-600 text-white py-3 px-4 rounded-lg 
                        hover:bg-blue-700 font-semibold">
                        Tamam
                    </button>
                `;
            }

            modal.classList.add('show');
        }

        function closeModal() {
            document.getElementById('modal').classList.remove('show');
        }
        
        // ** DÜZELTİLMİŞ FIREBASE CONFIG **
        const firebaseConfig = {
            apiKey: "AIzaSyDp2Yh8hamXi6OTfw03MT0S4rp5CjnlAcg",
            authDomain: "akcaprox-anket.firebaseapp.com",
            // Hata mesajınızdaki doğru veritabanı adresi eklendi:
            databaseURL: "https://akcaprox-anket-default-rtdb.firebaseio.com/", 
            projectId: "akcaprox-anket",
            storageBucket: "akcaprox-anket.appspot.com",
            messagingSenderId: "426135179922",
            appId: "1:426135179922:web:c16b3fd6fa5f3d9224cc4b",
            measurementId: "G-CD1ET7RGX1"
        };
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.database(); 
        
        // ** YÖNETİCİ GİRİŞ MANTIĞI BAŞLANGIÇ **
        const ADMIN_PASSWORD = "030714";
        function loginAdmin() {
            const adminPasswordInput = document.getElementById('adminPassword');
            const enteredPassword = adminPasswordInput ? adminPasswordInput.value : '';

            if (enteredPassword === ADMIN_PASSWORD) {
                document.getElementById('adminLogin').classList.add('hidden');
                document.getElementById('adminDashboard').classList.remove('hidden');
                
                console.log('✅ Yönetici Girişi Başarılı!');
                document.getElementById('totalCompanies').textContent = registeredCompanies.length; 
                document.getElementById('activeSurveys').textContent = window.allSurveys ? window.allSurveys.length : 0;
                document.getElementById('totalUsers').textContent = window.allSurveys ?
                window.allSurveys.length : 0;

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
        const registeredCompanies = ["Akça Hastanesi", "Deneme Kurumu A", "Örnek Poliklinik"];
        const COMPANY_PASSWORDS = {
            "akçahastanesi": "123456",
            "denemekuruma": "654321",
            "örnekpoliklinik": "987654"
        };
        
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
                                document.getElementById('firstName').value = user.displayName ?
                                user.displayName.split(' ')[0] : '';
                                document.getElementById('lastName').value = user.displayName ? user.displayName.split(' ').slice(1).join(' ') : '';
                                userInfoDiv.textContent = `Giriş yapıldı: ${user.displayName} (${user.email})`;
                                userInfoDiv.classList.remove('hidden');
                                document.getElementById('firstName').readOnly = false;
                                document.getElementById('lastName').readOnly = false;
                            }
                        })
                        .catch((error) => {
                            showModal('❌ Giriş Hatası', 'Google ile giriş başarısız oldu: ' + error.message);
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

            // Google Sign-In enforcement (Zorunlu Giriş)
            if (!googleUser) {
                showModal(
                    '🔒 Giriş Gerekli',
                    `<div class=\"text-2xl font-extrabold text-red-700 mb-4\">Google ile Giriş Yapmalısınız</div>
                    <div class=\"text-base text-gray-800 mb-2\">Ankete başlamadan önce kimliğinizi doğrulamanız gerekmektedir.</div>
                     <ul class=\"list-disc pl-6 text-base text-gray-700 mb-4\">
                         <li>Yukarıdaki <b>Google ile Giriş Yap</b> butonunu kullanarak hesabınızla oturum açın.</li>
                     </ul>`
                );
                if (e) e.preventDefault(); return;
            }

            if (missingFields.length > 0) {
                showModal(
                    '❌ Eksik Bilgi',
                    `<div class=\"text-lg text-red-700 font-bold mb-2\">Aşağıdaki alan(lar)ı doldurmalısınız:</div><ul class=\"list-disc pl-6 text-base text-gray-700 mb-4\">${missingFields.map(f=>`<li>${f}</li>`).join('')}</ul>`
                );
                if (e) e.preventDefault(); return;
            }
            currentQuestions = questions[selectedJobType];
            if (!currentQuestions || currentQuestions.length === 0) {
                showModal('❌ Hata', 'Seçilen rol için sorular bulunamadı. Lütfen sayfayı yenileyip tekrar deneyin.');
                if (e) e.preventDefault(); return;
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
                // 50 Soruluk Setler İçin Kategori Gruplaması (10'lu Bloklar)
                if (questionNumber >= 1 && questionNumber <= 10) groupTitle = 'Tıbbi Hizmet Kalitesi';
                else if (questionNumber >= 11 && questionNumber <= 20) groupTitle = 'Personel Davranışları ve İletişim';
                else if (questionNumber >= 21 && questionNumber <= 30) groupTitle = 'Kurum Ortamı ve İmkanlar';
                else if (questionNumber >= 31 && questionNumber <= 40) groupTitle = 'Yönlendirme ve Bilgilendirme';
                else if (questionNumber >= 41 && questionNumber <= 50) groupTitle = 'Genel Deneyim ve Tavsiye';
                
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
            surveyStartTime = new Date();
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
        
        // ** DÜZELTİLMİŞ SUBMITSURVEY FONKSİYONU (Auth Kontrolü Eklendi) **
        function submitSurvey() {
            stopTimer();

            // 1. GÜVENLİK KONTROLÜ: Oturum açmış kullanıcı kontrolü
            const user = firebase.auth().currentUser;
            if (!user) {
                showModal('Oturum Hatası', 'Anketi gönderebilmek için lütfen Google ile giriş yaptığınızdan emin olun.');
                startTimer();
                return;
            }

            if (answers.length !== currentQuestions.length) {
                showModal('❗ Eksik Cevap', 'Lütfen tüm soruları yanıtladığınızdan emin olun.');
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

            // Anketi kurum adına kaydetmek için temizlenmiş bir ID kullanılıyor.
            const companyId = companyName.toLowerCase().replace(/[^a-z0-9]/g, ''); 
            
            const totalScore = answers.reduce((sum, score) => sum + score, 0);
            const averageScore = (totalScore / currentQuestions.length).toFixed(2);

            // Grup skorlarını hesapla
            const groupScores = {};
            const groupMap = {
                'Tıbbi Hizmet Kalitesi': [0, 9],
                'Personel Davranışları ve İletişim': [10, 19],
                'Kurum Ortamı ve İmkanlar': [20, 29],
                'Yönlendirme ve Bilgilendirme': [30, 39],
                'Genel Deneyim ve Tavsiye': [40, 49]
            };

            for (const groupName in groupMap) {
                const [start, end] = groupMap[groupName];
                const groupAnswers = answers.slice(start, end + 1);
                const groupTotal = groupAnswers.reduce((sum, score) => sum + score, 0);
                groupScores[groupName] = (groupTotal / groupAnswers.length).toFixed(2);
            }
            
            const surveyData = {
                companyName: companyName,
                companyId: companyId,
                jobType: selectedJobType,
                participantName: `${document.getElementById('firstName').value.trim()} ${document.getElementById('lastName').value.trim()}`,
                participantUid: user.uid, // Katılımcı UID'si
                answers: answers,
                averageScore: parseFloat(averageScore),
                groupScores: groupScores,
                totalTime: document.getElementById('timeElapsed').textContent.replace('Süre: ', ''),
                timestamp: firebase.database.ServerValue.TIMESTAMP
            };

            // 2. VERİ GÖNDERİMİ: Düzgün yapılandırılmış db referansı ile gönderme
            db.ref(`surveys/${companyId}`).push(surveyData)
                .then(() => {
                    document.getElementById('surveySection').classList.add('hidden');
                    showModal(
                        '✅ Başarılı!',
                        'Anketiniz başarıyla gönderildi. Katkınız için teşekkür ederiz!'
                    );
                    // State'i temizle
                    resetSurveyState();
                    showModule('disclaimerSection');
                })
                .catch(error => {
                    console.error("Anket gönderme hatası:", error);
                    showModal(
                        '❌ Gönderme Hatası',
                        `Anket gönderilirken bir hata oluştu: ${error.message}. Lütfen Firebase Güvenlik Kurallarınızın (auth != null) doğru olduğundan ve ağ bağlantınızı kontrol edin.`
                    );
                    startTimer(); 
                });
        }

        function resetSurveyState() {
            currentQuestionIndex = 0;
            answers = [];
            stopTimer();
            document.getElementById('timeElapsed').textContent = 'Süre: 00:00';
            document.getElementById('progressText').textContent = 'Anket İlerlemesi 0/50 Yanıtlandı';
            document.getElementById('progressBar').style.width = '0%';
            document.getElementById('questionContainer').innerHTML = '';
            document.getElementById('submitSurvey').classList.add('hidden');
            
            if (googleUser) {
                document.getElementById('firstName').value = googleUser.displayName ? googleUser.displayName.split(' ')[0] : '';
                document.getElementById('lastName').value = googleUser.displayName ? googleUser.displayName.split(' ').slice(1).join(' ') : '';
            }
            
            document.querySelectorAll('.job-btn').forEach(btn => btn.classList.remove('active-tab'));
            document.getElementById('selectedJobDisplay').textContent = 'Seçilen Rol: Henüz Seçilmedi';
            window.selectedJobType = '';

        }

        // ** KURUM RAPORLAMA/YÖNETİM PANELİ FONKSİYONLARI **
        let allSurveys = [];
        let participantChart, satisfactionChart, trendChart, timeChart; 

        function loginCompany() {
            const companyLoginName = document.getElementById('companyLoginName').value.trim();
            const companyPassword = document.getElementById('companyPassword').value.trim();
            const companyId = companyLoginName.toLowerCase().replace(/[^a-z0-9]/g, '');

            if (COMPANY_PASSWORDS[companyId] === companyPassword) {
                loggedInCompany = companyId;
                document.getElementById('companyNameDisplay').textContent = `${companyLoginName} Rapor Paneli`;
                showModule('companyDashboard');
                loadCompanyData(loggedInCompany);
            } else {
                showModal('❌ Giriş Hatası', 'Kurum Adı veya Şifre yanlış.');
            }
        }

        function logoutCompany() {
            loggedInCompany = null;
            showModule('companyLogin');
        }

        function filterByDateRange() {
            const startDate = document.getElementById('reportStartDate').value;
            const endDate = document.getElementById('reportEndDate').value;

            if (!startDate || !endDate) {
                showModal('Uyarı', 'Lütfen geçerli bir başlangıç ve bitiş tarihi seçin.');
                return;
            }

            const startTimestamp = new Date(startDate).getTime();
            const endTimestamp = new Date(endDate).getTime() + 86400000; // Son günü kapsamak için 1 gün ekle

            filteredSurveys = allSurveys.filter(survey => {
                return survey.timestamp >= startTimestamp && survey.timestamp <= endTimestamp;
            });
            
            renderDashboard(filteredSurveys);
            showModal('Filtre Uygulandı', `Seçilen tarihler arasında ${filteredSurveys.length} anket bulundu.`);
        }

        function loadCompanyData(companyId) {
            // Filtreyi sıfırla
            filteredSurveys = null;
            document.getElementById('reportStartDate').value = '';
            document.getElementById('reportEndDate').value = '';

            db.ref(`surveys/${companyId}`).once('value')
                .then(snapshot => {
                    const data = snapshot.val();
                    if (data) {
                        allSurveys = Object.keys(data).map(key => ({ ...data[key], key }));
                        renderDashboard(allSurveys);
                    } else {
                        allSurveys = [];
                        renderDashboard([]);
                        showModal('Bilgi', 'Bu kurum için henüz tamamlanmış anket bulunmamaktadır.');
                    }
                })
                .catch(error => {
                    console.error("Veri çekme hatası:", error);
                    showModal('❌ Hata', `Veriler çekilirken bir hata oluştu: ${error.message}`);
                });
        }
        
        // ... (renderDashboard ve diğer Chart/Dashboard fonksiyonları buraya gelecek)
        function renderDashboard(surveys) {
            const totalParticipants = surveys.length;
            document.getElementById('totalParticipants').textContent = totalParticipants;

            if (totalParticipants === 0) {
                document.getElementById('averageScore').textContent = '0.0';
                document.getElementById('satisfactionRate').textContent = '0%';
                document.getElementById('participantTableBody').innerHTML = '<tr><td colspan="5" class="py-4 text-center text-gray-500">Veri bulunamadı.</td></tr>';
                if (participantChart) participantChart.destroy();
                if (satisfactionChart) satisfactionChart.destroy();
                if (trendChart) trendChart.destroy();
                if (timeChart) timeChart.destroy();
                document.getElementById('detailedFrequencyTables').innerHTML = '';
                return;
            }

            const totalScoreSum = surveys.reduce((sum, s) => sum + s.averageScore, 0);
            const overallAverage = (totalScoreSum / totalParticipants).toFixed(2);
            document.getElementById('averageScore').textContent = overallAverage;

            const satisfiedCount = surveys.filter(s => s.averageScore >= 4.0).length;
            const satisfactionRate = ((satisfiedCount / totalParticipants) * 100).toFixed(1);
            document.getElementById('satisfactionRate').textContent = `${satisfactionRate}%`;
            
            // Pozisyon Dağılımı
            const positionCounts = surveys.reduce((acc, s) => {
                acc[s.jobType] = (acc[s.jobType] || 0) + 1;
                return acc;
            }, {});
            drawPositionChart(positionCounts);
            
            // Grup Skorları ve Memnuniyet Durumu
            const groupScoreData = calculateGroupAverages(surveys);
            drawSatisfactionChart(groupScoreData);
            
            // Detaylı Frekans Tabloları
            renderFrequencyTables(surveys);
            
            // Katılımcı Detayları
            renderParticipantDetails(surveys);
        }

        function calculateGroupAverages(surveys) {
            const groups = ['Tıbbi Hizmet Kalitesi', 'Personel Davranışları ve İletişim', 'Kurum Ortamı ve İmkanlar', 'Yönlendirme ve Bilgilendirme', 'Genel Deneyim ve Tavsiye'];
            const groupAverages = {};
            const groupTotals = groups.reduce((acc, group) => ({ ...acc, [group]: [] }), {});

            surveys.forEach(survey => {
                for (const group of groups) {
                    if (survey.groupScores && survey.groupScores[group]) {
                        groupTotals[group].push(parseFloat(survey.groupScores[group]));
                    }
                }
            });

            for (const group of groups) {
                if (groupTotals[group].length > 0) {
                    const sum = groupTotals[group].reduce((a, b) => a + b, 0);
                    groupAverages[group] = (sum / groupTotals[group].length).toFixed(2);
                } else {
                    groupAverages[group] = 0;
                }
            }
            return groupAverages;
        }


        // Chart çizme fonksiyonları (Kısaltıldı)
        function drawPositionChart(counts) {
            const ctx = document.getElementById('positionChart').getContext('2d');
            if (participantChart) participantChart.destroy();
            participantChart = new Chart(ctx, {
                type: 'pie',
                data: {
                    labels: Object.keys(counts),
                    datasets: [{
                        data: Object.values(counts),
                        backgroundColor: ['#6366f1', '#10b981', '#f59e0b'],
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false }
            });
        }
        
        function drawSatisfactionChart(averages) {
            const ctx = document.getElementById('satisfactionChart').getContext('2d');
            if (satisfactionChart) satisfactionChart.destroy();
            satisfactionChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: Object.keys(averages).map(label => label.split(' ')[0]),
                    datasets: [{
                        label: 'Ortalama Puan (1-5)',
                        data: Object.values(averages),
                        backgroundColor: '#6366f1',
                    }]
                },
                options: { responsive: true, maintainAspectRatio: false, scales: { y: { min: 0, max: 5 } } }
            });
        }
        
        function renderFrequencyTables(surveys) {
            const container = document.getElementById('detailedFrequencyTables');
            container.innerHTML = `<h3 class="text-xl font-semibold text-gray-700 mb-4 border-b pb-2">Detaylı Frekans Dağılımları</h3>`;

            const jobTypes = ['Hasta', 'Doktor', 'Yönetim'];
            jobTypes.forEach(jobType => {
                const jobSurveys = surveys.filter(s => s.jobType === jobType);
                if (jobSurveys.length > 0) {
                    container.innerHTML += `<h4 class="text-lg font-bold mt-4 mb-2 text-purple-700">${jobType} - Cevap Frekansları (${jobSurveys.length} Katılımcı)</h4>`;
                    
                    const freqData = calculateQuestionFrequencies(jobSurveys);
                    const tableHtml = generateFrequencyTable(jobType, freqData);
                    container.innerHTML += tableHtml;
                }
            });
        }
        
        function calculateQuestionFrequencies(surveys) {
            const numQuestions = surveys[0].answers.length;
            const freqData = Array(numQuestions).fill(0).map(() => ({ total: 0, 1: 0, 2: 0, 3: 0, 4: 0, 5: 0 }));

            surveys.forEach(survey => {
                survey.answers.forEach((score, index) => {
                    if (score >= 1 && score <= 5) {
                        freqData[index].total++;
                        freqData[index][score]++;
                    }
                });
            });
            return freqData;
        }

        function generateFrequencyTable(jobType, freqData) {
            let html = `<div class="overflow-x-auto mb-6"><table class="min-w-full divide-y divide-gray-200 shadow-sm border border-gray-100">
                <thead class="bg-purple-50"><tr>
                    <th class="px-3 py-3 text-left text-xs font-medium text-gray-700 uppercase tracking-wider">Soru</th>
                    <th class="px-3 py-3 text-center text-xs font-medium text-red-600 uppercase tracking-wider">1 (Çok Kötü)</th>
                    <th class="px-3 py-3 text-center text-xs font-medium text-orange-600 uppercase tracking-wider">2 (Kötü)</th>
                    <th class="px-3 py-3 text-center text-xs font-medium text-yellow-600 uppercase tracking-wider">3 (Orta)</th>
                    <th class="px-3 py-3 text-center text-xs font-medium text-lime-600 uppercase tracking-wider">4 (İyi)</th>
                    <th class="px-3 py-3 text-center text-xs font-medium text-green-600 uppercase tracking-wider">5 (Çok İyi)</th>
                    <th class="px-3 py-3 text-center text-xs font-medium text-gray-700 uppercase tracking-wider">Ortalama</th>
                </tr></thead>
                <tbody class="bg-white divide-y divide-gray-200">`;

            freqData.forEach((data, index) => {
                const questionText = questions[jobType][index];
                const average = (data.total > 0) ? ((data[1]*1 + data[2]*2 + data[3]*3 + data[4]*4 + data[5]*5) / data.total).toFixed(2) : 'N/A';
                
                html += `<tr>
                    <td class="px-3 py-2 text-sm text-gray-800">${index + 1}. ${questionText.substring(0, 50)}...</td>
                    <td class="px-3 py-2 text-sm text-center">${data[1]} (${(data[1]/data.total*100).toFixed(0)}%)</td>
                    <td class="px-3 py-2 text-sm text-center">${data[2]} (${(data[2]/data.total*100).toFixed(0)}%)</td>
                    <td class="px-3 py-2 text-sm text-center">${data[3]} (${(data[3]/data.total*100).toFixed(0)}%)</td>
                    <td class="px-3 py-2 text-sm text-center">${data[4]} (${(data[4]/data.total*100).toFixed(0)}%)</td>
                    <td class="px-3 py-2 text-sm text-center">${data[5]} (${(data[5]/data.total*100).toFixed(0)}%)</td>
                    <td class="px-3 py-2 text-sm font-bold text-center ${average >= 4.0 ? 'text-green-600' : (average < 3.0 ? 'text-red-600' : 'text-gray-800')}">${average}</td>
                </tr>`;
            });

            html += `</tbody></table></div>`;
            return html;
        }

        function renderParticipantDetails(surveys) {
            const tableBody = document.getElementById('participantTableBody');
            tableBody.innerHTML = '';
            
            surveys.sort((a, b) => b.timestamp - a.timestamp).forEach(survey => {
                const date = new Date(survey.timestamp).toLocaleDateString('tr-TR');
                const scoreColor = survey.averageScore >= 4.0 ? 'text-green-600' : (survey.averageScore < 3.0 ? 'text-red-600' : 'text-gray-800');
                const scoreText = survey.averageScore >= 4.0 ? 'Memnun' : (survey.averageScore < 3.0 ? 'Memnun Değil' : 'Kararsız');

                const row = `
                    <tr class="hover:bg-gray-50">
                        <td class="px-3 py-2 text-sm font-medium text-gray-900">${survey.participantName}</td>
                        <td class="px-3 py-2 text-sm text-gray-500">${survey.jobType}</td>
                        <td class="px-3 py-2 text-sm font-bold text-center ${scoreColor}">${survey.averageScore}</td>
                        <td class="px-3 py-2 text-sm text-center ${scoreColor}">${scoreText}</td>
                        <td class="px-3 py-2 text-sm text-gray-500 text-center">${date}</td>
                    </tr>
                `;
                tableBody.innerHTML += row;
            });
        }

        function toggleParticipantDetails() {
            const detailsDiv = document.getElementById('participantDetails');
            const button = document.getElementById('toggleParticipantsBtn');
            const isHidden = detailsDiv.classList.toggle('hidden');
            button.textContent = isHidden ? '👀 Katılımcıları Göster' : '📋 Katılımcıları Gizle';
        }

        // **********************************************
        // ANKET SORULARI OBJESİ (150 SORU)
        // **********************************************
        const questions = {
            "Hasta": [
                // Kategori 1: Tıbbi Hizmet Kalitesi (1-10)
                "Hastaneye ilk geldiğinizde karşılama ve yönlendirme yeterli miydi?",
                "Tedavi süreciniz boyunca doktorunuzun size ayırdığı zaman ve ilgiden memnun kaldınız mı?",
                "Hekimlerin tanı ve tedavi yöntemlerini size anlaşılır bir dille açıkladığını düşünüyor musunuz?",
                "Hemşire ve sağlık personelinin tıbbi bilgi ve becerileri yeterli düzeyde miydi?",
                "Ağrı yönetimi ve konforunuz için yeterli özen gösterildi mi?",
                "Tedavi sürecinizdeki gelişmeler ve olası riskler hakkında tam ve zamanında bilgilendirildiniz mi?",
                "Acil durumlarda tıbbi müdahaleye ulaşım süresi tatmin edici miydi?",
                "İlaçlarınız ve yan etkileri hakkında yeterince bilgi aldınız mı?",
                "Hastanede aldığınız tıbbi hizmetin genel kalitesini nasıl değerlendirirsiniz?",
                "Tıbbi hataların önlenmesi konusunda hastanenin hassasiyetine güveniyor musunuz?",
                // Kategori 2: Personel Davranışları ve İletişim (11-20)
                "Hastane personelinin (temizlik, taşıma, sekreter) size karşı tutumu nazik ve saygılı mıydı?",
                "Personel ile iletişim kurarken kendinizi rahat hissettiniz mi?",
                "Personelin taleplerinize hızlı ve etkili bir şekilde yanıt verdiğini düşünüyor musunuz?",
                "Doktorunuzun ve hemşirenizin sizi bir birey olarak dinlediğine inanıyor musunuz?",
                "Hasta haklarınız konusunda yeterince bilgilendirildiniz mi?",
                "Taburcu olduktan sonraki bakımınızla ilgili net talimatlar verildi mi?",
                "Personelin kimlik kartlarını takması, kendilerini tanıtması güven verici miydi?",
                "Personelin hasta mahremiyetine ve gizliliğine yeterince önem verdiğini düşünüyor musunuz?",
                "Farklı departmanlar arasındaki iletişimin (randevu, test sonuçları vb.) sorunsuz olduğunu gözlemlediniz mi?",
                "Hastane personelinin, kültürel veya kişisel ihtiyaçlarınıza duyarlı davrandığını düşünüyor musunuz?",
                // Kategori 3: Kurum Ortamı ve İmkanlar (21-30)
                "Hastanenin genel temizliği ve hijyeni tatmin edici düzeyde miydi?",
                "Odanızın (eğer kaldıysanız) ve yatağınızın konforu yeterli miydi?",
                "Hastanenin otopark ve ulaşım imkanları kolaylık sağladı mı?",
                "Yemeklerin kalitesi ve çeşitliliği beklentinizi karşıladı mı?",
                "Hastanenin bekleme alanlarının rahat ve yeterli olduğunu düşünüyor musunuz?",
                "Randevularınızın zamanında başladığını düşünüyor musunuz?",
                "Tıbbi cihaz ve teknolojik donanımların güncel ve yeterli olduğuna inanıyor musunuz?",
                "Hastanedeki tabela ve işaretlerin yön bulmada yardımcı olduğunu düşünüyor musunuz?",
                "Hastanenin sessizlik ve gürültü kontrolü konusunda başarılı olduğunu düşünüyor musunuz?",
                "Hastanenin fiziki görünümü ve atmosferi kendinizi güvende hissetmenizi sağladı mı?",
                // Kategori 4: Yönlendirme ve Bilgilendirme (31-40)
                "Tedavinizin maliyeti ve ödeme seçenekleri hakkında önceden net bilgi aldınız mı?",
                "Hastane prosedürleri ve süreçleri hakkında yeterli broşür veya rehberlik mevcut muydu?",
                "Hastanede geçirdiğiniz süre boyunca ihtiyacınız olan ek bilgileri kolayca temin edebildiniz mi?",
                "Taburculuk işlemleriniz hızlı ve sorunsuz gerçekleşti mi?",
                "Test sonuçlarınızın size ulaştırılma hızı ve yöntemi uygun muydu?",
                "Hastanenin web sitesi/mobil uygulaması bilgi edinme ve randevu alma açısından kullanışlı mıydı?",
                "Farklı uzmanlık alanlarına yönlendirilirken süreç hızlı ve koordine miydi?",
                "Hastanenin hasta eğitim materyallerinin (hastalık, korunma vb.) kalitesini nasıl değerlendirirsiniz?",
                "Şikayet veya önerilerinizin kolayca alınabileceği bir sistem mevcut muydu?",
                "Tedavinizin uzun vadeli sonuçları hakkında size yeterli bilgi verildi mi?",
                // Kategori 5: Genel Deneyim ve Tavsiye (41-50)
                "Hastanede aldığınız genel hizmete verdiğiniz paranın karşılığını aldığınızı düşünüyor musunuz?",
                "Hastanede geçirdiğiniz süre boyunca tedavi motivasyonunuzu destekleyen bir ortam var mıydı?",
                "Hastanenin genel olarak beklentilerinizi ne ölçüde karşıladığını düşünüyorsunuz?",
                "Bu hastaneyi ailenize ve arkadaşlarınıza gönül rahatlığıyla tavsiye eder misiniz?",
                "Acil bir durumda bu hastaneyi tekrar tercih eder misiniz?",
                "Hastanenin çevre dostu ve sürdürülebilir uygulamalarına dikkat ettiğini düşünüyor musunuz?",
                "Aldığınız hizmete puanınız (Genel Memnuniyet Skoru) nedir? (1-5)",
                "Personelin size gösterdiği kişisel ilgi, iyileşmenize katkı sağladı mı?",
                "Hastanenin genel itibarı ve imajı hakkındaki görüşünüz olumlu mu?",
                "Hastanenizdeki deneyiminizden genel olarak memnun kaldınız mı?",
            ],
            "Doktor": [
                // Kategori 1: Tıbbi Hizmet Kalitesi (1-10)
                "Tıbbi karar alma süreçlerinde yönetim desteğini yeterli buluyor musunuz?",
                "Kullandığınız tıbbi cihaz ve teknolojik donanımların güncelliği ve kalitesi yeterli mi?",
                "Tanı ve tedavi yöntemlerinde meslektaşlarınızla iş birliği yapma imkanlarınız yeterli mi?",
                "Hastanenin bilimsel yayın ve araştırma faaliyetlerine verdiği önemi nasıl değerlendirirsiniz?",
                "Hasta verilerine erişim ve kayıt sistemlerinin hızı ve güvenilirliği tatmin edici mi?",
                "Laboratuvar ve görüntüleme hizmetlerinin sonuç verme süresi tanı sürecini aksatıyor mu?",
                "Zor vakalarda hastane bünyesinde konsültasyon desteği kolaylıkla sağlanabiliyor mu?",
                "Hastane Enfeksiyon Kontrol Komitesi'nin çalışmalarını ve sonuçlarını yeterli buluyor musunuz?",
                "Tıbbi uygulama hatalarının (malpraktis) önlenmesi konusunda hastanenin politikaları net mi?",
                "Hastanenin uzmanlık alanınızdaki gelişmelere yatırım yapma isteğini nasıl değerlendirirsiniz?",
                // Kategori 2: Personel Davranışları ve İletişim (11-20)
                "Hemşire ve destek personelinin tıbbi talimatlara uyumu ve profesyonelliği yeterli mi?",
                "Farklı disiplinler ve idari birimler arasındaki iletişim ve koordinasyon sorunsuz mu?",
                "Yönetimden geri bildirim almanız kolay ve yapıcı mı?",
                "Personel değişim hızı (sirkülasyon) hizmet kalitesini olumsuz etkiliyor mu?",
                "Hasta/yakınları ile yaşadığınız iletişim sorunlarında yönetimden destek alabiliyor musunuz?",
                "Hastanenin tıbbi etik kurallarına bağlılığı, mesleki çalışmalarınız için güven verici mi?",
                "Personel eğitim programlarının (CPR, yeni teknikler) güncel ve yeterli olduğunu düşünüyor musunuz?",
                "Tıbbi bilgi paylaşımı ve eğitim materyallerine erişim kolaylığı var mı?",
                "Yönetimin, doktorların mesleki gelişimlerine ve kongrelere katılımına desteğini yeterli buluyor musunuz?",
                "Hastanedeki sosyal ortam ve meslektaş dayanışması ruh sağlığınızı destekliyor mu?",
                // Kategori 3: Kurum Ortamı ve İmkanlar (21-30)
                "Çalışma saatlerinizin ve nöbet düzeninizin adil ve sürdürülebilir olduğunu düşünüyor musunuz?",
                "Ameliyathane veya muayene odalarının fiziki koşulları ve hijyeni çalışma konforunuz için yeterli mi?",
                "Tıbbi sekreterlik ve idari destek hizmetlerinin verimliliği iş yükünüzü azaltıyor mu?",
                "Hastanenin size sağladığı dinlenme ve sosyal imkanlar yeterli mi?",
                "Kişisel güvenlik ve mesleki risk yönetimi konusunda hastaneye güveniniz tam mı?",
                "Hastanenin ilaç ve malzeme stok yönetimi, kesintisiz hizmet sunmanıza olanak tanıyor mu?",
                "Tıbbi kayıt ve arşivleme sistemlerinin kullanım kolaylığını nasıl değerlendirirsiniz?",
                "Hasta kabul ve taburculuk süreçleri verimli bir şekilde yönetiliyor mu?",
                "Hastane yönetiminin, doktorların ihtiyaç ve önerilerine kulak verdiğini düşünüyor musunuz?",
                "Genel olarak çalıştığınız hastane ortamından mesleki tatmin duyuyor musunuz?",
                // Kategori 4: Yönlendirme ve Bilgilendirme (31-40)
                "Hastanenin kurumsal stratejileri ve hedefleri size açıkça iletiliyor mu?",
                "Tıbbi uygulamalarınızla ilgili performans geri bildirimleri (endikatörler) düzenli veriliyor mu?",
                "Maaş, ek ödeme ve teşvik sistemlerinin adil ve şeffaf olduğunu düşünüyor musunuz?",
                "Hastane içi karar alma süreçlerine (komiteler, toplantılar) yeterince katılabiliyor musunuz?",
                "Yönetimin, tıbbi departmanların bütçe taleplerini rasyonel bir şekilde değerlendirdiğine inanıyor musunuz?",
                "Hastanenin hasta portföyü ve pazar payı büyüme potansiyelini nasıl değerlendirirsiniz?",
                "Hastanenin sunduğu yasal ve hukuki danışmanlık hizmetlerinden memnun musunuz?",
                "Yeni hastane politikaları veya prosedürleri size yeterli zaman tanınarak duyuruluyor mu?",
                "Doktor performans değerlendirme sisteminin (varsa) objektif olduğuna inanıyor musunuz?",
                "Hastaların tıbbi geçmişine dair kapsamlı ve güncel bilgilere kolayca erişebiliyor musunuz?",
                // Kategori 5: Genel Deneyim ve Tavsiye (41-50)
                "Hastanenin markasını ve itibarını meslektaşlarınıza ve hastalarınıza tavsiye eder misiniz?",
                "Genel olarak aldığınız toplam ücret (maaş, döner sermaye, ek ödemeler) beklentinizi karşılıyor mu?",
                "Hastanenin, mesleki bağımsızlığınıza ve etik değerlerinize saygı gösterdiğini düşünüyor musunuz?",
                "Çalıştığınız kurumda kendinizi güvende ve değerli hissediyor musunuz?",
                "Bu hastanede uzun vadeli bir kariyer planı yapmayı düşünür müsünüz?",
                "Hastanenin hizmet kalitesini ve hasta memnuniyetini sürekli iyileştirme çabalarını destekliyor musunuz?",
                "Mesleki gelişiminiz için gerekli olanaklara (eğitim, araç) erişiminiz tam mı?",
                "Hastanenin size sunduğu iş-yaşam dengesi imkanlarını nasıl değerlendirirsiniz?",
                "Kendi branşınızda hastanenizi kaç puan üzerinden değerlendirirsiniz? (1-5)",
                "Hastanenin, doktorları ile uzun süreli ve karşılıklı güvene dayalı bir ilişki kurduğuna inanıyor musunuz?",
            ],
            "Yönetim": [
                // Kategori 1: Tıbbi Hizmet Kalitesi (1-10)
                "Kurumun tıbbi cihaz ve teknolojik altyapısı, rekabet avantajı yaratacak düzeyde mi?",
                "Hizmet kalitesi standartlarının (JCI, ISO vb.) sürdürülebilirliğini sağlamak kolay mı?",
                "Hastanenin medikal personelinin (Doktor/Hemşire) nicelik ve niteliği yeterli mi?",
                "Tıbbi süreçlerdeki verimlilik ve maliyet etkinliği hedeflerine ulaşabiliyor musunuz?",
                "Hasta güvenliği ve risk yönetimi programlarının etkinliğini nasıl değerlendirirsiniz?",
                "Klinik karar destek sistemlerinin kullanımının yaygınlaştırılması kolay mı?",
                "Hastanenin tıbbi atık ve çevre yönetimi politikaları güncel standartlara uygun mu?",
                "Hastanenin bilimsel araştırma ve akademik faaliyetler için ayırdığı bütçe yeterli mi?",
                "Yeni tıbbi teknolojilerin adaptasyon süreçleri hızlı ve sorunsuz mu işliyor?",
                "Tıbbi hizmet sunumunda uluslararası en iyi uygulamaları takip ettiğinize inanıyor musunuz?",
                // Kategori 2: Personel Davranışları ve İletişim (11-20)
                "Çalışan memnuniyeti ve bağlılığı (engagement) seviyesi, hizmet kalitesini destekliyor mu?",
                "İç iletişim kanallarının (departmanlar arası) şeffaf ve etkili olduğunu düşünüyor musunuz?",
                "Personel performans değerlendirme ve geri bildirim sisteminin (KPI'lar) adil olduğuna inanıyor musunuz?",
                "Çalışan sirkülasyon oranlarını kabul edilebilir düzeyde tutabiliyor musunuz?",
                "Personel eğitim ve gelişim programlarının, hastanenin stratejik hedeflerine hizmet ettiğini düşünüyor musunuz?",
                "Etik Kurul ve şikayet yönetim mekanizmalarının etkin çalıştığına inanıyor musunuz?",
                "Yönetici olarak astlarınızla olan iletişiminizde kendinizi desteklenmiş hissediyor musunuz?",
                "Çalışanların, hastane vizyon ve misyonunu benimsediğini düşünüyor musunuz?",
                "Personel arasındaki iş birliği ve ekip ruhu kültürünü nasıl değerlendirirsiniz?",
                "İnsan Kaynakları süreçlerinin (işe alım, terfi) adil ve şeffaf olduğuna inanıyor musunuz?",
                // Kategori 3: Kurum Ortamı ve İmkanlar (21-30)
                "Bina ve tesis yönetiminin (temizlik, bakım) maliyet etkinliği ve kalitesinden memnun musunuz?",
                "Hastanenin fiziki kapasitesi (oda, yatak, ameliyathane) pazar talebini karşılıyor mu?",
                "Bilgi Teknolojileri (IT) altyapısının iş süreçlerinizdeki verimliliğe katkısı yeterli mi?",
                "Hastanenin finansal kaynaklarının (bütçe) şeffaf ve rasyonel kullanıldığına inanıyor musunuz?",
                "Tedarik zinciri ve lojistik süreçlerinin hastanenin ihtiyaçlarına hızla yanıt verdiğini düşünüyor musunuz?",
                "Yönetici olarak size tahsis edilen ofis/çalışma alanı ergonomik ve yeterli mi?",
                "Hastanenin afet ve acil durum planlarının güncel ve uygulanabilir olduğunu düşünüyor musunuz?",
                "Kurumun enerji ve su tüketimi gibi operasyonel maliyetleri kontrol altında tutuluyor mu?",
                "Hastane genelinde dijitalleşme ve otomasyon seviyesini nasıl değerlendirirsiniz?",
                "Hastanenin dış paydaşlarla (sigorta, devlet kurumları) ilişkileri olumlu ilerliyor mu?",
                // Kategori 4: Yönlendirme ve Bilgilendirme (31-40)
                "Kurumun stratejik hedeflerine ulaşma yol haritası net mi?",
                "Kurumsal performans göstergeleri (KPI) düzenli olarak izleniyor ve analiz ediliyor mu?",
                "Hastanenin mali tabloları ve performans raporları, karar verme süreçlerinize yeterince katkı sağlıyor mu?",
                "Yönetim kurulu toplantıları ve karar alma süreçleri verimli işliyor mu?",
                "Pazar araştırmaları ve iş zekası (BI) araçlarının karar verme süreçlerinize yeterince katkı sağladığını düşünüyor musunuz?",
                "Kurumsal yönetim ve yönlendirme kalitesini nasıl değerlendiriyorsunuz?",
                "Hastanenin pazarlama ve halkla ilişkiler faaliyetleri, marka bilinirliğini artırmada başarılı mı?",
                "Yasal düzenlemelere (KVKK, tıbbi mevzuat) uyum konusunda yeterli kaynak ayrılıyor mu?",
                "Tüm departmanların hedeflerinin, kurumun genel stratejisiyle uyumlu olduğuna inanıyor musunuz?",
                "Risk Yönetimi ve İç Denetim mekanizmalarının etkinliğini nasıl değerlendirirsiniz?",
                // Kategori 5: Genel Deneyim ve Tavsiye (41-50)
                "Hastanenin yönetim ekibinin genel başarısından memnun musunuz?",
                "Hastanenizin, sağlık sektöründeki diğer kurumlara göre rekabet avantajı olduğunu düşünüyor musunuz?",
                "Kendi kariyeriniz açısından bu hastanede çalışmayı meslektaşlarınıza tavsiye eder misiniz?",
                "Hastanenin finansal sürdürülebilirliğine olan güveniniz tam mı?",
                "Aldığınız yönetim ücreti ve teşviklerin sorumluluklarınızla orantılı olduğunu düşünüyor musunuz?",
                "Hastanenizin gelecekteki pazar payını artırma potansiyelini nasıl görüyorsunuz?",
                "Yönetim ekibinin yenilikçi fikirlere ve değişim önerilerine açık olduğuna inanıyor musunuz?",
                "Hastanenin, çalışanlara ve topluma karşı etik sorumluluklarını yerine getirdiğine inanıyor musunuz?",
                "Kurumun vizyonuna ulaşmak için yönetim ekibinin yeterli kararlılığa sahip olduğunu düşünüyor musunuz?",
                "Genel olarak yönetici olarak hastanenizi kaç puan üzerinden değerlendirirsiniz? (1-5)"
            ]
        };

        </script>
        
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

</body>
</html>
