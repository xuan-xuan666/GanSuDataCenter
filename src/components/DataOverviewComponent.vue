<template>
    <section class="data-overview">
        <div class="container">
            <div class="overview-title">
                <h2>核心数据总览</h2>
            </div>
            <div class="overview-grid">
                <div class="overview-item" v-for="(item, index) in overviewData" :key="index">
                    <div class="overview-label">{{ item.label }}</div>
                    <div class="overview-number">{{ item.number }}</div>
                    
                </div>
            </div>
            <div class="overview-details">
                <div class="detail-panel">
                    <div class="panel-header">
                        <h3>今日各景区客流热力图</h3>
                        <span class="update-time">更新时间：{{ currentTime }}</span>
                    </div>
                    <div class="heatmap-container">
                        <div class="heatmap-grid">
                            <div 
                                class="heatmap-cell" 
                                v-for="(cell, index) in heatmapData" 
                                :key="index"
                                :style="{ 
                                    backgroundColor: getHeatmapColor(cell.value),
                                    opacity: cell.opacity
                                }"
                                :title="`${cell.name}: ${cell.value}人`">
                                <span class="cell-label">{{ cell.name }}</span>
                                <span class="cell-value">{{ cell.value }}人</span>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="detail-panel">
                    <div class="panel-header">
                        <h3>当前天气监测预警</h3>
                    </div>
                    <div class="weather-info-wrapper">
                        <div class="weather-info">
                            <div class="weather-item" v-for="(weather, index) in weatherData" :key="index">
                                <div class="weather-icon" :class="getWeatherClass(weather.type)"></div>
                                <div class="weather-content">
                                    <div class="weather-location">{{ weather.location }}</div>
                                    <div class="weather-status">{{ weather.status }}</div>
                                    <div class="weather-temp">{{ weather.temp }}</div>
                                    <div class="weather-warning" v-if="weather.warning">
                                        <span class="warning-icon">⚠️</span>
                                        {{ weather.warning }}
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="detail-panel">
                    <div class="panel-header">
                        <h3>最新入库数据提示</h3>
                    </div>
                    <div class="latest-updates">
                        <div class="update-item" v-for="(update, index) in latestUpdates" :key="index">
                            <span class="update-time-badge">{{ update.time }}</span>
                            <span class="update-content">{{ update.content }}</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const overviewData = [
    { number: '128', label: '已收录旅游资源：' },
    { number: '45', label: '实时监测景区：' },
    { number: '86', label: '非遗数字化项目：' },
    { number: '3500+', label: '文创产品库：' }
]

const heatmapData = ref([
    { name: '敦煌莫高窟', value: 2340, opacity: 1 },
    { name: '嘉峪关', value: 1850, opacity: 0.9 },
    { name: '张掖丹霞', value: 1520, opacity: 0.8 },
    { name: '鸣沙山', value: 1280, opacity: 0.7 },
    { name: '月牙泉', value: 980, opacity: 0.6 },
    { name: '麦积山', value: 850, opacity: 0.5 },
    { name: '崆峒山', value: 720, opacity: 0.4 },
    { name: '炳灵寺', value: 540, opacity: 0.3 },
    { name: '拉卜楞寺', value: 480, opacity: 0.2 }
])

const weatherData = ref([
    { location: '兰州市', type: 'sunny', status: '晴', temp: '18℃', warning: null },
    { location: '敦煌市', type: 'cloudy', status: '多云', temp: '15℃', warning: '沙尘预警' },
    { location: '张掖市', type: 'rainy', status: '小雨', temp: '12℃', warning: '道路湿滑' },
    { location: '酒泉市', type: 'windy', status: '大风', temp: '10℃', warning: '风力6级' }
])

