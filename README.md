<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Akça Pro X - Kurum Değerlendirme Anketi</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
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
                <span class="bg-green-100 text-green-800 text-xs font-semibold px-2 py-1 rounded-full">v3.0.0 - JSONBin.io Entegre</span>
            </div>

            <!-- Sorumluluk Reddi -->
            <div id="disclaimerSection" class="mb-4">
                <div class="bg-yellow-50 border border-yellow-300 rounded p-3 mb-3">
                    <h3 class="font-semibold text-yellow-800 mb-2 text-sm">⚠️ Veri Koruma Beyanı</h3>
                    <div class="text-xs text-yellow-700 space-y-1">
                        <p>• Verileriniz JSONBin.io güvenli sisteminde saklanır ve üçüncü taraflarla paylaşılmaz.</p>
                        <p>• Anket sonuçları sadece kurum yetkilileri tarafından görüntülenebilir.</p>
                        <p>• Sistem güvenliği JSONBin.io sorumluluğundadır.</p>
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
                            <div>Doktor</div>
                        </button>
                        <button type="button" onclick="selectJobType('Personel')" id="staffBtn" 
                                class="job-btn py-3 px-2 text-xs rounded border-2 border-purple-300 hover:border-purple-500 hover:bg-purple-50 transition-all duration-200 cursor-pointer font-medium bg-white text-center focus:outline-none focus:ring-2 focus:ring-purple-400">
                            <div class="text-lg mb-1">👩‍🔬</div>
                            <div>Personel</div>
                        </button>
                    </div>
                </div>
                
                <div id="selectedJobDisplay" class="text-center text-sm text-gray-600 mb-3 min-h-[20px]">
                    <!-- Seçilen rol burada gösterilecek -->
                </div>
                
                <div class="grid grid-cols-2 gap-2 mb-4">
                    <input type="text" id="firstName" placeholder="Adınız (İsteğe bağlı)" 
                           class="border-2 border-gray-300 rounded px-3 py-2 text-sm focus:ring-2 focus:ring-purple-500 focus:border-purple-500">
                    <input type="text" id="lastName" placeholder="Soyadınız (İsteğe bağlı)" 
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
                    <input type="text" id="companyLoginName" placeholder="Okul/Kurum Adı" 
                           class="w-full border-2 border-gray-300 rounded-lg px-4 py-4 text-base focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    <input type="password" id="companyPassword" placeholder="12 Karakterlik Şifre" 
                           class="w-full border-2 border-gray-300 rounded-lg px-4 py-4 text-base focus:ring-2 focus:ring-blue-500 focus:border-blue-500" autocomplete="off">
                    <button onclick="loginCompany()" class="w-full py-4 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors text-lg font-semibold">
                        🔐 Giriş Yap
                    </button>
                </div>
                <div class="mt-6 p-4 bg-blue-50 rounded-lg text-sm text-blue-700">
                    <p><strong>Not:</strong> Okul/kurum şifrenizi yöneticinizden alabilirsiniz.</p>
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
                        <h3 class="text-xl font-semibold">Anket Sonuçları</h3>
                        <button onclick="showPDFReport()" class="px-6 py-3 bg-red-600 text-white rounded-xl hover:bg-red-700 font-semibold">
                            📄 PDF Göster
                        </button>
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
                        <h3 class="font-semibold text-blue-800 mb-2">Toplam Okul/Kurum</h3>
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
                    <h3 class="text-xl font-semibold mb-6">Okul/Kurum Listesi ve Yönetimi</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full table-auto">
                            <thead>
                                <tr class="bg-gray-50">
                                    <th class="px-4 py-3 text-left">Okul/Kurum Adı</th>
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

        // JSONBin.io konfigürasyonu
        const JSONBIN_CONFIG = {
            apiKey: '$2a$10$Vre/Nl1Aa1vrK2xY1NHYguabG45SOU1sMt3dnh.UJYpdBoQSdnz1.',
            accessKey: '$2a$10$SCDSdHz/rW/Z3Q6EWaB68uSJR2GAhE3pjG/i3.gJEhKsviO.yl6DC',
            binId: '68ce8ca8ae596e708ff4f3fc',
            baseUrl: 'https://api.jsonbin.io/v3',
            maxRetries: 3,
            retryDelay: 1000
        };

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
                // Çalışma Koşulları ve Ücretler (10 Soru)
                "Maaşınızın, sektör ortalamasına ve yaptığınız işe göre adil olduğunu düşünüyor musunuz?",
                "Çalışma saatlerinizin makul ve yönetilebilir olduğunu düşünüyor musunuz?",
                "Fazla mesai ücretlerinin adil bir şekilde ödendiğine inanıyor musunuz?",
                "Hastanenin sunduğu yan haklardan (sağlık sigortası, yemek vb.) memnun musunuz?",
                "Çalışma ortamınızın fiziksel koşullarının (temizlik, aydınlatma) yeterli olduğunu düşünüyor musunuz?",
                "İşinizde kullandığınız araç ve ekipmanların yeterli olduğuna inanıyor musunuz?",
                "Hastanenin iş güvenliği ve sağlığına yönelik önlemlerini yeterli buluyor musunuz?",
                "İzin kullanma süreçlerinin kolay ve adil olduğunu düşünüyor musunuz?",
                "Hastanenin genel hijyen standartlarından memnun musunuz?",
                "Aldığınız ücret ve yan haklardan genel olarak memnun musunuz?",
                // Yönetim ve Takım İlişkileri (10 Soru)
                "Yöneticinizin, size karşı saygılı ve adil davrandığına inanıyor musunuz?",
                "Yöneticinizden geri bildirim almanın yapıcı ve yol gösterici olduğunu düşünüyor musunuz?",
                "Yöneticinizle iletişim kanallarınızın açık ve etkili olduğunu düşünüyor musunuz?",
                "Yönetimin, çalışanların fikirlerine ve önerilerine değer verdiğine inanıyor musunuz?",
                "Meslektaşlarınızla olan iş birliğinizin verimli ve pozitif olduğunu düşünüyor musunuz?",
                "Hastanedeki farklı birimler arasında iyi bir koordinasyon ve iş birliği olduğuna inanıyor musunuz?",
                "Yöneticinizin, işinizde size yeterli özerkliği tanıdığını düşünüyor musunuz?",
                "Yönetimin, başarılarınızı ve çabalarınızı takdir ettiğine inanıyor musunuz?",
                "Yönetimle olan ilişkinizin genel olarak güvene dayalı olduğunu düşünüyor musunuz?",
                "Hastane yönetiminin, çalışan memnuniyetini önemsediğine inanıyor musunuz?",
                // İş Yükü ve Verimlilik (10 Soru)
                "İş yükünüzün, işin kalitesini düşürmeyecek kadar makul olduğunu düşünüyor musunuz?",
                "Hastanede çalışan sayısının, işin gerekliliklerini karşılayacak kadar yeterli olduğuna inanıyor musunuz?",
                "İş süreçlerinizin verimli ve düzenli olduğunu düşünüyor musunuz?",
                "Hasta bilgilerine ve kayıtlarına kolayca erişebildiğinize inanıyor musunuz?",
                "Yaptığınız işin, hastaların yaşamına olumlu katkı sağladığını düşünüyor musunuz?",
                "Çalışma ortamınızın, odaklanmanıza ve verimli olmanıza yardımcı olduğunu düşünüyor musunuz?",
                "İş süreçlerinin dijitalleşmesinin, iş yükünüzü hafiflettiğine inanıyor musunuz?",
                "İşinizi yapmak için yeterli zaman ve kaynağa sahip olduğunuzu düşünüyor musunuz?",
                "Hastaların ihtiyaçlarını karşılamak için yeterli yetkiye sahip olduğunuza inanıyor musunuz?",
                "Yaptığınız işten genel olarak memnun musunuz?",
                // Eğitim ve Kariyer Fırsatları (10 Soru)
                "Hastanenin, mesleki gelişiminize yönelik eğitim ve seminerler sunduğunu düşünüyor musunuz?",
                "Hastanedeki kariyer ve terfi olanaklarının adil ve şeffaf olduğuna inanıyor musunuz?",
                "Yönetimin, yeni beceriler kazanmanız için size destek verdiğini düşünüyor musunuz?",
                "Hastanenin, çalışanların kişisel gelişimine önem verdiğine inanıyor musunuz?",
                "İşinizde yeni şeyler öğrenme ve kendinizi geliştirme fırsatlarınızın yeterli olduğunu düşünüyor musunuz?",
                "Hastanenin, çalışanların potansiyelini keşfetme ve kullanma konusunda yardımcı olduğuna inanıyor musunuz?",
                "Aldığınız eğitimlerin, iş performansınızı artırdığına inanıyor musunuz?",
                "Kariyer hedeflerinize ulaşmanız için hastanenin size rehberlik ettiğini düşünüyor musunuz?",
                "Hastanenin, çalışanların yeteneklerini ve deneyimlerini takdir ettiğine inanıyor musunuz?",
                "Hastane içinde yeni bir pozisyona başvurma süreçlerinin kolay ve adil olduğunu düşünüyor musunuz?",
                // Hasta Hizmeti ve Etik (10 Soru)
                "Hastaların memnuniyetini artırmaya yönelik hastane politikalarını başarılı buluyor musunuz?",
                "Hasta haklarına ve etik kurallara hastanenin yeterince önem verdiğine inanıyor musunuz?",
                "Hastaların şikayetlerinin ciddiye alındığını ve çözüme kavuşturulduğunu düşünüyor musunuz?",
                "Hasta güvenliğinin, en büyük öncelik olduğuna inanıyor musunuz?",
                "Hastalarla olan iletişiminizin verimli ve empatik olduğunu düşünüyor musunuz?",
                "Hastane personelinin, hastalara karşı saygılı ve yardımsever davrandığına inanıyor musunuz?",
                "Hastane içinde şeffaflık ve dürüstlüğün ön planda olduğuna inanıyor musunuz?",
                "Yaptığınız işin hastaların yaşam kalitesine olumlu katkı sağladığını düşünüyor musunuz?",
                "Hastanenin, hasta hizmetleri konusunda sürekli iyileştirme çabası içinde olduğuna inanıyor musunuz?",
                "Hastaların hastanede geçirdikleri süreden genel olarak memnun kaldıklarına inanıyor musunuz?"
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
            loadDemoData();
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

        function selectJobType(jobType) {
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
            
            // Seçili butonu işaretle
            const buttonMap = {
                'Hasta': 'patientBtn',
                'Doktor': 'doctorBtn', 
                'Personel': 'staffBtn'
            };
            
            const selectedButton = document.getElementById(buttonMap[jobType]);
            if (selectedButton) {
                selectedButton.style.border = '3px solid #3b82f6';
                selectedButton.style.backgroundColor = '#3b82f6';
                selectedButton.style.color = 'white';
                selectedButton.style.fontWeight = 'bold';
                selectedButton.style.transform = 'scale(1.05)';
                selectedButton.style.boxShadow = '0 4px 12px rgba(59, 130, 246, 0.4)';
                selectedButton.classList.add('selected-job');
            }
            
            // Seçimi göster
            const displayElement = document.getElementById('selectedJobDisplay');
            if (displayElement) {
                displayElement.innerHTML = `<span class="text-blue-600 font-semibold text-lg">✓ Seçilen rol: ${jobType}</span>`;
            }
        }

        function startSurvey() {
            console.log('Anket başlatma fonksiyonu çalışıyor...');
            
            const companyName = document.getElementById('companyName').value.trim();
            const disclaimerAccepted = document.getElementById('acceptDisclaimer').checked;
            
            console.log('Form verileri:', { companyName, selectedJobType, disclaimerAccepted });
            
            if (!disclaimerAccepted) {
                showModal('⚠️ Uyarı', 'Devam etmek için veri koruma beyanını kabul etmelisiniz.');
                return;
            }
            
            if (!companyName) {
                showModal('⚠️ Eksik Bilgi', 'Lütfen kurum adını girin.');
                return;
            }
            
            if (!selectedJobType) {
                showModal('⚠️ Eksik Bilgi', 'Lütfen rolünüzü seçin (Hasta, Doktor veya Personel).');
                return;
            }
            
            // Seçilen role göre soruları al
            currentQuestions = questions[selectedJobType];
            console.log('Seçilen rol:', selectedJobType);
            console.log('Sorular:', currentQuestions);
            
            if (!currentQuestions || currentQuestions.length === 0) {
                showModal('❌ Hata', 'Seçilen rol için sorular bulunamadı. Lütfen sayfayı yenileyip tekrar deneyin.');
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
                            <div class="text-2xl mb-2">😞</div>
                            <div class="text-sm font-medium">Hiç Memnun Değilim</div>
                        </button>
                        <button onclick="selectAnswer(2)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-orange-200 hover:border-orange-400 hover:bg-orange-50 transition-all duration-200 text-center">
                            <div class="text-2xl mb-2">😐</div>
                            <div class="text-sm font-medium">Memnun Değilim</div>
                        </button>
                        <button onclick="selectAnswer(3)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-yellow-200 hover:border-yellow-400 hover:bg-yellow-50 transition-all duration-200 text-center">
                            <div class="text-2xl mb-2">😊</div>
                            <div class="text-sm font-medium">Kararsızım</div>
                        </button>
                        <button onclick="selectAnswer(4)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-green-200 hover:border-green-400 hover:bg-green-50 transition-all duration-200 text-center">
                            <div class="text-2xl mb-2">😄</div>
                            <div class="text-sm font-medium">Memnunum</div>
                        </button>
                        <button onclick="selectAnswer(5)" class="answer-btn py-4 px-3 text-base rounded-lg border-2 border-blue-200 hover:border-blue-400 hover:bg-blue-50 transition-all duration-200 text-center">
                            <div class="text-2xl mb-2">🤩</div>
                            <div class="text-sm font-medium">Çok Memnunum</div>
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

        async function createCompanyIfNotExists(companyName) {
            if (!systemData.surveyData) await loadFromJSONBin();
            if (!systemData.surveyData.companies) systemData.surveyData.companies = {};
            let companyKey = Object.keys(systemData.surveyData.companies).find(key => systemData.surveyData.companies[key].name === companyName);
            if (!companyKey) {
                // Yeni şifre üret
                const password = generateCompanyPassword();
                companyKey = Date.now().toString();
                systemData.surveyData.companies[companyKey] = { name: companyName, password, createdAt: new Date().toISOString() };
                const saveResult = await saveToJSONBin(systemData.surveyData);
                if (!saveResult.success) {
                    return { success: false, error: saveResult.error };
                }
            }
            return { success: true, key: companyKey };
        }

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
                
                const companyResult = await createCompanyIfNotExists(companyName);
                
                if (!companyResult.success) {
                    throw new Error(`Kurum işlemi başarısız: ${companyResult.error}`);
                }
                
                systemData.surveyData = await loadFromJSONBin();
                
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
                
                const saveResult = await saveToJSONBin(systemData.surveyData);
                
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

        async function loginCompany() {
            const companyName = document.getElementById('companyLoginName').value.trim();
            const password = document.getElementById('companyPassword').value.trim();
            
            if (!companyName || !password) {
                showModal('⚠️ Eksik Bilgi', 'Lütfen kurum adı ve şifrenizi girin.');
                return;
            }
            
            try {
                if (!systemData.surveyData) {
                    systemData.surveyData = await loadFromJSONBin();
                }
                
                const companyEntry = Object.entries(systemData.surveyData.companies || {})
                    .find(([key, company]) => 
                        company.name.toLowerCase() === companyName.toLowerCase() && 
                        company.password === password
                    );
                
                if (companyEntry) {
                    loggedInCompany = {
                        key: companyEntry[0],
                        ...companyEntry[1]
                    };
                    document.getElementById('companyLogin').classList.add('hidden');
                    document.getElementById('companyDashboard').classList.remove('hidden');
                    loadCompanyDashboard();
                } else {
                    showModal('❌ Giriş Hatası', 'Okul/kurum adı veya şifre hatalı. Lütfen yöneticinizden doğru bilgileri alın.');
                }
            } catch (error) {
                showModal('❌ Hata', 'Giriş sırasında bir hata oluştu. Lütfen tekrar deneyin.');
                console.error('Giriş hatası:', error);
            }
        }

        function loadCompanyDashboard() {
            if (!loggedInCompany || !systemData.surveyData) return;
            
            document.getElementById('companyNameDisplay').textContent = loggedInCompany.name;
            
            const companySurveys = systemData.surveyData.responses.filter(s => 
                s.companyName.toLowerCase() === loggedInCompany.name.toLowerCase()
            );
            
            document.getElementById('totalParticipants').textContent = companySurveys.length;
            
            if (companySurveys.length > 0) {
                let totalScore = 0;
                let totalAnswers = 0;
                companySurveys.forEach(s => {
                    totalScore += s.totalScore;
                    totalAnswers += s.answers.length;
                });
                const avgScore = totalAnswers > 0 ? (totalScore / totalAnswers).toFixed(1) : '0.0';
                document.getElementById('averageScore').textContent = avgScore;
                
                let highSatisfactionAnswers = 0;
                companySurveys.forEach(s => {
                    s.answers.forEach(answer => {
                        if (answer.score >= 4) highSatisfactionAnswers++;
                    });
                });
                const overallSatisfactionPercent = totalAnswers > 0 ? 
                    Math.round((highSatisfactionAnswers / totalAnswers) * 100) : 0;
                document.getElementById('satisfactionRate').textContent = overallSatisfactionPercent + '%';
            } else {
                document.getElementById('averageScore').textContent = '0.0';
                document.getElementById('satisfactionRate').textContent = '0%';
            }
            
            generateSimpleReport(companySurveys);
            generateCharts(companySurveys);
        }

        // PDF ve rapor başlıkları için yeni roller ve başlıklar
        const roleLabels = {
            "Hasta": "Hasta",
            "Doktor": "Doktor",
            "Personel": "Personel"
        };
        const categoryLabels = [
            { title: "🏥 1. Tıbbi Hizmet Kalitesi", desc: "Bu başlık, hastanede sunulan tıbbi hizmetlerin güvenilirliğini, tedavi süreçlerinin şeffaflığını ve hasta güvenliğini ölçer." },
            { title: "🧑‍⚕️ 2. Personel Davranışları ve İletişim", desc: "Sağlık personelinin iletişimi, yaklaşımı ve hasta ile etkileşimi bu kategorinin temelini oluşturur." },
            { title: "🏨 3. Hastane Ortamı ve İmkanlar", desc: "Fiziksel ortam, temizlik, konfor ve hastane imkanlarının kalitesi değerlendirilir." },
            { title: "🗺️ 4. Yönlendirme ve Bilgilendirme", desc: "Hastane içi yönlendirme, bilgilendirme süreçleri ve hasta hakları konusundaki şeffaflık ölçülür." },
            { title: "🌟 5. Genel Deneyim ve Tavsiye", desc: "Hastaların genel memnuniyeti, tekrar tercih etme ve tavsiye etme eğilimleri analiz edilir." }
        ];

        function generateSimpleReport(surveys) {
            if (surveys.length === 0) {
                document.getElementById('detailedReport').innerHTML = '<p class="text-gray-500 text-center py-8 text-lg">Henüz anket verisi bulunmuyor.</p>';
                return;
            }
            const positionData = {};
            surveys.forEach(s => {
                if (validRoles.includes(s.jobType)) {
                    positionData[s.jobType] = (positionData[s.jobType] || 0) + 1;
                }
            });
            
            const satisfactionLevels = ['Düşük (1-2)', 'Orta (3)', 'Yüksek (4-5)'];
            const satisfactionCounts = [0, 0, 0];
            
            surveys.forEach(s => {
                const avgScore = parseFloat(s.averageScore);
                if (avgScore < 2.5) satisfactionCounts[0]++;
                else if (avgScore >= 2.5 && avgScore < 3.5) satisfactionCounts[1]++;
                else satisfactionCounts[2]++;
            });
            
            // Katılımcı grubu dışında bir şey varsa gösterme
            const report = `
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div class="bg-blue-50 p-6 rounded-lg">
                        <h4 class="font-semibold text-blue-800 mb-4 text-lg">👥 Katılımcı Grupları</h4>
                        ${Object.entries(positionData).map(([pos, count]) =>
                            `<div class="flex justify-between items-center mb-2">
                                <span class="font-medium">${pos}</span>
                                <span class="text-xl font-bold">${count}</span>
                            </div>`
                        ).join('')}
                    </div>
                    <div class="bg-green-50 p-6 rounded-lg">
                        <h4 class="font-semibold text-green-800 mb-4 text-lg">📊 Değerlendirme Seviyeleri</h4>
                        ${satisfactionLevels.map((level, i) => 
                            `<div class="flex justify-between items-center mb-2">
                                <span class="font-medium">${level}</span>
                                <span class="text-xl font-bold">${satisfactionCounts[i]}</span>
                            </div>`
                        ).join('')}
                    </div>
                </div>
                
                <div class="mt-6 bg-gray-50 p-6 rounded-lg">
                    <h4 class="font-semibold text-gray-800 mb-3 text-lg">📈 Özet</h4>
                    <p class="text-base text-gray-700">
                        Toplam ${surveys.length} hasta anketi tamamladı. 
                        Ortalama değerlendirme skoru ${(surveys.reduce((sum, s) => sum + parseFloat(s.averageScore), 0) / surveys.length).toFixed(1)}/5.0 olarak hesaplandı.
                    </p>
                </div>
            `;
            
            document.getElementById('detailedReport').innerHTML = report;
        }

        // Özet bölümünde sadece geçerli rolü yaz
        function getSummaryText(surveys) {
            if (!surveys || surveys.length === 0) return '';
            const last = surveys[surveys.length - 1];
            if (!validRoles.includes(last.jobType)) return '';
            const avg = last.averageScore ? parseFloat(last.averageScore).toFixed(1) : '';
            return `Toplam 1 ${last.jobType.toLowerCase()} anketi tamamlandı. Ortalama değerlendirme skoru ${avg}/5.0 olarak hesaplandı.`;
        }

        // PDF çıktısı için başlık ve grup isimleri güncellendi, sadece geçerli roller dahil edildi.
        function generateAdminPDFContent(companyName, surveys) {
            const totalParticipants = surveys.length;
            
            let totalScore = 0;
            let totalAnswers = 0;
            surveys.forEach(s => {
                totalScore += s.totalScore;
                totalAnswers += s.answers.length;
            });
            const avgScore = totalAnswers > 0 ? (totalScore / totalAnswers).toFixed(1) : '0.0';
            
            // Profesyonel memnuniyet yüzdesi hesaplama (50-250 puan arası)
            // Formül: ((Alınan Puan - Minimum Puan) / (Maksimum Puan - Minimum Puan)) * 100
            const minPossibleScore = totalAnswers * 1; // Her soru minimum 1 puan
            const maxPossibleScore = totalAnswers * 5; // Her soru maksimum 5 puan
            const satisfactionPercentage = totalAnswers > 0 ? 
                Math.round(((totalScore - minPossibleScore) / (maxPossibleScore - minPossibleScore)) * 100) : 0;
            
            const positionData = {};
            const positionScores = {};
            surveys.forEach(s => {
                positionData[s.jobType] = (positionData[s.jobType] || 0) + 1;
                if (!positionScores[s.jobType]) positionScores[s.jobType] = [];
                positionScores[s.jobType].push(parseFloat(s.averageScore));
            });
            
            // Pozisyon bazlı memnuniyet yüzdeleri
            const positionSatisfaction = {};
            Object.keys(positionScores).forEach(pos => {
                const avgPosScore = positionScores[pos].reduce((a, b) => a + b, 0) / positionScores[pos].length;
                positionSatisfaction[pos] = Math.round(((avgPosScore - 1) / 4) * 100);
            });
            
            // Durum analizi
            let statusAnalysis = '';
            let recommendations = '';
            
            if (satisfactionPercentage <= 50) {
                statusAnalysis = 'Düşük Memnuniyet - Acil Müdahale Gerekli';
                recommendations = 'Acil bir eylem planı oluşturulmalıdır. Hastanenin fiziki koşulları ve temel iletişim kanalları gözden geçirilmelidir. Hastalar, doktorlar ve personel ile düzenli toplantılar düzenlenerek çözüm süreçleri şeffaf bir şekilde paylaşılmalıdır.';
            } else if (satisfactionPercentage <= 75) {
                statusAnalysis = 'Orta Seviye Memnuniyet - İyileştirme Fırsatları';
                recommendations = 'Gelecek odaklı bir strateji belirlenmelidir. Hastanenin dijital dönüşüm stratejisi tüm paydaşlara net bir şekilde duyurulmalı ve bu alandaki yatırımlar artırılmalıdır. Doktorlar ve personel için profesyonel gelişim programları hayata geçirilmelidir.';
            } else {
                statusAnalysis = 'Yüksek Memnuniyet - Sürdürülebilirlik Odaklı';
                recommendations = 'Bu başarıyı sürdürmek için düzenli nabız anketleri yapılmalı ve paydaşların beklentileri sürekli takip edilmelidir. En güçlü olduğunuz alanlarda bile sürekli iyileştirme hedefleri belirlenmelidir.';
            }
            
            const satisfactionCounts = [0, 0, 0];
            surveys.forEach(s => {
                s.answers.forEach(answer => {
                    if (answer.score <= 2) satisfactionCounts[0]++;
                    else if (answer.score === 3) satisfactionCounts[1]++;
                    else satisfactionCounts[2]++;
                });
            });
            
            return `
                <!DOCTYPE html>
                <html>
                <head>
                    <meta charset="UTF-8">
                    <title>${companyName} - Yönetici Raporu</title>
                    <style>
                        body { font-family: Arial, sans-serif; margin: 15px; line-height: 1.4; font-size: 12px; }
                        .header { text-align: center; border-bottom: 2px solid #333; padding-bottom: 15px; margin-bottom: 20px; }
                        .stats { display: flex; justify-content: space-between; margin-bottom: 20px; }
                        .stat-box { background: #f5f5f5; padding: 10px; border-radius: 5px; text-align: center; width: 30%; }
                        .stat-number { font-size: 1.5em; font-weight: bold; color: #333; }
                        .section { margin-bottom: 20px; }
                        .section h3 { color: #333; border-bottom: 1px solid #ddd; padding-bottom: 5px; margin-bottom: 10px; }
                        table { width: 100%; border-collapse: collapse; margin-top: 5px; font-size: 11px; }
                        th, td { border: 1px solid #ddd; padding: 5px; text-align: left; }
                        th { background-color: #f2f2f2; }
                        .analysis-box { background: #e8f4fd; padding: 10px; border-radius: 5px; margin: 10px 0; }
                        .recommendations { background: #fff3cd; padding: 10px; border-radius: 5px; margin: 10px 0; }
                        .chart-placeholder { width: 100%; height: 150px; background: #f8f9fa; border: 1px solid #ddd; display: flex; align-items: center; justify-content: center; margin: 10px 0; }
                        .footer { margin-top: 30px; text-align: center; font-size: 10px; color: #666; }
                    </style>
                </head>
                <body>
                    <div class="header">
                        <h1>📊 ${companyName}</h1>
                        <h2>Yönetici Kurum Değerlendirme Raporu</h2>
                        <p>Rapor Tarihi: ${new Date().toLocaleDateString('tr-TR')}</p>
                    </div>
                    
                    <div class="stats">
                        <div class="stat-box">
                            <div class="stat-number">${totalParticipants}</div>
                            <div>Toplam Katılımcı</div>
                        </div>
                        <div class="stat-box">
                            <div class="stat-number">${avgScore}</div>
                            <div>Ortalama Puan</div>
                        </div>
                        <div class="stat-box">
                            <div class="stat-number">${satisfactionPercentage}%</div>
                            <div>Genel Memnuniyet</div>
                        </div>
                    </div>
                    
                    <div class="analysis-box">
                        <h4>📈 Genel Durum Değerlendirmesi</h4>
                        <div class="status-indicator" style="background-color: ${statusColor};">${statusAnalysis}</div>
                        <p style="margin-top: 10px;"><strong>Memnuniyet Hesaplama Formülü:</strong> ((${totalScore} - ${minPossibleScore}) / (${maxPossibleScore} - ${minPossibleScore})) × 100 = %${satisfactionPercentage}</p>
                        <p style="margin-top: 5px;">${detailedAnalysis}</p>
                    </div>
                    
                    <div class="section">
                        <h3>👥 Paydaş Grupları Analizi</h3>
                        <table>
                            <tr><th>Paydaş Grubu</th><th>Katılımcı</th><th>Memnuniyet %</th><th>Değerlendirme</th></tr>
                            ${Object.entries(positionData).map(([pos, count]) => {
                                const satisfaction = positionSatisfaction[pos] || 0;
                                const status = satisfaction <= 50 ? 'Düşük' : satisfaction <= 75 ? 'Orta' : 'Yüksek';
                                const statusColor = satisfaction <= 50 ? '#dc3545' : satisfaction <= 75 ? '#ffc107' : '#28a745';
                                return `<tr><td>${pos}</td><td>${count}</td><td>%${satisfaction}</td><td style="color: ${statusColor}; font-weight: bold;">${status}</td></tr>`;
                            }).join('')}
                        </table>
                    </div>
                    
                    ${comparisonAnalysis ? `<div class="comparison-box">
                        <h4>🔍 Paydaş Grupları Karşılaştırması</h4>
                        <p>${comparisonAnalysis}</p>
                        ${specialScenarioAnalysis ? `<div style="margin-top: 10px; padding: 10px; background: #fff3cd; border-radius: 5px; border-left: 4px solid #ffc107;">
                            <strong>⚠️ Özel Durum Analizi:</strong><br>
                            ${specialScenarioAnalysis}
                        </div>` : ''}
                    </div>` : ''}
                    
                    <div class="chart-placeholder">
                        <div style="text-align: center;">
                            <div style="font-size: 14px; margin-bottom: 10px;">📊 Memnuniyet Dağılımı</div>
                            ${Object.entries(positionData).map(([pos, count]) => 
                                `<div style="margin: 3px 0; font-size: 11px;">${pos}: ${count} kişi (%${positionSatisfaction[pos] || 0} memnuniyet)</div>`
                            ).join('')}
                        </div>
                    </div>
                    
                    <div class="section">
                        <h3>📈 Yanıt Dağılımı</h3>
                        <table>
                            <tr><th>Değerlendirme Seviyesi</th><th>Yanıt Sayısı</th><th>Oran</th></tr>
                            <tr><td>Düşük Memnuniyet (1-2)</td><td>${satisfactionCounts[0]}</td><td>${totalAnswers > 0 ? Math.round((satisfactionCounts[0]/totalAnswers)*100) : 0}%</td></tr>
                            <tr><td>Orta Memnuniyet (3)</td><td>${satisfactionCounts[1]}</td><td>${totalAnswers > 0 ? Math.round((satisfactionCounts[1]/totalAnswers)*100) : 0}%</td></tr>
                            <tr><td>Yüksek Memnuniyet (4-5)</td><td>${satisfactionCounts[2]}</td><td>${totalAnswers > 0 ? Math.round((satisfactionCounts[2]/totalAnswers)*100) : 0}%</td></tr>
                        </table>
                    </div>
                    
                    <div class="section">
                        <h3>🎯 Detaylı Kategori Analizleri</h3>
                        
                        <div style="margin-bottom: 15px; padding: 10px; background: #f8f9fa; border-radius: 5px;">
                            <h4 style="color: #333; margin-bottom: 8px;">📚 1. Tıbbi Hizmet Kalitesi</h4>
                            <p style="font-size: 11px; margin-bottom: 5px;"><strong>Tanım:</strong> Bu başlık, hastanede sunulan tıbbi hizmetlerin güvenilirliğini, tedavi süreçlerinin şeffaflığını ve hasta güvenliğini ölçer.</p>
                            
                            ${satisfactionPercentage <= 50 ? `
                            <div style="background: #f8d7da; padding: 8px; border-radius: 3px; border-left: 4px solid #dc3545;">
                                <strong>Puan Aralığı: Düşük (%0-50)</strong><br>
                                Hastalar, tedavi süreçlerinin belirsiz olduğunu, doktorların yetersiz bilgi verdiğini veya tıbbi hatalar yapıldığını düşünüyor. Bu durum, hastane için ciddi bir güven kaybı yaratır ve acil önlem alınması gerektiğini gösterir. Tıbbi süreçler gözden geçirilmeli, hasta bilgilendirme standartları artırılmalı ve düzenli kalite kontrolü yapılmalıdır.
                            </div>
                            ` : satisfactionPercentage <= 75 ? `
                            <div style="background: #fff3cd; padding: 8px; border-radius: 3px; border-left: 4px solid #ffc107;">
                                <strong>Puan Aralığı: Orta (%51-75)</strong><br>
                                Tıbbi hizmet kalitesi genel olarak kabul edilebilir düzeyde, ancak bazı alanlarda (örneğin, acil servisler, özel tedavi yöntemleri) iyileştirme potansiyeli var. Hastane, hasta güvenliğini artırmak ve tedavi süreçlerini daha şeffaf hale getirmek için ek önlemler almalıdır. Ayrıca, hasta geri bildirimlerine dayalı düzenli hizmet değerlendirmeleri yapılmalıdır.
                            </div>
                            ` : `
                            <div style="background: #d4edda; padding: 8px; border-radius: 3px; border-left: 4px solid #28a745;">
                                <strong>Puan Aralığı: Yüksek (%76-100)</strong><br>
                                Hastalar, aldıkları tıbbi hizmetlerin kalitesinden son derece memnun. Bu, hastanenin tıbbi alanda yüksek standartlara sahip olduğunu ve hasta odaklı bir yaklaşım benimsediğini gösterir. Bu başarıyı sürdürmek için sürekli eğitimler, güncel tıbbi protokoller ve hasta memnuniyeti anketleri ile kalite güvencesi sağlanmalıdır.
                            </div>
                            `}
                        </div>
                        
                        <div style="margin-bottom: 15px; padding: 10px; background: #f8f9fa; border-radius: 5px;">
                            <h4 style="color: #333; margin-bottom: 8px;">🧑‍⚕️ 2. Personel Davranışları ve İletişim</h4>
                            <p style="font-size: 11px; margin-bottom: 5px;"><strong>Tanım:</strong> Sağlık personelinin iletişimi, yaklaşımı ve hasta ile etkileşimi bu kategorinin temelini oluşturur.</p>
                            
                            ${satisfactionPercentage <= 50 ? `
                            <div style="background: #f8d7da; padding: 8px; border-radius: 3px; border-left: 4px solid #dc3545;">
                                <strong>Puan Aralığı: Düşük (%0-50)</strong><br>
                                Personelin hasta haklarına saygı göstermediği, iletişimde yetersiz kaldığı veya olumsuz tutum sergilediği düşünülüyor. Bu durum, hastaların kendilerini değersiz hissetmelerine ve tedavi süreçlerine güven duymamalarına yol açar. Personel eğitimi acilen gözden geçirilmeli, hasta iletişimi ve empati konularında güçlendirilmelidir.
                            </div>
                            ` : satisfactionPercentage <= 75 ? `
                            <div style="background: #fff3cd; padding: 8px; border-radius: 3px; border-left: 4px solid #ffc107;">
                                <strong>Puan Aralığı: Orta (%51-75)</strong><br>
                                Personel davranışları genelde olumlu, ancak bazı durumlarda (örneğin, yoğun bakım, acil servis) iletişim eksiklikleri veya yetersiz bilgi verme gibi sorunlar yaşanabiliyor. Bu tür durumların azaltılması için, stresli durumlar için özel iletişim protokolleri geliştirilmeli ve personel bu konularda düzenli olarak eğitilmelidir.
                            </div>
                            ` : `
                            <div style="background: #d4edda; padding: 8px; border-radius: 3px; border-left: 4px solid #28a745;">
                                <strong>Puan Aralığı: Yüksek (%76-100)</strong><br>
                                Hastalar, sağlık personelinin yaklaşımından ve iletişiminden son derece memnun. Personel, hastalara karşı nazik, saygılı ve yardımsever davranıyor. Bu, hastane ortamının ne kadar destekleyici ve hasta odaklı olduğunu gösteriyor. Bu başarıyı sürdürmek için, personelin motivasyonu yüksek tutulmalı ve başarılı iletişim örnekleri teşvik edilmelidir.
                            </div>
                            `}
                        </div>
                        
                        <div style="margin-bottom: 15px; padding: 10px; background: #f8f9fa; border-radius: 5px;">
                            <h4 style="color: #333; margin-bottom: 8px;">🏨 3. Hastane Ortamı ve İmkanlar</h4>
                            <p style="font-size: 11px; margin-bottom: 5px;"><strong>Tanım:</strong> Fiziksel ortam, temizlik, konfor ve hastane imkanlarının kalitesi değerlendirilir.</p>
                            
                            ${satisfactionPercentage <= 50 ? `
                            <div style="background: #f8d7da; padding: 8px; border-radius: 3px; border-left: 4px solid #dc3545;">
                                <strong>Puan Aralığı: Düşük (%0-50)</strong><br>
                                Hastane ortamının hijyenik olmadığı, odaların konforsuz ve gürültülü olduğu, tıbbi ekipmanların yetersiz veya eski olduğu düşünülüyor. Bu durum, hastaların tedavi süreçlerinde olumsuz etkiler yaratabilir. Acil olarak, hastane fiziki koşulları gözden geçirilmeli, temizlik standartları artırılmalı ve hasta konforunu artırıcı önlemler alınmalıdır.
                            </div>
                            ` : satisfactionPercentage <= 75 ? `
                            <div style="background: #fff3cd; padding: 8px; border-radius: 3px; border-left: 4px solid #ffc107;">
                                <strong>Puan Aralığı: Orta (%51-75)</strong><br>
                                Hastane ortamı genel olarak kabul edilebilir düzeyde, ancak bazı alanlarda (örneğin, tuvaletler, bekleme alanları) iyileştirme yapılması gerekiyor. Ayrıca, yemek hizmetleri ve ulaşım imkanları gibi konularda da geliştirmeler yapılabilir. Hastane yönetimi, bu alanlarda hasta geri bildirimlerini dikkate alarak iyileştirmeler yapmalıdır.
                            </div>
                            ` : `
                            <div style="background: #d4edda; padding: 8px; border-radius: 3px; border-left: 4px solid #28a745;">
                                <strong>Puan Aralığı: Yüksek (%76-100)</strong><br>
                                Hastane, fiziksel ortam ve imkanlar açısından yüksek standartlara sahip. Temizlik, konfor ve güvenlik önlemleri üst düzeyde. Bu, hastanenin hasta memnuniyetine verdiği önemi ve hizmet kalitesini gösteriyor. Bu başarıyı sürdürmek için, düzenli bakım ve yenileme çalışmaları yapılmalı, hasta memnuniyeti anketleri ile sürekli geri bildirim alınmalıdır.
                            </div>
                            `}
                        </div>
                        
                        <div style="margin-bottom: 15px; padding: 10px; background: #f8f9fa; border-radius: 5px;">
                            <h4 style="color: #333; margin-bottom: 8px;">🗺️ 4. Yönlendirme ve Bilgilendirme</h4>
                            <p style="font-size: 11px; margin-bottom: 5px;"><strong>Tanım:</strong> Hastane içi yönlendirme, bilgilendirme süreçleri ve hasta hakları konusundaki şeffaflık ölçülür.</p>
                            
                            ${satisfactionPercentage <= 50 ? `
                            <div style="background: #f8d7da; padding: 8px; border-radius: 3px; border-left: 4px solid #dc3545;">
                                <strong>Puan Aralığı: Düşük (%0-50)</strong><br>
                                Hastalar, hastaneye yatış, taburcu işlemleri ve randevu sistemleri gibi konularda yetersiz bilgilendirildiğini düşünüyor. Ayrıca, hasta hakları konusunda da yeterli bilgiye sahip olmadıklarını belirtiyorlar. Bu durum, hastaların tedavi süreçlerinde belirsizlik yaşamasına ve haklarını tam olarak bilememesine yol açıyor. Hastane, bilgilendirme süreçlerini gözden geçirmeli, hasta hakları konusunda daha şeffaf ve erişilebilir olmalıdır.
                            </div>
                            ` : satisfactionPercentage <= 75 ? `
                            <div style="background: #fff3cd; padding: 8px; border-radius: 3px; border-left: 4px solid #ffc107;">
                                <strong>Puan Aralığı: Orta (%51-75)</strong><br>
                                Yönlendirme ve bilgilendirme süreçleri genel olarak iyi, ancak bazı durumlarda (örneğin, acil durumlar, yoğun bakım) yetersiz kalabiliyor. Hastane, bu tür durumlar için özel protokoller geliştirmeli ve personeli bu konularda eğitmelidir. Ayrıca, hasta bilgilendirme materyalleri güncellenmeli ve hastaların kolayca erişebileceği yerlere konulmalıdır.
                            </div>
                            ` : `
                            <div style="background: #d4edda; padding: 8px; border-radius: 3px; border-left: 4px solid #28a745;">
                                <strong>Puan Aralığı: Yüksek (%76-100)</strong><br>
                                Hastalar, hastane içindeki yönlendirme ve bilgilendirme süreçlerinden son derece memnun. Tüm bilgilere kolayca erişebiliyor ve hakları konusunda yeterli bilgiye sahip olduklarını düşünüyorlar. Bu, hastanenin şeffaflık ve hasta memnuniyeti konusundaki başarısını gösteriyor. Bu başarıyı sürdürmek için, düzenli olarak hasta geri bildirimleri alınmalı ve bilgilendirme süreçleri sürekli olarak iyileştirilmelidir.
                            </div>
                            `}
                        </div>
                        
                        <div style="margin-bottom: 15px; padding: 10px; background: #f8f9fa; border-radius: 5px;">
                            <h4 style="color: #333; margin-bottom: 8px;">🌟 5. Genel Deneyim ve Tavsiye</h4>
                            <p style="font-size: 11px; margin-bottom: 5px;"><strong>Tanım:</strong> Hastaların genel memnuniyeti, tekrar tercih etme ve tavsiye etme eğilimleri analiz edilir.</p>
                            
                            ${satisfactionPercentage <= 50 ? `
                            <div style="background: #f8d7da; padding: 8px; border-radius: 3px; border-left: 4px solid #dc3545;">
                                <strong>Puan Aralığı: Düşük (%0-50)</strong><br>
                                Hastalar, genel olarak hastane deneyimlerinden memnun değil. Bu durum, hastanenin hizmet kalitesinde ciddi sorunlar olduğunu ve acil önlem alınması gerektiğini gösteriyor. Hastane yönetimi, hasta memnuniyetini artırmak için köklü değişiklikler yapmalı ve hasta geri bildirimlerini dikkate alarak iyileştirmeler gerçekleştirmelidir.
                            </div>
                            ` : satisfactionPercentage <= 75 ? `
                            <div style="background: #fff3cd; padding: 8px; border-radius: 3px; border-left: 4px solid #ffc107;">
                                <strong>Puan Aralığı: Orta (%51-75)</strong><br>
                                Hastalar, hastane deneyimlerini genel olarak yeterli buluyor, ancak bazı alanlarda (örneğin, randevu sistemleri, bekleme süreleri) iyileştirmeler yapılabileceğini düşünüyor. Hastane, bu alanlarda hasta memnuniyetini artırmak için çalışmalar yapmalı ve süreçlerini daha da iyileştirmelidir.
                            </div>
                            ` : `
                            <div style="background: #d4edda; padding: 8px; border-radius: 3px; border-left: 4px solid #28a745;">
                                <strong>Puan Aralığı: Yüksek (%76-100)</strong><br>
                                Hastalar, hastane deneyimlerinden son derece memnun. Bu, hastanenin hasta odaklı bir yaklaşım benimsediğini ve yüksek kaliteli hizmet sunduğunu gösteriyor. Bu başarıyı sürdürmek için, hastane sürekli olarak hizmet kalitesini izlemeli ve hasta geri bildirimlerine dayalı iyileştirmeler yapmalıdır.
                            </div>
                            `}
                        </div>
                    </div>
                    
                    <div class="recommendations">
                        <h4>💡 Öneriler ve Eylem Planı</h4>
                        <p><strong>Öncelikli Aksiyonlar:</strong> ${recommendations}</p>
                        <p><strong>Takip:</strong> Bu rapor sonuçlarını 3-6 ay sonra tekrar değerlendirmek için yeni anket düzenleyiniz.</p>
                    </div>
                    
                    <div class="footer">
                        <p>Akça Pro X - Profesyonel Kurum Değerlendirme Sistemi | ${new Date().toLocaleString('tr-TR')}</p>
                        <p>Bu rapor ${totalAnswers} adet soru yanıtı analiz edilerek oluşturulmuştur.</p>
                    </div>
                </body>
                </html>
            `;
        }

        function generateCharts(surveys) {
            if (surveys.length === 0) return;
            
            // Pozisyon grafiği
            const positionData = {};
            surveys.forEach(s => {
                positionData[s.jobType] = (positionData[s.jobType] || 0) + 1;
            });
            
            const positionCtx = document.getElementById('positionChart').getContext('2d');
            new Chart(positionCtx, {
                type: 'doughnut',
                data: {
                    labels: Object.keys(positionData),
                    datasets: [{
                        data: Object.values(positionData),
                        backgroundColor: ['#3b82f6', '#10b981', '#f59e0b']
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    }
                }
            });
            
            // Değerlendirme grafiği
            const satisfactionCounts = [0, 0, 0];
            surveys.forEach(s => {
                const avgScore = parseFloat(s.averageScore);
                if (avgScore < 2.5) satisfactionCounts[0]++;
                else if (avgScore < 3.5) satisfactionCounts[1]++;
                else satisfactionCounts[2]++;
            });
            
            const satisfactionCtx = document.getElementById('satisfactionChart').getContext('2d');
            new Chart(satisfactionCtx, {
                type: 'bar',
                data: {
                    labels: ['Düşük', 'Orta', 'Yüksek'],
                    datasets: [{
                        data: satisfactionCounts,
                        backgroundColor: ['#ef4444', '#f59e0b', '#10b981']
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    },
                    scales: {
                        y: { beginAtZero: true }
                    }
                }
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
            
            const timeCtx = document.getElementById('timeChart').getContext('2d');
            new Chart(timeCtx, {
                type: 'pie',
                data: {
                    labels: Object.keys(timeCounts),
                    datasets: [{
                        data: Object.values(timeCounts),
                        backgroundColor: ['#8b5cf6', '#06b6d4', '#f97316']
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    }
                }
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
            
            const trendCtx = document.getElementById('trendChart').getContext('2d');
            new Chart(trendCtx, {
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
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    },
                    scales: {
                        y: { beginAtZero: true }
                    }
                }
            });
        }

        function toggleParticipantDetails() {
            const detailsDiv = document.getElementById('participantDetails');
            const toggleBtn = document.getElementById('toggleParticipantsBtn');
            
            if (detailsDiv.classList.contains('hidden')) {
                detailsDiv.classList.remove('hidden');
                toggleBtn.textContent = '📋 Katılımcıları Gizle';
                loadParticipantTable();
            } else {
                detailsDiv.classList.add('hidden');
                toggleBtn.textContent = '📋 Katılımcıları Görüntüle';
            }
        }

        function loadParticipantTable() {
            if (!loggedInCompany || !systemData.surveyData) return;
            
            const companySurveys = systemData.surveyData.responses.filter(s => 
                s.companyName.toLowerCase() === loggedInCompany.name.toLowerCase()
            );
            
            const tbody = document.getElementById('participantTableBody');
            
            if (companySurveys.length === 0) {
                tbody.innerHTML = '<tr><td colspan="5" class="text-center py-4 text-gray-500">Henüz katılımcı bulunmuyor.</td></tr>';
                return;
            }
            
            tbody.innerHTML = companySurveys.map(survey => {
                const displayName = (survey.firstName && survey.lastName) ? 
                    `${survey.firstName} ${survey.lastName}` : 
                    (survey.firstName || survey.lastName || 'İsimsiz');
                
                const avgScore = parseFloat(survey.averageScore);
                let evaluation = '';
                let evaluationColor = '';
                
                if (avgScore < 2.5) {
                    evaluation = 'Düşük';
                    evaluationColor = 'text-red-600';
                } else if (avgScore < 3.5) {
                    evaluation = 'Orta';
                    evaluationColor = 'text-yellow-600';
                } else {
                    evaluation = 'Yüksek';
                    evaluationColor = 'text-green-600';
                }
                
                return `
                    <tr class="hover:bg-gray-50">
                        <td class="px-3 py-2">${displayName}</td>
                        <td class="px-3 py-2">${survey.jobType}</td>
                        <td class="px-3 py-2 text-center font-semibold">${avgScore}</td>
                        <td class="px-3 py-2 text-center ${evaluationColor} font-semibold">${evaluation}</td>
                        <td class="px-3 py-2 text-center text-sm">${new Date(survey.submittedAt).toLocaleDateString('tr-TR')}</td>
                    </tr>
                `;
            }).join('');
        }

        function loadDemoData() {
            // Demo veri yükleme fonksiyonu
        }

        function loginAdmin() {
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
            loadFromJSONBin().then(() => {
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
                    tbody.innerHTML = Object.entries(systemData.surveyData.companies || {}).map(([key, c]) => {
                        const count = (systemData.surveyData.responses || []).filter(r => r.companyName === c.name).length;
                        return `<tr><td class='px-4 py-2'>${c.name}</td><td class='px-4 py-2'>${c.password}</td><td class='px-4 py-2'>${count}</td><td class='px-4 py-2'>Aktif</td><td class='px-4 py-2'>-</td></tr>`;
                    }).join('');
                }
            });
        }
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'981af265f22bd620',t:'MTc1ODMwNDQ1MS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
