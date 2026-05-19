<template>
  <div v-if="!isAuthenticated" class="min-h-screen bg-gray-50 flex items-center justify-center p-4 font-pretendard transition-colors duration-500" :class="{ 'dark': isDark }">
    <div class="bg-white p-8 sm:p-10 rounded-3xl shadow-2xl max-w-md w-full text-center border-2 border-gray-100 transition-colors">
      <div class="w-20 h-20 bg-indigo-50 text-indigo-600 rounded-full flex items-center justify-center mx-auto mb-6 text-4xl shadow-inner">🔒</div>
      <h1 class="text-3xl font-black text-gray-800 mb-2">시스템 잠금</h1>
      <p class="text-gray-500 mb-8 font-bold">관리자 6자리 비밀번호를 입력해주세요.</p>
      <input v-model="pinPassword" type="password" maxlength="6" @keyup.enter="handleLogin"
             class="w-full text-center text-4xl tracking-[0.5em] font-black px-4 py-6 bg-gray-50 border-2 border-gray-200 rounded-2xl focus:border-indigo-500 focus:ring-4 focus:ring-indigo-100 outline-none mb-6 transition-all text-gray-900"
             placeholder="******">
      <button @click="handleLogin" class="w-full bg-indigo-600 text-white font-black text-xl py-5 rounded-2xl shadow-lg hover:bg-indigo-700 active:translate-y-1 transition-all">접속하기</button>
    </div>
  </div>

  <div v-else class="min-h-screen bg-gray-50 pb-10 font-pretendard transition-colors duration-500" :class="{ 'dark': isDark }">
    <header class="bg-white shadow-sm sticky top-0 z-30 transition-colors">
      <div class="max-w-[1600px] mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-col lg:flex-row justify-between items-center gap-4">
        <div class="flex items-center gap-4">
          <h1 class="text-xl font-bold text-gray-900 flex items-center gap-2 whitespace-nowrap">📦 스위스 신발 관리 시스템</h1>
          <button @click="toggleDarkMode" class="w-10 h-10 rounded-full bg-gray-100 flex items-center justify-center text-lg hover:bg-gray-200 transition-colors shadow-inner border border-gray-200">
            {{ isDark ? '🌙' : '☀️' }}
          </button>
        </div>
        
        <div class="flex space-x-1 bg-gray-100 p-1 rounded-lg shrink-0 transition-colors">
          <button @click="currentTab = 'log'" class="px-4 py-2 rounded-md text-sm font-bold transition-colors" :class="currentTab === 'log' ? 'bg-white text-indigo-600 shadow-sm' : 'text-gray-500 hover:text-gray-700'">📝 입출고 등록</button>
          <button @click="currentTab = 'status'" class="px-4 py-2 rounded-md text-sm font-bold transition-colors" :class="currentTab === 'status' ? 'bg-white text-indigo-600 shadow-sm' : 'text-gray-500 hover:text-gray-700'">📊 재고 현황</button>
          <button @click="currentTab = 'sales'" class="px-4 py-2 rounded-md text-sm font-bold transition-colors" :class="currentTab === 'sales' ? 'bg-white text-indigo-600 shadow-sm' : 'text-gray-500 hover:text-gray-700'">📈 판매 현황</button>
          <button @click="currentTab = 'as'" class="px-4 py-2 rounded-md text-sm font-bold transition-colors" :class="currentTab === 'as' ? 'bg-white text-indigo-600 shadow-sm' : 'text-gray-500 hover:text-gray-700'">🛠️ AS 현황</button>
        </div>

        <div class="flex flex-wrap justify-center items-center gap-3">
          <div class="flex items-center gap-3 text-sm font-bold bg-white px-4 py-1.5 rounded-lg border-2 border-gray-100 shadow-sm transition-colors">
            <span class="text-blue-700">기분: {{ totalKybunStock }} 족</span>
            <span class="text-gray-300">|</span>
            <span class="text-orange-600">조야: {{ totalJoyaStock }} 족</span>
          </div>
        </div>
      </div>
    </header>

    <main class="max-w-[1600px] mx-auto px-4 sm:px-6 lg:px-8 py-8">
      
      <div v-if="currentTab === 'log'" class="space-y-8">
        <div class="bg-white rounded-3xl shadow-md border-2 border-gray-100 relative overflow-hidden transition-colors">
          <div class="absolute top-0 left-0 w-full h-2 transition-colors duration-300" :class="{'bg-[#FABCBC]': logForm.type==='출고', 'bg-[#C5E1FF]': logForm.type==='입고', 'bg-[#FFF2CC]': logForm.type==='양도'}"></div>
