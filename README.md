<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>钥匙管理系统 - 增强版</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#165DFF',
                        secondary: '#36CFC9',
                        accent: '#722ED1',
                        neutral: '#F2F3F5',
                        'neutral-dark': '#4E5969',
                        success: '#52C41A',
                        warning: '#FAAD14',
                        danger: '#FF4D4F',
                        info: '#3498db',
                        inactive: '#95a5a6'
                    },
                    fontFamily: {
                        sans: ['Inter', 'system-ui', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .card-shadow {
                box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
            }
            .menu-hover {
                @apply transition-all duration-300 hover:bg-primary/10 hover:text-primary;
            }
            .btn-primary {
                @apply bg-primary text-white px-4 py-2 rounded-lg transition-all duration-300 hover:bg-primary/90 active:bg-primary/80 focus:outline-none focus:ring-2 focus:ring-primary/50;
            }
            .btn-secondary {
                @apply bg-white text-neutral-dark border border-gray-300 px-4 py-2 rounded-lg transition-all duration-300 hover:bg-gray-50 active:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-gray-300;
            }
            .btn-danger {
                @apply bg-danger text-white px-4 py-2 rounded-lg transition-all duration-300 hover:bg-danger/90 active:bg-danger/80 focus:outline-none focus:ring-2 focus:ring-danger/50;
            }
            .btn-success {
                @apply bg-success text-white px-4 py-2 rounded-lg transition-all duration-300 hover:bg-success/90 active:bg-success/80 focus:outline-none focus:ring-2 focus:ring-success/50;
            }
            .form-input {
                @apply w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-primary/50 focus:border-primary transition-all duration-300;
            }
            .section-title {
                @apply text-[clamp(1.25rem,3vw,1.75rem)] font-bold text-gray-800 mb-6 flex items-center;
            }
            .section-title i {
                @apply mr-2 text-primary;
            }
            .fade-in {
                animation: fadeIn 0.5s ease-in-out;
            }
            @keyframes fadeIn {
                from { opacity: 0; transform: translateY(10px); }
                to { opacity: 1; transform: translateY(0); }
            }
            .notification {
                @apply fixed top-20 right-4 px-6 py-3 rounded-lg shadow-lg transform transition-all duration-500 translate-x-full opacity-0 z-50;
            }
            .notification.show {
                @apply translate-x-0 opacity-100;
            }
            .clickable-category {
                @apply text-primary cursor-pointer hover:underline;
            }
            .import-preview {
                @apply max-h-96 overflow-y-auto border border-gray-200 rounded-lg mt-4;
            }
            .preview-table {
                @apply min-w-full divide-y divide-gray-200;
            }
            .preview-table th {
                @apply px-4 py-2 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider;
            }
            .preview-table td {
                @apply px-4 py-2 whitespace-nowrap text-sm text-gray-700;
            }
            .preview-table tr:nth-child(even) {
                @apply bg-gray-50;
            }
            .badge-success {
                @apply px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800;
            }
            .badge-warning {
                @apply px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-yellow-100 text-yellow-800;
            }
            .badge-error {
                @apply px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-red-100 text-red-800;
            }
            .badge-info {
                @apply px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-blue-100 text-blue-800;
            }
            .badge-inactive {
                @apply px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-gray-200 text-gray-800;
            }
            .status-chip {
                @apply px-3 py-1 rounded-full text-xs font-medium;
            }
            .status-inactive {
                @apply status-chip bg-gray-200 text-gray-800;
            }
            .status-borrow {
                @apply status-chip bg-blue-100 text-blue-800;
            }
            .status-lend {
                @apply status-chip bg-green-100 text-green-800;
            }
            .status-lost {
                @apply status-chip bg-red-100 text-red-800;
            }
            .status-stock {
                @apply status-chip bg-purple-100 text-purple-800;
            }
            .file-drop-area {
                @apply border-2 border-dashed border-gray-300 rounded-lg p-8 text-center cursor-pointer transition-all duration-300 hover:border-primary hover:bg-blue-50;
            }
            .file-drop-area.active {
                @apply border-primary bg-blue-50;
            }
            .notification-badge {
                @apply absolute top-0 right-0 inline-flex items-center justify-center px-2 py-1 text-xs font-bold leading-none text-white transform translate-x-1/2 -translate-y-1/2 bg-danger rounded-full;
            }
            .login-container {
                @apply flex items-center justify-center min-h-screen bg-gray-100;
            }
            .login-box {
                @apply bg-white p-8 rounded-xl shadow-lg w-full max-w-sm;
            }
            .autocomplete-suggestions {
                @apply absolute z-10 w-full bg-white border border-gray-300 rounded-lg shadow-lg mt-1 max-h-60 overflow-y-auto;
            }
            .suggestion-item {
                @apply p-2 cursor-pointer hover:bg-gray-100 text-sm text-gray-800;
            }
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">

    <div id="loginSection" class="login-container">
        <div class="login-box fade-in">
            <div class="text-center mb-6">
                <i class="fa fa-key text-primary text-4xl mb-2"></i>
                <h2 class="text-2xl font-bold text-gray-800">钥匙管理系统登录</h2>
            </div>
            <form id="loginForm" class="space-y-4">
                <div>
                    <label for="passwordInput" class="block text-sm font-medium text-gray-700 mb-1">请输入密码：</label>
                    <input type="password" id="passwordInput" class="form-input" placeholder="输入您的密码" required>
                </div>
                <button type="submit" class="btn-primary w-full">
                    <i class="fa fa-sign-in mr-1"></i>登录
                </button>
            </form>
        </div>
    </div>

    <header id="mainHeader" class="bg-white shadow-sm fixed top-0 left-0 right-0 z-50 hidden">
        <div class="container mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <i class="fa fa-key text-primary text-2xl"></i>
                <h1 class="text-xl font-bold text-gray-800">钥匙管理系统</h1>
            </div>
            <div class="flex items-center space-x-6">
                <span class="text-sm text-gray-500">
                    <i class="fa fa-user-circle-o mr-1"></i><span id="currentUserName"></span> (<span id="currentUserRole"></span>)
                </span>
                <button id="logoutBtn" class="text-gray-600 hover:text-danger transition-colors">
                    <i class="fa fa-sign-out mr-1"></i>登出
                </button>
            </div>
        </div>
    </header>

    <div id="notification" class="notification">
        <span id="notificationText"></span>
    </div>

    <main id="mainContent" class="container mx-auto px-4 pt-24 pb-16 hidden">
        <section id="mainMenu" class="fade-in">
            <h2 class="section-title"><i class="fa fa-tachometer"></i>主菜单</h2>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" id="menuOptions">
                </div>
        </section>

        <section id="myKeysSection" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-user-circle-o"></i>我持有的钥匙</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>
            <div class="bg-white rounded-xl p-6 card-shadow">
                <p class="text-gray-500 mb-4">您当前共持有 <span id="myKeysCount">0</span> 把钥匙。</p>
                <div class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">编号</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">名称</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">类别</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">部门</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">存放位置</th>
                            </tr>
                        </thead>
                        <tbody id="myKeysTableBody" class="bg-white divide-y divide-gray-200">
                            <tr>
                                <td colspan="6" class="px-6 py-10 text-center text-gray-500">
                                    <i class="fa fa-info-circle mr-2"></i>您当前没有持有的钥匙。
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <section id="statusManagement" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-search"></i>钥匙信息查询</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow mb-6">
                <div class="mb-6 flex flex-wrap items-center gap-4">
                    <span class="text-gray-700 font-medium">查询钥匙:</span>
                    <div class="flex flex-grow max-w-sm relative">
                        <input type="text" id="statusSearchInput" class="form-input flex-grow" placeholder="按钥匙编号或名称查询">
                        <div id="statusSuggestions" class="autocomplete-suggestions hidden"></div>
                        <button onclick="searchKeys()" class="btn-primary ml-2">
                            <i class="fa fa-search"></i>
                        </button>
                    </div>
                    <button onclick="displayKeys('all')" class="btn-primary">
                        <i class="fa fa-list mr-1"></i>显示所有钥匙
                    </button>
                </div>

                <div class="flex flex-wrap gap-4 mb-6 border-t pt-4">
                    <span class="text-gray-700 font-medium">状态筛选:</span>
                    <button onclick="displayKeys('在库')" class="btn-secondary">
                        <i class="fa fa-home mr-1"></i>在库钥匙
                    </button>
                    <button onclick="displayKeys('借用')" class="btn-secondary">
                        <i class="fa fa-handshake-o mr-1"></i>借用钥匙
                    </button>
                    <button onclick="displayKeys('领用')" class="btn-secondary">
                        <i class="fa fa-check-circle mr-1"></i>领用钥匙
                    </button>
                    <button onclick="displayKeys('挂失')" class="btn-secondary">
                        <i class="fa fa-exclamation-triangle mr-1"></i>挂失钥匙
                    </button>
                    <button onclick="displayKeys('停用')" class="btn-secondary">
                        <i class="fa fa-ban mr-1"></i>停用钥匙
                    </button>
                </div>

                <div id="statusResults" class="overflow-x-auto">
                    <p class="text-gray-500 mb-4">共 <span id="totalKeysCount">0</span> 条记录</p>
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    编号</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    名称</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    类别</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    状态</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    部门</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    存放位置</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    借/领用人</th>
                            </tr>
                        </thead>
                        <tbody id="statusTableBody" class="bg-white divide-y divide-gray-200">
                            <tr>
                                <td colspan="7" class="px-6 py-10 text-center text-gray-500">
                                    <i class="fa fa-info-circle mr-2"></i>正在加载钥匙数据...
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <section id="departmentManagement" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-building-o"></i>部门钥匙管理</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-4xl mx-auto">
                <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-4 mb-6">
                    <button onclick="displayByDepartment('all')" class="btn-primary">
                        <i class="fa fa-globe mr-1"></i>所有部门
                    </button>
                    <div id="departmentButtons"></div>
                </div>

                <div class="flex flex-wrap gap-2 mb-6" id="categoryFilters">
                    <span class="text-gray-500">类别筛选:</span>
                </div>

                <div id="departmentResults" class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    编号</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    名称</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    类别</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    状态</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    部门</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    存放位置</th>
                            </tr>
                        </thead>
                        <tbody id="departmentTableBody" class="bg-white divide-y divide-gray-200">
                            <tr>
                                <td colspan="6" class="px-6 py-10 text-center text-gray-500">
                                    <i class="fa fa-info-circle mr-2"></i>请选择部门查看钥匙信息
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <section id="addKey" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-plus"></i>添加钥匙</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-4xl mx-auto">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <div>
                        <h3 class="text-lg font-semibold mb-4">手动添加钥匙</h3>
                        <form id="addKeyForm" class="space-y-4">
                            <div>
                                <label for="addId" class="block text-sm font-medium text-gray-700 mb-1">钥匙编号：</label>
                                <input type="number" id="addId" class="form-input" placeholder="请输入数字编号" required>
                            </div>

                            <div>
                                <label for="addName" class="block text-sm font-medium text-gray-700 mb-1">钥匙名称：</label>
                                <input type="text" id="addName" class="form-input" placeholder="请输入钥匙名称" required>
                            </div>

                            <div>
                                <label for="addCategory" class="block text-sm font-medium text-gray-700 mb-1">钥匙类别：</label>
                                <input type="text" id="addCategory" class="form-input" placeholder="如：办公室、设备间、仓库等" required>
                            </div>

                            <div>
                                <label for="addStatus" class="block text-sm font-medium text-gray-700 mb-1">钥匙状态：</label>
                                <select id="addStatus" class="form-input" required>
                                    <option value="">请选择状态</option>
                                    <option value="停用">停用</option>
                                    <option value="借用">借用</option>
                                    <option value="领用">领用</option>
                                    <option value="挂失">挂失</option>
                                    <option value="在库">在库</option>
                                </select>
                            </div>

                            <div>
                                <label for="addDepartment" class="block text-sm font-medium text-gray-700 mb-1">所属部门：</label>
                                <input type="text" id="addDepartment" class="form-input" placeholder="请输入部门名称" required>
                            </div>

                            <div>
                                <label for="addLocation" class="block text-sm font-medium text-gray-700 mb-1">存放位置：</label>
                                <input type="text" id="addLocation" class="form-input" placeholder="请输入存放位置" required>
                            </div>

                            <div class="flex justify-end space-x-3 pt-4">
                                <button type="submit" class="btn-primary">
                                    <i class="fa fa-save mr-1"></i>添加钥匙
                                </button>
                            </div>
                        </form>
                    </div>

                    <div class="border-t md:border-t-0 md:border-l pt-8 md:pt-0 md:pl-8 border-gray-200">
                        <h3 class="text-lg font-semibold mb-4">批量导入钥匙 (Excel)</h3>

                        <div id="fileDropArea" class="file-drop-area mb-4">
                            <div class="text-center py-8">
                                <i class="fa fa-cloud-upload text-4xl text-primary mb-3"></i>
                                <p class="font-medium text-lg">拖放Excel文件到此处</p>
                                <p class="text-gray-500 text-sm mt-1">或</p>
                                <button id="chooseExcelFileBtn" class="btn-primary mt-3">
                                    <i class="fa fa-folder-open mr-2"></i>选择Excel文件
                                </button>
                            </div>
                        </div>

                        <input type="file" id="excelFile" accept=".xlsx, .xls" class="hidden">

                        <div id="importPreview" class="hidden">
                            <h4 class="text-md font-medium mb-3">导入预览 <span id="previewCount" class="text-sm text-gray-500"></span></h4>
                            <div class="import-preview">
                                <table class="preview-table">
                                    <thead>
                                        <tr>
                                            <th>编号</th>
                                            <th>名称</th>
                                            <th>类别</th>
                                            <th>状态</th>
                                            <th>部门</th>
                                            <th>存放位置</th>
                                            <th>导入状态</th>
                                        </tr>
                                    </thead>
                                    <tbody id="previewTableBody"></tbody>
                                </table>
                            </div>

                            <div class="flex justify-end mt-4 space-x-3">
                                <button id="cancelImportBtn" class="btn-secondary">
                                    <i class="fa fa-times mr-1"></i>取消导入
                                </button>
                                <button id="confirmImportBtn" class="btn-primary">
                                    <i class="fa fa-check mr-1"></i>确认导入
                                </button>
                            </div>
                        </div>

                        <div class="mt-4 text-sm text-gray-600">
                            <p class="mb-1">Excel文件格式要求：</p>
                            <ul class="list-disc pl-5 space-y-1">
                                <li>第一行为表头：编号,名称,类别,状态,部门,存放位置</li>
                                <li>状态支持：停用、借用、领用、挂失、在库</li>
                                <li>编号必须是唯一数字</li>
                                <li>每行代表一条钥匙记录</li>
                            </ul>
                        </div>

                        <div class="mt-6 flex justify-center space-x-4">
                            <a href="#" id="downloadTemplate" class="text-primary hover:underline flex items-center">
                                <i class="fa fa-download mr-1"></i>下载Excel模板
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <section id="deleteKey" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-trash-o"></i>删除钥匙</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-4xl mx-auto">
                <h3 class="text-lg font-semibold mb-4">单独删除钥匙</h3>
                <form id="deleteKeyForm" class="space-y-4">
                    <div>
                        <label for="deleteInput" class="block text-sm font-medium text-gray-700 mb-1">钥匙编号或名称：</label>
                        <div class="relative">
                            <input type="text" id="deleteInput" class="form-input" placeholder="请输入要删除的钥匙编号或名称" required>
                            <div id="deleteSuggestions" class="autocomplete-suggestions hidden"></div>
                        </div>
                    </div>
                    <div class="flex justify-end space-x-3 pt-4">
                        <button type="submit" class="btn-danger">
                            <i class="fa fa-trash-o mr-1"></i>删除钥匙
                        </button>
                    </div>
                </form>

                <hr class="my-6">

                <div class="mb-4">
                    <p class="text-gray-700 font-medium mb-2">可选择多把钥匙进行批量删除。</p>
                    <button onclick="batchDeleteKeys()" class="btn-danger">
                        <i class="fa fa-trash mr-1"></i>批量删除选中的钥匙
                    </button>
                </div>

                <div id="deleteKeyTableContainer" class="overflow-x-auto mb-6">
                    <p class="text-gray-500 mb-4">共 <span id="deleteTotalKeysCount">0</span> 条记录</p>
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    <input type="checkbox" id="selectAllKeys">
                                </th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    编号</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    名称</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    类别</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    状态</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    部门</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    存放位置</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    操作</th>
                            </tr>
                        </thead>
                        <tbody id="deleteTableBody" class="bg-white divide-y divide-gray-200">
                            <tr>
                                <td colspan="8" class="px-6 py-10 text-center text-gray-500">
                                    <i class="fa fa-info-circle mr-2"></i>正在加载钥匙数据...
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <section id="modifyKey" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-pencil"></i>修改钥匙信息</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-2xl mx-auto">
                <div id="modifySearchBox" class="mb-4">
                    <label for="modifySearchInput" class="block text-sm font-medium text-gray-700 mb-1">查找钥匙（编号或名称）：</label>
                    <div class="flex space-x-2 relative">
                        <input type="text" id="modifySearchInput" class="form-input flex-grow" placeholder="请输入钥匙编号或名称">
                        <div id="modifySuggestions" class="autocomplete-suggestions hidden"></div>
                        <button type="button" onclick="searchKeyForModify()" class="btn-primary">查找</button>
                    </div>
                </div>
                <form id="modifyKeyForm" class="space-y-4 hidden">
                    <h3 class="text-lg font-semibold mb-2" id="modifyFormTitle">修改信息</h3>
                    <div>
                        <label for="modifyId" class="block text-sm font-medium text-gray-700 mb-1">钥匙编号：</label>
                        <input type="text" id="modifyId" class="form-input bg-gray-100 cursor-not-allowed" disabled>
                    </div>
                    <div>
                        <label for="modifyName" class="block text-sm font-medium text-gray-700 mb-1">钥匙名称：</label>
                        <input type="text" id="modifyName" class="form-input" required>
                    </div>
                    <div>
                        <label for="modifyCategory" class="block text-sm font-medium text-gray-700 mb-1">钥匙类别：</label>
                        <input type="text" id="modifyCategory" class="form-input" required>
                    </div>
                    <div>
                        <label for="modifyStatus" class="block text-sm font-medium text-gray-700 mb-1">钥匙状态：</label>
                        <select id="modifyStatus" class="form-input" required>
                            <option value="停用">停用</option>
                            <option value="借用">借用</option>
                            <option value="领用">领用</option>
                            <option value="挂失">挂失</option>
                            <option value="在库">在库</option>
                        </select>
                    </div>
                    <div>
                        <label for="modifyDepartment" class="block text-sm font-medium text-gray-700 mb-1">所属部门：</label>
                        <input type="text" id="modifyDepartment" class="form-input" required>
                    </div>
                    <div>
                        <label for="modifyLocation" class="block text-sm font-medium text-gray-700 mb-1">存放位置：</label>
                        <input type="text" id="modifyLocation" class="form-input" required>
                    </div>
                    <div class="flex justify-end space-x-3 pt-4">
                        <button type="submit" class="btn-primary">
                            <i class="fa fa-pencil mr-1"></i>更新钥匙信息
                        </button>
                    </div>
                </form>
            </div>
        </section>

        <section id="dataComparison" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-exchange"></i>数据比对与导出</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-4xl mx-auto">
                <div class="mb-6 border-b pb-6">
                    <h3 class="text-lg font-semibold mb-4">导出所有钥匙数据</h3>
                    <p class="text-gray-600 mb-4">点击下方按钮，即可将系统中的所有钥匙数据导出为 Excel 文件。</p>
                    <button id="exportAllKeysBtn" class="btn-success w-full">
                        <i class="fa fa-file-excel-o mr-1"></i>导出所有钥匙到Excel
                    </button>
                </div>

                <div>
                    <h3 class="text-lg font-semibold mb-4">比对钥匙数据</h3>
                    <p class="text-gray-600 mb-2">上传一个包含所有“在库”钥匙的Excel文件，系统将比对并列出：</p>
                    <ul class="list-disc pl-5 text-gray-600 mb-4 text-sm">
                        <li>系统中钥匙状态非“在库”的钥匙（有状态，标黄）。</li>
                        <li>系统中状态为“在库”，但未在您上传表格中出现的钥匙（数据异常，标红）。</li>
                    </ul>
                    <div id="fileDropAreaComparison" class="file-drop-area mb-4">
                        <div class="text-center py-8">
                            <i class="fa fa-cloud-upload text-4xl text-primary mb-3"></i>
                            <p class="font-medium text-lg">拖放Excel文件到此处进行比对</p>
                            <p class="text-gray-500 text-sm mt-1">或</p>
                            <button id="chooseFileBtnComparison" class="btn-primary mt-3">
                                <i class="fa fa-folder-open mr-2"></i>选择Excel文件
                            </button>
                        </div>
                    </div>
                    <input type="file" id="excelFileComparison" accept=".xlsx, .xls" class="hidden">
                    <div class="mt-4 text-sm text-gray-600">
                        <p class="mb-1">比对Excel文件格式要求：</p>
                        <ul class="list-disc pl-5 space-y-1">
                            <li>文件需包含 "编号" 列，用于比对。</li>
                        </ul>
                    </div>

                    <div id="comparisonResults" class="hidden mt-6">
                        <h4 class="text-md font-medium mb-3">比对结果 <span id="comparisonCount" class="text-sm text-gray-500"></span></h4>
                        <div class="import-preview">
                            <table class="preview-table">
                                <thead>
                                    <tr>
                                        <th>编号</th>
                                        <th>名称</th>
                                        <th>类别</th>
                                        <th>状态</th>
                                        <th>部门</th>
                                        <th>存放位置</th>
                                        <th>比对结果</th>
                                    </tr>
                                </thead>
                                <tbody id="comparisonTableBody"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <section id="borrowKey" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-handshake-o"></i>借/领用钥匙</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-2xl mx-auto">
                <form id="borrowKeyForm" class="space-y-4">
                    <div>
                        <label for="borrowerName" class="block text-sm font-medium text-gray-700 mb-1">借/领用人姓名：</label>
                        <input type="text" id="borrowerName" class="form-input" placeholder="请输入姓名" required>
                    </div>
                    <div>
                        <label for="borrowType" class="block text-sm font-medium text-gray-700 mb-1">申请类型：</label>
                        <select id="borrowType" class="form-input" required>
                            <option value="">请选择申请类型</option>
                            <option value="借用">借用</option>
                            <option value="领用">领用</option>
                        </select>
                    </div>

                    <div id="borrowDateFields" class="space-y-4">
                        <div>
                            <label for="borrowDate" class="block text-sm font-medium text-gray-700 mb-1">借用日期：</label>
                            <input type="date" id="borrowDate" class="form-input">
                        </div>
                        <div>
                            <label for="returnDate" class="block text-sm font-medium text-gray-700 mb-1">归还日期：</label>
                            <input type="date" id="returnDate" class="form-input">
                        </div>
                    </div>

                    <div>
                        <label for="borrowKeyId" class="block text-sm font-medium text-gray-700 mb-1">钥匙编号或名称：</label>
                        <div class="relative">
                            <input type="text" id="borrowKeyId" class="form-input" placeholder="请输入要借用的钥匙编号或名称" required>
                            <div id="borrowSuggestions" class="autocomplete-suggestions hidden"></div>
                        </div>
                    </div>
                    <div>
                        <label for="borrowReason" class="block text-sm font-medium text-gray-700 mb-1">申请原因：</label>
                        <textarea id="borrowReason" class="form-input" rows="3" placeholder="请输入申请原因" required></textarea>
                    </div>
                    <div class="flex justify-end space-x-3 pt-4">
                        <button type="submit" class="btn-primary">
                            <i class="fa fa-send-o mr-1"></i>提交申请
                        </button>
                    </div>
                </form>
            </div>
        </section>

        <section id="reportLostKey" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-exclamation-triangle"></i>挂失钥匙</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-2xl mx-auto">
                <form id="lostKeyForm" class="space-y-4">
                    <div>
                        <label for="lostKeyId" class="block text-sm font-medium text-gray-700 mb-1">钥匙编号或名称：</label>
                        <div class="relative">
                            <input type="text" id="lostKeyId" class="form-input" placeholder="请输入挂失的钥匙编号或名称" required>
                            <div id="lostSuggestions" class="autocomplete-suggestions hidden"></div>
                        </div>
                    </div>
                    <div>
                        <label for="reporterName" class="block text-sm font-medium text-gray-700 mb-1">挂失人姓名：</label>
                        <input type="text" id="reporterName" class="form-input" placeholder="请输入姓名" required>
                    </div>
                    <div>
                        <label for="lostReason" class="block text-sm font-medium text-gray-700 mb-1">挂失原因：</label>
                        <textarea id="lostReason" class="form-input" rows="3" placeholder="请输入挂失原因" required></textarea>
                    </div>
                    <div class="flex justify-end space-x-3 pt-4">
                        <button type="submit" class="btn-danger">
                            <i class="fa fa-send-o mr-1"></i>提交挂失申请
                        </button>
                    </div>
                </form>
            </div>
        </section>

        <section id="returnKey" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-undo"></i>还钥匙</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow max-w-2xl mx-auto">
                <form id="returnKeyForm" class="space-y-4">
                    <div>
                        <label for="returnKeyId" class="block text-sm font-medium text-gray-700 mb-1">钥匙编号或名称：</label>
                        <div class="relative">
                            <input type="text" id="returnKeyId" class="form-input" placeholder="请输入要归还的钥匙编号或名称" required>
                            <div id="returnSuggestions" class="autocomplete-suggestions hidden"></div>
                        </div>
                    </div>
                    <div>
                        <label for="returnerName" class="block text-sm font-medium text-gray-700 mb-1">归还人姓名：</label>
                        <input type="text" id="returnerName" class="form-input" placeholder="请输入归还人姓名" required>
                    </div>
                    <div class="flex justify-end space-x-3 pt-4">
                        <button type="submit" class="btn-primary">
                            <i class="fa fa-check-circle mr-1"></i>确认归还
                        </button>
                    </div>
                </form>
            </div>
        </section>


        <section id="approvals" class="hidden fade-in">
            <div class="flex justify-between items-center mb-6">
                <h2 class="section-title"><i class="fa fa-gavel"></i>待审批列表</h2>
                <button onclick="showSection('mainMenu')" class="btn-secondary">
                    <i class="fa fa-arrow-left mr-1"></i>返回主菜单
                </button>
            </div>

            <div class="bg-white rounded-xl p-6 card-shadow">
                <p class="text-gray-500 mb-4">共有 <span id="pendingApprovalsCount">0</span> 条待审批请求</p>
                <div id="approvalsList" class="space-y-4">
                    </div>
            </div>
        </section>

    </main>
    <script>
        // 原始钥匙数据（用于生成新数据）
        const originalKeys = [
            { id: 1001, name: "总经理办公室钥匙", category: "办公室", status: "在库", department: "行政部", location: "行政办公室", holder: "" },
            { id: 1002, name: "财务室钥匙", category: "办公室", status: "在库", department: "财务部", location: "财务办公室", holder: "" },
            { id: 1003, name: "会议室钥匙", category: "会议室", status: "在库", department: "行政部", location: "行政办公室", holder: "" },
            { id: 1004, name: "设备间钥匙", category: "设备间", status: "借用", department: "工程部", location: "工程部办公室", holder: "王平" },
            { id: 1005, name: "仓库钥匙", category: "仓库", status: "领用", department: "物流部", location: "仓库办公室", holder: "李华" },
            { id: 1006, name: "一号车间大门", category: "车间", status: "在库", department: "生产部", location: "生产部值班室", holder: "" },
            { id: 1007, name: "二号车间大门", category: "车间", status: "在库", department: "生产部", location: "生产部值班室", holder: "" },
            { id: 1008, name: "行政部资料室", category: "办公室", status: "在库", department: "行政部", location: "行政办公室", holder: "" },
            { id: 1009, name: "研发部实验室1", category: "实验室", status: "在库", department: "研发部", location: "研发部办公室", holder: "" },
            { id: 1010, name: "研发部实验室2", category: "实验室", status: "借用", department: "研发部", location: "研发部办公室", holder: "赵雷" },
            { id: 1011, name: "消防栓钥匙", category: "安全设施", status: "在库", department: "安保部", location: "安保部办公室", holder: "" },
            { id: 1012, name: "监控室钥匙", category: "安全设施", status: "在库", department: "安保部", location: "安保部办公室", holder: "" },
            { id: 1013, name: "机房钥匙", category: "设备间", status: "在库", department: "IT部", location: "IT办公室", holder: "" },
            { id: 1014, name: "一号楼配电室钥匙", category: "设备间", status: "在库", department: "工程部", location: "工程部办公室", holder: "" },
            { id: 1015, name: "二号楼配电室钥匙", category: "设备间", status: "在库", department: "工程部", location: "工程部办公室", holder: "" },
            { id: 1016, name: "临时工具间钥匙", category: "设备间", status: "停用", department: "工程部", location: "工程部储物柜", holder: "" },
            { id: 1017, name: "备用仓库钥匙", category: "仓库", status: "挂失", department: "物流部", location: "仓库办公室", holder: "" },
            { id: 1018, name: "研发部实验室3", category: "实验室", status: "在库", department: "研发部", location: "研发部办公室", holder: "" },
            { id: 1019, name: "研发部实验室4", category: "实验室", status: "在库", department: "研发部", location: "研发部办公室", holder: "" }
        ];

        // 自动生成新钥匙的逻辑
        let keys = [];
        let nextId = 1020; // 从1020开始新的编号

        originalKeys.forEach(key => {
            // 保留原始钥匙
            keys.push(key);

            // 为每一把原始钥匙新增两把备用钥匙
            for (let i = 0; i < 2; i++) {
                keys.push({
                    id: nextId++,
                    name: key.name,
                    category: key.category,
                    status: key.status,
                    department: key.department,
                    location: key.location,
                    holder: key.holder
                });
            }
        });

        // 存储待审批请求
        let borrowRequests = [];
        // 存储当前登录用户的信息
        let currentUser = { role: null, name: null }; // 新增 name 属性
        // 密码映射
        const passwords = {
            '111': { role: '总管理员', name: '张三' },
            '222': { role: '员工', name: '王平' },
            '333': { role: '主管', name: '李明' },
            '444': { role: '区域负责人A', name: '李华' },
            '555': { role: '区域负责人B', name: '赵雷' }
        };
        // 部门与区域负责人的映射关系
        const departmentToAreaManager = {
            '行政部': '区域负责人A',
            '财务部': '区域负责人A',
            '会议室': '区域负责人A',
            '工程部': '区域负责人A',
            '物流部': '区域负责人B',
            '生产部': '区域负责人B',
            '研发部': '区域负责人B',
            '安保部': '区域负责人B',
            'IT部': '区域负责人A', // 添加IT部到区域负责人A
        };
        // 用于在DOM中移除和重新添加登录界面的引用
        let loginSectionElement = null;
        // 登录逻辑
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const password = document.getElementById('passwordInput').value;
            const user = passwords[password];
            if (user) {
                currentUser.role = user.role;
                currentUser.name = user.name;
                // 存储引用并从DOM中移除登录界面
                loginSectionElement = document.getElementById('loginSection');
                loginSectionElement.remove();
                document.getElementById('mainHeader').classList.remove('hidden'); // 显示头部导航
                document.getElementById('mainContent').classList.remove('hidden'); // 显示主内容
                document.getElementById('currentUserRole').textContent = currentUser.role;
                document.getElementById('currentUserName').textContent = currentUser.name;
                renderMainMenu(); // 根据角色渲染菜单
                showSection('mainMenu'); // 显示主菜单
                showNotification(`欢迎，${currentUser.name} (${currentUser.role})！`, 'success');
            } else {
                showNotification('密码错误，请重试。', 'error');
            }
            document.getElementById('passwordInput').value = '';
        });
        // 登出逻辑
        document.getElementById('logoutBtn').addEventListener('click', () => {
            currentUser.role = null;
            currentUser.name = null;
            // 将登录界面重新添加到body中
            if (loginSectionElement) {
                document.body.appendChild(loginSectionElement);
                // 确保它没有 hidden 类，以便显示
                loginSectionElement.classList.remove('hidden');
            }
            document.getElementById('mainHeader').classList.add('hidden'); // 隐藏头部导航
            document.getElementById('mainContent').classList.add('hidden'); // 隐藏主内容
            showNotification('您已安全退出。', 'info');
        });
        // 页面切换逻辑
        function showSection(sectionId) {
            document.querySelectorAll('section').forEach(section => {
                section.classList.add('hidden');
                section.classList.remove('fade-in');
            });
            const sectionToShow = document.getElementById(sectionId);
            if (sectionToShow) {
                sectionToShow.classList.remove('hidden');
                sectionToShow.classList.add('fade-in');
            }
            // 针对不同页面刷新数据
            if (sectionId === 'statusManagement') {
                displayKeys('all');
            } else if (sectionId === 'deleteKey') {
                displayKeysForDeletion();
            } else if (sectionId === 'departmentManagement') {
                generateDepartmentButtons();
                displayByDepartment('all');
            } else if (sectionId === 'approvals') {
                renderApprovalsList(); // 每次进入审批页面都刷新列表
            } else if (sectionId === 'myKeysSection') {
                displayMyHeldKeys();
            }
            scrollToTop(); // 切换页面后滚动到顶部
        }
        // 动态渲染主菜单
        function renderMainMenu() {
            const menuOptions = document.getElementById('menuOptions');
            menuOptions.innerHTML = '';
            const menuItems = {
                'myKeysSection': {
                    title: '我持有的钥匙',
                    desc: '查看您个人当前持有的钥匙清单',
                    icon: 'fa fa-user-circle-o'
                },
                'statusManagement': {
                    title: '钥匙信息查询',
                    desc: '查看所有钥匙、按状态筛选及查询',
                    icon: 'fa fa-search'
                },
                'departmentManagement': {
                    title: '部门钥匙管理',
                    desc: '按部门和类别管理钥匙信息',
                    icon: 'fa fa-building-o'
                },
                'addKey': {
                    title: '添加钥匙',
                    desc: '录入新的钥匙信息或批量导入',
                    icon: 'fa fa-plus'
                },
                'deleteKey': {
                    title: '删除钥匙',
                    desc: '从系统中移除不需要的钥匙信息，支持批量删除',
                    icon: 'fa fa-trash-o'
                },
                'modifyKey': {
                    title: '修改钥匙信息',
                    desc: '更新现有钥匙的信息内容，可按编号或名称查找',
                    icon: 'fa fa-pencil'
                },
                'borrowKey': {
                    title: '借/领用钥匙',
                    desc: '提交钥匙借用或领用申请',
                    icon: 'fa fa-handshake-o'
                },
                'returnKey': {
                    title: '还钥匙',
                    desc: '归还已借出或领用的钥匙',
                    icon: 'fa fa-undo'
                },
                'reportLostKey': {
                    title: '挂失钥匙',
                    desc: '提交钥匙遗失申请，并进入审批流程',
                    icon: 'fa fa-exclamation-triangle'
                },
                'approvals': {
                    title: '审批功能',
                    desc: '处理待审批的钥匙借/领用/挂失请求',
                    icon: 'fa fa-gavel'
                },
                'dataComparison': {
                    title: '数据比对与导出',
                    desc: '比对外部文件，查找不在库钥匙，并导出所有数据',
                    icon: 'fa fa-exchange'
                }
            };
            const rolesPermissions = {
                '总管理员': ['myKeysSection', 'statusManagement', 'departmentManagement', 'addKey', 'deleteKey', 'modifyKey', 'reportLostKey', 'dataComparison'],
                '员工': ['myKeysSection', 'statusManagement', 'departmentManagement', 'borrowKey', 'returnKey', 'reportLostKey'],
                '主管': ['myKeysSection', 'statusManagement', 'departmentManagement', 'modifyKey', 'borrowKey', 'returnKey', 'reportLostKey', 'approvals'],
                '区域负责人A': ['myKeysSection', 'statusManagement', 'departmentManagement', 'modifyKey', 'reportLostKey', 'approvals'],
                '区域负责人B': ['myKeysSection', 'statusManagement', 'departmentManagement', 'modifyKey', 'reportLostKey', 'approvals']
            };
            const allowedItems = rolesPermissions[currentUser.role] || [];
            allowedItems.forEach(sectionId => {
                const item = menuItems[sectionId];
                if (item) {
                    const card = document.createElement('div');
                    card.className = 'bg-white rounded-xl p-6 card-shadow cursor-pointer menu-hover relative';
                    card.setAttribute('onclick', `showSection('${sectionId}')`);
                    let notificationBadge = '';
                    if (sectionId === 'approvals') {
                        const pendingCount = getPendingApprovals().length;
                        if (pendingCount > 0) {
                            notificationBadge = `<span class="notification-badge">${pendingCount}</span>`;
                        }
                    }
                    card.innerHTML = `
                <div class="flex items-center mb-4">
                    <div class="w-12 h-12 rounded-full bg-primary/10 flex items-center justify-center text-primary mr-4">
                        <i class="${item.icon} text-xl"></i>
                    </div>
                    <h3 class="text-lg font-semibold">${item.title}</h3>
                </div>
                <p class="text-gray-500 text-sm">${item.desc}</p>
                ${notificationBadge}
                `;
                    menuOptions.appendChild(card);
                }
            });
        }
        // 获取当前用户角色对应的待审批请求
        function getPendingApprovals() {
            if (currentUser.role === '主管') {
                return borrowRequests.filter(req => req.status === '待主管审批');
            }
            if (currentUser.role === '区域负责人A') {
                return borrowRequests.filter(req => req.status === '待区域负责人A审批');
            }
            if (currentUser.role === '区域负责人B') {
                return borrowRequests.filter(req => req.status === '待区域负责人B审批');
            }
            return [];
        }
        // 滚动到页面顶部
        function scrollToTop() {
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }
        // 绑定返回主菜单按钮的点击事件，并添加滚动到顶部功能
        document.querySelectorAll('button[onclick*="showSection(\'mainMenu\')"]').forEach(btn => {
            btn.addEventListener('click', () => {
                showSection('mainMenu'); // 调用showSection会处理滚动和菜单刷新
            });
        });
        // 显示通知
        function showNotification(message, type = 'info') {
            const notification = document.getElementById('notification');
            const notificationText = document.getElementById('notificationText');
            notificationText.textContent = message;
            notification.className = 'notification';
            notification.classList.add('show');
            if (type === 'success') {
                notification.style.backgroundColor = '#d4edda';
                notification.style.color = '#155724';
            } else if (type === 'error') {
                notification.style.backgroundColor = '#f8d7da';
                notification.style.color = '#721c24';
            } else if (type === 'info') {
                notification.style.backgroundColor = '#cce5ff';
                notification.style.color = '#004085';
            } else if (type === 'warning') {
                notification.style.backgroundColor = '#fff3cd';
                notification.style.color = '#856404';
            }
            setTimeout(() => {
                notification.classList.remove('show');
            }, 3000);
        }
        // 我持有的钥匙
        function displayMyHeldKeys() {
            const myKeysTableBody = document.getElementById('myKeysTableBody');
            const myKeysCount = document.getElementById('myKeysCount');
            const heldKeys = keys.filter(key => key.holder === currentUser.name);
            myKeysTableBody.innerHTML = '';
            myKeysCount.textContent = heldKeys.length;

            if (heldKeys.length === 0) {
                myKeysTableBody.innerHTML = `<tr><td colspan="6" class="px-6 py-10 text-center text-gray-500">
                                            <i class="fa fa-info-circle mr-2"></i>您当前没有持有的钥匙。</td></tr>`;
            } else {
                heldKeys.forEach(key => {
                    const row = document.createElement('tr');
                    const statusClass = getStatusClass(key.status);
                    row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${key.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.name}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.category}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                        <span class="${statusClass}">${key.status}</span>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.department}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.location}</td>
                `;
                    myKeysTableBody.appendChild(row);
                });
            }
        }
        // 钥匙信息查询
        function displayKeys(status) {
            const statusTableBody = document.getElementById('statusTableBody');
            let filteredKeys = keys;
            if (status !== 'all') {
                filteredKeys = keys.filter(key => key.status === status);
            }
            statusTableBody.innerHTML = '';
            document.getElementById('totalKeysCount').textContent = filteredKeys.length;
            if (filteredKeys.length === 0) {
                statusTableBody.innerHTML = `<tr><td colspan="7" class="px-6 py-10 text-center text-gray-500">
                                            <i class="fa fa-info-circle mr-2"></i>没有找到符合条件的钥匙。</td></tr>`;
            } else {
                filteredKeys.forEach(key => {
                    const row = document.createElement('tr');
                    const statusClass = getStatusClass(key.status);
                    // 根据钥匙状态显示借/领用人
                    const holderName = (key.status === '借用' || key.status === '领用') ? key.holder : '';
                    row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${key.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.name}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.category}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                        <span class="${statusClass}">${key.status}</span>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.department}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.location}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${holderName}</td>
                `;
                    statusTableBody.appendChild(row);
                });
            }
        }
        function searchKeys() {
            const searchInput = document.getElementById('statusSearchInput').value.toLowerCase();
            const statusTableBody = document.getElementById('statusTableBody');
            let filteredKeys = keys.filter(key =>
                key.id.toString().includes(searchInput) || key.name.toLowerCase().includes(searchInput)
            );
            statusTableBody.innerHTML = '';
            document.getElementById('totalKeysCount').textContent = filteredKeys.length;
            if (filteredKeys.length === 0) {
                statusTableBody.innerHTML = `<tr><td colspan="7" class="px-6 py-10 text-center text-gray-500">
                                            <i class="fa fa-info-circle mr-2"></i>没有找到符合条件的钥匙。</td></tr>`;
            } else {
                filteredKeys.forEach(key => {
                    const row = document.createElement('tr');
                    const statusClass = getStatusClass(key.status);
                    const holderName = (key.status === '借用' || key.status === '领用') ? key.holder : '';
                    row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${key.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.name}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.category}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                        <span class="${statusClass}">${key.status}</span>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.department}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.location}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${holderName}</td>
                `;
                    statusTableBody.appendChild(row);
                });
            }
        }
        function getStatusClass(status) {
            switch (status) {
                case '在库':
                    return 'status-stock';
                case '借用':
                    return 'status-borrow';
                case '领用':
                    return 'status-lend';
                case '挂失':
                    return 'status-lost';
                case '停用':
                    return 'status-inactive';
                case '借用中': // 新增的审批中状态
                    return 'badge-info';
                default:
                    return '';
            }
        }
        // 部门钥匙管理
        function generateDepartmentButtons() {
            const departmentButtons = document.getElementById('departmentButtons');
            departmentButtons.innerHTML = '';
            // 移除根据角色过滤部门的逻辑，让所有部门按钮都显示
            let departments = [...new Set(keys.map(key => key.department))].sort();
            departments.forEach(dept => {
                const button = document.createElement('button');
                button.className = 'btn-secondary';
                button.textContent = dept;
                button.setAttribute('onclick', `displayByDepartment('${dept}')`);
                departmentButtons.appendChild(button);
            });
        }
        function displayByDepartment(department) {
            const departmentTableBody = document.getElementById('departmentTableBody');
            const categoryFilters = document.getElementById('categoryFilters');
            let filteredKeys = keys;
            let currentDepartment = department;
            if (department !== 'all') {
                // 移除根据角色限制访问的逻辑
                filteredKeys = keys.filter(key => key.department === department);
            }
            departmentTableBody.innerHTML = '';
            if (filteredKeys.length === 0) {
                departmentTableBody.innerHTML = `<tr><td colspan="6" class="px-6 py-10 text-center text-gray-500">
                                                <i class="fa fa-info-circle mr-2"></i>该部门没有钥匙记录。</td></tr>`;
                categoryFilters.classList.add('hidden');
            } else {
                // 渲染类别筛选器
                categoryFilters.innerHTML = '<span class="text-gray-500">类别筛选:</span>';
                const categories = [...new Set(filteredKeys.map(key => key.category))];
                categories.forEach(cat => {
                    const span = document.createElement('span');
                    span.className = 'clickable-category';
                    span.textContent = cat;
                    span.onclick = () => filterByCategory(cat, currentDepartment);
                    categoryFilters.appendChild(span);
                });
                categoryFilters.classList.remove('hidden');
                // 渲染钥匙列表
                filteredKeys.forEach(key => {
                    const row = document.createElement('tr');
                    const statusClass = getStatusClass(key.status);
                    row.innerHTML = `
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${key.id}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.name}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.category}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                        <span class="${statusClass}">${key.status}</span>
                    </td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.department}</td>
                    <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.location}</td>
                `;
                departmentTableBody.appendChild(row);
                });
            }
        }
        function filterByCategory(category, department) {
            const departmentTableBody = document.getElementById('departmentTableBody');
            let filteredKeys = keys.filter(key => (department === 'all' || key.department === department) && key.category === category);
            departmentTableBody.innerHTML = '';
            filteredKeys.forEach(key => {
                const row = document.createElement('tr');
                const statusClass = getStatusClass(key.status);
                row.innerHTML = `
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${key.id}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.name}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.category}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                    <span class="${statusClass}">${key.status}</span>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.department}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.location}</td>
            `;
                departmentTableBody.appendChild(row);
            });
        }
        // 添加钥匙
        document.getElementById('addKeyForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const newKey = {
                id: parseInt(document.getElementById('addId').value),
                name: document.getElementById('addName').value,
                category: document.getElementById('addCategory').value,
                status: document.getElementById('addStatus').value,
                department: document.getElementById('addDepartment').value,
                location: document.getElementById('addLocation').value,
                holder: ''
            };
            if (keys.some(key => key.id === newKey.id)) {
                showNotification('钥匙编号已存在，请使用不同编号。', 'error');
                return;
            }
            keys.push(newKey);
            showNotification('钥匙添加成功！', 'success');
            this.reset();
        });
        // 批量导入
        const fileDropArea = document.getElementById('fileDropArea');
        const excelFile = document.getElementById('excelFile');
        const chooseExcelFileBtn = document.getElementById('chooseExcelFileBtn');
        fileDropArea.addEventListener('dragover', (e) => {
            e.preventDefault();
            fileDropArea.classList.add('active');
        });
        fileDropArea.addEventListener('dragleave', (e) => {
            fileDropArea.classList.remove('active');
        });
        fileDropArea.addEventListener('drop', (e) => {
            e.preventDefault();
            fileDropArea.classList.remove('active');
            const file = e.dataTransfer.files[0];
            if (file) {
                handleFile(file);
            }
        });
        chooseExcelFileBtn.addEventListener('click', () => {
            excelFile.click();
        });
        excelFile.addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file) {
                handleFile(file);
            }
        });
        function handleFile(file) {
            const reader = new FileReader();
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array' });
                const sheetName = workbook.SheetNames[0];
                const worksheet = workbook.Sheets[sheetName];
                const importedData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
                // 检查文件头部
                if (importedData.length < 2 || importedData[0].join(',') !== '编号,名称,类别,状态,部门,存放位置') {
                    showNotification('Excel文件格式不正确，请检查文件头。', 'error');
                    return;
                }
                const newKeys = importedData.slice(1).map(row => ({
                    id: parseInt(row[0]),
                    name: row[1],
                    category: row[2],
                    status: row[3],
                    department: row[4],
                    location: row[5],
                    holder: '' // 导入的钥匙默认没有持有人
                }));
                renderImportPreview(newKeys);
            };
            reader.readAsArrayBuffer(file);
        }
        function renderImportPreview(newKeys) {
            const previewTableBody = document.getElementById('previewTableBody');
            previewTableBody.innerHTML = '';
            document.getElementById('previewCount').textContent = `(${newKeys.length} 条记录)`;
            const validStatuses = ['停用', '借用', '领用', '挂失', '在库'];
            let importableKeys = [];
            newKeys.forEach(key => {
                const row = document.createElement('tr');
                let status = '';
                if (!key.id || isNaN(key.id)) {
                    status = '<span class="badge-error">编号无效</span>';
                } else if (keys.some(k => k.id === key.id)) {
                    status = '<span class="badge-error">编号已存在</span>';
                } else if (!key.name || !key.category || !key.department || !key.location) {
                    status = '<span class="badge-error">信息不完整</span>';
                } else if (!validStatuses.includes(key.status)) {
                    status = '<span class="badge-error">状态无效</span>';
                } else {
                    status = '<span class="badge-success">可导入</span>';
                    importableKeys.push(key);
                }
                row.innerHTML = `
                <td>${key.id || 'N/A'}</td>
                <td>${key.name || 'N/A'}</td>
                <td>${key.category || 'N/A'}</td>
                <td>${key.status || 'N/A'}</td>
                <td>${key.department || 'N/A'}</td>
                <td>${key.location || 'N/A'}</td>
                <td>${status}</td>
            `;
                previewTableBody.appendChild(row);
            });
            document.getElementById('importPreview').classList.remove('hidden');
            document.getElementById('fileDropArea').classList.add('hidden');
            document.getElementById('confirmImportBtn').onclick = () => confirmImport(importableKeys);
            document.getElementById('cancelImportBtn').onclick = cancelImport;
        }
        function confirmImport(importableKeys) {
            keys = keys.concat(importableKeys);
            showNotification(`成功导入 ${importableKeys.length} 条钥匙。`, 'success');
            cancelImport();
        }
        function cancelImport() {
            document.getElementById('importPreview').classList.add('hidden');
            document.getElementById('fileDropArea').classList.remove('hidden');
            document.getElementById('excelFile').value = '';
        }
        document.getElementById('downloadTemplate').addEventListener('click', (e) => {
            e.preventDefault();
            const data = [
                ['编号', '名称', '类别', '状态', '部门', '存放位置']
            ];
            const ws = XLSX.utils.aoa_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "钥匙模板");
            XLSX.writeFile(wb, "钥匙导入模板.xlsx");
        });
        // 删除钥匙
        document.getElementById('deleteKeyForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const deleteInput = document.getElementById('deleteInput').value.trim();
            let keyToDelete;
            // 判断输入是数字还是字符串
            if (!isNaN(deleteInput) && deleteInput !== '') {
                keyToDelete = keys.find(key => key.id === parseInt(deleteInput));
            } else {
                keyToDelete = keys.find(key => key.name === deleteInput);
            }
            if (keyToDelete) {
                if (confirm(`确定要删除钥匙 "${keyToDelete.name}" (编号: ${keyToDelete.id}) 吗？`)) {
                    keys = keys.filter(key => key.id !== keyToDelete.id);
                    showNotification('钥匙删除成功！', 'success');
                    displayKeysForDeletion(); // 刷新列表
                }
            } else {
                showNotification('未找到匹配的钥匙，请检查编号或名称。', 'error');
            }
            this.reset();
        });
        function displayKeysForDeletion() {
            const deleteTableBody = document.getElementById('deleteTableBody');
            deleteTableBody.innerHTML = '';
            document.getElementById('deleteTotalKeysCount').textContent = keys.length;
            if (keys.length === 0) {
                deleteTableBody.innerHTML = `<tr><td colspan="8" class="px-6 py-10 text-center text-gray-500">
                                            <i class="fa fa-info-circle mr-2"></i>没有钥匙记录可供删除。</td></tr>`;
                return;
            }
            keys.forEach(key => {
                const row = document.createElement('tr');
                const statusClass = getStatusClass(key.status);
                row.innerHTML = `
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                    <input type="checkbox" class="key-checkbox" data-key-id="${key.id}">
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">${key.id}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.name}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.category}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">
                    <span class="${statusClass}">${key.status}</span>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.department}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-700">${key.location}</td>
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <button onclick="deleteKeyById(${key.id})" class="text-danger hover:text-danger/70"><i class="fa fa-trash"></i></button>
                </td>
            `;
                deleteTableBody.appendChild(row);
            });
            document.getElementById('selectAllKeys').addEventListener('change', (e) => {
                document.querySelectorAll('.key-checkbox').forEach(checkbox => {
                    checkbox.checked = e.target.checked;
                });
            });
        }
        function deleteKeyById(id) {
            const key = keys.find(k => k.id === id);
            if (key && confirm(`确定要删除钥匙 "${key.name}" (编号: ${key.id}) 吗？`)) {
                keys = keys.filter(k => k.id !== id);
                showNotification('钥匙删除成功！', 'success');
                displayKeysForDeletion();
            }
        }
        function batchDeleteKeys() {
            const selectedKeys = [...document.querySelectorAll('.key-checkbox:checked')]
                .map(checkbox => parseInt(checkbox.dataset.keyId));
            if (selectedKeys.length === 0) {
                showNotification('请先选择要删除的钥匙。', 'warning');
                return;
            }
            if (confirm(`确定要删除这 ${selectedKeys.length} 把钥匙吗？`)) {
                keys = keys.filter(key => !selectedKeys.includes(key.id));
                showNotification(`${selectedKeys.length} 把钥匙已成功删除！`, 'success');
                displayKeysForDeletion();
            }
        }
        // 修改钥匙信息
        let keyToModify = null;
        function editKeyFromStatus(id) {
            const key = keys.find(k => k.id === id);
            if (key) {
                keyToModify = key;
                document.getElementById('modifyId').value = key.id;
                document.getElementById('modifyName').value = key.name;
                document.getElementById('modifyCategory').value = key.category;
                document.getElementById('modifyStatus').value = key.status;
                document.getElementById('modifyDepartment').value = key.department;
                document.getElementById('modifyLocation').value = key.location;
                // 隐藏查找框，显示表单
                document.getElementById('modifySearchBox').classList.add('hidden');
                document.getElementById('modifyKeyForm').classList.remove('hidden');
                document.getElementById('modifyFormTitle').textContent = `修改钥匙: ${key.name} (${key.id})`;
                showSection('modifyKey');
            } else {
                showNotification('未找到该钥匙。', 'error');
            }
        }
        function searchKeyForModify() {
            const modifySearchInput = document.getElementById('modifySearchInput').value.trim();
            if (!modifySearchInput) {
                showNotification('请输入钥匙编号或名称。', 'warning');
                return;
            }
            let key;
            if (!isNaN(modifySearchInput) && modifySearchInput !== '') {
                key = keys.find(k => k.id === parseInt(modifySearchInput));
            } else {
                key = keys.find(k => k.name === modifySearchInput);
            }
            if (key) {
                keyToModify = key;
                document.getElementById('modifyId').value = key.id;
                document.getElementById('modifyName').value = key.name;
                document.getElementById('modifyCategory').value = key.category;
                document.getElementById('modifyStatus').value = key.status;
                document.getElementById('modifyDepartment').value = key.department;
                document.getElementById('modifyLocation').value = key.location;
                document.getElementById('modifyKeyForm').classList.remove('hidden');
                document.getElementById('modifyFormTitle').textContent = `修改钥匙: ${key.name} (${key.id})`;
                document.getElementById('modifySearchBox').classList.remove('hidden'); // 保持显示
                showNotification('找到钥匙，请修改信息。', 'info');
            } else {
                showNotification('未找到匹配的钥匙。', 'error');
                document.getElementById('modifyKeyForm').classList.add('hidden');
            }
        }
        document.getElementById('modifyKeyForm').addEventListener('submit', function(e) {
            e.preventDefault();
            if (keyToModify) {
                keyToModify.name = document.getElementById('modifyName').value;
                keyToModify.category = document.getElementById('modifyCategory').value;
                keyToModify.status = document.getElementById('modifyStatus').value;
                keyToModify.department = document.getElementById('modifyDepartment').value;
                keyToModify.location = document.getElementById('modifyLocation').value;
                showNotification('钥匙信息更新成功！', 'success');
                // 重置修改表单状态，方便下次使用
                document.getElementById('modifyKeyForm').classList.add('hidden');
                document.getElementById('modifySearchBox').classList.remove('hidden');
                document.getElementById('modifySearchInput').value = '';
                keyToModify = null;
                showSection('statusManagement'); // 更新后返回状态管理页面
            }
        });
        // 数据比对与导出
        document.getElementById('exportAllKeysBtn').addEventListener('click', () => {
            if (keys.length === 0) {
                showNotification('没有钥匙数据可以导出。', 'warning');
                return;
            }
            const data = [
                ['编号', '名称', '类别', '状态', '部门', '存放位置', '借/领用人']
            ].concat(keys.map(key => [key.id, key.name, key.category, key.status, key.department, key.location, key.holder]));
            const ws = XLSX.utils.aoa_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "所有钥匙数据");
            XLSX.writeFile(wb, "所有钥匙数据.xlsx");
            showNotification('钥匙数据已成功导出！', 'success');
        });
        const fileDropAreaComparison = document.getElementById('fileDropAreaComparison');
        const excelFileComparison = document.getElementById('excelFileComparison');
        const chooseFileBtnComparison = document.getElementById('chooseFileBtnComparison');
        fileDropAreaComparison.addEventListener('dragover', (e) => {
            e.preventDefault();
            fileDropAreaComparison.classList.add('active');
        });
        fileDropAreaComparison.addEventListener('dragleave', () => {
            fileDropAreaComparison.classList.remove('active');
        });
        fileDropAreaComparison.addEventListener('drop', (e) => {
            e.preventDefault();
            fileDropAreaComparison.classList.remove('active');
            const file = e.dataTransfer.files[0];
            if (file) {
                handleComparisonFile(file);
            }
        });
        chooseFileBtnComparison.addEventListener('click', () => {
            excelFileComparison.click();
        });
        excelFileComparison.addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file) {
                handleComparisonFile(file);
            }
        });
        function handleComparisonFile(file) {
            const reader = new FileReader();
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array' });
                const sheetName = workbook.SheetNames[0];
                const worksheet = workbook.Sheets[sheetName];
                const comparisonData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });

                if (comparisonData.length < 2 || !comparisonData[0].includes('编号')) {
                    showNotification('比对文件格式不正确，请确保包含“编号”列。', 'error');
                    return;
                }

                const comparisonResults = [];
                const header = comparisonData[0];
                const idIndex = header.indexOf('编号');

                // 使用 Set 提高查找效率
                const uploadedInStockKeyIds = new Set(
                    comparisonData.slice(1)
                        .map(row => parseInt(row[idIndex]))
                        .filter(id => !isNaN(id))
                );

                const dataAnomalies = [];
                const statusMismatches = [];

                keys.forEach(key => {
                    const isKeyInUploadedList = uploadedInStockKeyIds.has(key.id);

                    if (!isKeyInUploadedList) {
                        // 钥匙不在上传的“在库”列表中
                        if (key.status === '在库') {
                            // 在系统总库中显示为“在库”，但在上传的表格中却不存在，说明存在异常
                            dataAnomalies.push({
                                id: key.id,
                                name: key.name,
                                category: key.category,
                                status: key.status,
                                department: key.department,
                                location: key.location,
                                result: '在总库中但未在上传表格中，数据异常'
                            });
                        } else {
                            // 钥匙在系统总库中状态正常，但不是“在库”状态，所以不在上传的表格中是合理的
                            statusMismatches.push({
                                id: key.id,
                                name: key.name,
                                category: key.category,
                                status: key.status,
                                department: key.department,
                                location: key.location,
                                result: `状态为 ${key.status} (有状态)`
                            });
                        }
                    }
                });

                // 合并并排序，红色在前，黄色在后
                const sortedResults = [...dataAnomalies, ...statusMismatches];
                renderComparisonResults(sortedResults);
            };
            reader.readAsArrayBuffer(file);
        }
        function renderComparisonResults(results) {
            const comparisonTableBody = document.getElementById('comparisonTableBody');
            comparisonTableBody.innerHTML = '';
            document.getElementById('comparisonCount').textContent = `(${results.length} 条记录)`;
            if (results.length === 0) {
                comparisonTableBody.innerHTML = `<tr><td colspan="7" class="px-6 py-10 text-center text-gray-500">
                                                <i class="fa fa-check-circle-o mr-2"></i>比对完成，没有发现不一致的记录。</td></tr>`;
            } else {
                results.forEach(result => {
                    const row = document.createElement('tr');
                    // 根据比对结果分配不同的徽章颜色
                    const badgeClass = result.result.includes('异常') ? 'badge-error' : 'badge-warning';
                    row.innerHTML = `
                    <td class="px-4 py-2">${result.id}</td>
                    <td class="px-4 py-2">${result.name}</td>
                    <td class="px-4 py-2">${result.category}</td>
                    <td class="px-4 py-2">${result.status}</td>
                    <td class="px-4 py-2">${result.department}</td>
                    <td class="px-4 py-2">${result.location}</td>
                    <td class="px-4 py-2"><span class="${badgeClass}">${result.result}</span></td>
                `;
                    comparisonTableBody.appendChild(row);
                });
            }
            document.getElementById('comparisonResults').classList.remove('hidden');
        }
        // 员工借/领用
        document.getElementById('borrowKeyForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const borrowerName = document.getElementById('borrowerName').value;
            const borrowType = document.getElementById('borrowType').value;
            const borrowDate = document.getElementById('borrowDate').value;
            const returnDate = document.getElementById('returnDate').value;
            const borrowKeyIdInput = document.getElementById('borrowKeyId').value.trim();
            const borrowReason = document.getElementById('borrowReason').value;

            // 校验借用日期和归还日期
            if (borrowType === '借用' && (!borrowDate || !returnDate)) {
                showNotification('请选择借用日期和归还日期。', 'error');
                return;
            }
            if (borrowType === '借用' && new Date(borrowDate) > new Date(returnDate)) {
                showNotification('归还日期不能早于借用日期。', 'error');
                return;
            }

            let keyToBorrow;
            if (!isNaN(borrowKeyIdInput) && borrowKeyIdInput !== '') {
                keyToBorrow = keys.find(k => k.id === parseInt(borrowKeyIdInput));
            } else {
                keyToBorrow = keys.find(k => k.name === borrowKeyIdInput);
            }
            if (!keyToBorrow) {
                showNotification('未找到该钥匙。', 'error');
                return;
            }
            if (keyToBorrow.status !== '在库') {
                showNotification(`钥匙 "${keyToBorrow.name}" 当前状态为 ${keyToBorrow.status}，无法借用。`, 'error');
                return;
            }

            // 新增的规则：当同一名称的钥匙只剩一把在库时，不能领用
            const inStockCount = keys.filter(k => k.name === keyToBorrow.name && k.status === '在库').length;
            if (inStockCount === 1 && borrowType === '领用') {
                showNotification(`钥匙 "${keyToBorrow.name}" 在库仅剩一把，不可领用，只能借用。`, 'warning');
                return;
            }

            const newRequest = {
                id: borrowRequests.length + 1,
                keyId: keyToBorrow.id,
                keyName: keyToBorrow.name,
                department: keyToBorrow.department,
                borrower: borrowerName,
                type: borrowType,
                reason: borrowReason,
                borrowDate: borrowDate, // 新增的借用日期
                returnDate: returnDate, // 新增的归还日期
                status: '待主管审批' // 员工提交后，首先进入主管审批
            };
            borrowRequests.push(newRequest);
            // keyToBorrow.status = '借用中'; // 不在这里修改钥匙状态，等到区域负责人批准
            showNotification('钥匙借用/领用申请已提交，请等待审批。', 'success');
            this.reset();
            renderMainMenu(); // 更新主菜单上的通知
        });
        // 员工挂失
        document.getElementById('lostKeyForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const reporterName = document.getElementById('reporterName').value;
            const lostKeyIdInput = document.getElementById('lostKeyId').value.trim();
            const lostReason = document.getElementById('lostReason').value;
            let keyToLost;
            if (!isNaN(lostKeyIdInput) && lostKeyIdInput !== '') {
                keyToLost = keys.find(k => k.id === parseInt(lostKeyIdInput));
            } else {
                keyToLost = keys.find(k => k.name === lostKeyIdInput);
            }
            if (!keyToLost) {
                showNotification('未找到该钥匙。', 'error');
                return;
            }
            if (keyToLost.status === '挂失') {
                showNotification(`钥匙 "${keyToLost.name}" 已处于挂失状态，无需重复申请。`, 'warning');
                return;
            }

            // 总管理员挂失无需审批
            if (currentUser.role === '总管理员') {
                keyToLost.status = '挂失';
                showNotification(`钥匙 "${keyToLost.name}" 已成功挂失。`, 'success');
                this.reset();
                return;
            }

            const newRequest = {
                id: borrowRequests.length + 1,
                keyId: keyToLost.id,
                keyName: keyToLost.name,
                department: keyToLost.department,
                borrower: reporterName,
                type: '挂失',
                reason: lostReason,
                status: '待主管审批' // 挂失申请同样进入审批流程
            };
            borrowRequests.push(newRequest);
            // keyToLost.status = '借用中'; // 同样等待审批
            showNotification('钥匙挂失申请已提交，请等待审批。', 'success');
            this.reset();
            renderMainMenu();
        });
        // 员工还钥匙
        document.getElementById('returnKeyForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const returnKeyIdInput = document.getElementById('returnKeyId').value.trim();
            const returnerName = document.getElementById('returnerName').value;
            let keyToReturn;
            if (!isNaN(returnKeyIdInput) && returnKeyIdInput !== '') {
                keyToReturn = keys.find(k => k.id === parseInt(returnKeyIdInput));
            } else {
                keyToReturn = keys.find(k => k.name === returnKeyIdInput);
            }
            if (!keyToReturn) {
                showNotification('未找到该钥匙。', 'error');
                return;
            }
            if (keyToReturn.status === '在库' || keyToReturn.status === '停用' || keyToReturn.status === '挂失') {
                showNotification(`钥匙 "${keyToReturn.name}" 当前状态为 ${keyToReturn.status}，无需归还。`, 'warning');
                return;
            }
            // 新增：校验归还人是否为持有人
            if (keyToReturn.holder !== currentUser.name) {
                showNotification('您不能归还非您名下的钥匙。', 'error');
                return;
            }

            keyToReturn.status = '在库';
            keyToReturn.holder = ''; // 归还后清空持有人
            showNotification(`钥匙 "${keyToReturn.name}" 已成功归还！`, 'success');
            this.reset();
        });
        // 审批功能
        function renderApprovalsList() {
            const approvalsList = document.getElementById('approvalsList');
            approvalsList.innerHTML = '';
            const pendingRequests = getPendingApprovals();
            document.getElementById('pendingApprovalsCount').textContent = pendingRequests.length;
            if (pendingRequests.length === 0) {
                approvalsList.innerHTML = `<p class="text-gray-500 text-center py-8">
                                        <i class="fa fa-check-circle-o mr-2"></i>没有待处理的审批请求。</p>`;
                return;
            }
            pendingRequests.forEach(req => {
                const approvalCard = document.createElement('div');
                approvalCard.className = 'bg-gray-50 p-4 rounded-lg border border-gray-200 fade-in';
                let dateInfo = '';
                if (req.type === '借用') {
                    dateInfo = `<p class="text-sm text-gray-600 mb-1">借用日期: ${req.borrowDate} 至 ${req.returnDate}</p>`;
                }

                approvalCard.innerHTML = `
                <div class="flex items-center justify-between mb-2">
                    <h4 class="font-semibold text-lg text-gray-800">钥匙编号: ${req.keyId} (${req.keyName})</h4>
                    <span class="text-xs font-medium text-gray-500">${req.type}申请 | 部门: ${req.department}</span>
                </div>
                <p class="text-sm text-gray-600 mb-1">申请人: ${req.borrower}</p>
                ${dateInfo}
                <p class="text-sm text-gray-600 mb-2">原因: ${req.reason}</p>
                <p class="text-sm text-gray-600 mb-2">当前状态: <span class="badge-warning">${req.status}</span></p>
                <div class="flex justify-end space-x-3">
                    <button onclick="approveRequest(${req.id})" class="btn-success btn-sm">
                        <i class="fa fa-check mr-1"></i>批准
                    </button>
                    <button onclick="rejectRequest(${req.id})" class="btn-danger btn-sm">
                        <i class="fa fa-times mr-1"></i>拒绝
                    </button>
                </div>
            `;
                approvalsList.appendChild(approvalCard);
            });
        }
        function approveRequest(id) {
            const request = borrowRequests.find(req => req.id === id);
            if (request) {
                const key = keys.find(k => k.id === request.keyId);
                if (!key) {
                    showNotification('错误：未找到钥匙。', 'error');
                    return;
                }

                if (currentUser.role === '主管') {
                    // 主管批准后，流转到对应的区域负责人
                    const areaManager = departmentToAreaManager[key.department];
                    if (areaManager) {
                         request.status = `待${areaManager}审批`;
                         showNotification(`已批准 ${request.borrower} 的 ${request.keyName} 钥匙申请，已流转至${areaManager}。`, 'success');
                    } else {
                        // 如果没有对应的区域负责人，则主管直接批准
                        if (request.type === '挂失') {
                            key.status = '挂失';
                        } else {
                            key.status = request.type;
                            key.holder = request.borrower; // 直接批准时设置持有人
                        }
                        borrowRequests = borrowRequests.filter(req => req.id !== id);
                        showNotification(`已批准 ${request.borrower} 的 ${request.keyName} 钥匙 ${request.type} 申请。`, 'success');
                    }
                } else if (currentUser.role.startsWith('区域负责人')) {
                    // 区域负责人批准后，最终改变钥匙状态
                    if (request.type === '挂失') {
                        key.status = '挂失';
                    } else {
                        key.status = request.type;
                        key.holder = request.borrower; // 最终批准时设置持有人
                    }
                    // 从待审批列表中移除
                    borrowRequests = borrowRequests.filter(req => req.id !== id);
                    showNotification(`已批准 ${request.borrower} 的 ${request.keyName} 钥匙 ${request.type} 申请。`, 'success');
                }
                renderApprovalsList(); // 刷新列表
                renderMainMenu(); // 更新主菜单上的通知
            }
        }
        function rejectRequest(id) {
            const request = borrowRequests.find(req => req.id === id);
            if (request) {
                const key = keys.find(k => k.id === request.keyId);
                if (key) {
                    key.status = '在库'; // 拒绝后钥匙状态恢复为在库
                    key.holder = ''; // 清空持有人
                }
                showNotification(`已拒绝 ${request.borrower} 的 ${request.keyName} 钥匙 ${request.type} 申请。`, 'error');
                // 从待审批列表中移除
                borrowRequests = borrowRequests.filter(req => req.id !== id);
                renderApprovalsList(); // 刷新列表
                renderMainMenu(); // 更新主菜单上的通知
            }
        }
        // 自动提示功能
        function setupAutocomplete(inputId, suggestionsId, filterFn = () => true) {
            const input = document.getElementById(inputId);
            const suggestionsContainer = document.getElementById(suggestionsId);

            input.addEventListener('input', function() {
                const value = this.value.trim().toLowerCase();
                suggestionsContainer.innerHTML = '';
                suggestionsContainer.classList.add('hidden');

                if (value.length > 0) {
                    const filteredKeys = keys.filter(key =>
                        filterFn(key) && (key.name.toLowerCase().includes(value) || String(key.id).includes(value))
                    );

                    if (filteredKeys.length > 0) {
                        filteredKeys.forEach(key => {
                            const item = document.createElement('div');
                            item.className = 'suggestion-item';
                            item.textContent = `${key.name} (编号: ${key.id})`;
                            item.addEventListener('click', () => {
                                input.value = key.name;
                                suggestionsContainer.classList.add('hidden');
                            });
                            suggestionsContainer.appendChild(item);
                        });
                        suggestionsContainer.classList.remove('hidden');
                    }
                }
            });

            // 点击其他地方隐藏提示
            document.addEventListener('click', (e) => {
                if (!e.target.closest(`#${suggestionsId}`) && e.target !== input) {
                    suggestionsContainer.classList.add('hidden');
                }
            });
        }
        // 控制借用日期字段的显示和必填状态
        document.getElementById('borrowType').addEventListener('change', (e) => {
            const borrowDateFields = document.getElementById('borrowDateFields');
            const borrowDateInput = document.getElementById('borrowDate');
            const returnDateInput = document.getElementById('returnDate');
            if (e.target.value === '借用') {
                borrowDateFields.classList.remove('hidden');
                borrowDateInput.setAttribute('required', 'required');
                returnDateInput.setAttribute('required', 'required');
            } else {
                borrowDateFields.classList.add('hidden');
                borrowDateInput.removeAttribute('required');
                returnDateInput.removeAttribute('required');
                borrowDateInput.value = '';
                returnDateInput.value = '';
            }
        });
        // 初始化页面
        document.addEventListener('DOMContentLoaded', () => {
            // 在DOM加载后，渲染主菜单，但一开始是隐藏的，等待登录
            renderMainMenu();
            // 默认隐藏借用日期字段
            document.getElementById('borrowDateFields').classList.add('hidden');
            // 设置自动提示功能
            setupAutocomplete('statusSearchInput', 'statusSuggestions');
            setupAutocomplete('deleteInput', 'deleteSuggestions');
            setupAutocomplete('modifySearchInput', 'modifySuggestions');
            setupAutocomplete('borrowKeyId', 'borrowSuggestions', (key) => key.status === '在库');
            setupAutocomplete('returnKeyId', 'returnSuggestions', (key) => (key.status === '借用' || key.status === '领用') && key.holder === currentUser.name);
            setupAutocomplete('lostKeyId', 'lostSuggestions');
        });
    </script>
</body>
</html>
