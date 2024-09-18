<template>
  <div class="min-h-screen bg-purple-900 text-purple-100">
    <header class="bg-purple-800 py-4 shadow-lg rounded-md">
      <div class="container mx-auto px-4">
        <h1 class="text-3xl font-bold text-purple-200">Luna Route</h1>
        <p class="text-purple-300">夜ドライブルート生成アプリ</p>
      </div>
    </header>

    <main class="container mx-auto px-4 py-8">
      <div class="max-w-4xl mx-auto bg-purple-800 rounded-lg shadow-xl p-6">
        <form @submit.prevent="generateRoute" class="space-y-4" v-if="!generatedRoute">
          <div>
            <label for="start" class="block text-sm font-medium text-purple-200">出発地点</label>
            <input
                id="start"
                v-model="startLocation"
                type="text"
                required
                class="mt-1 block w-full px-3 py-2 bg-purple-700 border border-purple-600 rounded-md text-purple-100 placeholder-purple-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent"
                placeholder="出発地点を入力してください"
            />
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
          <div class="bg-purple-700 rounded-lg p-4">
            <h2 class="text-xl font-semibold text-purple-200 mb-2">生成されたルート</h2>
            <p class="text-purple-100">{{ generatedRoute }}</p>
          </div>

          <div class="bg-purple-700 rounded-lg p-4">
            <h2 class="text-xl font-semibold text-purple-200 mb-4">おすすめスポット</h2>
            <ul class="space-y-4">
              <li v-for="spot in recommendedSpots" :key="spot.id" class="bg-purple-600 rounded-lg p-3">
                <h3 class="text-lg font-medium text-purple-200">{{ spot.name }}</h3>
                <p class="text-purple-300">{{ spot.description }}</p>
              </li>
            </ul>
          </div>

          <div class="bg-purple-700 rounded-lg p-4">
            <h2 class="text-xl font-semibold text-purple-200 mb-4">ルートマップ</h2>
            <div class="bg-purple-600 rounded-lg p-2 aspect-video flex items-center justify-center">
<!--              <img src="/placeholder.svg?height=400&width=600" alt="ルートマップ" class="max-w-full h-auto rounded" />-->
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
body {
  background-color: #44337A; /* bg-purple-900 */
  color: #FAF5FF; /* text-purple-100 */
}
</style>