<div class="p-6 sm:p-8">
            <div class="flex flex-col lg:flex-row gap-6">
              <div class="flex-1 space-y-5">
                
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-5 mb-1">
                  <div class="flex justify-between items-center border-b-2 border-gray-50 pb-3">
                    <h2 class="text-xl font-black text-gray-800 flex items-center gap-2">
                      ✏️ 입출고 등록
                      <button @click="resetLogForm" class="ml-2 p-1.5 text-gray-400 hover:text-indigo-600 bg-gray-50 hover:bg-indigo-50 rounded-full transition-all border border-gray-200">🔄</button>
                    </h2>
                    
                    <div class="relative w-[140px] bg-white rounded-xl">
                      <input v-model="logForm.date" type="date" class="w-full px-3 py-2 border-2 border-gray-100 rounded-xl font-black bg-transparent text-sm outline-none focus:border-indigo-500 text-center shadow-sm relative z-10 cursor-pointer" style="color: transparent;">
                      <div class="absolute inset-0 flex items-center justify-center pointer-events-none z-0 pr-4">
                        <span class="font-black text-sm text-gray-800">{{ formattedFormDate }}</span>
                      </div>
                    </div>
                  </div>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 gap-5">
                  <div class="space-y-1.5">
                    <label class="block text-xs font-black text-gray-400">구분</label>
                    <div class="flex gap-2 h-[52px]">
                      <button @click="logForm.type = '출고'" :class="logForm.type === '출고' ? 'bg-[#FABCBC] text-gray-900 shadow-md' : 'bg-gray-50 text-gray-400 hover:bg-gray-100'" class="flex-1 rounded-xl font-bold transition-all border border-gray-100">출고</button>
                      <button @click="logForm.type = '입고'" :class="logForm.type === '입고' ? 'bg-[#C5E1FF] text-gray-900 shadow-md' : 'bg-gray-50 text-gray-400 hover:bg-gray-100'" class="flex-1 rounded-xl font-bold transition-all border border-gray-100">입고</button>
                      <button @click="logForm.type = '양도'" :class="logForm.type === '양도' ? 'bg-[#FFF2CC] text-gray-900 shadow-md' : 'bg-gray-50 text-gray-400 hover:bg-gray-100'" class="flex-1 rounded-xl font-bold transition-all border border-gray-100">양도</button>
                    </div>
                  </div>
                  </div>
                
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-5">
                  <div class="space-y-1.5"><label class="block text-xs font-black text-gray-400">브랜드</label>
                    <div class="flex gap-2 h-[52px]">
                      <button @click="logForm.brand = 'Kybun'; logForm.modelName = ''" :class="logForm.brand === 'Kybun' ? 'bg-blue-600 text-white' : 'bg-gray-100 text-gray-500'" class="flex-1 rounded-xl font-bold">기분</button>
                      <button @click="logForm.brand = 'Joya'; logForm.modelName = ''" :class="logForm.brand === 'Joya' ? 'bg-orange-600 text-white' : 'bg-gray-100 text-gray-500'" class="flex-1 rounded-xl font-bold">조야</button>
                    </div></div>
                  <div class="space-y-1.5 sm:col-span-2"><label class="block text-xs font-black text-gray-400">상품명</label>
                    <select v-model="logForm.modelName" class="w-full px-4 py-3 border-2 border-gray-100 rounded-2xl font-bold bg-white">
                      <option value="" disabled>모델 선택</option><option v-for="model in availableModelsForForm" :key="model" :value="model">{{ model }}</option>
                    </select></div>
                </div>
                
                <div class="grid grid-cols-2 gap-5">
                  <div class="space-y-1.5"><label class="block text-xs font-black text-gray-400">사이즈</label>
                    <select v-model="logForm.size" class="w-full px-4 py-3 border-2 border-indigo-100 rounded-2xl text-indigo-700 font-black bg-indigo-50/30">
                      <option v-for="size in availableSizes" :key="size" :value="size">{{ size }}</option>
                    </select></div>
                  <div class="space-y-1.5"><label class="block text-xs font-black text-gray-400">수량</label>
                    <div class="relative"><input v-model="logForm.quantity" type="number" min="1" class="w-full px-4 py-3 border-2 border-gray-100 rounded-2xl font-black text-right pr-12 bg-white">
                      <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-400 font-bold text-sm">족</span></div></div>
                </div>
              </div>
              
              <button @click="submitLog" class="lg:w-48 py-6 rounded-3xl transition-all font-black text-4xl" :class="{'bg-[#FABCBC]': logForm.type === '출고', 'bg-[#C5E1FF]': logForm.type === '입고', 'bg-[#FFF2CC]': logForm.type === '양도'}">
                {{ logForm.type }} 등록
              </button>
            </div>
          </div>
        </div>

        <div>
          <div class="flex justify-between items-center mb-3">
            <h2 class="text-sm font-bold text-gray-700 flex items-center gap-2">
              최근 입출고 내역 
              <span class="flex items-center text-green-500 text-xs bg-green-50 px-2 py-0.5 rounded-full border border-green-200">
                <span class="w-1.5 h-1.5 bg-green-500 rounded-full animate-pulse mr-1"></span>실시간 동기화 중
              </span>
            </h2>
            <button @click="fetchTransactionLogs(true)" class="text-xs font-bold text-indigo-500 hover:text-indigo-700 flex items-center gap-1 bg-white px-3 py-1.5 rounded-lg border shadow-sm">
              <span :class="{'animate-spin': isFetchingLogs}">🔄</span> 강제 전체 동기화
            </button>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full text-left text-sm whitespace-nowrap border-separate" style="border-spacing: 0 8px;">
              <thead class="text-gray-500">
                <tr><th class="w-12"></th><th>날짜</th><th>구분</th><th>브랜드</th><th>상품명</th><th class="text-center">사이즈</th><th class="text-right pr-6">수량</th></tr>
              </thead>
              <TransitionGroup tag="tbody" name="list">
                <tr v-if="isFetchingLogs && displayTransactionLogs.length === 0" key="loading-row"><td colspan="7" class="px-6 py-8 text-center text-indigo-400 font-bold bg-white rounded-xl shadow-sm border border-gray-100">데이터를 안전하게 불러오는 중입니다...</td></tr>
                <tr v-else-if="displayTransactionLogs.length === 0" key="empty-row"><td colspan="7" class="px-6 py-8 text-center text-gray-400 bg-white rounded-xl shadow-sm border border-gray-100">아직 등록된 내역이 없습니다.</td></tr>
                <tr v-for="log in displayTransactionLogs" :key="log.id" class="group/row">
                  <td class="text-center"><button @click="deleteLog(log)" class="text-gray-400 hover:text-red-500 opacity-0 group-hover/row:opacity-100 transition">🗑️</button></td>
                  <td class="px-4 py-4 rounded-l-xl transition-colors" :class="getRowBg(log.type)">{{ getFormattedDate(log.date) }}</td>
                  <td class="px-4 py-4 font-bold transition-colors" :class="getRowBg(log.type)">{{ log.type }}</td>
                  <td class="px-4 py-4 transition-colors" :class="getRowBg(log.type)">
                  {{ log.brand === 'Kybun' ? '기분' : (log.brand === 'Joya' ? '조야' : log.brand) }}
                  </td>
                  <td class="px-4 py-4 font-bold transition-colors" :class="getRowBg(log.type)">{{ log.modelName }}</td>
                  <td class="px-4 py-4 text-center font-bold transition-colors" :class="getRowBg(log.type)">{{ log.size }}</td>
                  <td class="px-4 py-4 text-right pr-6 font-black transition-colors rounded-r-xl" :class="getRowBg(log.type)">
                    {{ log.type === '재고조정' && log.quantity > 0 ? '+' : ''}}{{ log.quantity }}
                  </td>
                </tr>
              </TransitionGroup>
            </table>
          </div>
        </div>
      </div>

      <div v-if="currentTab === 'status'" class="space-y-4">
        
        <div class="flex flex-col md:flex-row gap-4 items-end bg-white p-4 rounded-2xl shadow-sm sticky top-[80px] z-20 border-2 border-gray-100 transition-colors">
          <div class="flex-1 space-y-1.5"><label class="text-xs font-black text-gray-400">모델 선택 (브랜드)</label>
            <div class="flex gap-2">
              <button @click="selectedBrand = 'All'" :class="selectedBrand === 'All' ? 'bg-indigo-600 text-white shadow-md' : 'bg-gray-50 text-gray-500 border border-gray-200'" class="px-4 py-2 rounded-xl font-bold transition-all">전체</button>
              <button @click="selectedBrand = 'Kybun'" :class="selectedBrand === 'Kybun' ? 'bg-blue-600 text-white shadow-md' : 'bg-gray-50 text-gray-500 border border-gray-200'" class="px-4 py-2 rounded-xl font-bold transition-all">기분</button>
              <button @click="selectedBrand = 'Joya'" :class="selectedBrand === 'Joya' ? 'bg-orange-600 text-white shadow-md' : 'bg-gray-50 text-gray-500 border border-gray-200'" class="px-4 py-2 rounded-xl font-bold transition-all">조야</button>
            </div>
          </div>
          <div class="flex-[2] space-y-1.5 relative"><label class="text-xs font-black text-gray-400">모델 검색</label>
            <input v-model="searchQuery" type="text" placeholder="검색어를 입력하세요" class="w-full px-4 py-2.5 rounded-xl border border-gray-200 bg-gray-50 font-bold pr-10 outline-none focus:ring-2 focus:ring-indigo-500 transition-all">
            <button v-if="searchQuery" @click="searchQuery = ''" class="absolute right-3 top-[34px] w-5 h-5 flex items-center justify-center bg-gray-300 hover:bg-gray-400 text-white rounded-full text-xs font-bold transition-colors">X</button>
          </div>
          <div class="flex gap-2 h-[46px]">
            <button @click="showModal = true" class="bg-indigo-600 text-white px-5 rounded-xl font-black shadow-md hover:bg-indigo-700 transition">+ 새 모델 추가</button>
          </div>
        </div>

        <div class="pt-4">
          <div class="flex justify-between items-center mb-3">
            <button @click="toggleEditMode" class="flex items-center gap-2 px-5 py-2.5 rounded-xl font-black transition-all shadow-sm border-2"
                    :class="isEditMode ? 'bg-red-50 text-red-600 border-red-500' : 'bg-white text-gray-700 border-gray-200 hover:bg-gray-50'">
              <span class="text-lg">{{ isEditMode ? '💾' : '✏️' }}</span>
              {{ isEditMode ? '수정 완료 (저장)' : '다이렉트 재고 수정 (엑셀 붙여넣기 지원)' }}
            </button>

            <button @click="toggleB2BMode" class="flex items-center gap-2 px-5 py-2.5 rounded-xl font-black transition-all shadow-sm border-2"
                    :class="isB2BMode ? 'bg-indigo-600 text-white border-indigo-700' : 'bg-white text-indigo-600 border-indigo-200 hover:bg-indigo-50'">
              <span v-if="isLoadingB2B" class="animate-spin text-lg">⏳</span>
              <span v-else class="text-lg">{{ isB2BMode ? '✅' : '🌐' }}</span>
              {{ isB2BMode ? (isLoadingB2B ? 'B2B 갱신 중...' : 'B2B 연동 켜짐') : 'B2B 실시간 재고 켜기' }}
            </button>
          </div>

          <div class="overflow-auto max-h-[70vh] bg-white rounded-2xl shadow-sm border-2 border-gray-200 transition-colors relative">
            <table class="w-full whitespace-nowrap text-sm border-collapse select-none">
              <thead class="sticky top-0 z-30 shadow-md">
                <tr class="bg-[#5c7f59] text-white">
                  <th class="px-3 py-3 font-bold border-r border-[#6b8e68] text-center w-16 bg-[#5c7f59]">브랜드</th>
                  <th class="px-4 py-3 font-bold border-r border-[#6b8e68] text-center min-w-[200px] bg-[#5c7f59]">상품명</th>
                  <th v-for="size in availableSizes" :key="size" 
                      @click="toggleSizeSort(size)"
                      class="px-1 py-3 font-bold border-r border-[#6b8e68] w-12 text-center transition-all cursor-pointer hover:bg-[#4a6b47]"
                      :class="sortBySize === size ? 'bg-indigo-600 text-white shadow-inner scale-110 z-10 relative' : 'bg-[#5c7f59] text-white'">
                    {{ size }}
                  </th>
                  <th class="px-3 py-3 font-bold text-center w-16 bg-[#5c7f59]">총합</th>
                </tr>
              </thead>
              <tbody>
                <tr v-if="sortedAndFilteredInventory.length === 0">
                  <td :colspan="availableSizes.length + 3" class="py-12 text-center text-gray-400 font-bold bg-gray-50/50">검색 결과가 없습니다.</td>
                </tr>
                <tr v-for="(shoe, index) in sortedAndFilteredInventory" :key="shoe.id" 
                    class="border-b border-gray-200 transition-colors" 
                    :class="index % 2 === 0 ? 'bg-white' : 'bg-gray-50/30'">
                  <td class="px-3 py-2 border-r border-dotted border-gray-300 text-center align-middle">
                    <span class="text-[11px] font-black" :class="shoe.brand === 'Kybun' ? 'text-blue-700' : 'text-orange-700'">{{ shoe.brand }}</span>
                  </td>
                  <td class="px-4 py-2 border-r border-dotted border-gray-300 font-bold text-gray-800 align-middle">
                    <div class="flex justify-between items-center group">
                      <span>{{ shoe.modelName }}</span>
                      <button @click="deleteModel(shoe)" class="opacity-0 group-hover:opacity-100 text-red-400 hover:text-red-600 transition-opacity" title="이 모델을 목록에서 완전히 숨깁니다.">🗑️</button>
                    </div>
                  </td>
                  
                  <td v-for="(size, sizeIndex) in availableSizes" :key="size" 
                      class="px-1 py-1 border-r border-dotted border-gray-300 text-center align-middle transition-colors relative"
                      :class="{
                        'bg-indigo-100/80': isEditMode && isDraftChanged(shoe, size),
                        'bg-indigo-50/50': sortBySize === size && !(isEditMode && isDraftChanged(shoe, size))
                      }">
                    
                    <input v-if="isEditMode"
                           v-model="shoe.draftSizes[size]"
                           @paste="handlePaste($event, index, sizeIndex)"
                           type="text"
                           class="w-full h-8 text-center font-black bg-transparent border border-transparent focus:border-indigo-400 focus:ring-2 focus:ring-indigo-100 rounded outline-none transition-colors"
                           :class="getInputClass(shoe, size)" />
                    
                    <div v-else class="h-8 flex flex-col justify-center items-center">
                      <div class="font-black text-[13px]" 
                           :class="{
                             'text-black dark:text-white': shoe.sizes[size] > 0,
                             'text-red-600': shoe.sizes[size] < 0,
                             'text-[#5c7f59]': shoe.sizes[size] === 0 && shoe.history?.[size],
                             'text-transparent': shoe.sizes[size] === 0 && !shoe.history?.[size]
                           }">
                        {{ shoe.sizes[size] !== 0 || shoe.history?.[size] ? shoe.sizes[size] : '0' }}
                      </div>
                      <div v-if="isB2BMode && shoe.hqStockData" class="mt-0.5 text-[10px] font-black tracking-tighter leading-none" :class="getHQStockTextColor(shoe.hqStockData[size])">
                        {{ shoe.hqStockData[size] !== undefined ? shoe.hqStockData[size] : '' }}
                      </div>
                    </div>
                  </td>
                  <td class="px-3 py-2 text-center font-black text-black dark:text-white bg-gray-100/50 align-middle">
                    {{ getRowTotalStock(shoe) }}
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

