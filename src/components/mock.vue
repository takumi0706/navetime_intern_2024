<template>
    <div class=" bg-gradient-to-b from-purple-950 via-purple-800 to-purple-900 text-purple-100">
        <header class="flex bg-purple-800 py-4 shadow-lg rounded-md">
            <img class="size-16 ml-4 rounded-lg" src="/home/iship2024-4/internship_base/public/images/logo.png" />
            <div class="container mx-auto px-4">
                <h1 class="text-3xl font-bold text-purple-200">Luna Route</h1>
                <p class="text-purple-300">夜ドライブルート生成アプリ</p>
            </div>
        </header>

        <main class="container mx-auto px-4 py-8 relative">
            <div v-show="!generatedRoute"
                class="max-w-4xl mx-auto bg-purple-800 rounded-lg shadow-xl p-6 z-50 absolute inset-0 mx-4 my-8">
                <form @submit.prevent="generateRoute" class="space-y-4">
                    <div>
                        <label for="start" class="block text-sm font-medium text-purple-200">出発地点</label>
                        <input id="start" v-model="word" type="text" required
                            class="mt-1 block w-full px-3 py-2 bg-purple-700 border border-purple-600 rounded-md text-purple-100 placeholder-purple-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent"
                            placeholder="出発地点を入力してください" />
                        <button type="button" @click="getCurrentLocation"
                            class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md text-purple-900 bg-purple-300 hover:bg-purple-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-400">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-1" viewBox="0 0 20 20"
                                fill="currentColor">
                                <path fill-rule="evenodd"
                                    d="M5.05 4.05a7 7 0 119.9 9.9L10 18.9l-4.95-4.95a7 7 0 010-9.9zM10 11a2 2 0 100-4 2 2 0 000 4z"
                                    clip-rule="evenodd" />
                            </svg>
                            現在地
                        </button>
                    </div>

                    <div>
                        <label for="duration" class="block text-sm font-medium text-purple-200">ドライブ時間（分）</label>
                        <input id="duration" v-model.number="driveDuration" type="number" required min="15" max="180"
                            class="mt-1 block w-full px-3 py-2 bg-purple-700 border border-purple-600 rounded-md text-purple-100 placeholder-purple-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent" />
                    </div>

                    <div>
                        <label for="preferences" class="block text-sm font-medium text-purple-200">ルート設定</label>
                        <select id="preferences" v-model="selectedFeeling"
                            class="mt-1 block w-full px-3 py-2 bg-purple-700 border border-purple-600 rounded-md text-purple-100 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent">
                            <option value="refresh">スッキリしたい</option>
                            <option value="chill">落ち着きたい</option>
                            <option value="aggressive">攻めたい</option>
                            <option value="random">ランダム</option>
                        </select>
                    </div>

                    <button type="submit"
                        class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-purple-900 bg-purple-300 hover:bg-purple-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-400"
                        @click="searchSpot()">

                        ルートを生成

                    </button>
                </form>
            </div>
            <div class="max-w-4xl mx-auto bg-purple-800 rounded-lg shadow-xl p-6">
                <div class="space-y-6">
                    <div class="bg-purple-700 rounded-lg p-4">
                        <h2 class="text-xl font-semibold text-purple-200 mb-2">生成されたルート</h2>
                        <p class="text-purple-100">{{ startLocation.lat }},{{ startLocation.lon }}から{{
                            moveTime }}分の{{ feelings[selectedFeeling] }}ルートが生成されました。素敵なLuna
                            Routeをお楽しみください！</p>
                    </div>

                    <div class="bg-purple-700 rounded-lg  p-4">
                        <h2 class="text-xl font-semibold text-purple-200 mb-4">おすすめスポット</h2>
                        <ul class="space-y-4">
                            <div class="flex flex-col gap-2" v-if="data">
                                <li v-for="spot in data" :key="spot.code" class="bg-purple-600 rounded-lg p-3 ">
                                    <h3 class="text-lg font-medium text-purple-200">{{ spot.name }}</h3>
                                    <p class="text-purple-300">{{ spot.address_name }}</p>
                                </li>
                            </div>
                        </ul>
                    </div>

                    <div class="bg-purple-700 rounded-lg p-4">
                        <h2 class="text-xl font-semibold text-purple-200 mb-4">ルートマップ</h2>
                        <div class="bg-purple-600 rounded-lg p-2 h-80">
                            <div class="z-10 w-full h-full" id="map" />
                        </div>
                    </div>

                    <button @click="resetRoute"
                        class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-purple-900 bg-purple-300 hover:bg-purple-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-400">
                        新しいルートを作成
                    </button>
                </div>
            </div>
        </main>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import { request } from '../../../../Downloads/The Moutains/internship_base/api/fetcher.js'

