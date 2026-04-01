<template>
    <div class="scenic-monitoring-page">
        <div class="main-content">
            <!-- 左侧筛选栏 -->
            <div class="sidebar">
                <div class="filter-group">
                    <h3 class="filter-title">景区类型</h3>
                    <ul class="filter-list">
                        <li :class="{ active: selectedType === 'all' }" @click="selectedType = 'all'">
                            全部景区
                        </li>
                        <li :class="{ active: selectedType === 'world-heritage' }" @click="selectedType = 'world-heritage'">
                            世界遗产
                        </li>
                        <li :class="{ active: selectedType === '5A' }" @click="selectedType = '5A'">
                            5A 级景区
                        </li>
                        <li :class="{ active: selectedType === '4A' }" @click="selectedType = '4A'">
                            4A 级景区
                        </li>
                    </ul>
                </div>

                <div class="filter-group">
                    <h3 class="filter-title">监测类型</h3>
                    <ul class="filter-list">
                        <li :class="{ active: selectedMonitor === 'all' }" @click="selectedMonitor = 'all'">
                            全部监测
                        </li>
                        <li :class="{ active: selectedMonitor === 'weather' }" @click="selectedMonitor = 'weather'">
                            天气监测
                        </li>
                        <li :class="{ active: selectedMonitor === 'crowd' }" @click="selectedMonitor = 'crowd'">
                            人流监测
                        </li>
                        <li :class="{ active: selectedMonitor === 'environment' }" @click="selectedMonitor = 'environment'">
                            环境监测
                        </li>
                    </ul>
                </div>

                <div class="filter-group">
                    <h3 class="filter-title">区域分布</h3>
                    <ul class="filter-list">
                        <li :class="{ active: selectedRegion === 'all' }" @click="selectedRegion = 'all'">
                            全部区域
                        </li>
                        <li :class="{ active: selectedRegion === 'hexi' }" @click="selectedRegion = 'hexi'">
                            河西走廊
                        </li>
                        <li :class="{ active: selectedRegion === 'longzhong' }" @click="selectedRegion = 'longzhong'">
                            陇中地区
                        </li>
                        <li :class="{ active: selectedRegion === 'longdongnan' }" @click="selectedRegion = 'longdongnan'">
                            陇东南地区
                        </li>
                    </ul>
                </div>
            </div>

            <!-- 右侧内容区域 -->
            <div class="content-area">
                <!-- 监测概览 -->
                <div class="overview-section">
                    <h3 class="section-title">甘肃省重点景区实时监测</h3>
                    <div class="overview-stats">
                        <div class="stat-card">
                            <div class="stat-icon">🏞️</div>
                            <div class="stat-info">
                                <div class="stat-value">{{ totalScenicSpots }}</div>
                                <div class="stat-label">监测景区总数</div>
                            </div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-icon">👥</div>
                            <div class="stat-info">
                                <div class="stat-value">{{ totalVisitors.toLocaleString() }}</div>
                                <div class="stat-label">今日累计游客</div>
                            </div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-icon">⚠️</div>
                            <div class="stat-info">
                                <div class="stat-value">{{ warningCount }}</div>
                                <div class="stat-label">预警景区数量</div>
                            </div>
                        </div>
                        <div class="stat-card">
                            <div class="stat-icon">🌤️</div>
                            <div class="stat-info">
                                <div class="stat-value">{{ goodWeatherCount }}</div>
                                <div class="stat-label">适宜游览景区</div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- 景区监测卡片 -->
                <div class="monitoring-section">
                    <h3 class="section-title">景区实时监测数据</h3>
                    <div class="monitoring-grid">
                        <div class="monitoring-card" v-for="spot in filteredSpots" :key="spot.id">
                            <div class="card-header">
                                <div class="spot-info">
                                    <h4>{{ spot.name }}</h4>
                                    <div class="spot-tags">
                                        <span class="tag level" :class="spot.levelClass">{{ spot.level }}</span>
                                        <span class="tag type">{{ spot.typeName }}</span>
                                    </div>
                                </div>
                                <div class="status-badge" :class="spot.statusClass">
                                    {{ spot.statusText }}
                                </div>
                            </div>
                            
                            <div class="card-body">
                                <!-- 天气监测 -->
                                <div class="monitor-item" v-if="showWeather">
                                    <div class="monitor-icon">🌤️</div>
                                    <div class="monitor-content">
                                        <div class="monitor-label">实时天气</div>
                                        <div class="monitor-value">
                                            <span>{{ spot.weather.condition }}</span>
                                            <span class="temperature">{{ spot.weather.temperature }}°C</span>
                                        </div>
                                        <div class="monitor-details">
                                            <span>湿度：{{ spot.weather.humidity }}%</span>
                                            <span>风力：{{ spot.weather.wind }}</span>
                                            <span>空气质量：{{ spot.weather.aqi }}</span>
                                        </div>
                                    </div>
                                </div>

                                <!-- 人流监测 -->
                                <div class="monitor-item" v-if="showCrowd">
                                    <div class="monitor-icon">👥</div>
                                    <div class="monitor-content">
                                        <div class="monitor-label">今日客流</div>
                                        <div class="monitor-value">
                                            <span>{{ spot.crowd.today }}人次</span>
                                            <span :class="crowdTrendClass(spot.crowd.trend)">
                                                {{ spot.crowd.trend }}
                                            </span>
                                        </div>
                                        <div class="monitor-details">
                                            <span>承载量：{{ spot.crowd.capacity }}人次</span>
                                            <span>饱和度：{{ calculateSaturation(spot.crowd) }}%</span>
                                        </div>
                                        <div class="crowd-bar">
                                            <div class="crowd-fill" :style="{ width: calculateSaturation(spot.crowd) + '%', background: getCrowdColor(spot.crowd) }"></div>
                                        </div>
                                    </div>
                                </div>

                                <!-- 环境监测 -->
                                <div class="monitor-item" v-if="showEnvironment">
                                    <div class="monitor-icon">🌿</div>
                                    <div class="monitor-content">
                                        <div class="monitor-label">环境质量</div>
                                        <div class="monitor-value">
                                            <span>{{ spot.environment.quality }}</span>
                                        </div>
                                        <div class="monitor-details">
                                            <span>负氧离子：{{ spot.environment.oxygen }}/cm³</span>
                                            <span>噪声：{{ spot.environment.noise }}dB</span>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <div class="card-footer">
                                <span class="update-time">更新时间：{{ spot.updateTime }}</span>
                                <button class="detail-btn" @click="viewDetail(spot)">查看详情</button>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- 人流趋势图表 -->
                <div class="trend-section">
                    <h3 class="section-title">重点景区人流趋势分析</h3>
                    <div class="trend-container">
                        <div class="trend-chart">
                            <div class="chart-header">
                                <h4>莫高窟 - 近 7 日客流趋势</h4>
                            </div>
                            <div class="chart-body">
                                <div class="bar-chart">
                                    <div class="bar-item" v-for="(day, index) in weekData" :key="index">
                                        <div class="bar-wrapper">
                                            <div class="bar-fill" :style="{ height: day.percent + '%' }"></div>
                                        </div>
                                        <div class="bar-label">{{ day.date }}</div>
                                        <div class="bar-value">{{ day.visitors }}人</div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="trend-chart">
                            <div class="chart-header">
                                <h4>麦积山 - 近 7 日客流趋势</h4>
                            </div>
                            <div class="chart-body">
                                <div class="bar-chart">
                                    <div class="bar-item" v-for="(day, index) in weekData2" :key="index">
                                        <div class="bar-wrapper">
                                            <div class="bar-fill" :style="{ height: day.percent + '%' }"></div>
                                        </div>
                                        <div class="bar-label">{{ day.date }}</div>
                                        <div class="bar-value">{{ day.visitors }}人</div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- 预警信息 -->
                <div class="warning-section">
                    <h3 class="section-title">景区预警信息</h3>
                    <div class="warning-list">
                        <div class="warning-item" v-for="warning in warnings" :key="warning.id" :class="warning.level">
                            <div class="warning-icon">{{ warning.icon }}</div>
                            <div class="warning-content">
                                <div class="warning-title">
                                    <span class="warning-badge" :class="warning.level">{{ warning.levelText }}</span>
                                    <span class="warning-spot">{{ warning.spotName }}</span>
                                </div>
                                <div class="warning-desc">{{ warning.description }}</div>
                                <div class="warning-time">{{ warning.time }}</div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// 筛选条件