<div v-if="currentTab === 'sales'" class="space-y-8">
        <div class="flex justify-between items-center bg-white p-6 rounded-3xl border-2 border-gray-100 shadow-sm transition-colors">
          <h2 class="text-xl font-bold">📊 월별 판매 현황</h2>
          <input type="month" v-model="currentSalesMonth" class="px-4 py-2 border-2 border-indigo-100 rounded-xl font-bold text-indigo-700 bg-gray-50 outline-none">
        </div>
        
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 text-center">
          <div class="bg-white p-8 rounded-3xl border-2 border-gray-100 shadow-sm transition-colors relative flex flex-col items-center justify-center">
            <p class="text-gray-400 text-sm font-bold uppercase mb-1">전체 판매량</p>
            <p class="text-5xl font-black text-indigo-600">{{ monthlyTotalQuantity }}<span class="text-xl ml-1">족</span></p>
            <div class="mt-3 px-3 py-1 bg-gray-50 rounded-lg text-sm font-bold flex items-center gap-1 border border-gray-100" :class="getYoYDiff(monthlyTotalQuantity, lastYearTotalQuantity) >= 0 ? 'text-red-500' : 'text-blue-500'">
              <span class="text-gray-400 text-xs mr-1">전년 {{ lastYearMonth.split('-')[1] }}월 ({{ lastYearTotalQuantity }}) 대비</span>
              {{ getYoYDiff(monthlyTotalQuantity, lastYearTotalQuantity) > 0 ? '▲' : (getYoYDiff(monthlyTotalQuantity, lastYearTotalQuantity) < 0 ? '▼' : '-') }} {{ Math.abs(getYoYDiff(monthlyTotalQuantity, lastYearTotalQuantity)) }}
            </div>
          </div>
          <div class="bg-white p-8 rounded-3xl border-2 border-gray-100 shadow-sm transition-colors relative flex flex-col items-center justify-center">
            <p class="text-blue-400 text-sm font-bold uppercase mb-1">기분(Kybun)</p>
            <p class="text-5xl font-black text-blue-700">{{ monthlyKybunQuantity }}<span class="text-xl ml-1">족</span></p>
            <div class="mt-3 px-3 py-1 bg-gray-50 rounded-lg text-sm font-bold flex items-center gap-1 border border-gray-100" :class="getYoYDiff(monthlyKybunQuantity, lastYearKybunQuantity) >= 0 ? 'text-red-500' : 'text-blue-500'">
              <span class="text-gray-400 text-xs mr-1">전년 {{ lastYearMonth.split('-')[1] }}월 ({{ lastYearKybunQuantity }}) 대비</span>
              {{ getYoYDiff(monthlyKybunQuantity, lastYearKybunQuantity) > 0 ? '▲' : (getYoYDiff(monthlyKybunQuantity, lastYearKybunQuantity) < 0 ? '▼' : '-') }} {{ Math.abs(getYoYDiff(monthlyKybunQuantity, lastYearKybunQuantity)) }}
            </div>
          </div>
          <div class="bg-white p-8 rounded-3xl border-2 border-gray-100 shadow-sm transition-colors relative flex flex-col items-center justify-center">
            <p class="text-orange-400 text-sm font-bold uppercase mb-1">조야(Joya)</p>
            <p class="text-5xl font-black text-orange-700">{{ monthlyJoyaQuantity }}<span class="text-xl ml-1">족</span></p>
            <div class="mt-3 px-3 py-1 bg-gray-50 rounded-lg text-sm font-bold flex items-center gap-1 border border-gray-100" :class="getYoYDiff(monthlyJoyaQuantity, lastYearJoyaQuantity) >= 0 ? 'text-red-500' : 'text-blue-500'">
              <span class="text-gray-400 text-xs mr-1">전년 {{ lastYearMonth.split('-')[1] }}월 ({{ lastYearJoyaQuantity }}) 대비</span>
              {{ getYoYDiff(monthlyJoyaQuantity, lastYearJoyaQuantity) > 0 ? '▲' : (getYoYDiff(monthlyJoyaQuantity, lastYearJoyaQuantity) < 0 ? '▼' : '-') }} {{ Math.abs(getYoYDiff(monthlyJoyaQuantity, lastYearJoyaQuantity)) }}
            </div>
          </div>
        </div>

        <div class="bg-white rounded-3xl border-2 border-gray-100 shadow-sm overflow-hidden transition-colors">
          <div class="p-6 border-b border-gray-100 font-bold text-lg bg-gray-50/50">🏆 {{ currentSalesMonth }} 베스트셀러 (판매 수량순)</div>
          <div class="overflow-x-auto">
            <table class="w-full text-left whitespace-nowrap">
              <thead class="text-gray-400 bg-gray-50 border-b border-gray-200">
                <tr><th class="px-6 py-4">순위</th><th>브랜드</th><th>상품명</th><th class="text-right pr-10">판매 수량</th></tr>
              </thead>
              <tbody>
                <tr v-if="monthlyBestSellers.length === 0"><td colspan="4" class="py-12 text-center text-gray-400 font-bold bg-white">해당 월에 판매(출고)된 기록이 없습니다.</td></tr>
                <tr v-for="(item, idx) in monthlyBestSellers" :key="item.modelName" class="border-b border-gray-50 hover:bg-indigo-50/30 transition-colors">
                  <td class="px-6 py-4 font-black">
                    <span class="inline-flex items-center justify-center w-8 h-8 rounded-full font-black text-sm" :class="idx === 0 ? 'bg-yellow-100 text-yellow-700' : (idx === 1 ? 'bg-gray-200 text-gray-700' : (idx === 2 ? 'bg-orange-100 text-orange-800' : 'bg-transparent text-gray-500'))">{{ idx + 1 }}</span>
                  </td>
                  <td><span class="px-2.5 py-1 rounded-md text-[11px] font-black" :class="item.brand === 'Kybun' ? 'bg-blue-100 text-blue-700' : 'bg-orange-100 text-orange-700'">{{ item.brand }}</span></td>
                  <td class="font-bold text-gray-800">{{ item.modelName }}</td>
                  <td class="text-right pr-10 font-black text-xl text-indigo-600">{{ item.quantity }}<span class="text-sm text-gray-400 ml-1">족</span></td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <div class="bg-white p-6 sm:p-8 rounded-3xl border-2 border-gray-100 shadow-sm transition-colors relative">
          <div class="flex flex-col sm:flex-row justify-between items-center mb-8 gap-4 relative z-20">
            <h3 class="font-black text-lg text-gray-800">📈 {{ currentSalesMonth.split('-')[0] }}년 월별 판매 추이</h3>
            <div class="flex gap-2 bg-gray-50 p-1 rounded-xl border border-gray-200 shadow-inner">
              <button @click="graphBrandFilter = 'All'" :class="graphBrandFilter === 'All' ? 'bg-indigo-600 text-white shadow-md' : 'text-gray-500 hover:text-gray-800'" class="px-4 py-1.5 rounded-lg text-sm font-bold transition-all">전체보기</button>
              <button @click="graphBrandFilter = 'Kybun'" :class="graphBrandFilter === 'Kybun' ? 'bg-blue-600 text-white shadow-md' : 'text-gray-500 hover:text-gray-800'" class="px-4 py-1.5 rounded-lg text-sm font-bold transition-all">기분(Kybun)</button>
              <button @click="graphBrandFilter = 'Joya'" :class="graphBrandFilter === 'Joya' ? 'bg-orange-600 text-white shadow-md' : 'text-gray-500 hover:text-gray-800'" class="px-4 py-1.5 rounded-lg text-sm font-bold transition-all">조야(Joya)</button>
            </div>
          </div>
          
          <div class="relative h-64 w-full border-b-2 border-gray-200 flex items-end justify-between gap-1 sm:gap-2 pt-10">
            <div class="absolute inset-0 flex flex-col justify-between pointer-events-none pb-0 opacity-40 z-0">
              <div class="border-b border-dashed border-gray-300 w-full"></div>
              <div class="border-b border-dashed border-gray-300 w-full"></div>
              <div class="border-b border-dashed border-gray-300 w-full"></div>
              <div class="border-b border-dashed border-gray-300 w-full"></div>
            </div>

            <div v-for="item in yearlyGraphData" :key="item.month" class="flex-1 flex flex-col items-center justify-end h-full group relative z-10">
              <span class="text-[11px] sm:text-xs font-black transition-all mb-1" 
                    :class="item.total > 0 ? 'text-gray-700 translate-y-0 opacity-100' : 'text-transparent translate-y-2 opacity-0'">
                {{ item.total }}
              </span>
              
              <div class="w-full sm:w-2/3 lg:w-1/2 relative flex items-end justify-center rounded-t-md overflow-hidden bg-gray-50/50" style="height: 100%;">
                <div class="w-full rounded-t-md transition-all duration-700 ease-out relative" 
                     :style="{ height: item.total === 0 ? '0%' : Math.max(item.heightPercent, 2) + '%' }" 
                     :class="graphBrandFilter === 'Kybun' ? 'bg-gradient-to-t from-blue-600 to-blue-400 shadow-[0_0_15px_rgba(59,130,246,0.5)]' : (graphBrandFilter === 'Joya' ? 'bg-gradient-to-t from-orange-600 to-orange-400 shadow-[0_0_15px_rgba(249,115,22,0.5)]' : 'bg-gradient-to-t from-indigo-600 to-indigo-400 shadow-[0_0_15px_rgba(79,70,229,0.5)]')">
                </div>
              </div>
              
              <div class="absolute -bottom-8 text-xs font-bold text-gray-400">{{ item.month }}월</div>
            </div>
          </div>
          <div class="h-8"></div>
        </div>
      </div>

