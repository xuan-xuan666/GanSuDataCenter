<template>
    <div class="platform-intro-page">
        <!-- 平台介绍横幅 -->
        <section class="platform-banner">
            <div class="container">
                <div class="banner-content">
                    <h1>甘肃文旅融合科学数据中心</h1>
                    <p class="banner-subtitle">Gansu Cultural Tourism Integration Science Data Center</p>
                    <p class="banner-description">整合文旅资源 · 服务科技创新 · 促进融合发展</p>
                </div>
            </div>
        </section>

        <!-- 中心简介 -->
        <section class="center-intro">
            <div class="container">
                <div class="section-title">
                    <h2>中心简介</h2>
                </div>
                <div class="intro-content">
                    <div class="intro-text">
                        <p>甘肃文旅融合科学数据中心是甘肃省重点建设的科学数据管理与共享服务平台，致力于整合全省文旅资源，提供全面的数据服务和分析支持。</p>
                        <p>中心依托先进的大数据技术和专业的研究团队，构建开放、高效、安全的科学数据管理体系，为政府决策、科学研究、企业发展和公众服务提供优质的数据支撑。</p>
                        <p>通过整合多源异构数据资源，建立标准化的数据管理流程和共享机制，中心促进文旅与科学的深度融合，推动数据资源的创新应用和价值转化。</p>
                    </div>
                    <div class="intro-stats">
                        <div class="stat-item" v-for="(stat, index) in platformStats" :key="index">
                            <div class="stat-number">{{ stat.number }}</div>
                            <div class="stat-label">{{ stat.label }}</div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- 功能定位 -->
        <section class="function-positioning">
            <div class="container">
                <div class="section-title">
                    <h2>功能定位</h2>
                </div>
                <div class="positioning-grid">
                    <div class="positioning-card" v-for="(item, index) in positioningItems" :key="index">
                        <div class="positioning-icon">
                            <span class="icon-number">{{ index + 1 }}</span>
                        </div>
                        <h3>{{ item.title }}</h3>
                        <p>{{ item.description }}</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- 数据资源 -->
        <section class="data-resources-section">
            <div class="container">
                <div class="section-title">
                    <h2>数据资源</h2>
                </div>
                <div class="resources-grid">
                    <div class="resource-card" v-for="(resource, index) in dataResources" :key="index">
                        <div class="resource-icon">
                            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                                <ellipse cx="12" cy="5" rx="9" ry="3"></ellipse>
                                <path d="M21 12c0 1.66-4 3-9 3s-9-1.34-9-3"></path>
                                <path d="M3 5v14c0 1.66 4 3 9 3s9-1.34 9-3V5"></path>
                            </svg>
                        </div>
                        <h3>{{ resource.title }}</h3>
                        <p>{{ resource.description }}</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- 服务内容 -->
        <section class="service-content-section">
            <div class="container">
                <div class="section-title">
                    <h2>服务内容</h2>
                </div>
                <div class="service-list">
                    <div class="service-item" v-for="(service, index) in serviceItems" :key="index">
                        <div class="service-number">{{ String(index + 1).padStart(2, '0') }}</div>
                        <div class="service-content">
                            <h3>{{ service.title }}</h3>
                            <p>{{ service.description }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </section>



        <!-- 联系方式 -->
        <section class="contact-section" id="contact">
            <div class="container">
                <div class="section-title">
                    <h2>联系我们</h2>
                </div>
                <div class="contact-content">
                    <div class="contact-info" v-for="(info, index) in contactInfo" :key="index">
                        <div class="contact-label">{{ info.label }}</div>
                        <div class="contact-value">{{ info.value }}</div>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script setup>
import { ref } from 'vue'

const platformStats = ref([
    { number: '1000+', label: '数据资源' },
    { number: '50+', label: '专题项目' },
    { number: '100 万+', label: '累计访问量' },
    { number: '20+', label: '合作单位' }
])

const positioningItems = ref([
    {
        title: '数据汇聚中心',
        description: '整合全省文旅资源数据，建立统一的数据资源池，实现多源异构数据的标准化存储和管理。'
    },
    {
        title: '分析研究中心',
        description: '运用大数据分析和人工智能技术，开展文旅融合发展的理论研究和实践探索。'
    },
    {
        title: '共享服务中心',
        description: '面向政府、企业、科研机构和社会公众，提供多层次、多样化的数据共享服务。'
    },
    {
        title: '创新应用中心',
        description: '推动数据资源与创新应用深度融合，促进文旅产业数字化转型和高质量发展。'
    }
])

const dataResources = ref([
    {
        title: '文化资源数据',
        description: '包括文化遗产、博物馆、图书馆、文化艺术等基础数据和专题数据。'
    },
    {
        title: '旅游资源数据',
        description: '涵盖旅游景区、酒店餐饮、旅行社、旅游交通等全产业链数据资源。'
    },
    {
        title: '融合发展的数据',
        description: '文旅融合项目、文旅消费、文旅产业经济等融合发展相关数据。'
    },
    {
        title: '专题监测数据',
        description: '生态环境、客流监测、舆情分析等专题监测和统计数据。'
    }
])

const serviceItems = ref([
    {
        title: '数据查询与检索服务',
        description: '提供便捷的数据查询、检索和浏览功能，支持多维度筛选和精准定位。'
    },
    {
        title: '数据下载与接口服务',
        description: '支持数据在线下载和 API 接口调用，满足多样化数据使用需求。'
    },
    {
        title: '数据分析与可视化服务',
        description: '提供数据分析工具和可视化展示，帮助用户深入理解数据价值。'
    },
    {
        title: '定制化数据服务',
        description: '根据用户需求提供定制化数据采集、处理和分析服务。'
    },
    {
        title: '决策支持服务',
        description: '基于数据分析结果为政府决策和企业发展提供科学依据和建议。'
    }
])

const contactInfo = ref([
    { label: '单位名称', value: '甘肃文旅融合科学数据中心' },
    { label: '联系地址', value: '甘肃省兰州市城关区 XXX 路 XXX 号' },
    { label: '联系电话', value: '0931-XXXXXXXX' },
    { label: '电子邮箱', value: 'contact@gswlkx.cn' },
    { label: '服务时间', value: '周一至周五 9:00-17:00' }
])
</script>

<style scoped>
.platform-intro-page {
    min-height: 100vh;
    background: #f5f5f5;
}

/* 平台横幅 */
.platform-banner {
    background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
    padding: 80px 0;
    text-align: center;
    color: #fff;
}

.banner-content h1 {
    font-size: 42px;
    font-weight: 600;
    margin-bottom: 15px;
    letter-spacing: 2px;
}

.banner-subtitle {
    font-size: 18px;
    opacity: 0.9;
    margin-bottom: 20px;
    font-weight: 300;
}

.banner-description {
    font-size: 16px;
    opacity: 0.85;
    max-width: 800px;
    margin: 0 auto;
}

/* 通用 section 样式 */
.center-intro,
.function-positioning,
.data-resources-section,
.service-content-section,
.contact-section {
    padding: 70px 0;
}

.center-intro:nth-child(even) {
    background: #fff;
}

.function-positioning,
.data-resources-section,
.service-content-section,
.contact-section {
    background: #f5f5f5;
}

/* 标题样式 */
.section-title {
    text-align: center;
    margin-bottom: 50px;
}

.section-title h2 {
    font-size: 32px;
    color: #1a237e;
    position: relative;
    display: inline-block;
    padding-bottom: 15px;
}

.section-title h2::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 80px;
    height: 4px;
    background: linear-gradient(135deg, #4fc3f7 0%, #00bcd4 100%);
    border-radius: 2px;
}

/* 中心简介 */
.intro-content {
    display: flex;
    gap: 60px;
    align-items: flex-start;
}

.intro-text {
    flex: 1;
    line-height: 2;
    color: #333;
}

.intro-text p {
    margin-bottom: 20px;
    font-size: 16px;
    text-align: justify;
}

.intro-stats {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 20px;
    min-width: 300px;
}

.stat-item {
    text-align: center;
    padding: 25px 20px;
    background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
    border-radius: 10px;
    color: #fff;
    box-shadow: 0 4px 15px rgba(26, 35, 126, 0.2);
}

.stat-number {
    font-size: 38px;
    font-weight: 600;
    color: #4fc3f7;
    margin-bottom: 8px;
}

.stat-label {
    font-size: 15px;
    opacity: 0.9;
}

/* 功能定位 */
.positioning-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 25px;
}