const selectedType = ref('all')
const selectedMonitor = ref('all')
const selectedRegion = ref('all')

// 监测数据
const totalScenicSpots = 12
const totalVisitors = 45680
const warningCount = 2
const goodWeatherCount = 9

// 景区监测数据
const scenicSpots = ref([
    {
        id: 1,
        name: '莫高窟',
        level: '世界遗产',
        levelClass: 'world-heritage',
        typeName: '石窟艺术',
        region: 'hexi',
        status: 'normal',
        statusClass: 'normal',
        statusText: '正常',
        weather: {
            condition: '晴',
            temperature: 28,
            humidity: 45,
            wind: '西北风 2 级',
            aqi: '优'
        },
        crowd: {
            today: 8520,
            yesterday: 7800,
            trend: '↑9.2%',
            capacity: 12000
        },
        environment: {
            quality: '优',
            oxygen: 2800,
            noise: 42
        },
        updateTime: '2026-03-23 10:30'
    },
    {
        id: 2,
        name: '鸣沙山月牙泉',
        level: '5A 级',
        levelClass: '5a',
        typeName: '沙漠奇观',
        region: 'hexi',
        status: 'crowded',
        statusClass: 'crowded',
        statusText: '拥挤',
        weather: {
            condition: '多云',
            temperature: 30,
            humidity: 35,
            wind: '西北风 3 级',
            aqi: '良'
        },
        crowd: {
            today: 15200,
            yesterday: 13500,
            trend: '↑12.6%',
            capacity: 15000
        },
        environment: {
            quality: '良',
            oxygen: 2200,
            noise: 58
        },
        updateTime: '2026-03-23 10:30'
    },
    {
        id: 3,
        name: '麦积山石窟',
        level: '5A 级',
        levelClass: '5a',
        typeName: '石窟艺术',
        region: 'longdongnan',
        status: 'normal',
        statusClass: 'normal',
        statusText: '正常',
        weather: {
            condition: '小雨',
            temperature: 18,
            humidity: 78,
            wind: '东南风 2 级',
            aqi: '优'
        },
        crowd: {
            today: 3200,
            yesterday: 4500,
            trend: '↓28.9%',
            capacity: 8000
        },
        environment: {
            quality: '优',
            oxygen: 3500,
            noise: 38
        },
        updateTime: '2026-03-23 10:30'
    },
    {
        id: 4,
        name: '嘉峪关关城',
        level: '5A 级',
        levelClass: '5a',
        typeName: '长城遗址',
        region: 'hexi',
        status: 'normal',
        statusClass: 'normal',
        statusText: '正常',
        weather: {
            condition: '晴',
            temperature: 26,
            humidity: 40,
            wind: '西北风 3 级',
            aqi: '优'
        },
        crowd: {
            today: 5600,
            yesterday: 5200,
            trend: '↑7.7%',
            capacity: 10000
        },
        environment: {
            quality: '优',
            oxygen: 2600,
            noise: 45
        },
        updateTime: '2026-03-23 10:30'
    },
    {
        id: 5,
        name: '崆峒山',
        level: '5A 级',
        levelClass: '5a',
        typeName: '道教名山',
        region: 'longzhong',
        status: 'normal',
        statusClass: 'normal',
        statusText: '正常',
        weather: {
            condition: '阴',
            temperature: 20,
            humidity: 65,
            wind: '东北风 2 级',
            aqi: '良'
        },
        crowd: {
            today: 4800,
            yesterday: 4600,
            trend: '↑4.3%',
            capacity: 9000
        },
        environment: {
            quality: '优',
            oxygen: 3200,
            noise: 40
        },
        updateTime: '2026-03-23 10:30'
    },
    {
        id: 6,
        name: '张掖丹霞',
        level: '5A 级',
        levelClass: '5a',
        typeName: '地质公园',
        region: 'hexi',
        status: 'warning',
        statusClass: 'warning',
        statusText: '限流',
        weather: {
            condition: '晴',
            temperature: 32,
            humidity: 30,
            wind: '西北风 4 级',
            aqi: '良'
        },
        crowd: {
            today: 9800,
            yesterday: 8500,
            trend: '↑15.3%',
            capacity: 10000
        },
        environment: {
            quality: '良',
            oxygen: 2400,
            noise: 52
        },
        updateTime: '2026-03-23 10:30'
    },
    {
        id: 7,
        name: '炳灵寺石窟',
        level: '4A 级',
        levelClass: '4a',
        typeName: '石窟艺术',
        region: 'longzhong',
        status: 'normal',
        statusClass: 'normal',
        statusText: '正常',
        weather: {
            condition: '多云',
            temperature: 22,
            humidity: 55,
            wind: '西南风 2 级',
            aqi: '优'
        },
        crowd: {
            today: 1800,
            yesterday: 1650,
            trend: '↑9.1%',
            capacity: 5000
        },
        environment: {
            quality: '优',
            oxygen: 3000,
            noise: 35
        },
        updateTime: '2026-03-23 10:30'
    },
    {
        id: 8,
        name: '拉卜楞寺',
        level: '4A 级',
        levelClass: '4a',
        typeName: '藏传佛教',
        region: 'longzhong',
        status: 'normal',
        statusClass: 'normal',
        statusText: '正常',
        weather: {
            condition: '晴',
            temperature: 15,
            humidity: 50,
            wind: '西北风 3 级',
            aqi: '优'
        },
        crowd: {
            today: 2600,
            yesterday: 2400,
            trend: '↑8.3%',
            capacity: 6000
        },
        environment: {
            quality: '优',
            oxygen: 2900,
            noise: 38
        },
        updateTime: '2026-03-23 10:30'
    }
])