<div v-if="currentTab === 'as'" class="space-y-8">
        <div class="bg-white rounded-3xl shadow-md border-2 border-gray-100 p-6 sm:p-8 transition-colors">
          <h2 class="text-xl font-black text-gray-800 flex items-center gap-2 mb-6">🛠️ AS 접수 등록
            <button @click="resetAsForm" class="ml-2 p-1.5 text-gray-400 hover:text-indigo-600 bg-gray-50 hover:bg-indigo-50 rounded-full transition-all border border-gray-200">🔄</button>
          </h2>
          <div class="grid grid-cols-1 md:grid-cols-4 gap-4 items-end">
            <div class="space-y-1.5"><label class="text-xs font-black text-gray-400">접수날짜</label>
              <input v-model="asForm.receiveDate" type="date" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold bg-white"></div>
            <div class="space-y-1.5"><label class="text-xs font-black text-gray-400">고객명</label>
              <input v-model="asForm.customer" type="text" placeholder="홍길동" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold bg-white"></div>
            <div class="space-y-1.5"><label class="text-xs font-black text-gray-400">연락처</label>
              <input v-model="asForm.phone" type="text" placeholder="010-0000-0000" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold bg-white"></div>
            <div class="space-y-1.5"><label class="text-xs font-black text-gray-400">상품명</label>
              <input v-model="asForm.modelName" type="text" placeholder="모델명 입력" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold bg-white"></div>
            
            <div class="space-y-1.5 md:col-span-2"><label class="text-xs font-black text-gray-400">AS 사유</label>
              <input v-model="asForm.reason" type="text" placeholder="수선 내용 기재" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold bg-white"></div>
            
            <div class="space-y-1.5"><label class="text-xs font-black text-gray-400">비용 및 결제메모</label>
              <input v-model="asForm.fee" type="text" placeholder="예: 85,000선불, 무료해드림" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold bg-white">
            </div>
            
            <div class="space-y-1.5"><label class="text-xs font-black text-gray-400">주소</label>
              <input v-model="asForm.address" type="text" placeholder="상세 주소" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold bg-white"></div>
            <button @click="submitAs" class="h-14 bg-gray-900 text-white rounded-xl font-black hover:bg-black transition shadow-lg w-full text-lg md:col-start-4">접수 등록</button>
          </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6 pb-8">
          <TransitionGroup name="list">
            <div v-for="as in asRecords" :key="as.id" 
                 class="bg-white rounded-3xl p-6 md:p-8 border-2 transition-all relative group flex flex-col justify-between h-full shadow-sm hover:shadow-md"
                 :class="as.isCompleted ? 'border-gray-200 bg-gray-50 opacity-75 grayscale' : 'border-indigo-50'">
                 
               <div>
                 <div class="flex justify-between items-start mb-5">
                   <div class="flex flex-col">
                     <span class="text-[11px] font-black text-gray-400 bg-gray-100 px-2 py-1 rounded-md w-fit mb-1">접수일: {{ as.receiveDate }}</span>
                     <span v-if="as.isCompleted" class="text-[11px] font-black text-indigo-500 bg-indigo-50 px-2 py-1 rounded-md w-fit border border-indigo-100 mt-1">출고완료: {{ as.releaseDate }}</span>
                   </div>
                   <div class="flex gap-2">
                     <button @click="toggleAsComplete(as)" title="완료 처리" class="w-10 h-10 rounded-full border-2 flex items-center justify-center font-bold transition-all shadow-sm active:scale-95" :class="as.isCompleted ? 'bg-green-500 border-green-500 text-white' : 'border-gray-200 text-gray-300 bg-white hover:border-green-400 hover:text-green-500'">✓</button>
                     <button @click="deleteAs(as.id)" title="기록 삭제" class="w-10 h-10 rounded-full bg-red-50 text-red-400 hover:bg-red-500 hover:text-white transition-all opacity-0 group-hover:opacity-100 active:scale-95">🗑️</button>
                   </div>
                 </div>

                 <div class="space-y-4">
                   <div class="flex items-baseline gap-2">
                     <h3 class="text-2xl font-black text-gray-800">{{ as.customer }}</h3>
                     <span class="text-sm font-bold text-gray-400">{{ as.phone }}</span>
                   </div>
                   
                   <div class="inline-block px-3 py-1.5 bg-indigo-50/50 border border-indigo-100 rounded-xl text-sm font-black text-indigo-700">
                     👟 {{ as.modelName }}
                   </div>
                   
                   <div class="bg-gray-50 p-4 rounded-2xl border border-gray-100 relative">
                     <span class="absolute -top-2 left-3 bg-gray-50 px-1 text-[10px] font-black text-gray-400">요청 사유</span>
                     <p class="text-sm text-gray-700 font-bold leading-relaxed whitespace-pre-wrap">{{ as.reason }}</p>
                   </div>
                 </div>
               </div>

               <div class="mt-6 pt-5 border-t border-dashed border-gray-200 space-y-3">
                 <div class="flex flex-col">
                   <span class="text-[11px] font-black text-gray-400 mb-0.5">배송/고객 주소</span>
                   <span class="text-sm font-bold text-gray-600 truncate" :title="as.address">{{ as.address || '주소 미입력' }}</span>
                 </div>
                 
                 <div class="flex justify-between items-center bg-gray-900 text-white p-4 rounded-2xl shadow-inner">
                   <span class="text-xs font-black text-gray-400 shrink-0">비용/메모</span>
                   <span class="text-lg font-black truncate ml-2 text-right text-yellow-400" :title="as.fee">{{ as.fee || '미입력' }}</span>
                 </div>
               </div>

            </div>
          </TransitionGroup>
        </div>
      </div>

    </main>
  </div>

  <div v-if="showModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
    <div class="bg-white p-8 rounded-3xl w-full max-w-md shadow-2xl transition-colors">
      <h3 class="text-2xl font-black mb-6 text-gray-800">✨ 새 모델 등록</h3>
      <div class="space-y-4">
        <div>
          <label class="block text-sm font-black text-gray-400 mb-1 ml-1">브랜드</label>
          <div class="flex gap-2">
            <button @click="newShoe.brand = 'Kybun'" :class="newShoe.brand === 'Kybun' ? 'bg-blue-600 text-white' : 'bg-gray-100 text-gray-500'" class="flex-1 py-3 rounded-xl font-bold transition-all">기분</button>
            <button @click="newShoe.brand = 'Joya'" :class="newShoe.brand === 'Joya' ? 'bg-orange-600 text-white' : 'bg-gray-100 text-gray-500'" class="flex-1 py-3 rounded-xl font-bold transition-all">조야</button>
          </div>
        </div>
        <div>
          <label class="block text-sm font-black text-gray-400 mb-1 ml-1">모델명 (API 용 영문 슬러그 권장)</label>
          <input v-model="newShoe.modelName" type="text" placeholder="예: bauma-silver" class="w-full px-4 py-3 border-2 border-gray-100 rounded-xl font-bold outline-none focus:border-indigo-500 bg-white">
        </div>
      </div>
      <div class="mt-8 flex gap-2">
        <button @click="showModal = false" class="flex-1 py-4 bg-gray-100 text-gray-500 rounded-2xl font-bold hover:bg-gray-200">취소</button>
        <button @click="addShoe" class="flex-[2] py-4 bg-indigo-600 text-white rounded-2xl font-black text-lg hover:bg-indigo-700 shadow-lg">모델 추가하기</button>
      </div>
    </div>
  </div>

</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { createClient } from '@supabase/supabase-js'

// ==========================================
// 🔥 Supabase 설정값
// ==========================================
const supabaseUrl = import.meta.env.VITE_SUPABASE_URL
const supabaseKey = import.meta.env.VITE_SUPABASE_KEY
const supabase = createClient(supabaseUrl, supabaseKey)