const latestUpdates = ref([
    { time: '10:23', content: '新增景区实时监测数据：嘉峪关景区' },
    { time: '09:45', content: '更新非遗数字化项目：敦煌壁画数字化' },
    { time: '08:30', content: '新增文创产品入库：丝绸之路系列' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
    { time: '07:15', content: '更新游客行为分析报告' },
])

const currentTime = ref('')

const getHeatmapColor = (value) => {
    if (value >= 2000) return '#dc2626'
    if (value >= 1500) return '#f97316'
    if (value >= 1000) return '#eab308'
    if (value >= 500) return '#84cc16'
    return '#22c55e'
}

const startHeatmapAnimation = () => {
    setInterval(() => {
        heatmapData.value = heatmapData.value.map(cell => {
            const change = Math.floor(Math.random() * 200) - 100
            let newValue = cell.value + change
            if (newValue < 0) newValue = 0
            if (newValue > 3000) newValue = 3000
            return {
                ...cell,
                value: newValue,
                opacity: newValue > 500 ? 1 : 0.3
            }
        })
    }, 3000)
}

const getWeatherClass = (type) => {
    return `weather-icon-${type}`
}

const startWeatherUpdates = () => {
    setInterval(() => {
        weatherData.value = weatherData.value.map(weather => {
            const tempChange = Math.floor(Math.random() * 5) - 2
            return {
                ...weather,
                temp: `${parseInt(weather.temp) + tempChange}℃`
            }
        })
    }, 60000)
}

const startLatestUpdates = () => {
    setInterval(() => {
        const newUpdate = {
            time: new Date().toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit' }),
            content: `新增数据更新：${['景区监测数据', '游客行为数据', '环境监测数据', '文化遗产数据'][Math.floor(Math.random() * 4)]}`
        }
        latestUpdates.value.unshift(newUpdate)
        if (latestUpdates.value.length > 4) {
            latestUpdates.value.pop()
        }
    }, 30000)
}

const updateCurrentTime = () => {
    currentTime.value = new Date().toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit', second: '2-digit' })
    setInterval(() => {
        currentTime.value = new Date().toLocaleTimeString('zh-CN', { hour: '2-digit', minute: '2-digit', second: '2-digit' })
    }, 1000)
}

onMounted(() => {
    startHeatmapAnimation()
    startWeatherUpdates()
    startLatestUpdates()
    updateCurrentTime()
})
</script>

<style scoped>
.data-overview {
    padding: 40px 0;
    background: linear-gradient(135deg, #fff 0%, #f5f7fa 100%);
}

.overview-title {
    text-align: center;
    margin-bottom: 30px;
}

.overview-title h2 {
    font-size: 28px;
    color: #1a237e;
    font-weight: 600;
}

.overview-grid {
    display: flex;
    justify-content: space-around;
    gap: 20px;
    margin-bottom: 40px;
    flex-wrap: wrap;
}

.overview-item {
    background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
    color: #fff;
    padding: 25px 40px;
    border-radius: 10px;
    text-align: center;
    min-width: 200px;
    box-shadow: 0 4px 15px rgba(0,0,0,0.1);
    transition: transform 0.3s;
}

.overview-item:hover {
    transform: translateY(-5px);
}

.overview-number {
    font-size: 36px;
    font-weight: 600;
    margin-bottom: 10px;
    color: #4fc3f7;
}

.overview-label {
    font-size: 14px;
    color: #e3f2fd;
}

.overview-details {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 25px;
}

.detail-panel {
    background: #fff;
    border-radius: 10px;
    padding: 25px 30px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
    transition: all 0.3s;
}

.detail-panel:hover {
    box-shadow: 0 10px 25px rgba(0,0,0,0.15);
}

.panel-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
    padding-bottom: 15px;
    border-bottom: 2px solid #e0e0e0;
}

.panel-header h3 {
    font-size: 18px;
    color: #1a237e;
    font-weight: 600;
}

.update-time {
    font-size: 12px;
    color: #999;
}

.heatmap-container {
    min-height: 200px;
}

.heatmap-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
}

.heatmap-cell {
    background: #84cc16;
    color: #fff;
    padding: 15px;
    border-radius: 8px;
    text-align: center;
    position: relative;
    transition: all 0.3s;
    cursor: pointer;
}

.heatmap-cell:hover {
    transform: scale(1.05);
    z-index: 10;
}

.cell-label {
    display: block;
    font-size: 12px;
    margin-bottom: 5px;
}

.cell-value {
    font-size: 14px;
    font-weight: 600;
}

.weather-info-wrapper {
    max-height: 250px;
    overflow-y: auto;
}

.weather-info {
    display: flex;
    flex-direction: column;
    gap: 15px;
}

.weather-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 10px;
    background: #f9f9f9;
    border-radius: 8px;
    animation: slideIn 0.3s ease-out;
}

.weather-item:last-child {
    border-bottom: none;
}

.weather-icon {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
}

.weather-icon-sunny {
    background: #fff3e0;
    color: #ff9800;
}

.weather-icon-cloudy {
    background: #e0e0e0;
    color: #757575;
}

.weather-icon-rainy {
    background: #e3f2fd;
    color: #2196f3;
}

.weather-icon-windy {
    background: #e0f7fa;
    color: #00bcd4;
}

.weather-content {
    flex: 1;
}

.weather-location {
    font-size: 14px;
    font-weight: 600;
    color: #1a237e;
}

.weather-status {
    font-size: 13px;
    color: #666;
}

.weather-temp {
    font-size: 14px;
    font-weight: 600;
    color: #f97316;
}

.weather-warning {
    display: flex;
    align-items: center;
    gap: 5px;
    font-size: 12px;
    color: #dc2626;
    margin-top: 5px;
}

.warning-icon {
    font-size: 14px;
}

.latest-updates {
    max-height: 250px;
    overflow-y: auto;
}

.update-item {
    display: flex;
    gap: 10px;
    padding: 10px;
    border-bottom: 1px solid #eee;
    animation: slideIn 0.3s ease-out;
}

.update-item:last-child {
    border-bottom: none;
}

.update-time-badge {
    background: #1a237e;
    color: #fff;
    padding: 5px 10px;
    border-radius: 4px;
    font-size: 12px;
    flex-shrink: 0;
}

.update-content {
    font-size: 13px;
    color: #666;
    flex: 1;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

@media (max-width: 992px) {
    .overview-grid {
        flex-direction: column;
        align-items: center;
    }
    
    .overview-item {
        width: 100%;
        max-width: 300px;
    }
    
    .overview-details {
        grid-template-columns: 1fr;
    }
}

@media (max-width: 768px) {
    .overview-title h2 {
        font-size: 24px;
    }
    
    .overview-item {
        padding: 20px;
        min-width: 100%;
    }
    
    .overview-number {
        font-size: 28px;
    }
    
    .panel-header h3 {
        font-size: 16px;
    }
    
    .heatmap-grid {
        grid-template-columns: repeat(2, 1fr);
    }
}
</style>