// 预警信息
const warnings = ref([
    {
        id: 1,
        icon: '⚠️',
        level: 'yellow',
        levelText: '黄色预警',
        spotName: '张掖丹霞国家地质公园',
        description: '当前游客量接近最大承载量，建议错峰游览或选择其他景区。',
        time: '2026-03-23 09:00'
    },
    {
        id: 2,
        icon: '🌧️',
        level: 'blue',
        levelText: '蓝色预警',
        spotName: '麦积山石窟',
        description: '预计今日下午有小雨，路面湿滑，请注意游览安全。',
        time: '2026-03-23 08:30'
    }
])

// 周客流数据
const weekData = ref([
    { date: '03-17', visitors: 7200, percent: 60 },
    { date: '03-18', visitors: 6800, percent: 57 },
    { date: '03-19', visitors: 7500, percent: 63 },
    { date: '03-20', visitors: 8100, percent: 68 },
    { date: '03-21', visitors: 9200, percent: 77 },
    { date: '03-22', visitors: 7800, percent: 65 },
    { date: '03-23', visitors: 8520, percent: 71 }
])

const weekData2 = ref([
    { date: '03-17', visitors: 3800, percent: 54 },
    { date: '03-18', visitors: 3500, percent: 50 },
    { date: '03-19', visitors: 4200, percent: 60 },
    { date: '03-20', visitors: 4800, percent: 69 },
    { date: '03-21', visitors: 5500, percent: 79 },
    { date: '03-22', visitors: 4500, percent: 64 },
    { date: '03-23', visitors: 3200, percent: 46 }
])