// 본사 API 통신용 토큰
const HQ_API_TOKEN = import.meta.env.VITE_HQ_API_TOKEN;

const isAuthenticated = ref(false)
const pinPassword = ref('')
const handleLogin = () => { if (pinPassword.value === '123456') isAuthenticated.value = true; else alert('비밀번호 불일치'); pinPassword.value = ''; }

const isDark = ref(false)
const toggleDarkMode = () => { isDark.value = !isDark.value; document.documentElement.classList.toggle('dark', isDark.value); }

const currentTab = ref('log')
const availableSizes = [220, 225, 230, 235, 240, 245, 250, 255, 260, 265, 270, 275, 280, 285, 290, 295, 300]
const weekDays = ['일', '월', '화', '수', '목', '금', '토'] 

const createEmptySizes = () => { const s = {}; availableSizes.forEach(sz => s[sz] = 0); return s; }

// ------------------------------------------
// 🔥 새로 추가할 날짜 포맷팅 로직
// ------------------------------------------
const formattedFormDate = computed(() => {
  if (!logForm.value.date) return '';
  const d = new Date(logForm.value.date);
  const yy = String(d.getFullYear()).slice(-2);
  const mm = String(d.getMonth() + 1).padStart(2, '0');
  const dd = String(d.getDate()).padStart(2, '0');
  const day = weekDays[d.getDay()];
  return `${yy}-${mm}-${dd}(${day})`;
});

// ==========================================
// 👟 오리지널 전체 신발 리스트
// ==========================================
const rawList = [
  { brand: 'Kybun', modelName: '가야 20 블랙' },
  { brand: 'Kybun', modelName: '겐프 브론즈 W' },
  { brand: 'Kybun', modelName: '겐프 블랙 W' },
  { brand: 'Kybun', modelName: '고려 20 블랙' },
  { brand: 'Kybun', modelName: '글라루스 블랙 W' },
  { brand: 'Kybun', modelName: '글라루스 블루 W' },
  { brand: 'Kybun', modelName: '글라루스 틴 W' },
  { brand: 'Kybun', modelName: '글라루스 화이트 W' },
  { brand: 'Kybun', modelName: '기분파크 20 블랙 W' },
  { brand: 'Kybun', modelName: '니온 20 네이비 W' },
  { brand: 'Kybun', modelName: '니온 20 블랙 W' },
  { brand: 'Kybun', modelName: '렌시 블랙' },
  { brand: 'Kybun', modelName: '로이크 20 브론즈 W' },
  { brand: 'Kybun', modelName: '로이크 20 블랙 W' },
  { brand: 'Kybun', modelName: '로카르노 캐비어 W' },
  { brand: 'Kybun', modelName: '롤 네이비 M' },
  { brand: 'Kybun', modelName: '롤 화이트 M' },
  { brand: 'Kybun', modelName: '루체른 20 캐비어 W' },
  { brand: 'Kybun', modelName: '뤼티 그레이 M' },
  { brand: 'Kybun', modelName: '뤼티 블랙 M' },
  { brand: 'Kybun', modelName: '뤼티 블루 M' },
  { brand: 'Kybun', modelName: '리기 20 아쿠아 W' },
  { brand: 'Kybun', modelName: '마글린겐 M 그레이' },
  { brand: 'Kybun', modelName: '마일렌 골드' },
  { brand: 'Kybun', modelName: '마일렌 블랙 그레이 M' },
  { brand: 'Kybun', modelName: '마테호른 20 다크블루 M' },
  { brand: 'Kybun', modelName: '몬타나 진회색 W' },
  { brand: 'Kybun', modelName: '바덴 블랙 W' },
  { brand: 'Kybun', modelName: '바우마 20 블랙' },
  { brand: 'Kybun', modelName: '바우마 20 블루' },
  { brand: 'Kybun', modelName: '바우마 20 샌드' },
  { brand: 'Kybun', modelName: '바우마 20 퍼플' },
  { brand: 'Kybun', modelName: '바우마 그레이' },
  { brand: 'Kybun', modelName: '바우마 실버' },
  { brand: 'Kybun', modelName: '바우마 핑크' },
  { brand: 'Kybun', modelName: '바우마 화이트' },
  { brand: 'Kybun', modelName: '바우마 CL 블랙' },
  { brand: 'Kybun', modelName: '바젤 20 오닉스 W' },
  { brand: 'Kybun', modelName: '발스 20 블랙 W' },
  { brand: 'Kybun', modelName: '베르니에 20 블랙 W' },
  { brand: 'Kybun', modelName: '부르크도르프 블랙' },
  { brand: 'Kybun', modelName: '브리그 그레이 W' },
  { brand: 'Kybun', modelName: '비엘 블랙' },
  { brand: 'Kybun', modelName: '비엘 실버 W' },
  { brand: 'Kybun', modelName: '사르간스 샌드 M' },
  { brand: 'Kybun', modelName: '센티스 20 블랙 M' },
  { brand: 'Kybun', modelName: '수르제 20 그레이블루' },
  { brand: 'Kybun', modelName: '수르제 20 블루레드' },
  { brand: 'Kybun', modelName: '스피츠 블랙 W' },
  { brand: 'Kybun', modelName: '시러스 DXB 블루옐로우 W' },
  { brand: 'Kybun', modelName: '시온 20 블랙 W' },
  { brand: 'Kybun', modelName: '시온 20 피치 W' },
  { brand: 'Kybun', modelName: '시온 화이트 W' },
  { brand: 'Kybun', modelName: '실바플라나 20 블랙 M' },
  { brand: 'Kybun', modelName: '아라우 블랙' },
  { brand: 'Kybun', modelName: '아라우 블루 W' },
  { brand: 'Kybun', modelName: '아로사 20 블랙 W' },
  { brand: 'Kybun', modelName: '아스코나 20 샌드' },
  { brand: 'Kybun', modelName: '아이롤로 20 앤트러사이트 M' },
  { brand: 'Kybun', modelName: '아이롤로 문 락 M' },
  { brand: 'Kybun', modelName: '오본느 블랙 W' },
  { brand: 'Kybun', modelName: '오본느 블랙체크 W' },
  { brand: 'Kybun', modelName: '올텐 블랙 M' },
  { brand: 'Kybun', modelName: '우리 블랙 M' },
  { brand: 'Kybun', modelName: '우스터 20 문 락 W' },
  { brand: 'Kybun', modelName: '우스터 블랙 W' },
  { brand: 'Kybun', modelName: '융프라우 20 피넛 W' },
  { brand: 'Kybun', modelName: '인터라켄 20 그레이' },
  { brand: 'Kybun', modelName: '조나 20 블랙 W' },
  { brand: 'Kybun', modelName: '조나 20 토프 W' },
  { brand: 'Kybun', modelName: '쥐라 블랙' },
  { brand: 'Kybun', modelName: '쥬크 20 브라운 M' },
  { brand: 'Kybun', modelName: '쥬크 20 캐비어 M' },
  { brand: 'Kybun', modelName: '취리히 II 브라운' },
  { brand: 'Kybun', modelName: '취리히 II 블랙' },
  { brand: 'Kybun', modelName: '카루즈 20 그래파이트 M' },
  { brand: 'Kybun', modelName: '카루즈 20 올리브 M' },
  { brand: 'Kybun', modelName: '칼 그레이블루' },
  { brand: 'Kybun', modelName: '칼 베이지' },
  { brand: 'Kybun', modelName: '쾨니즈 20 그레이 M' },
  { brand: 'Kybun', modelName: '쾨니즈 탄 M' },
  { brand: 'Kybun', modelName: '쿠어 20 블랙 M' },
  { brand: 'Kybun', modelName: '클로스터스 블랙 W' },
  { brand: 'Kybun', modelName: '클로텐 캐비어 M' },
  { brand: 'Kybun', modelName: '키아소 블랙 M' },
  { brand: 'Kybun', modelName: '테네로 20 그레이 W' },
  { brand: 'Kybun', modelName: '테신 20 레드 W' },
  { brand: 'Kybun', modelName: '테신 블랙 W' },
  { brand: 'Kybun', modelName: '테신 인디고 W' },
  { brand: 'Kybun', modelName: '테신 화이트 W' },
  { brand: 'Kybun', modelName: '툰 20 블랙 M' },
  { brand: 'Kybun', modelName: '파도 블랙 M' },
  { brand: 'Kybun', modelName: '푸라 실버' },
  { brand: 'Kybun', modelName: '필름스 그레이옐로우' },
  { brand: 'Kybun', modelName: '하이덴 브라운 M' },
  { brand: 'Joya', modelName: 'ID Zack III 블랙' },
  { brand: 'Joya', modelName: 'ID Zack 블루' },
  { brand: 'Joya', modelName: 'ID ZOOM II 블랙핑크' },
  { brand: 'Joya', modelName: 'ID ZOOM III 그레이블루' },
  { brand: 'Joya', modelName: 'ID ZOOM III 다크그레이' },
  { brand: 'Joya', modelName: '다이나모 벨로체 블루' },
  { brand: 'Joya', modelName: '다이나모 짚 다크그레이' },
  { brand: 'Joya', modelName: '다이나모 짚 베이지' },
  { brand: 'Joya', modelName: '다이나모 짚 블랙 II' },
  { brand: 'Joya', modelName: '다이나모 짚 블랙 III' },
  { brand: 'Joya', modelName: '다이나모 짚 화이트 M' },
  { brand: 'Joya', modelName: '다이나모 짚 화이트 W' },
  { brand: 'Joya', modelName: '더블린 블랙' },
  { brand: 'Joya', modelName: '라우라 라이트 그레이' },
  { brand: 'Joya', modelName: '라우라 라이트 블루 III' },
  { brand: 'Joya', modelName: '라우라 블랙' },
  { brand: 'Joya', modelName: '라우라 화이트' },
  { brand: 'Joya', modelName: '릴렉스 II 블랙' },
  { brand: 'Joya', modelName: '마벨라 라이트 그레이' },
  { brand: 'Joya', modelName: '마우이 블랙' },
  { brand: 'Joya', modelName: '마우이 화이트' },
  { brand: 'Joya', modelName: '마우이 STX 블루' },
  { brand: 'Joya', modelName: '말루쿠 라이트 그레이' },
  { brand: 'Joya', modelName: '말루쿠 옐로우블랙' },
  { brand: 'Joya', modelName: '메리다 레드' },
  { brand: 'Joya', modelName: '모스코 다크블루' },
  { brand: 'Joya', modelName: '모스코 브라운' },
  { brand: 'Joya', modelName: '모스코 짚 블루' },
  { brand: 'Joya', modelName: '몬타나 PTX 베이지' },
  { brand: 'Joya', modelName: '몬타나 PTX 블랙 II' },
  { brand: 'Joya', modelName: '몬타나 PTX 커리브라운' },
  { brand: 'Joya', modelName: '베니스 블랙 II' },
  { brand: 'Joya', modelName: '베니스 짚 라이트베이지' },
  { brand: 'Joya', modelName: '베니스 짚 베이지' },
  { brand: 'Joya', modelName: '베니스 짚 블랙' },
  { brand: 'Joya', modelName: '베니스 짚 화이트' },
  { brand: 'Joya', modelName: '벨로체 블랙' },
  { brand: 'Joya', modelName: '벨로체 화이트' },
  { brand: 'Joya', modelName: '비엔나 II 블랙' },
  { brand: 'Joya', modelName: '비엔나 화이트' },
  { brand: 'Joya', modelName: '스벤 브라운' },
  { brand: 'Joya', modelName: '시드니 II 라이트블루' },
  { brand: 'Joya', modelName: '시드니 II 블루' },
  { brand: 'Joya', modelName: '시에라 STX 브라운블랙' },
  { brand: 'Joya', modelName: '시카고 브라운' },
  { brand: 'Joya', modelName: '시카고 블랙' },
  { brand: 'Joya', modelName: '알렉산더 다크블루' },
  { brand: 'Joya', modelName: '알렉산더 브라운' },
  { brand: 'Joya', modelName: '에드워드 블랙' },
  { brand: 'Joya', modelName: '에이스 SR 블랙' },
  { brand: 'Joya', modelName: '에이스 SR 화이트' },
  { brand: 'Joya', modelName: '엘레나 라이트베이지' },
  { brand: 'Joya', modelName: '엘렉트라 화이트그레이' },
  { brand: 'Joya', modelName: '오사카 블랙' },
  { brand: 'Joya', modelName: '제니 라이트골드' },
  { brand: 'Joya', modelName: '제니 블랙' },
  { brand: 'Joya', modelName: '취리히 II 브라운' },
  { brand: 'Joya', modelName: '카도레 STX 브라운블랙' },
  { brand: 'Joya', modelName: '칸쿤 II 블루' },
  { brand: 'Joya', modelName: '칸쿤 II 화이트그레이' },
  { brand: 'Joya', modelName: '칸쿤 SR 블랙' },
  { brand: 'Joya', modelName: '칸쿤 stx 블랙' },
  { brand: 'Joya', modelName: '코모 II 브라운' },
  { brand: 'Joya', modelName: '코모 II 블랙' },
  { brand: 'Joya', modelName: '코모도 SR 화이트' },
  { brand: 'Joya', modelName: '코모도 라이트 블루/화이트' },
  { brand: 'Joya', modelName: '코모도 레드' },
  { brand: 'Joya', modelName: '코모도 베이지' },
  { brand: 'Joya', modelName: '코모도 블랙' },
  { brand: 'Joya', modelName: '트레블러 브라운' },
  { brand: 'Joya', modelName: '트레블러 블랙' },
  { brand: 'Joya', modelName: '파소피노 III 블랙' },
  { brand: 'Joya', modelName: '플래쉬 그레이' }
]