const word = ref("");
const data = ref(null);
const error = ref(null);

const feeling = ref("");
const feelings = {
    refresh: 'スッキリしたい',
    chill: '落ち着きたい',
    aggressive: '攻めたい'
}
const selectedFeeling = ref("chill");
const startLocation = ref('')
const driveDuration = ref(30)
const generatedRoute = ref('')
const recommendedSpots = ref([])
const markers = [];
let startMarker = {};
const via = []
const moveTime = ref(0);

const searchSpot = async () => {

    if (!startLocation.value) {
        alert("キーワードを入力してください")
        return
    }
    try {
        const response = await request('spot/category_code', {
            coord: word.value,
            category: selectFeeling(selectedFeeling.value),
            radius: 20000,
            limit: 100,
        })
        data.value = response.items

        via.length = 0
        console.log("hogehogehoge")
        // for (const item of data.value) {
        //     via.push(`{"lat":${item.coord.lat},"lon":${item.coord.lon}}`)
        // }
        console.log("fuga")
        // const vias = `[${via.toString()}]`;
        moveTime.value = await drawRoute(word.value, "");

        //data.value = response.items.slice(-3);
        addMarkers(data.value);
        addStartMarkers(startLocation.value);

        const rect = mapscript.util.locationsToLatLngRect([
            new mapscript.value.LatLng(startLocation.value.lat, startLocation.value.lon),
            ...data.value.map((item) => {
                return new mapscript.value.LatLng(item.coord.lat, item.coord.lon)
            })
        ])
        map.moveBasedOnLatLngRect(rect, true)
    } catch (e) {
        // 通信結果がエラーのときは何もしない
        error.value = e
        return
    }
}

const readCoord = (num) => {
    const lat = data.value[num].coord.lat
    const lon = data.value[num].coord.lon
    return `${lat},${lon}`;
}

const readCoordJson = (num) => {
    const lat = dataList.value.items[num].coord.lat
    const lon = dataList.value.items[num].coord.lon
    return JSON.stringify({ lat, lon });
}

const generateRoute = () => {
    generatedRoute.value = `${startLocation.value.lat},${startLocation.value.lon}から${moveTime.value}分の${feelings[selectedFeeling.value]}ルートが生成されました。素敵なLuna Routeをお楽しみください！`

    // 仮のおすすめスポットデータ
    recommendedSpots.value = data.value
}

const selectFeeling = (feeling) => {
    if (feeling == "refresh") {
        const category_filter = "0710006.0710008.0803005.0711001001";
        return category_filter;
    } else if (feeling == "chill") {
        const category_filter = "0710003.0710008.0710007.0706004.0706012.0706010.0702001.0702004";
        return category_filter;
    } else if (feeling == "aggressive") {
        const category_filter = "0706001.0706002.0711001002.0702004";
        return category_filter;
    } else {
        // TODO: Randomの仕様を考える
        const category_filter = "";
        return category_filter;
    }
}

const resetRoute = () => {
    driveDuration.value = 30
    selectedFeeling.value = 'refresh'
    generatedRoute.value = ``
    recommendedSpots.value = []
}

// map用
const routeFigures = [];
// アイコンの設定を事前定義
const iconInfo = new mapscript.value.GLMarkerIconInfo({
    size: new mapscript.value.Size(46, 30),
    icon: "/images/pin.png",
});
// アイコンの設定を事前定義
const iconInfo2 = new mapscript.value.GLMarkerIconInfo({
    size: new mapscript.value.Size(46, 30),
    icon: "/images/pin2.png",
});
// 通信結果に基づいて地図にアイコンを置く
const addMarkers = (items) => {
    console.log(`hoge`)

    // 既存のマーカーを除去
    map.removeGLMarkers(markers);
    markers.length = 0;

    items.forEach((spot) => {
        // 検索結果各々に対してマーカーを定義
        const glMarker = new mapscript.object.GLMarker({
            position: new mapscript.value.LatLng(spot.coord.lat, spot.coord.lon), // 表示する緯度経度
            info: iconInfo,
        });
        // 変数markers（配列）に追加
        markers.push(glMarker);
    });
    // 変数markersの内容を地図に反映
    map.addGLMarkers(markers);
};

