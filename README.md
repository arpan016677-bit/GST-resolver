# GST-resolver
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI GST Resolver Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom Font and Animations */
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        body { 
            font-family: 'Inter', sans-serif; 
            background-color: #f8fafc; /* Soft slate/lavender hint */
            color: #1e1b4b; /* Deep Indigo text default */
        }
        
        /* Drag & Drop Styles */
        .drag-active { 
            border-color: #14b8a6 !important; /* Mint */
            background-color: #f0fdfa !important; 
        }

        /* Toast Animations */
        @keyframes slideIn {
            from { transform: translateX(100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }
        @keyframes fadeOut {
            from { opacity: 1; }
            to { opacity: 0; }
        }
        .toast-enter { animation: slideIn 0.3s ease-out forwards; }
        .toast-exit { animation: fadeOut 0.3s ease-out forwards; }

        /* Loader */
        .loader {
            border: 3px solid #f3f3f3;
            border-radius: 50%;
            border-top: 3px solid #14b8a6;
            width: 24px;
            height: 24px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Custom Scrollbar for Invoice List */
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: #f1f1f1; border-radius: 4px;}
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
    </style>
</head>
<body class="min-h-screen flex flex-col transition-colors duration-300">

    <!-- ==================== AUTHENTICATION VIEW ==================== -->
    <div id="auth-view" class="min-h-screen flex flex-col md:flex-row bg-[#1e1b4b] relative">
        
        <!-- Language Selector Auth -->
        <div class="absolute top-6 right-6 z-50">
            <select class="lang-select bg-white/10 text-white border border-white/20 rounded-lg px-3 py-1.5 text-sm focus:outline-none focus:ring-2 focus:ring-[#14b8a6]">
                <option value="en" class="text-black">English</option>
                <option value="hi" class="text-black">Hindi (हिंदी)</option>
                <option value="mr" class="text-black">Marathi (मराठी)</option>
                <option value="ta" class="text-black">Tamil (தமிழ்)</option>
            </select>
        </div>

        <!-- Left Branding Panel -->
        <div class="hidden md:flex w-1/2 flex-col justify-center items-start p-16 text-white relative overflow-hidden">
            <div class="absolute inset-0 bg-gradient-to-br from-[#1e1b4b] to-[#312e81] opacity-90 z-0"></div>
            <!-- Decorative circles -->
            <div class="absolute -top-24 -left-24 w-96 h-96 bg-[#14b8a6] rounded-full mix-blend-multiply filter blur-3xl opacity-20 z-0"></div>
            <div class="absolute -bottom-24 -right-24 w-96 h-96 bg-indigo-500 rounded-full mix-blend-multiply filter blur-3xl opacity-20 z-0"></div>
            
            <div class="relative z-10 space-y-6">
                <div class="flex items-center gap-3">
                    <div class="p-3 bg-[#14b8a6] rounded-xl shadow-lg">
                        <svg class="w-8 h-8 text-[#1e1b4b]" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path></svg>
                    </div>
                    <h1 class="text-4xl font-bold tracking-tight" data-i18n="title">GST AI Resolver</h1>
                </div>
                <h2 class="text-2xl font-light text-indigo-100" data-i18n="subtitle">Automate your ITC Reconciliation instantly.</h2>
                <p class="text-indigo-200/80 max-w-md leading-relaxed" data-i18n="desc">
                    Upload messy physical bills or WhatsApp screenshots. Our AI extracts supplier details, tax splits, and HSN codes directly into a personalized, CA-ready dashboard.
                </p>
            </div>
        </div>

        <!-- Right Login Panel -->
        <div class="w-full md:w-1/2 flex items-center justify-center p-8 bg-slate-50 rounded-l-none md:rounded-l-3xl shadow-2xl relative z-10">
            <div class="max-w-md w-full space-y-8 mt-10 md:mt-0">
                <div class="text-center">
                    <h2 id="auth-title" class="text-3xl font-extrabold text-[#1e1b4b]" data-i18n="welcome">Welcome back</h2>
                    <p id="auth-subtitle" class="mt-2 text-sm text-gray-500" data-i18n="authSub">Please sign in to your account.</p>
                </div>
                
                <form id="auth-form" class="mt-8 space-y-6">
                    <div class="space-y-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700" data-i18n="email">Email address</label>
                            <input id="email-input" name="email" type="email" required class="appearance-none relative block w-full px-4 py-3 border border-gray-300 placeholder-gray-400 text-gray-900 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#14b8a6] focus:border-[#14b8a6] focus:z-10 sm:text-sm transition-colors" placeholder="merchant@msme.in">
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700" data-i18n="password">Password</label>
                            <input id="password-input" name="password" type="password" required class="appearance-none relative block w-full px-4 py-3 border border-gray-300 placeholder-gray-400 text-gray-900 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#14b8a6] focus:border-[#14b8a6] focus:z-10 sm:text-sm transition-colors" placeholder="••••••••">
                        </div>
                    </div>

                    <div>
                        <button type="submit" id="auth-submit-btn" class="group relative w-full flex justify-center py-3 px-4 border border-transparent text-sm font-medium rounded-lg text-[#1e1b4b] bg-[#14b8a6] hover:bg-[#0f9788] focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-[#14b8a6] shadow-md transition-all duration-200">
                            <span id="auth-btn-text" data-i18n="signinBtn">Sign in</span>
                        </button>
                    </div>
                </form>
                
                <div class="text-center mt-4">
                    <button id="toggle-auth-mode" class="text-sm font-medium text-[#14b8a6] hover:text-[#0f9788] transition-colors" data-i18n="toggleAuth">
                        Don't have an account? Sign up
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- ==================== DASHBOARD VIEW ==================== -->
    <div id="dashboard-view" class="hidden flex-col min-h-screen h-screen overflow-hidden">
        
        <!-- Header -->
        <header class="bg-[#1e1b4b] text-white shadow-lg py-4 px-6 md:px-10 flex justify-between items-center z-20 flex-shrink-0">
            <div class="flex items-center gap-3">
                <div class="p-2 bg-[#14b8a6] rounded-lg">
                    <svg class="w-6 h-6 text-[#1e1b4b]" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path></svg>
                </div>
                <h1 class="text-xl font-bold tracking-wide"><span data-i18n="title">GST AI</span> <span class="font-light text-indigo-300" data-i18n="dash">Dashboard</span></h1>
            </div>
            <div class="flex items-center gap-4">
                <select class="lang-select bg-white/10 text-white border border-white/20 rounded-lg px-2 py-1 text-sm focus:outline-none focus:ring-2 focus:ring-[#14b8a6]">
                    <option value="en" class="text-black">English</option>
                    <option value="hi" class="text-black">Hindi (हिंदी)</option>
                    <option value="mr" class="text-black">Marathi (मराठी)</option>
                    <option value="ta" class="text-black">Tamil (தமிழ்)</option>
                </select>
                <span id="user-email-display" class="hidden md:inline text-sm text-indigo-200"></span>
                <button id="logout-btn" class="text-sm bg-white/10 hover:bg-white/20 border border-white/20 px-4 py-2 rounded-lg transition-colors flex items-center gap-2">
                    <span data-i18n="logout">Logout</span>
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"></path></svg>
                </button>
            </div>
        </header>

        <!-- Main Dashboard Content (Split Layout) -->
        <main class="flex-grow flex flex-col lg:flex-row overflow-hidden">
            
            <!-- Left Column: Upload & AI Processing -->
            <div class="w-full lg:w-1/2 p-6 lg:p-10 overflow-y-auto custom-scrollbar border-r border-slate-200 bg-white">
                <div class="max-w-2xl mx-auto">
                    
                    <h2 class="text-2xl font-bold text-[#1e1b4b] mb-6" data-i18n="extractNew">Extract New Invoice</h2>
                    
                    <!-- Drag & Drop Zone -->
                    <div id="dropzone" class="border-2 border-dashed border-slate-300 rounded-2xl p-8 flex flex-col items-center justify-center text-center transition-all duration-200 relative min-h-[240px] bg-slate-50 hover:bg-slate-100 cursor-pointer shadow-sm">
                        <input type="file" id="fileInput" accept="image/*" class="absolute inset-0 w-full h-full opacity-0 cursor-pointer z-10" title="Upload invoice">
                        
                        <div id="uploadPlaceholder" class="flex flex-col items-center pointer-events-none">
                            <div class="w-16 h-16 bg-indigo-50 rounded-full flex items-center justify-center mb-4 text-[#14b8a6]">
                                <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12"></path></svg>
                            </div>
                            <p class="text-[#1e1b4b] font-semibold text-lg" data-i18n="uploadText">Upload Invoice Image</p>
                            <p class="text-sm text-slate-500 mt-1" data-i18n="dragDrop">Drag & drop or click to browse (PNG, JPG)</p>
                        </div>

                        <img id="imagePreview" class="hidden absolute inset-0 w-full h-full object-contain p-2 rounded-2xl bg-white z-0" alt="Preview">
                        
                        <button id="removeImgBtn" class="hidden absolute top-3 right-3 bg-red-500/90 text-white rounded-full p-2 hover:bg-red-600 shadow-md z-20 transition-transform hover:scale-110 backdrop-blur-sm">
                            <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                        </button>
                    </div>

                    <!-- AI Process Button -->
                    <button id="processBtn" class="mt-6 w-full bg-[#1e1b4b] text-white font-medium py-3.5 rounded-xl shadow-lg hover:bg-[#2d296b] transition-all duration-200 disabled:opacity-50 disabled:cursor-not-allowed flex justify-center items-center gap-3" disabled>
                        <svg class="h-5 w-5 text-[#14b8a6]" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M11.3 1.046A1 1 0 0112 2v5h4a1 1 0 01.82 1.573l-7 10A1 1 0 018 18v-5H4a1 1 0 01-.82-1.573l7-10a1 1 0 011.12-.38z" clip-rule="evenodd" /></svg>
                        <span id="processBtnText" data-i18n="runAI">Run AI Extraction</span>
                    </button>

                    <!-- Editable Extracted Form -->
                    <div id="extractionFormContainer" class="mt-10 hidden opacity-0 transition-opacity duration-500">
                        <div class="flex justify-between items-center mb-4 border-b border-slate-200 pb-3">
                            <h3 class="text-lg font-bold text-[#1e1b4b]" data-i18n="extractedData">Extracted Data</h3>
                            <span class="text-xs bg-[#14b8a6]/10 text-[#0f9788] px-2.5 py-1 rounded-full border border-[#14b8a6]/20 font-medium" data-i18n="reviewMsg">Review before saving</span>
                        </div>

                        <form id="extractedDataForm" class="space-y-5">
                            <div class="grid grid-cols-2 gap-4">
                                <div>
                                    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-1">Supplier Name</label>
                                    <input type="text" id="form-supplier" class="w-full px-3 py-2.5 bg-slate-50 border border-slate-200 rounded-lg focus:ring-2 focus:ring-[#14b8a6] focus:border-transparent text-[#1e1b4b] font-medium" placeholder="Name">
                                </div>
                                <div>
                                    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-1">GSTIN</label>
                                    <input type="text" id="form-gstin" class="w-full px-3 py-2.5 bg-slate-50 border border-slate-200 rounded-lg focus:ring-2 focus:ring-[#14b8a6] focus:border-transparent text-[#1e1b4b] font-medium uppercase" placeholder="15 Digits">
                                </div>
                                <div>
                                    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-1">Inv Number</label>
                                    <input type="text" id="form-invNum" class="w-full px-3 py-2.5 bg-slate-50 border border-slate-200 rounded-lg focus:ring-2 focus:ring-[#14b8a6] focus:border-transparent text-[#1e1b4b] font-medium" placeholder="Number">
                                </div>
                                <div>
                                    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-1">Date</label>
                                    <input type="text" id="form-date" class="w-full px-3 py-2.5 bg-slate-50 border border-slate-200 rounded-lg focus:ring-2 focus:ring-[#14b8a6] focus:border-transparent text-[#1e1b4b] font-medium" placeholder="DD/MM/YYYY">
                                </div>
                            </div>

                            <div class="bg-indigo-50/50 p-4 rounded-xl border border-indigo-100 space-y-4">
                                <div>
                                    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-1">Taxable Amount (₹)</label>
                                    <input type="number" step="0.01" id="form-taxable" class="w-full px-3 py-2 bg-white border border-indigo-100 rounded-lg text-right font-mono text-[#1e1b4b]" placeholder="0.00">
                                </div>
                                <div class="grid grid-cols-3 gap-3">
                                    <div>
                                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1">CGST (₹)</label>
                                        <input type="number" step="0.01" id="form-cgst" class="w-full px-2 py-2 bg-white border border-indigo-100 rounded-lg text-right font-mono text-sm text-[#1e1b4b]" placeholder="0.00">
                                    </div>
                                    <div>
                                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1">SGST (₹)</label>
                                        <input type="number" step="0.01" id="form-sgst" class="w-full px-2 py-2 bg-white border border-indigo-100 rounded-lg text-right font-mono text-sm text-[#1e1b4b]" placeholder="0.00">
                                    </div>
                                    <div>
                                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1">IGST (₹)</label>
                                        <input type="number" step="0.01" id="form-igst" class="w-full px-2 py-2 bg-white border border-indigo-100 rounded-lg text-right font-mono text-sm text-[#1e1b4b]" placeholder="0.00">
                                    </div>
                                </div>
                                <div class="pt-2">
                                    <label class="block text-xs font-bold text-[#1e1b4b] uppercase tracking-wider mb-1">Grand Total (₹)</label>
                                    <input type="number" step="0.01" id="form-total" class="w-full px-3 py-3 bg-[#14b8a6]/10 border border-[#14b8a6]/30 rounded-lg text-right font-mono text-lg font-bold text-[#0f9788]" placeholder="0.00">
                                </div>
                            </div>
                            
                            <div class="flex gap-3 pt-2">
                                <button type="button" id="discardBtn" class="w-1/3 bg-white border border-slate-300 text-slate-600 font-medium py-3 rounded-xl shadow-sm hover:bg-slate-50 transition-colors" data-i18n="discardBtn">Discard</button>
                                <button type="button" id="saveInvoiceBtn" class="w-2/3 bg-[#14b8a6] text-[#1e1b4b] font-bold py-3 rounded-xl shadow-md hover:bg-[#0f9788] hover:text-white transition-all flex justify-center items-center gap-2">
                                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path></svg>
                                    <span data-i18n="saveBtn">Save to Dashboard</span>
                                </button>
                            </div>
                        </form>
                    </div>

                </div>
            </div>

            <!-- Right Column: Database / Saved Invoices -->
            <div class="w-full lg:w-1/2 bg-slate-50 p-6 lg:p-10 flex flex-col h-full overflow-hidden">
                <div class="flex justify-between items-center mb-6 flex-shrink-0">
                    <div>
                        <h2 class="text-2xl font-bold text-[#1e1b4b]" data-i18n="myInvoices">My Invoices</h2>
                        <p class="text-sm text-slate-500 mt-1" data-i18n="historyDesc">Your securely saved records.</p>
                    </div>
                    <button id="exportAllBtn" class="bg-white border border-slate-300 text-[#1e1b4b] px-4 py-2 rounded-lg shadow-sm hover:bg-[#1e1b4b] hover:text-white hover:border-[#1e1b4b] transition-all flex items-center gap-2 text-sm font-medium">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path></svg>
                        <span data-i18n="exportAll">Export All CSV</span>
                    </button>
                </div>

                <!-- Invoice List -->
                <div id="invoiceList" class="flex-grow overflow-y-auto custom-scrollbar pr-2 space-y-4 relative">
                    <!-- Loading skeleton -->
                    <div id="listLoader" class="animate-pulse flex flex-col gap-4">
                        <div class="h-24 bg-slate-200 rounded-xl"></div>
                        <div class="h-24 bg-slate-200 rounded-xl"></div>
                        <div class="h-24 bg-slate-200 rounded-xl"></div>
                    </div>
                    
                    <!-- Empty State -->
                    <div id="emptyState" class="hidden flex-col items-center justify-center h-full text-center p-8">
                        <div class="w-20 h-20 bg-indigo-50 rounded-full flex items-center justify-center mb-4">
                            <svg class="w-10 h-10 text-indigo-200" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path></svg>
                        </div>
                        <p class="text-slate-600 font-medium" data-i18n="noData">No invoices saved yet.</p>
                        <p class="text-sm text-slate-400 mt-1" data-i18n="noDataSub">Upload and process your first bill to see it here.</p>
                    </div>

                    <!-- List Container -->
                    <div id="invoicesContainer" class="hidden flex-col gap-3 pb-8">
                        <!-- Invoices injected here via JS -->
                    </div>
                </div>
            </div>

        </main>
    </div>

    <!-- Toast Container -->
    <div id="toastContainer" class="fixed bottom-6 right-6 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <!-- Firebase & Logic Scripts -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword, signOut, onAuthStateChanged, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, collection, addDoc, getDocs, onSnapshot, deleteDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Handle Canvas Environment Globals
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'demo-gst-app';
        let firebaseConfig;
        
        if (typeof __firebase_config !== 'undefined') {
            firebaseConfig = JSON.parse(__firebase_config);
        } else {
            // Fallback for visual rendering outside strict canvas inject if needed
            firebaseConfig = { projectId: "demo-project" };
        }

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        // Global State
        let currentUser = null;
        let unsubscribeInvoices = null;
        let savedInvoicesData = [];
        
        // UI Elements - Auth
        const authView = document.getElementById('auth-view');
        const dashboardView = document.getElementById('dashboard-view');
        const authForm = document.getElementById('auth-form');
        const emailInput = document.getElementById('email-input');
        const passwordInput = document.getElementById('password-input');
        const toggleAuthModeBtn = document.getElementById('toggle-auth-mode');
        const authTitle = document.getElementById('auth-title');
        const authSubtitle = document.getElementById('auth-subtitle');
        const authSubmitBtn = document.getElementById('auth-submit-btn');
        const authBtnText = document.getElementById('auth-btn-text');
        const userEmailDisplay = document.getElementById('user-email-display');
        const logoutBtn = document.getElementById('logout-btn');

        // UI Elements - Upload & AI
        const dropzone = document.getElementById('dropzone');
        const fileInput = document.getElementById('fileInput');
        const imagePreview = document.getElementById('imagePreview');
        const uploadPlaceholder = document.getElementById('uploadPlaceholder');
        const removeImgBtn = document.getElementById('removeImgBtn');
        const processBtn = document.getElementById('processBtn');
        const processBtnText = document.getElementById('processBtnText');
        const extractionFormContainer = document.getElementById('extractionFormContainer');
        const saveInvoiceBtn = document.getElementById('saveInvoiceBtn');
        const discardBtn = document.getElementById('discardBtn');
        
        // UI Elements - DB List
        const listLoader = document.getElementById('listLoader');
        const emptyState = document.getElementById('emptyState');
        const invoicesContainer = document.getElementById('invoicesContainer');
        const exportAllBtn = document.getElementById('exportAllBtn');

        let isLoginMode = true;
        let base64ImageData = null;
        let imageMimeType = null;
        let currentLanguage = 'en';

        // --- Multi-Language i18n System ---
        const translations = {
            en: {
                title: "GST AI Resolver", subtitle: "Automate your ITC Reconciliation instantly.", desc: "Upload messy physical bills or WhatsApp screenshots. Our AI extracts supplier details, tax splits, and HSN codes directly into a personalized, CA-ready dashboard.",
                welcome: "Welcome back", authSub: "Please sign in to your account.", email: "Email address", password: "Password", signinBtn: "Sign in", toggleAuth: "Don't have an account? Sign up",
                dash: "Dashboard", logout: "Logout", extractNew: "Extract New Invoice", uploadText: "Upload Invoice Image", dragDrop: "Drag & drop or click to browse (PNG, JPG)",
                runAI: "Run AI Extraction", extractedData: "Extracted Data", reviewMsg: "Review before saving", discardBtn: "Discard", saveBtn: "Save to Dashboard",
                myInvoices: "My Invoices", historyDesc: "Your securely saved records.", exportAll: "Export All CSV", noData: "No invoices saved yet.", noDataSub: "Upload and process your first bill to see it here.",
                downloadSingle: "Download CSV"
            },
            hi: {
                title: "GST एआई रिज़ॉल्वर", subtitle: "अपना आईटीसी मिलान तुरंत स्वचालित करें।", desc: "कोई भी बिल अपलोड करें। हमारा AI आपके सीए के लिए आपूर्तिकर्ता विवरण और करों को सीधे डैशबोर्ड में निकालता है।",
                welcome: "वापसी पर स्वागत है", authSub: "कृपया अपने खाते में प्रवेश करें।", email: "ईमेल पता", password: "पासवर्ड", signinBtn: "साइन इन करें", toggleAuth: "खाता नहीं है? साइन अप करें",
                dash: "डैशबोर्ड", logout: "लॉग आउट", extractNew: "नया चालान निकालें", uploadText: "चालान छवि अपलोड करें", dragDrop: "खींचें और छोड़ें या ब्राउज़ करें",
                runAI: "AI निष्कर्षण चलाएं", extractedData: "निकाला गया डेटा", reviewMsg: "सहेजने से पहले समीक्षा करें", discardBtn: "रद्द करें", saveBtn: "डैशबोर्ड में सहेजें",
                myInvoices: "मेरे चालान", historyDesc: "आपके सुरक्षित रूप से सहेजे गए रिकॉर्ड।", exportAll: "सभी CSV निर्यात करें", noData: "अभी तक कोई चालान सहेजा नहीं गया है।", noDataSub: "इसे यहां देखने के लिए अपना पहला बिल अपलोड करें।",
                downloadSingle: "CSV डाउनलोड"
            },
            mr: {
                title: "GST एआय रिझॉल्वर", subtitle: "तुमचे ITC रिकन्सिलिएशन स्वयंचलित करा.", desc: "कोणतेही बिल अपलोड करा. आमचे AI सीएसाठी तपशील थेट डॅशबोर्डमध्ये काढते.",
                welcome: "पुन्हा स्वागत आहे", authSub: "कृपया तुमच्या खात्यात साइन इन करा.", email: "ईमेल पत्ता", password: "पासवर्ड", signinBtn: "साइन इन करा", toggleAuth: "खाते नाही? साइन अप करा",
                dash: "डॅशबोर्ड", logout: "लॉगआउट", extractNew: "नवीन चलन काढा", uploadText: "चलन प्रतिमा अपलोड करा", dragDrop: "ड्रॅग आणि ड्रॉप करा किंवा ब्राउझ करा",
                runAI: "AI एक्सट्रॅक्शन चालवा", extractedData: "काढलेला डेटा", reviewMsg: "सेव्ह करण्यापूर्वी तपासा", discardBtn: "रद्द करा", saveBtn: "डॅशबोर्डवर सेव्ह करा",
                myInvoices: "माझे चलन", historyDesc: "तुमचे सुरक्षित रेकॉर्ड.", exportAll: "सर्व CSV एक्सपोर्ट करा", noData: "अद्याप कोणतेही चलन सेव्ह केलेले नाही.", noDataSub: "पहिले बिल अपलोड करा आणि प्रक्रिया करा.",
                downloadSingle: "CSV डाउनलोड"
            },
            ta: {
                title: "GST AI தீர்வி", subtitle: "உங்கள் ITC சமரசத்தை தானியங்குபடுத்துங்கள்.", desc: "பில்களை பதிவேற்றவும். எங்கள் AI உங்கள் CA க்கான விவரங்களை நேரடியாக டாஷ்போர்டில் பிரித்தெடுக்கிறது.",
                welcome: "மீண்டும் வருக", authSub: "தயவுசெய்து உள்நுழையவும்.", email: "மின்னஞ்சல் முகவரி", password: "கடவுச்சொல்", signinBtn: "உள்நுழைக", toggleAuth: "கணக்கு இல்லையா? பதிவு செய்க",
                dash: "டாஷ்போர்டு", logout: "வெளியேறு", extractNew: "புதிய விலைப்பட்டியலை பிரித்தெடு", uploadText: "படம் பதிவேற்றுக", dragDrop: "இழுத்து விடவும் அல்லது உலாவவும்",
                runAI: "AI ஐ இயக்கு", extractedData: "பிரித்தெடுக்கப்பட்ட தரவு", reviewMsg: "சேமிக்கும் முன் மதிப்பாய்வு செய்யவும்", discardBtn: "நிராகரி", saveBtn: "டாஷ்போர்டில் சேமி",
                myInvoices: "என் விலைப்பட்டியல்கள்", historyDesc: "உங்கள் பாதுகாப்பான பதிவுகள்.", exportAll: "அனைத்தும் CSV ஏற்றுமதி", noData: "எந்த விலைப்பட்டியலும் இல்லை.", noDataSub: "உங்கள் முதல் விலைப்பட்டியலை பதிவேற்றவும்.",
                downloadSingle: "CSV பதிவிறக்கு"
            }
        };

        function setLanguage(lang) {
            currentLanguage = lang;
            const dict = translations[lang];
            
            document.querySelectorAll('[data-i18n]').forEach(el => {
                const key = el.getAttribute('data-i18n');
                if (dict[key]) {
                    el.textContent = dict[key];
                }
            });

            // Keep Auth toggle text logically synced with state
            if(dict) {
                toggleAuthModeBtn.textContent = isLoginMode ? (lang==='en'?"Don't have an account? Sign up":(lang==='hi'?"खाता नहीं है? साइन अप करें":(lang==='mr'?"खाते नाही? साइन अप करा":"கணக்கு இல்லையா? பதிவு செய்க"))) : (lang==='en'?"Already have an account? Sign in":(lang==='hi'?"पहले से खाता है? साइन इन करें":(lang==='mr'?"आधीच खाते आहे? साइन इन करा":"ஏற்கனவே கணக்கு உள்ளதா? உள்நுழைக")));
            }
            
            // Re-render invoices list to apply translation to per-invoice buttons
            renderInvoicesList();
        }

        document.querySelectorAll('.lang-select').forEach(select => {
            select.addEventListener('change', (e) => {
                const newLang = e.target.value;
                document.querySelectorAll('.lang-select').forEach(s => s.value = newLang);
                setLanguage(newLang);
            });
        });


        // Custom Toast System
        function showToast(message, type = 'info') {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            toast.className = `toast-enter flex items-center p-4 mb-2 w-full max-w-xs text-sm rounded-xl shadow-xl pointer-events-auto`;
            
            if (type === 'success') {
                toast.classList.add('bg-[#14b8a6]', 'text-white');
            } else if (type === 'error') {
                toast.classList.add('bg-red-500', 'text-white');
            } else {
                toast.classList.add('bg-[#1e1b4b]', 'text-white');
            }

            const icon = type === 'success' 
                ? `<svg class="w-5 h-5 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path></svg>`
                : type === 'error' 
                ? `<svg class="w-5 h-5 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>`
                : `<svg class="w-5 h-5 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>`;

            toast.innerHTML = `${icon} <span class="font-medium">${message}</span>`;
            container.appendChild(toast);

            setTimeout(() => {
                toast.classList.replace('toast-enter', 'toast-exit');
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        // --- Authentication Logic ---
        
        // Canvas Requirement: Check for initial token and sign in silently if exists. 
        // This ensures backend firestore permissions hold if the user comes from a canvas session.
        if (typeof __initial_auth_token !== 'undefined') {
            signInWithCustomToken(auth, __initial_auth_token).catch(e => console.error("Custom token auth failed", e));
        }

        toggleAuthModeBtn.addEventListener('click', () => {
            isLoginMode = !isLoginMode;
            const dict = translations[currentLanguage];
            if (isLoginMode) {
                authTitle.textContent = dict['welcome'];
                authSubtitle.textContent = dict['authSub'];
                authBtnText.textContent = dict['signinBtn'];
                toggleAuthModeBtn.textContent = currentLanguage==='en'?"Don't have an account? Sign up":(currentLanguage==='hi'?"खाता नहीं है? साइन अप करें":(currentLanguage==='mr'?"खाते नाही? साइन अप करा":"கணக்கு இல்லையா? பதிவு செய்க"));
            } else {
                authTitle.textContent = currentLanguage==='en'?"Create an account":(currentLanguage==='hi'?"खाता बनाएं":(currentLanguage==='mr'?"खाते तयार करा":"கணக்கை உருவாக்கு"));
                authSubtitle.textContent = currentLanguage==='en'?"Start automating your GST today.":(currentLanguage==='hi'?"आज ही अपना GST स्वचालित करना शुरू करें।":(currentLanguage==='mr'?"आजच तुमचे GST स्वयंचलित करणे सुरू करा.":"இன்றே உங்கள் GST ஐ தானியங்குபடுத்துங்கள்."));
                authBtnText.textContent = currentLanguage==='en'?"Sign up":(currentLanguage==='hi'?"साइन अप करें":(currentLanguage==='mr'?"साइन अप करा":"பதிவு செய்க"));
                toggleAuthModeBtn.textContent = currentLanguage==='en'?"Already have an account? Sign in":(currentLanguage==='hi'?"पहले से खाता है? साइन इन करें":(currentLanguage==='mr'?"आधीच खाते आहे? साइन इन करा":"ஏற்கனவே கணக்கு உள்ளதா? உள்நுழைக"));
            }
        });

        authForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const email = emailInput.value;
            const password = passwordInput.value;
            authSubmitBtn.disabled = true;
            authBtnText.innerHTML = '<div class="loader w-5 h-5 border-t-white"></div>';

            try {
                if (isLoginMode) {
                    await signInWithEmailAndPassword(auth, email, password);
                    showToast('Logged in successfully', 'success');
                } else {
                    await createUserWithEmailAndPassword(auth, email, password);
                    showToast('Account created successfully', 'success');
                }
            } catch (error) {
                showToast(error.message.replace('Firebase: ', ''), 'error');
            } finally {
                authSubmitBtn.disabled = false;
                authBtnText.textContent = isLoginMode ? 'Sign in' : 'Sign up';
            }
        });

        logoutBtn.addEventListener('click', async () => {
            try {
                await signOut(auth);
                showToast('Logged out', 'info');
            } catch (error) {
                showToast('Logout failed', 'error');
            }
        });

        onAuthStateChanged(auth, (user) => {
            currentUser = user;
            if (user) {
                authView.classList.add('hidden');
                dashboardView.classList.remove('hidden');
                dashboardView.classList.add('flex');
                userEmailDisplay.textContent = user.email || 'Anonymous Canvas User';
                setupFirestoreListener(user.uid);
            } else {
                authView.classList.remove('hidden');
                dashboardView.classList.add('hidden');
                dashboardView.classList.remove('flex');
                if (unsubscribeInvoices) {
                    unsubscribeInvoices();
                    unsubscribeInvoices = null;
                }
                resetUpload();
            }
        });

        // --- Drag and Drop & File Handle ---
        ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(evt => dropzone.addEventListener(evt, e => { e.preventDefault(); e.stopPropagation(); }));
        ['dragenter', 'dragover'].forEach(evt => dropzone.addEventListener(evt, () => { if(!base64ImageData) dropzone.classList.add('drag-active'); }));
        ['dragleave', 'drop'].forEach(evt => dropzone.addEventListener(evt, () => dropzone.classList.remove('drag-active')));

        dropzone.addEventListener('drop', (e) => { if (e.dataTransfer.files.length) handleFile(e.dataTransfer.files[0]); });
        fileInput.addEventListener('change', function() { if (this.files.length) handleFile(this.files[0]); });

        function handleFile(file) {
            if (!file.type.startsWith('image/')) {
                showToast('Valid image required (PNG, JPG)', 'error');
                return;
            }
            if (file.size > 5 * 1024 * 1024) {
                showToast('File must be under 5MB', 'error');
                return;
            }
            
            imageMimeType = file.type;
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onloadend = () => {
                base64ImageData = reader.result.split(',')[1];
                imagePreview.src = reader.result;
                imagePreview.classList.remove('hidden');
                uploadPlaceholder.classList.add('hidden');
                removeImgBtn.classList.remove('hidden');
                processBtn.disabled = false;
                
                // Hide form if visible
                extractionFormContainer.classList.add('hidden', 'opacity-0');
            }
        }

        removeImgBtn.addEventListener('click', (e) => {
            e.stopPropagation();
            resetUpload();
        });

        function resetUpload() {
            base64ImageData = null;
            fileInput.value = '';
            imagePreview.classList.add('hidden');
            uploadPlaceholder.classList.remove('hidden');
            removeImgBtn.classList.add('hidden');
            processBtn.disabled = true;
            extractionFormContainer.classList.add('hidden', 'opacity-0');
            document.getElementById('extractedDataForm').reset();
        }

        discardBtn.addEventListener('click', resetUpload);

        // --- Gemini AI Processing ---
        processBtn.addEventListener('click', async () => {
            if (!base64ImageData) return;
            
            processBtn.disabled = true;
            processBtnText.innerHTML = '<div class="loader w-5 h-5 border-t-[#14b8a6]"></div> <span class="ml-2">Processing...</span>';

            const apiKey = ""; // Will be injected
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;
            
            const promptText = `
                Act as an expert Indian Chartered Accountant assistant. Extract GST details from this invoice image.
                CRITICAL INSTRUCTIONS:
                - Return STRICT JSON. No markdown backticks.
                - Keys: "supplierName" (string), "gstin" (string), "invoiceNumber" (string), "date" (string DD/MM/YYYY), "taxableAmount" (number), "cgst" (number), "sgst" (number), "igst" (number), "grandTotal" (number).
                - Use 0.00 for missing taxes. Ensure numbers have NO commas in the JSON output.
            `;

            try {
                const res = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{ role: "user", parts: [{ text: promptText }, { inlineData: { mimeType: imageMimeType, data: base64ImageData } }] }],
                        generationConfig: { responseMimeType: "application/json" }
                    })
                });

                if (!res.ok) throw new Error("API Error");
                const result = await res.json();
                
                const jsonText = result.candidates[0].content.parts[0].text.replace(/```json\n|\n```|```/g, '').trim();
                const data = JSON.parse(jsonText);
                
                // Populate Form
                const setVal = (id, val, isNum) => document.getElementById(id).value = (val !== null && val !== undefined) ? (isNum ? Number(val).toFixed(2) : val) : '';
                setVal('form-supplier', data.supplierName);
                setVal('form-gstin', data.gstin);
                setVal('form-invNum', data.invoiceNumber);
                setVal('form-date', data.date);
                setVal('form-taxable', data.taxableAmount, true);
                setVal('form-cgst', data.cgst, true);
                setVal('form-sgst', data.sgst, true);
                setVal('form-igst', data.igst, true);
                setVal('form-total', data.grandTotal, true);
                
                extractionFormContainer.classList.remove('hidden');
                // slight delay for transition
                setTimeout(() => extractionFormContainer.classList.remove('opacity-0'), 50);
                showToast('Extraction successful! Please review.', 'success');

            } catch (err) {
                console.error(err);
                showToast('AI failed to process image. Try a clearer photo.', 'error');
            } finally {
                processBtn.disabled = false;
                processBtnText.textContent = 'Run AI Extraction';
            }
        });

        // --- Firestore Saving & Syncing ---
        saveInvoiceBtn.addEventListener('click', async () => {
            if (!currentUser) return;
            
            const originalBtnText = saveInvoiceBtn.innerHTML;
            saveInvoiceBtn.disabled = true;
            saveInvoiceBtn.innerHTML = '<div class="loader w-5 h-5 border-t-[#1e1b4b]"></div>';

            const invoiceData = {
                supplierName: document.getElementById('form-supplier').value || 'Unknown',
                gstin: document.getElementById('form-gstin').value || '',
                invoiceNumber: document.getElementById('form-invNum').value || '',
                date: document.getElementById('form-date').value || '',
                taxableAmount: parseFloat(document.getElementById('form-taxable').value) || 0,
                cgst: parseFloat(document.getElementById('form-cgst').value) || 0,
                sgst: parseFloat(document.getElementById('form-sgst').value) || 0,
                igst: parseFloat(document.getElementById('form-igst').value) || 0,
                grandTotal: parseFloat(document.getElementById('form-total').value) || 0,
                createdAt: Date.now()
            };

            try {
                // RULE 1 Strict path
                const userInvoicesRef = collection(db, 'artifacts', appId, 'users', currentUser.uid, 'invoices');
                await addDoc(userInvoicesRef, invoiceData);
                showToast('Invoice saved securely!', 'success');
                resetUpload();
            } catch (error) {
                console.error("Save error:", error);
                showToast('Failed to save invoice to database.', 'error');
            } finally {
                saveInvoiceBtn.disabled = false;
                saveInvoiceBtn.innerHTML = originalBtnText;
            }
        });

        function setupFirestoreListener(userId) {
            // RULE 1 Strict Path
            const userInvoicesRef = collection(db, 'artifacts', appId, 'users', userId, 'invoices');
            
            listLoader.classList.remove('hidden');
            invoicesContainer.classList.add('hidden');
            emptyState.classList.add('hidden');

            // RULE 2: No orderBy, where, etc. Fetch all, process client side.
            unsubscribeInvoices = onSnapshot(userInvoicesRef, (snapshot) => {
                savedInvoicesData = [];
                snapshot.forEach(doc => {
                    savedInvoicesData.push({ id: doc.id, ...doc.data() });
                });
                
                // Sort by createdAt descending in memory
                savedInvoicesData.sort((a, b) => b.createdAt - a.createdAt);
                
                renderInvoicesList();
            }, (error) => {
                console.error("Snapshot error:", error);
                showToast("Error syncing data.", "error");
            });
        }

        function renderInvoicesList() {
            listLoader.classList.add('hidden');
            invoicesContainer.innerHTML = '';
            
            if (savedInvoicesData.length === 0) {
                emptyState.classList.remove('hidden');
                emptyState.classList.add('flex');
                invoicesContainer.classList.add('hidden');
                return;
            }
            
            emptyState.classList.add('hidden');
            emptyState.classList.remove('flex');
            invoicesContainer.classList.remove('hidden');

            const btnText = translations[currentLanguage]['downloadSingle'];

            savedInvoicesData.forEach(inv => {
                const card = document.createElement('div');
                card.className = "bg-white p-4 rounded-xl border border-slate-200 shadow-sm hover:shadow-md transition-shadow group relative overflow-hidden";
                
                // Decorative left border
                const decBorder = document.createElement('div');
                decBorder.className = "absolute left-0 top-0 bottom-0 w-1 bg-[#14b8a6]";
                card.appendChild(decBorder);

                card.innerHTML += `
                    <div class="flex justify-between items-start ml-2 pr-12">
                        <div>
                            <h4 class="font-bold text-[#1e1b4b] text-lg">${inv.supplierName}</h4>
                            <p class="text-xs text-slate-500 font-mono mt-0.5">GSTIN: ${inv.gstin || 'N/A'} | INV: ${inv.invoiceNumber || 'N/A'}</p>
                        </div>
                        <div class="text-right">
                            <p class="font-bold text-lg text-[#0f9788]">₹${inv.grandTotal.toFixed(2)}</p>
                            <p class="text-[10px] text-slate-400 mt-1">${new Date(inv.createdAt).toLocaleDateString()}</p>
                        </div>
                    </div>
                    <div class="mt-3 ml-2 flex gap-3 text-xs text-slate-600 bg-slate-50 p-2 rounded-lg border border-slate-100 flex-wrap">
                        <span><span class="text-slate-400">Taxable:</span> ₹${inv.taxableAmount.toFixed(2)}</span>
                        <span><span class="text-slate-400">CGST:</span> ₹${inv.cgst.toFixed(2)}</span>
                        <span><span class="text-slate-400">SGST:</span> ₹${inv.sgst.toFixed(2)}</span>
                    </div>
                    
                    <!-- Individual Action Buttons -->
                    <div class="absolute top-3 right-3 flex flex-col gap-2">
                        <button class="export-single-btn bg-indigo-50 text-[#1e1b4b] p-1.5 rounded border border-indigo-100 hover:bg-[#1e1b4b] hover:text-white transition-colors" data-id="${inv.id}" title="${btnText}">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"></path></svg>
                        </button>
                        <button class="delete-btn hidden group-hover:flex bg-red-50 text-red-500 p-1.5 rounded border border-red-100 hover:bg-red-500 hover:text-white transition-colors" data-id="${inv.id}" title="Delete Record">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg>
                        </button>
                    </div>
                `;
                
                invoicesContainer.appendChild(card);
            });

            // Attach delete listeners
            document.querySelectorAll('.delete-btn').forEach(btn => {
                btn.addEventListener('click', async (e) => {
                    const id = e.currentTarget.getAttribute('data-id');
                    try {
                        await deleteDoc(doc(db, 'artifacts', appId, 'users', currentUser.uid, 'invoices', id));
                        showToast('Record deleted.', 'info');
                    } catch(err) {
                        showToast('Failed to delete.', 'error');
                    }
                });
            });

            // Attach individual export listeners
            document.querySelectorAll('.export-single-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const id = e.currentTarget.getAttribute('data-id');
                    exportSingleCsv(id);
                });
            });
        }

        // --- Export CSV Logic ---
        const escapeCsv = (str) => {
            if (str === null || str === undefined) return '""';
            const s = str.toString();
            return s.includes(',') || s.includes('"') || s.includes('\n') ? `"${s.replace(/"/g, '""')}"` : s;
        };
        const csvHeaders = ['Supplier Name', 'GSTIN', 'Invoice Number', 'Invoice Date', 'Taxable Amount', 'CGST', 'SGST', 'IGST', 'Grand Total', 'Added On'];

        function downloadCsv(content, filename) {
            const blob = new Blob([content], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            link.setAttribute("href", url);
            link.setAttribute("download", filename);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        function exportSingleCsv(id) {
            const inv = savedInvoicesData.find(i => i.id === id);
            if (!inv) return;

            let csvContent = csvHeaders.join(',') + '\n';
            const row = [
                inv.supplierName, inv.gstin, inv.invoiceNumber, inv.date, 
                inv.taxableAmount, inv.cgst, inv.sgst, inv.igst, inv.grandTotal,
                new Date(inv.createdAt).toLocaleDateString()
            ].map(escapeCsv);
            
            csvContent += row.join(',') + '\n';
            downloadCsv(csvContent, `invoice_${inv.invoiceNumber || id.slice(0,6)}.csv`);
            showToast("Invoice Exported", "success");
        }

        exportAllBtn.addEventListener('click', () => {
            if (savedInvoicesData.length === 0) {
                showToast("No data to export.", "error");
                return;
            }

            let csvContent = csvHeaders.join(',') + '\n';
            
            savedInvoicesData.forEach(inv => {
                const row = [
                    inv.supplierName, inv.gstin, inv.invoiceNumber, inv.date, 
                    inv.taxableAmount, inv.cgst, inv.sgst, inv.igst, inv.grandTotal,
                    new Date(inv.createdAt).toLocaleDateString()
                ].map(escapeCsv);
                csvContent += row.join(',') + '\n';
            });

            downloadCsv(csvContent, `all_gst_export_${new Date().getTime()}.csv`);
            showToast("All Invoices Exported", "success");
        });

    </script>
</body>
</html>
```