const deletedModels = ref(JSON.parse(localStorage.getItem('deleted_models') || '[]'))

const inventory = ref(rawList.map((item, idx) => ({ 
  id: idx + 1, brand: item.brand, modelName: item.modelName, 
  apiSlug: item.modelName === '바우마 실버' ? 'bauma-silver' : '', 
  sizes: createEmptySizes(), 
  draftSizes: createEmptySizes(), 
  history: {}, 
  hqStockData: null 
})))

// ==========================================
// 👇👇👇 여기에 복사해서 붙여넣으세요 👇👇👇
// ==========================================
const selectedBrand = ref('All')
const searchQuery = ref('')

// 클릭한 사이즈를 추적하는 변수
const sortBySize = ref(null)

// 헤더 클릭 시 작동하는 함수
const toggleSizeSort = (size) => {
  sortBySize.value = sortBySize.value === size ? null : size
}

const sortedAndFilteredInventory = computed(() => {
  // 1. 기존 필터링 로직
  let filtered = inventory.value.filter(shoe => {
    if (deletedModels.value.includes(shoe.modelName)) return false
    if (selectedBrand.value !== 'All' && shoe.brand !== selectedBrand.value) return false
    if (searchQuery.value && !shoe.modelName.toLowerCase().includes(searchQuery.value.toLowerCase())) return false
    return true
  })

  // 2. 사이즈 헤더 클릭 시 정렬 로직 (재고 유무 기준)
  if (sortBySize.value) {
    filtered.sort((a, b) => {
      const stockA = a.sizes[sortBySize.value] || 0
      const stockB = b.sizes[sortBySize.value] || 0
      
      // 재고가 1개 이상이면 1, 없으면 0으로 평가
      const hasStockA = stockA > 0 ? 1 : 0
      const hasStockB = stockB > 0 ? 1 : 0
      
      // 1순위: 재고가 있는 신발을 위로 올림
      if (hasStockA !== hasStockB) return hasStockB - hasStockA
      
      // 2순위: 재고 유무가 같다면 브랜드순 정렬
      if (a.brand !== b.brand) {
        return a.brand === 'Kybun' ? -1 : 1
      }
      
      // 3순위: 브랜드도 같다면 상품명순 정렬
      return a.modelName.localeCompare(b.modelName)
    })
  }
  
  return filtered
})
// ==========================================
// 👆👆👆 여기까지 붙여넣기 👆👆👆
// ==========================================


// ==========================================
// 🚀 Supabase 데이터 연동
// ==========================================
const transactionLogs = ref([])
const isFetchingLogs = ref(false)

// [화면에 보여줄 입출고 내역 필터링 - 재고조정은 숨김]
const displayTransactionLogs = computed(() => {
  return transactionLogs.value.filter(log => log.type !== '재고조정').slice(0, 50)
})

const fetchTransactionLogs = async (forceFullSync = false) => {
  isFetchingLogs.value = true
  
  const cacheKey = 'cached_transactions'
  let cachedLogs = []
  let lastId = 0
  
  if (!forceFullSync) {
    try {
      const cachedStr = localStorage.getItem(cacheKey)
      if (cachedStr) {
        const parsed = JSON.parse(cachedStr)
        if (Array.isArray(parsed)) {
          cachedLogs = parsed
          if (cachedLogs.length > 0) lastId = Math.max(...cachedLogs.map(l => Number(l.id) || 0))
        }
      }
    } catch(e) {
      cachedLogs = [] 
    }
  }

  let query = supabase.from('logs').select('*').order('id', { ascending: false }).limit(10000)
  if (lastId > 0) query = query.gt('id', lastId)
  
  const { data, error } = await query
  
  if (!error) { 
    let finalLogs = forceFullSync ? (data || []) : [...(data || []), ...cachedLogs]
    finalLogs.sort((a, b) => b.id - a.id)
    
    const uniqueLogs = []
    const ids = new Set()
    for (const log of finalLogs) {
      if (!ids.has(log.id)) {
        ids.add(log.id)
        uniqueLogs.push(log)
      }
    }
    
    transactionLogs.value = uniqueLogs
    localStorage.setItem(cacheKey, JSON.stringify(uniqueLogs))
    calculateInventory() 
  }
  isFetchingLogs.value = false
}

const calculateInventory = () => {
  inventory.value.forEach(shoe => {
    Object.keys(shoe.sizes).forEach(size => {
      shoe.sizes[size] = 0
      if (!shoe.history) shoe.history = {}
      shoe.history[size] = false
    })
  })
  
  transactionLogs.value.forEach(log => {
    const shoe = inventory.value.find(s => s.modelName === log.modelName)
    if (shoe && log.size && log.quantity) {
      const q = Number(log.quantity)
      shoe.history[log.size] = true 

      if (['입고', '양수', '재고조정'].includes(log.type)) shoe.sizes[log.size] += q
      else if (['출고', '양도'].includes(log.type)) shoe.sizes[log.size] -= q
    }
  })
}

