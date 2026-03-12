<template>
    <section class="data-resources">
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
                    <a :href="resource.href" class="resource-link">进入 &gt;</a>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
export default {
    name: 'DataResourcesComponent',
    data() {
        return {
            dataResources: [
                { title: '旅游资源数据', description: '包含全省各类旅游资源的详细信息和统计数据', icon: 'fas fa-map', href: 'javascript:;' },
                { title: '游客行为数据', description: '分析游客行为特征和旅游偏好数据', icon: 'fas fa-users', href: 'javascript:;' },
                { title: '环境监测数据', description: '实时环境监测和生态数据', icon: 'fas fa-tree', href: 'javascript:;' },
                { title: '文化遗产数据', description: '全省文化遗产数字化档案数据', icon: 'fas fa-landmark', href: 'javascript:;' }
            ]
        }
    }
}
</script>

<style scoped>
.data-resources {
    padding: 60px 0;
    background: linear-gradient(135deg, #e3f2fd 0%, #bbdefb 100%);
}

.resources-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 25px;
}

.resource-card {
    background: #fff;
    padding: 30px 20px;
    border-radius: 8px;
    text-align: center;
    transition: all 0.3s;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
}

.resource-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 25px rgba(0,0,0,0.15);
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
