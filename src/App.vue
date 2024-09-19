<template>
  <!-- 背景のグラデーションを暗めに変更 -->
  <div class="min-h-screen bg-gradient-to-b from-purple-900 via-purple-800 to-purple-900 text-purple-100">
    <!-- ヘッダーにロゴを追加し、レイアウトを調整 -->
    <header class="bg-purple-800 bg-opacity-50 py-4 shadow-lg">
      <div class="container mx-auto px-4 flex items-center">
        <!-- ロゴを追加 -->
        <svg class="h-10 w-10 mr-4" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M12 2L2 7L12 12L22 7L12 2Z" fill="#A78BFA"/>
          <path d="M2 17L12 22L22 17" stroke="#A78BFA" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M2 12L12 17L22 12" stroke="#A78BFA" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
        <div>
          <h1 class="text-3xl font-bold text-purple-200">Luna Route</h1>
          <p class="text-purple-300">夜ドライブルート生成アプリ</p>
        </div>
      </div>
    </header>

    <main class="container mx-auto px-4 py-8">
      <div class="max-w-4xl mx-auto bg-purple-800 bg-opacity-50 rounded-lg shadow-xl p-6">
        <form @submit.prevent="generateRoute" class="space-y-4" v-if="!generatedRoute">
          <div>
            <label for="start" class="block text-sm font-medium text-purple-200">出発地点</label>
            <div class="mt-1 flex rounded-md shadow-sm">
              <input
                  id="start"
                  v-model="startLocation"
                  type="text"
                  required
                  class="flex-grow px-3 py-2 bg-purple-700 border border-purple-600 rounded-l-md text-purple-100 placeholder-purple-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent"
                  placeholder="出発地点を入力してください"
              />
              <!-- 位置情報を取得するボタンを追加 -->
              <button
                  type="button"
                  @click="getCurrentLocation"
                  class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-r-md text-purple-900 bg-purple-300 hover:bg-purple-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-400"
              >
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-1" viewBox="0 0 20 20" fill="currentColor">
                  <path fill-rule="evenodd" d="M5.05 4.05a7 7 0 119.9 9.9L10 18.9l-4.95-4.95a7 7 0 010-9.9zM10 11a2 2 0 100-4 2 2 0 000 4z" clip-rule="evenodd" />
                </svg>
                現在地
              </button>
            </div>
          </div>

          <div>
            <label for="duration" class="block text-sm font-medium text-purple-200">ドライブ時間（分）</label>
            <input
                id="duration"
                v-model.number="driveDuration"
                type="number"
                required
                min="15"
                max="180"
                class="mt-1 block w-full px-3 py-2 bg-purple-700 border border-purple-600 rounded-md text-purple-100 placeholder-purple-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent"
            />
          </div>

          <div>
            <label for="preferences" class="block text-sm font-medium text-purple-200">ルート設定</label>
            <select
                id="preferences"
                v-model="routePreferences"
                class="mt-1 block w-full px-3 py-2 bg-purple-700 border border-purple-600 rounded-md text-purple-100 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent"
            >
              <option value="scenic">景色重視</option>
              <option value="quiet">静かな道路</option>
              <option value="city">都市の夜景</option>
            </select>
          </div>

          <button
              type="submit"
              class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-purple-900 bg-purple-300 hover:bg-purple-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-400"
          >
            ルートを生成
          </button>
        </form>

        <div v-if="generatedRoute" class="space-y-6">
          <div class="bg-purple-700 bg-opacity-50 rounded-lg p-4">
            <h2 class="text-xl font-semibold text-purple-200 mb-2">生成されたルート</h2>
            <p class="text-purple-100">{{ generatedRoute }}</p>
          </div>

          <div class="bg-purple-700 bg-opacity-50 rounded-lg p-4">
            <h2 class="text-xl font-semibold text-purple-200 mb-4">おすすめスポット</h2>
            <ul class="space-y-4">
              <li v-for="spot in recommendedSpots" :key="spot.id" class="bg-purple-600 bg-opacity-50 rounded-lg p-3">
                <h3 class="text-lg font-medium text-purple-200">{{ spot.name }}</h3>
                <p class="text-purple-300">{{ spot.description }}</p>
              </li>
            </ul>
          </div>

          <div class="bg-purple-700 bg-opacity-50 rounded-lg p-4">
            <h2 class="text-xl font-semibold text-purple-200 mb-4">ルートマップ</h2>
            <div class="bg-purple-600 bg-opacity-50 rounded-lg p-2 aspect-video flex items-center justify-center">
              <img src="C:\Users\gannd\WebstormProjects\navitime_intern_basic_2024\img.png?height=400&width=600" alt="ルートマップ" class="max-w-full h-auto rounded" />
            </div>
          </div>

          <button
              @click="resetRoute"
              class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-purple-900 bg-purple-300 hover:bg-purple-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-400"
          >
            新しいルートを作成
          </button>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const startLocation = ref('')
const driveDuration = ref(30)
const routePreferences = ref('scenic')
const generatedRoute = ref('')
const recommendedSpots = ref([])

//TODO: Changed
// 位置情報を取得する関数を追加
const getCurrentLocation = () => {
  if ("geolocation" in navigator) {
    navigator.geolocation.getCurrentPosition(
        position => {
          const { latitude, longitude } = position.coords;
          startLocation.value = `緯度: ${latitude}, 経度: ${longitude}`;
        },
        error => {
          console.error("位置情報の取得に失敗しました:", error.message);
          alert("位置情報の取得に失敗しました。手動で入力してください。");
        }
    );
  } else {
    alert("お使いのブラウザは位置情報をサポートしていません。");
  }
}

const generateRoute = () => {
  const preferences = {
    scenic: '景色重視',
    quiet: '静かな道路',
    city: '都市の夜景'
  }
  generatedRoute.value = `${startLocation.value}から${driveDuration.value}分の${preferences[routePreferences.value]}ルートが生成されました。素敵なLuna Routeをお楽しみください！`

  // 仮のおすすめスポットデータ
  recommendedSpots.value = [
    { id: 1, name: '夜景スポットA', description: '街を一望できる絶景ポイント' },
    { id: 2, name: '静かな公園B', description: '星空観察に最適な場所' },
    { id: 3, name: 'ドライブインC', description: '24時間営業の人気レストラン' },
  ]
}

const resetRoute = () => {
  startLocation.value = ''
  driveDuration.value = 30
  routePreferences.value = 'scenic'
  generatedRoute.value = ''
  recommendedSpots.value = []
}
</script>

<style>
//TODO
/* 背景のグラデーションを全体に適用 */
body {
  background: linear-gradient(to bottom, #44337A, #322659, #44337A);
  color: #FAF5FF;
  min-height: 100vh;
}
</style>