onMounted(() => {
  fetchTransactionLogs()
  fetchAsRecords()

  // 1. 기존: 입출고 실시간 연동
  supabase
    .channel('public:logs')
    .on('postgres_changes', { event: '*', schema: 'public', table: 'logs' }, (payload) => {
      if (payload.eventType === 'INSERT') {
        const exists = transactionLogs.value.find(l => l.id === payload.new.id)
        if (!exists) transactionLogs.value.unshift(payload.new)
      } 
      else if (payload.eventType === 'UPDATE') {
        const index = transactionLogs.value.findIndex(l => l.id === payload.new.id)
        if (index !== -1) transactionLogs.value[index] = payload.new
      } 
      else if (payload.eventType === 'DELETE') {
        transactionLogs.value = transactionLogs.value.filter(l => l.id !== payload.old.id)
      }

      localStorage.setItem('cached_transactions', JSON.stringify(transactionLogs.value))
      calculateInventory()
    })
    .subscribe()

  // 2. 🔥 추가됨: AS 현황 실시간 연동 (카톡 대화창처럼!)
  supabase
    .channel('public:as_records')
    .on('postgres_changes', { event: '*', schema: 'public', table: 'as_records' }, (payload) => {
      if (payload.eventType === 'INSERT') {
        const exists = asRecords.value.find(a => a.id === payload.new.id)
        if (!exists) asRecords.value.unshift(payload.new)
      } 
      else if (payload.eventType === 'UPDATE') {
        const index = asRecords.value.findIndex(a => a.id === payload.new.id)
        if (index !== -1) asRecords.value[index] = payload.new
      } 
      else if (payload.eventType === 'DELETE') {
        asRecords.value = asRecords.value.filter(a => a.id !== payload.old.id)
      }
    })
    .subscribe()
})

// --- 입출고 등록 로직 ---
const logForm = ref({ type: '출고', date: new Date().toISOString().split('T')[0], brand: 'Kybun', modelName: '', size: 240, quantity: 1 })
const resetLogForm = () => { logForm.value = { type: '출고', date: new Date().toISOString().split('T')[0], brand: 'Kybun', modelName: '', size: 240, quantity: 1 } }
const availableModelsForForm = computed(() => inventory.value.filter(s => s.brand === logForm.value.brand && !deletedModels.value.includes(s.modelName)).map(s => s.modelName).sort())

const submitLog = async () => {
  if (!logForm.value.modelName) return alert('모델을 선택해주세요!');
  const payload = {
    date: logForm.value.date, type: logForm.value.type, brand: logForm.value.brand,
    modelName: logForm.value.modelName, size: logForm.value.size, quantity: logForm.value.quantity
  };

  const { data, error } = await supabase.from('logs').insert([payload]).select()
  if (!error && data && data[0]) { 
    const exists = transactionLogs.value.find(l => l.id === data[0].id)
    if(!exists) transactionLogs.value.unshift(data[0]) 
    localStorage.setItem('cached_transactions', JSON.stringify(transactionLogs.value))
    calculateInventory() 
    logForm.value.quantity = 1 
  } else {
    alert('저장 실패: ' + (error?.message || '알 수 없는 오류'))
  }
}

const deleteLog = async (log) => {
  if (!confirm('영구 삭제하시겠습니까? 데이터베이스에서도 삭제됩니다.')) return
  const { error } = await supabase.from('logs').delete().eq('id', log.id)
  if (!error) { 
    transactionLogs.value = transactionLogs.value.filter(l => l.id !== log.id)
    localStorage.setItem('cached_transactions', JSON.stringify(transactionLogs.value))
    calculateInventory() 
  } else {
    alert('삭제 실패: ' + error.message)
  }
}

// 🔥 다이렉트 재고 수정 (엑셀 복사/붙여넣기 지원)
const isEditMode = ref(false)

const toggleEditMode = async () => {
  if (isEditMode.value) {
    const payloads = []
    const today = new Date().toISOString().split('T')[0]
    
    inventory.value.forEach(shoe => {
      if(deletedModels.value.includes(shoe.modelName)) return;

      availableSizes.forEach(size => {
        const originalVal = shoe.sizes[size] || 0
        const draftValRaw = shoe.draftSizes[size]
        const newVal = draftValRaw === '' || draftValRaw === null ? 0 : Number(draftValRaw)
        const diff = newVal - originalVal
        
        if (diff !== 0) {
          payloads.push({ date: today, type: '재고조정', brand: shoe.brand, modelName: shoe.modelName, size: size, quantity: diff })
        }
      })
    })
    
    if (payloads.length > 0) {
      const { error } = await supabase.from('logs').insert(payloads)
      if (error) { alert('수정 내역 저장 실패: ' + error.message); return; }
    }
    
    isEditMode.value = false
  } else {
    inventory.value.forEach(shoe => {
      availableSizes.forEach(sz => {
        shoe.draftSizes[sz] = (shoe.sizes[sz] === 0 && !shoe.history[sz]) ? '' : shoe.sizes[sz]
      })
    })
    isEditMode.value = true
  }
}

const isDraftChanged = (shoe, size) => {
  const original = shoe.sizes[size] || 0
  const draftRaw = shoe.draftSizes[size]
  const current = draftRaw === '' || draftRaw === null ? 0 : Number(draftRaw)
  return original !== current
}

const getInputClass = (shoe, size) => {
  if (isDraftChanged(shoe, size)) return 'text-indigo-700 bg-indigo-50 font-black ring-2 ring-indigo-400'
  
  const draftRaw = shoe.draftSizes[size]
  const current = draftRaw === '' || draftRaw === null ? 0 : Number(draftRaw)
  
  if (current > 0) return 'text-black dark:text-white font-black'
  if (current < 0) return 'text-red-600 font-black'
  if (current === 0 && shoe.history?.[size]) return 'text-[#5c7f59] font-black'
  
  return 'text-transparent focus:text-gray-400'
}

const getRowTotalStock = (shoe) => {
  let total = 0
  availableSizes.forEach(size => { 
    const val = isEditMode.value ? shoe.draftSizes[size] : shoe.sizes[size]
    total += val === '' || val === null ? 0 : Number(val)
  })
  return total
}

const deleteModel = (shoe) => {
  if (confirm(`[${shoe.modelName}] 모델을 목록에서 숨기시겠습니까?\n(기존 입출고 기록은 보존됩니다.)`)) {
    deletedModels.value.push(shoe.modelName)
    localStorage.setItem('deleted_models', JSON.stringify(deletedModels.value))
  }
}

const handlePaste = (e, startRowIdx, startColIdx) => {
  e.preventDefault();
  const pasteData = e.clipboardData.getData('text');
  if (!pasteData) return;

  const rows = pasteData.split(/\r?\n/);
  
  for (let i = 0; i < rows.length; i++) {
    if (rows[i] === '' && i === rows.length - 1) continue; 
    
    const cols = rows[i].split('\t');
    const targetShoe = sortedAndFilteredInventory.value[startRowIdx + i];
    if (!targetShoe) break; 
    
    for (let j = 0; j < cols.length; j++) {
      const targetSize = availableSizes[startColIdx + j];
      if (!targetSize) break; 
      
      const valStr = cols[j].trim().replace(/,/g, ''); 
      if (valStr !== '') {
        const val = Number(valStr);
        if (!isNaN(val)) {
          targetShoe.draftSizes[targetSize] = val;
        }
      }
    }
  }
}

// --- B2B 실시간 연동 ---
const isB2BMode = ref(false)
const isLoadingB2B = ref(false)

const fetchHQStock = async (shoe) => {
  if (!shoe.apiSlug) return;
  const cacheKey = `hq_cache_${shoe.apiSlug}`;
  const cached = localStorage.getItem(cacheKey);

  if (cached) {
    const parsedCache = JSON.parse(cached);
    if (Date.now() - parsedCache.timestamp < 12 * 60 * 60 * 1000) { shoe.hqStockData = parsedCache.data; return; }
  }

  try {
    const res = await fetch(`https://kybunjoya.aico.swiss/api/v2/aico-shops/1/products/cache/${shoe.apiSlug}`, { headers: { "authorization": HQ_API_TOKEN } })
    if(!res.ok) return;
    const json = await res.json()
    const map = { "34 1/3":215, "35":220, "35 2/3":225, "36 1/3":230, "37":235, "37 2/3":240, "38 1/3":245, "39":250, "39 2/3":255, "40 1/3":260, "41":265, "41 2/3":270, "42 1/3":275, "43":280, "43 2/3":285, "44 1/3":290, "45":295 }
    const stock = {}; 
    (json.data?.productVariants || []).forEach(v => { 
      const kr = map[v.productVariantOptions?.[0]?.value]; 
      if(kr) stock[kr] = v.stockData?.[0]?.stocks?.confirmedAvailableAmount || 0 
    })
    shoe.hqStockData = stock;
    localStorage.setItem(cacheKey, JSON.stringify({ timestamp: Date.now(), data: stock }));
  } catch (e) {}
}

const toggleB2BMode = async () => {
  isB2BMode.value = !isB2BMode.value;
  if (isB2BMode.value) {
    isLoadingB2B.value = true;
    const targetShoes = sortedAndFilteredInventory.value.filter(s => s.apiSlug);
    await Promise.all(targetShoes.map(shoe => fetchHQStock(shoe)));
    isLoadingB2B.value = false;
  }
};

const getHQStockTextColor = (n) => { 
  if (n === undefined || n === null) return 'text-transparent';
  if (n >= 15) return 'text-green-500'; 
  if (n > 0) return 'text-yellow-500'; 
  return 'text-red-500'; 
}