const addStartMarkers = (location) => {
    map.removeGLMarkers([startMarker]);

    // 検索結果各々に対してマーカーを定義
    const glMarker = new mapscript.object.GLMarker({
        position: new mapscript.value.LatLng(location.lat, location.lon), // 表示する緯度経度
        info: iconInfo2,
    });
    startMarker = glMarker

    // 変数markersの内容を地図に反映
    map.addGLMarkers([startMarker]);
};



// このvueファイル内で使える変数を宣言
let map = null;
// DOMを操作する処理を画面を開いた直後に実行させたいときは、onMounted()の中に書く
onMounted(() => {
    // 地図を対象のDOM要素にセットする
    map = new mapscript.Map("intern01", {
        target: "#map", // 対象のDOM要素またはセレクタを指定してください
        center: new mapscript.value.LatLng(35.667207, 139.715021), // 地図を表示する際中心となる緯度経度を指定してください
        zoomLevel: 12, // ズームレベルを指定してください
    });


    onUnmounted(() => {
        map?.destroy()
    });
});

const hoge = async (startGoal, via) => {
    const data = await request('route_car', {
        start: `${startGoal}`,
        via: via,
        goal: `${startGoal}`,
        format: "geojson",
        shape: true,
        via_type: "via_optimal",
    })

    // 既存のルート線を除去
    routeFigures.forEach((x) => { map.removeGeoJsonFigure(x) });
    // 地図にルート線を追加するときの書き方です（詳しくは地図仕様書を参照）
    const routeFigure = new mapscript.value.GeoJsonFigureCondition(data.items[0].shapes);
    map.addGeoJsonFigure(routeFigure);
    // 変数routeFigures（配列）に追加（削除で使うため）
    routeFigures.push(routeFigure);

    return data.items[0].summary.move.time;
}

//drowRoute
const drawRoute = async (startGoal, via) => {
    console.log("hogehoge")
    try {
        console.log(data.value.length)
        console.log(data.value.length)

        for (let i = 2; i < data.value.length; i + 3) {
            console.log(data.value.length)
            const targetVia = data.value.slice(i - 2, i + 1)
            console.log(targetVia)
            const vias = []
            for (const item of targetVia) {
                vias.push(`{"lat":${item.coord.lat},"lon":${item.coord.lon}}`)
            }
            console.log(vias[0])

            const time = await hoge(startGoal, `[${vias.toString()}]`)
            // ユーザーが入力した時間との絶対差を計算
            const difference = Math.abs(time - userMinutes);

            if (difference < smallestDifference) {

            }

            // 一番差が小さいルートを保存
            if (difference < smallestDifference) {
                smallestDifference = difference;
                closestRoute = { via, time };
            }
            data.value = targetVia
            break
        }

    } catch (error) {
        console.log(error);
        alert("エラーが発生しました");
        return;
    }
};

const getCurrentLocation = () => {
    if ("geolocation" in navigator) {
        navigator.geolocation.getCurrentPosition(
            position => {
                const { latitude, longitude } = position.coords;
                startLocation.value = { lat: latitude, lon: longitude };
                word.value = `${startLocation.value.lat},${startLocation.value.lon}`
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

// ユーザーが入力したminutesと最も近いルートを検索する関数
const findClosestRoute = async (startGoal, vias, userMinutes) => {
    let closestRoute = null;
    let smallestDifference = Infinity;

    // 各経由地に対してAPIを叩く
    for (const via of vias) {
        try {
            const time = await drawRoute(startGoal, vias);

            // ユーザーが入力した時間との絶対差を計算
            const difference = Math.abs(time - userMinutes);

            // 一番差が小さいルートを保存
            if (difference < smallestDifference) {
                smallestDifference = difference;
                closestRoute = { via, time };
            }
        } catch (error) {
            console.error(`経由地 ${via} のルート取得に失敗しました:`, error);
        }
    }

    return closestRoute;
}

</script>

<style>
body {
    background: linear-gradient(to bottom, #44337A, #322659, #44337A);
    color: #FAF5FF;
    min-height: 100vh;
}
</style>