// 计算属性
const showWeather = computed(() => selectedMonitor.value === 'all' || selectedMonitor.value === 'weather')
const showCrowd = computed(() => selectedMonitor.value === 'all' || selectedMonitor.value === 'crowd')
const showEnvironment = computed(() => selectedMonitor.value === 'all' || selectedMonitor.value === 'environment')

const filteredSpots = computed(() => {
    return scenicSpots.value.filter(spot => {
        if (selectedType.value !== 'all') {
            const typeMap = {
                'world-heritage': '世界遗产',
                '5A': '5A 级',
                '4A': '4A 级'
            }
            if (spot.level !== typeMap[selectedType.value]) return false
        }
        
        if (selectedRegion.value !== 'all') {
            if (spot.region !== selectedRegion.value) return false
        }
        
        return true
    })
})

// 方法
const calculateSaturation = (crowd) => {
    return Math.round((crowd.today / crowd.capacity) * 100)
}

const getCrowdColor = (crowd) => {
    const saturation = calculateSaturation(crowd)
    if (saturation < 50) return '#4caf50'
    if (saturation < 80) return '#ff9800'
    return '#f44336'
}

const crowdTrendClass = (trend) => {
    return trend.includes('↑') ? 'trend-up' : 'trend-down'
}

const viewDetail = (spot) => {
    console.log('查看详情:', spot)
    alert(`查看 ${spot.name} 的详细监测数据`)
}
</script>