// ==========================================
// 📈 판매 현황 로직 (브랜드 매칭 도우미 포함)
// ==========================================
const getStrictBrand = (log) => {
  if (['Kybun', 'kybun', '기분'].includes(log.brand)) return 'Kybun';
  if (['Joya', 'joya', '조야'].includes(log.brand)) return 'Joya';
  const shoe = inventory.value.find(s => s.modelName === log.modelName);
  return shoe ? shoe.brand : 'Kybun'; 
}

const currentSalesMonth = ref(new Date().toISOString().slice(0, 7))

// 1. 현재 월 기본 통계
const monthlyLogs = computed(() => transactionLogs.value.filter(l => l.type === '출고' && String(l.date).startsWith(currentSalesMonth.value)))
const monthlyTotalQuantity = computed(() => monthlyLogs.value.reduce((s, l) => s + Number(l.quantity), 0))
const monthlyKybunQuantity = computed(() => monthlyLogs.value.filter(l => getStrictBrand(l) === 'Kybun').reduce((s, l) => s + Number(l.quantity), 0))
const monthlyJoyaQuantity = computed(() => monthlyLogs.value.filter(l => getStrictBrand(l) === 'Joya').reduce((s, l) => s + Number(l.quantity), 0))

// 2. 베스트셀러 집계
const monthlyBestSellers = computed(() => {
  const m = {}; 
  monthlyLogs.value.forEach(l => {
    if (!m[l.modelName]) m[l.modelName] = { brand: getStrictBrand(l), modelName: l.modelName, quantity: 0 }
    m[l.modelName].quantity += Number(l.quantity)
  }); 
  return Object.values(m).sort((a, b) => b.quantity - a.quantity)
})

// 3. 전년 동월(YoY) 비교
const lastYearMonth = computed(() => {
  const [year, month] = currentSalesMonth.value.split('-');
  return `${Number(year) - 1}-${month}`;
});
const lastYearLogs = computed(() => transactionLogs.value.filter(l => l.type === '출고' && String(l.date).startsWith(lastYearMonth.value)));
const lastYearTotalQuantity = computed(() => lastYearLogs.value.reduce((s, l) => s + Number(l.quantity), 0));
const lastYearKybunQuantity = computed(() => lastYearLogs.value.filter(l => getStrictBrand(l) === 'Kybun').reduce((s, l) => s + Number(l.quantity), 0));
const lastYearJoyaQuantity = computed(() => lastYearLogs.value.filter(l => getStrictBrand(l) === 'Joya').reduce((s, l) => s + Number(l.quantity), 0));

const getYoYDiff = (current, last) => current - last;

// 4. 월별 판매 차트 (해당 연도 1~12월)
const graphBrandFilter = ref('All'); 
const yearlyGraphData = computed(() => {
  const currentYear = currentSalesMonth.value.split('-')[0];
  const data = [];
  let maxVal = 0;

  for (let i = 1; i <= 12; i++) {
    const monthStr = `${currentYear}-${String(i).padStart(2, '0')}`;
    const monthLogs = transactionLogs.value.filter(l => l.type === '출고' && String(l.date).startsWith(monthStr));

    let total = 0;
    monthLogs.forEach(l => {
      const brand = getStrictBrand(l);
      if (graphBrandFilter.value === 'All' || graphBrandFilter.value === brand) {
        total += Number(l.quantity);
      }
    });

    if (total > maxVal) maxVal = total;
    data.push({ month: i, total });
  }

  return data.map(d => ({
    ...d,
    heightPercent: maxVal === 0 ? 0 : Math.round((d.total / maxVal) * 100)
  }));
});

// --- AS 현황 로직 (Supabase 연동 완료) ---
const asRecords = ref([])
const asForm = ref({ receiveDate: new Date().toISOString().split('T')[0], customer: '', phone: '', modelName: '', reason: '', fee: '', address: '' })
const resetAsForm = () => asForm.value = { receiveDate: new Date().toISOString().split('T')[0], customer: '', phone: '', modelName: '', reason: '', fee: '', address: '' }

// Supabase에서 AS 데이터 불러오기
const fetchAsRecords = async () => {
  const { data, error } = await supabase.from('as_records').select('*').order('id', { ascending: false })
  if (!error && data) asRecords.value = data
}

// 초기 로딩 시 AS 데이터 불러오기 추가
onMounted(() => {
  fetchTransactionLogs()
  fetchAsRecords() // 추가됨
  
  // (기존 logs 채널 구독 코드는 그대로 유지)
  supabase.channel('public:logs').on('postgres_changes', { event: '*', schema: 'public', table: 'logs' }, (payload) => {
    // ... 생략 ...
  }).subscribe()
})

const submitAs = async () => { 
  if (!asForm.value.customer) return alert("고객명을 입력하세요."); 
  
  const payload = { ...asForm.value, isCompleted: false, releaseDate: null }
  const { data, error } = await supabase.from('as_records').insert([payload]).select()
  
  if (!error && data) {
    asRecords.value.unshift(data[0])
    resetAsForm()
  } else {
    alert("AS 등록 실패: " + error?.message)
  }
}

const deleteAs = async (id) => {
  if(!confirm('해당 AS 접수 내역을 삭제하시겠습니까?')) return;
  const { error } = await supabase.from('as_records').delete().eq('id', id)
  if(!error) asRecords.value = asRecords.value.filter(a => a.id !== id)
}

const toggleAsComplete = async (as) => { 
  const newStatus = !as.isCompleted;
  const newReleaseDate = newStatus ? new Date().toISOString().split('T')[0] : null;
  
  const { error } = await supabase.from('as_records').update({ isCompleted: newStatus, releaseDate: newReleaseDate }).eq('id', as.id)
  
  if(!error) {
    as.isCompleted = newStatus;
    as.releaseDate = newReleaseDate;
  } else {
    alert("상태 변경 실패: " + error.message)
  }
}

const getTotalStock = (s) => Object.values(s).reduce((a, b) => a + b, 0)
const totalKybunStock = computed(() => inventory.value.filter(s => s.brand === 'Kybun').reduce((sum, s) => sum + getTotalStock(s.sizes), 0))
const totalJoyaStock = computed(() => inventory.value.filter(s => s.brand === 'Joya').reduce((sum, s) => sum + getTotalStock(s.sizes), 0))
const getFormattedDate = (d) => { if(!d) return ''; const date = new Date(d); return `${String(date.getFullYear()).slice(-2)}-${String(date.getMonth()+1).padStart(2,'0')}-${String(date.getDate()).padStart(2,'0')}(${weekDays[date.getDay()]})` }
const getRowBg = (t) => t === '출고' ? 'bg-[#FABCBC] shadow-sm' : t === '입고' ? 'bg-[#C5E1FF] shadow-sm' : t === '재고조정' ? 'bg-[#E9D5FF] shadow-sm' : 'bg-[#FFF2CC] shadow-sm'

const showModal = ref(false), newShoe = ref({ brand: 'Kybun', modelName: '' })
const addShoe = () => { 
  if (!newShoe.value.modelName) return; 
  const delIdx = deletedModels.value.indexOf(newShoe.value.modelName)
  if (delIdx > -1) {
    deletedModels.value.splice(delIdx, 1)
    localStorage.setItem('deleted_models', JSON.stringify(deletedModels.value))
  } else {
    inventory.value.push({ id: Date.now(), brand: newShoe.value.brand, modelName: newShoe.value.modelName, sizes: createEmptySizes(), draftSizes: createEmptySizes(), history: {}, hqStockData: null }); 
  }
  showModal.value = false; 
}
</script>

<style>
@import url("https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css");
body { font-family: "Pretendard", sans-serif; }
.dark body { background-color: #111827; color: #f9fafb; }
.dark .bg-white { background-color: #1f2937 !important; border-color: #374151 !important; }
.dark .bg-gray-50 { background-color: #111827 !important; }
.dark .bg-gray-100 { background-color: #374151 !important; }
.dark .text-gray-900, .dark .text-gray-800 { color: #f9fafb !important; }
.dark .text-gray-700, .dark .text-gray-600 { color: #e5e7eb !important; }
.dark .text-gray-500, .dark .text-gray-400 { color: #9ca3af !important; }
.dark .border-gray-100, .dark .border-gray-200, .dark .border-gray-300 { border-color: #374151 !important; }
.dark input, .dark select { background-color: #374151 !important; color: #f9fafb !important; border-color: #4b5563 !important; }
.dark input::placeholder { color: #9ca3af !important; }
.dark .bg-\[\#C5E1FF\] { background-color: #1e3a8a !important; color: #bfdbfe !important; border-color: #1e3a8a !important; }
.dark .bg-\[\#FABCBC\] { background-color: #7f1d1d !important; color: #fecaca !important; border-color: #7f1d1d !important; }
.dark .bg-\[\#FFF2CC\] { background-color: #713f12 !important; color: #fef08a !important; border-color: #713f12 !important; }
.dark .bg-\[\#E9D5FF\] { background-color: #6b21a8 !important; color: #f3e8ff !important; border-color: #6b21a8 !important; }
.list-enter-active, .list-leave-active { transition: all 0.3s; }
.list-enter-from, .list-leave-to { opacity: 0; transform: translateY(10px); }

/* 화살표 없애기 (수정 모드 인풋용) */
input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
  -webkit-appearance: none; 
  margin: 0; 
}
</style>