.positioning-card {
    background: #fff;
    padding: 30px 25px;
    border-radius: 10px;
    text-align: center;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
    transition: transform 0.3s, box-shadow 0.3s;
}

.positioning-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.12);
}

.positioning-icon {
    width: 60px;
    height: 60px;
    margin: 0 auto 20px;
    background: linear-gradient(135deg, #4fc3f7 0%, #00bcd4 100%);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
}

.icon-number {
    font-size: 28px;
    font-weight: 600;
    color: #fff;
}

.positioning-card h3 {
    font-size: 18px;
    color: #1a237e;
    margin-bottom: 12px;
}

.positioning-card p {
    font-size: 14px;
    color: #666;
    line-height: 1.8;
}

/* 数据资源 */
.resources-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 25px;
}

.resource-card {
    background: #fff;
    padding: 30px 25px;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
    transition: all 0.3s;
}

.resource-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.12);
}

.resource-icon {
    width: 50px;
    height: 50px;
    margin-bottom: 20px;
    color: #1a237e;
}

.resource-icon svg {
    width: 100%;
    height: 100%;
}

.resource-card h3 {
    font-size: 17px;
    color: #1a237e;
    margin-bottom: 12px;
}

.resource-card p {
    font-size: 14px;
    color: #666;
    line-height: 1.8;
}