<style scoped>
.scenic-monitoring-page {
    min-height: 100vh;
    background: #f5f7fa;
}

.main-content {
    display: flex;
    max-width: 1600px;
    margin: 0 auto;
    padding: 30px 20px;
    gap: 20px;
}

/* 左侧边栏 */
.sidebar {
    width: 240px;
    flex-shrink: 0;
    background: #fff;
    border-radius: 12px;
    padding: 20px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
    height: fit-content;
}

.filter-group {
    margin-bottom: 25px;
}

.filter-group:last-child {
    margin-bottom: 0;
}

.filter-title {
    font-size: 16px;
    font-weight: 600;
    color: #1a237e;
    margin-bottom: 12px;
    padding-bottom: 10px;
    border-bottom: 2px solid #e0e0e0;
}

.filter-list {
    list-style: none;
    padding: 0;
    margin: 0;
}

.filter-list li {
    padding: 10px 15px;
    margin-bottom: 5px;
    border-radius: 6px;
    cursor: pointer;
    transition: all 0.3s;
    font-size: 14px;
    color: #333;
}

.filter-list li:hover {
    background: #e3f2fd;
    color: #005da6;
}

.filter-list li.active {
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
    font-weight: 500;
}

/* 右侧内容区 */
.content-area {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 25px;
}

/* 通用标题样式 */
.section-title {
    font-size: 20px;
    font-weight: 600;
    color: #1a237e;
    margin-bottom: 20px;
    padding-bottom: 10px;
    border-bottom: 2px solid #e0e0e0;
}

/* 概览统计 */
.overview-section {
    background: #fff;
    border-radius: 12px;
    padding: 25px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
}

.overview-stats {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 20px;
}

.stat-card {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 20px;
    background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
    border-radius: 10px;
    transition: transform 0.3s;
}

.stat-card:hover {
    transform: translateY(-3px);
}

.stat-icon {
    font-size: 40px;
}

.stat-info {
    flex: 1;
}

.stat-value {
    font-size: 28px;
    font-weight: 700;
    color: #1a237e;
    margin-bottom: 5px;
}

.stat-label {
    font-size: 13px;
    color: #666;
}

/* 监测卡片 */
.monitoring-section {
    background: #fff;
    border-radius: 12px;
    padding: 25px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
}

.monitoring-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(380px, 1fr));
    gap: 20px;
}

.monitoring-card {
    background: #fff;
    border-radius: 12px;
    border: 1px solid #e0e0e0;
    overflow: hidden;
    transition: all 0.3s;
}

.monitoring-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.12);
    border-color: #4fc3f7;
}

.card-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    padding: 20px;
    background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
    border-bottom: 1px solid #e0e0e0;
}

.spot-info h4 {
    font-size: 18px;
    font-weight: 600;
    color: #1a237e;
    margin-bottom: 8px;
}

.spot-tags {
    display: flex;
    gap: 8px;
}

.tag {
    padding: 4px 10px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 500;
}

.tag.level {
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
}

.tag.level.world-heritage {
    background: linear-gradient(135deg, #ffd700 0%, #ff8c00 100%);
}

.tag.level.5a {
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
}

.tag.level.4a {
    background: linear-gradient(135deg, #28a745 0%, #20c997 100%);
}

.tag.type {
    background: #e9ecef;
    color: #495057;
}

.status-badge {
    padding: 6px 14px;
    border-radius: 20px;
    font-size: 13px;
    font-weight: 500;
}

.status-badge.normal {
    background: #d4edda;
    color: #155724;
}

.status-badge.crowded {
    background: #fff3cd;
    color: #856404;
}

.status-badge.warning {
    background: #f8d7da;
    color: #721c24;
}

.card-body {
    padding: 20px;
}

.monitor-item {
    display: flex;
    gap: 15px;
    padding: 15px;
    margin-bottom: 15px;
    background: #f8f9fa;
    border-radius: 8px;
    transition: background 0.3s;
}

.monitor-item:last-child {
    margin-bottom: 0;
}

.monitor-item:hover {
    background: #e9ecef;
}

.monitor-icon {
    font-size: 32px;
    flex-shrink: 0;
}

.monitor-content {
    flex: 1;
}

.monitor-label {
    font-size: 13px;
    color: #666;
    margin-bottom: 8px;
    font-weight: 500;
}

.monitor-value {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 8px;
}

.monitor-value span {
    font-size: 16px;
    font-weight: 600;
    color: #1a237e;
}

.temperature {
    font-size: 20px !important;
    font-weight: 700 !important;
}

.trend-up {
    color: #dc3545 !important;
}

.trend-down {
    color: #28a745 !important;
}

.monitor-details {
    display: flex;
    gap: 15px;
    font-size: 12px;
    color: #666;
    margin-bottom: 8px;
}

.crowd-bar {
    height: 6px;
    background: #e0e0e0;
    border-radius: 3px;
    overflow: hidden;
}

.crowd-fill {
    height: 100%;
    border-radius: 3px;
    transition: width 0.3s, background 0.3s;
}

.card-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px 20px;
    background: #f8f9fa;
    border-top: 1px solid #e0e0e0;
}

.update-time {
    font-size: 12px;
    color: #999;
}

.detail-btn {
    padding: 6px 16px;
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
    border: none;
    border-radius: 6px;
    font-size: 13px;
    cursor: pointer;
    transition: all 0.3s;
}

.detail-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 93, 166, 0.3);
}

