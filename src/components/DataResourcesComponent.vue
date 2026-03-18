<template>
    <section id="data-source" class="data-resources">
        <div class="container">
            <div class="section-title">
                <h2>数据资源</h2>
                <a href="javascript:;" class="more-link">更多 &gt;</a>
            </div>
            <div class="resources-grid">
                <div class="resource-card" v-for="(resource, index) in dataResources" :key="index">
                    <div class="resource-icon">
                        <i :class="resource.icon"></i>
                    </div>
                    <h3>{{ resource.title }}</h3>
                    <p>{{ resource.description }}</p>
                    <a v-if="resource.href && resource.href !== 'javascript:;'" @click="handleNavigation(resource.href)" class="resource-link">进入 &gt;</a>
                    <a v-else :href="resource.href" class="resource-link">进入 &gt;</a>
                </div>
            </div>
        </div>
    </section>
</template>

<script setup>
const handleNavigation = (path) => {
    window.location.href = path
}

const dataResources = [
    { title: '旅游资源', description: '甘肃地图 + 地标图标', icon: 'fas fa-map', href: '/tourism' },
    { title: '生态监测', description: '动态气象云图图标', icon: 'fas fa-cloud-sun', href: '/eco-monitoring' },
    { title: '非遗资源', description: '剪纸/皮影动态图标', icon: 'fas fa-film', href: '/ich' },
    { title: '专题数据', description: '多图层叠加图标', icon: 'fas fa-landmark', href: 'javascript:;' },
    { title: '文创数据', description: '三维文物旋转图标', icon: 'fas fa-landmark', href: '/cultural-creativity' },
    { title: '特色数据', description: '飞天舞蹈剪影图标', icon: 'fas fa-landmark', href: '/dunhuang-dance' },
]
</script>

<style scoped>
.data-resources {
    padding: 60px 0;
    background: linear-gradient(135deg, #e3f2fd 0%, #bbdefb 100%);
}

.resources-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
}

.resource-card {
    background: #fff;
    padding: 30px 20px;
    border-radius: 12px;
    text-align: center;
    transition: all 0.3s;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
}

.resource-card:hover {
    transform: translateY(-8px);
    box-shadow: 0 12px 30px rgba(0,0,0,0.12);
}

.resource-icon {
    width: 60px;
    height: 60px;
    margin: 0 auto 20px;
    border-radius: 50%;
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    display: flex;
    align-items: center;
    justify-content: center;
    color: #fff;
    font-size: 28px;
}

.resource-card h3 {
    font-size: 18px;
    color: #1a237e;
    margin-bottom: 10px;
}

.resource-card p {
    font-size: 14px;
    color: #666;
    margin-bottom: 15px;
}

.resource-link {
    color: #005da6;
    font-weight: 500;
}

@media (max-width: 768px) {
    .resources-grid {
        grid-template-columns: repeat(2, 1fr);
    }
}
</style>