/* 服务内容 */
.service-list {
    display: flex;
    flex-direction: column;
    gap: 20px;
}

.service-item {
    display: flex;
    gap: 25px;
    background: #fff;
    padding: 25px 30px;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
    transition: all 0.3s;
}

.service-item:hover {
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.12);
}

.service-number {
    font-size: 32px;
    font-weight: 600;
    color: #4fc3f7;
    min-width: 50px;
}

.service-content h3 {
    font-size: 18px;
    color: #1a237e;
    margin-bottom: 10px;
}

.service-content p {
    font-size: 15px;
    color: #666;
    line-height: 1.8;
}

/* 联系方式 */
.contact-content {
    background: #fff;
    padding: 40px;
    border-radius: 10px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
}

.contact-info {
    display: flex;
    padding: 20px 0;
    border-bottom: 1px solid #e0e0e0;
}

.contact-info:last-child {
    border-bottom: none;
}

.contact-label {
    width: 120px;
    font-size: 15px;
    color: #666;
    font-weight: 500;
}

.contact-value {
    flex: 1;
    font-size: 15px;
    color: #333;
}

/* 响应式设计 */
@media (max-width: 1200px) {
    .positioning-grid,
    .resources-grid {
        grid-template-columns: repeat(2, 1fr);
    }
}

@media (max-width: 768px) {
    .banner-content h1 {
        font-size: 28px;
    }
    
    .banner-subtitle {
        font-size: 14px;
    }
    
    .intro-content {
        flex-direction: column;
        gap: 30px;
    }
    
    .positioning-grid,
    .resources-grid {
        grid-template-columns: 1fr;
    }
    
    .section-title h2 {
        font-size: 26px;
    }
    
    .service-item {
        flex-direction: column;
        gap: 15px;
    }
    
    .service-number {
        min-width: auto;
    }
}
</style>