/* 趋势图表 */
.trend-section {
    background: #fff;
    border-radius: 12px;
    padding: 25px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
}

.trend-container {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 25px;
}

.trend-chart {
    background: #f8f9fa;
    border-radius: 10px;
    padding: 20px;
}

.chart-header h4 {
    font-size: 16px;
    font-weight: 600;
    color: #1a237e;
    margin-bottom: 15px;
}

.chart-body {
    padding: 10px 0;
}

.bar-chart {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    height: 200px;
    padding: 10px;
    gap: 10px;
}

.bar-item {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
}

.bar-wrapper {
    width: 100%;
    height: 160px;
    background: #e0e0e0;
    border-radius: 4px;
    overflow: hidden;
    display: flex;
    align-items: flex-end;
}

.bar-fill {
    width: 100%;
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    border-radius: 4px 4px 0 0;
    transition: height 0.3s;
}

.bar-label {
    font-size: 12px;
    color: #666;
}

.bar-value {
    font-size: 11px;
    color: #1a237e;
    font-weight: 600;
}

/* 预警信息 */
.warning-section {
    background: #fff;
    border-radius: 12px;
    padding: 25px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
}

.warning-list {
    display: flex;
    flex-direction: column;
    gap: 15px;
}

.warning-item {
    display: flex;
    gap: 15px;
    padding: 20px;
    border-radius: 10px;
    background: #f8f9fa;
    border-left: 4px solid #e0e0e0;
    transition: all 0.3s;
}

.warning-item:hover {
    transform: translateX(5px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.warning-item.yellow {
    border-left-color: #ffc107;
    background: linear-gradient(135deg, #fff8e1 0%, #fff3e0 100%);
}

.warning-item.blue {
    border-left-color: #2196f3;
    background: linear-gradient(135deg, #e3f2fd 0%, #bbdefb 100%);
}

.warning-icon {
    font-size: 32px;
    flex-shrink: 0;
}

.warning-content {
    flex: 1;
}

.warning-title {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 8px;
}

.warning-badge {
    padding: 4px 10px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 500;
}

.warning-badge.yellow {
    background: #ffc107;
    color: #fff;
}

.warning-badge.blue {
    background: #2196f3;
    color: #fff;
}

.warning-spot {
    font-size: 16px;
    font-weight: 600;
    color: #1a237e;
}

.warning-desc {
    font-size: 14px;
    color: #666;
    line-height: 1.6;
    margin-bottom: 8px;
}

.warning-time {
    font-size: 12px;
    color: #999;
}

/* 响应式设计 */
@media (max-width: 1200px) {
    .overview-stats {
        grid-template-columns: repeat(2, 1fr);
    }
    
    .monitoring-grid {
        grid-template-columns: 1fr;
    }
    
    .trend-container {
        grid-template-columns: 1fr;
    }
}

@media (max-width: 768px) {
    .main-content {
        flex-direction: column;
    }
    
    .sidebar {
        width: 100%;
    }
    
    .overview-stats {
        grid-template-columns: repeat(2, 1fr);
    }
    
    .stat-card {
        padding: 15px;
    }
    
    .stat-icon {
        font-size: 32px;
    }
    
    .stat-value {
        font-size: 24px;
    }
}
</style>
