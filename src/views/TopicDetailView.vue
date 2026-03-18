<template>
  <div class="topic-detail-page">
    <!-- 页面标题 -->
    <section class="page-header">
      <div class="container">
        <div class="breadcrumb">
          <router-link to="/topic-data">专题数据</router-link>
          <span>/</span>
          <span>{{ topicDetail.title }}</span>
        </div>
        <h1>{{ topicDetail.title }}</h1>
        <div class="meta-info">
          <span><i class="fas fa-calendar"></i> {{ topicDetail.date }}</span>
          <span><i class="fas fa-download"></i> {{ topicDetail.downloads }} 次下载</span>
          <span><i class="fas fa-eye"></i> {{ topicDetail.views }} 次浏览</span>
        </div>
      </div>
    </section>

    <!-- 专题介绍 -->
    <section class="intro-section">
      <div class="container">
        <div class="section-title">
          <h2>专题介绍</h2>
          <div class="title-line"></div>
        </div>
        <div class="intro-content">
          <p>{{ topicDetail.description }}</p>
        </div>
      </div>
    </section>

    <!-- 数据集列表 -->
    <section class="dataset-section">
      <div class="container">
        <div class="section-title">
          <h2>数据集列表</h2>
          <div class="title-line"></div>
        </div>

        <div v-if="datasets.length > 0" class="dataset-grid">
          <div
            v-for="dataset in datasets"
            :key="dataset.id"
            class="dataset-card"
            @click="handleDatasetClick(dataset)"
          >
            <div class="dataset-card-header">
              <div class="dataset-icon">
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path>
                  <polyline points="14 2 14 8 20 8"></polyline>
                  <line x1="16" y1="13" x2="8" y2="13"></line>
                  <line x1="16" y1="17" x2="8" y2="17"></line>
                </svg>
              </div>
              <h3 class="dataset-title">{{ dataset.title }}</h3>
            </div>
            <p class="dataset-description">{{ truncateText(dataset.description, 100) }}</p>
            <div class="dataset-meta">
              <span class="dataset-format">{{ dataset.format }}</span>
              <span class="dataset-size">{{ dataset.size }}</span>
              <span class="dataset-date">{{ dataset.date }}</span>
            </div>
            <div class="dataset-actions">
              <button class="download-btn" @click.stop="handleDownload(dataset)">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                  <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
                  <polyline points="7 10 12 15 17 10"></polyline>
                  <line x1="12" y1="15" x2="12" y2="3"></line>
                </svg>
                下载
              </button>
              <button class="detail-btn" @click.stop="handleViewDetail(dataset)">
                查看详情
              </button>
            </div>
          </div>
        </div>

        <div v-else class="empty-state">
          <svg xmlns="http://www.w3.org/2000/svg" width="80" height="80" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path>
            <polyline points="14 2 14 8 20 8"></polyline>
          </svg>
          <h3>暂无数据集</h3>
          <p>该专题暂无相关数据集</p>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()

// 专题详情数据
const topicDetail = ref({
  id: 1,
  title: '甘肃省文化遗产资源专题数据库',
  description: '整合甘肃省内石窟寺、古遗址、古墓葬、古建筑等不可移动文物资源数据，以及可移动文物、非物质文化遗产等文化遗产资源数据。',
  date: '2026-03-15',
  downloads: 2850,
  views: 15680
})

// 数据集列表
const datasets = ref([
  {
    id: 1,
    title: '甘肃省石窟寺文物资源数据集',
    description: '包含甘肃省境内所有石窟寺的基础信息、壁画数据、彩塑数据、建筑数据等，共计 5000+ 条记录。',
    format: 'CSV/Excel',
    size: '125 MB',
    date: '2026-03-15',
    downloads: 856
  },
  {
    id: 2,
    title: '甘肃省古遗址分布数据集',
    description: '收录甘肃省历代古遗址 3000 余处，包括遗址名称、位置、年代、类型、保护级别等属性信息。',
    format: 'CSV/Shapefile',
    size: '89 MB',
    date: '2026-03-12',
    downloads: 623
  },
  {
    id: 3,
    title: '甘肃省非物质文化遗产名录数据集',
    description: '整合国家级、省级、市县级非物质文化遗产代表性项目名录，涵盖民间文学、传统音乐、传统舞蹈等 10 大门类。',
    format: 'CSV/Excel',
    size: '45 MB',
    date: '2026-03-10',
    downloads: 1024
  },
  {
    id: 4,
    title: '甘肃省可移动文物普查数据集',
    description: '基于第一次全国可移动文物普查数据，整理甘肃省国有单位收藏可移动文物信息，共计 10 万件。',
    format: 'CSV/Excel',
    size: '256 MB',
    date: '2026-03-08',
    downloads: 789
  },
  {
    id: 5,
    title: '甘肃省历史文化名城名镇名村数据集',
    description: '收录甘肃省历史文化名城、名镇、名村的基础信息、历史建筑、保护规划等数据。',
    format: 'CSV/Excel/PDF',
    size: '78 MB',
    date: '2026-03-05',
    downloads: 567
  },
  {
    id: 6,
    title: '甘肃省长城资源调查数据集',
    description: '基于长城资源调查成果，整理甘肃省境内明长城、汉长城等长城遗迹的分布、形制、保存状况等数据。',
    format: 'CSV/Shapefile',
    size: '156 MB',
    date: '2026-03-03',
    downloads: 445
  }
])

// 方法
const truncateText = (text, maxLength) => {
  if (text.length <= maxLength) return text
  return text.substring(0, maxLength) + '...'
}

const handleDatasetClick = (dataset) => {
  console.log('点击数据集:', dataset)
}

const handleDownload = (dataset) => {
  alert(`正在下载：${dataset.title}`)
}

const handleViewDetail = (dataset) => {
  console.log('查看详情:', dataset)
}
</script>

<style scoped>
.topic-detail-page {
  min-height: 100vh;
  background: #f5f5f5;
}

/* 页面标题 */
.page-header {
  background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
  color: #fff;
  padding: 60px 0;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 15px;
}

.breadcrumb {
  display: flex;
  gap: 8px;
  font-size: 14px;
  margin-bottom: 20px;
  opacity: 0.9;
}

.breadcrumb a {
  color: #fff;
  text-decoration: none;
  transition: opacity 0.3s;
}

.breadcrumb a:hover {
  opacity: 0.8;
}

.page-header h1 {
  font-size: 32px;
  margin: 0 0 20px 0;
  font-weight: 600;
  line-height: 1.4;
}

.meta-info {
  display: flex;
  gap: 20px;
  font-size: 14px;
  opacity: 0.9;
}

.meta-info i {
  margin-right: 4px;
}

/* 专题介绍 */
.intro-section {
  background: #fff;
  padding: 40px 0;
  margin-bottom: 20px;
}

.section-title {
  text-align: center;
  margin-bottom: 30px;
}

.section-title h2 {
  font-size: 24px;
  color: #1a237e;
  margin: 0 0 10px 0;
  font-weight: 600;
}

.title-line {
  width: 60px;
  height: 3px;
  background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
  margin: 0 auto;
}

.intro-content {
  max-width: 900px;
  margin: 0 auto;
  line-height: 1.8;
  color: #333;
  font-size: 16px;
}

/* 数据集列表 */
.dataset-section {
  padding: 40px 0;
}

.dataset-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.dataset-card {
  background: #fff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  cursor: pointer;
  transition: transform 0.3s, box-shadow 0.3s;
}

.dataset-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.12);
}

.dataset-card-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 15px;
}

.dataset-icon {
  width: 40px;
  height: 40px;
  background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  flex-shrink: 0;
}

.dataset-title {
  font-size: 16px;
  color: #1a237e;
  margin: 0;
  font-weight: 600;
  line-height: 1.4;
  flex: 1;
}

.dataset-description {
  font-size: 14px;
  color: #666;
  line-height: 1.6;
  margin: 0 0 15px 0;
}

.dataset-meta {
  display: flex;
  gap: 15px;
  margin-bottom: 15px;
  font-size: 13px;
  color: #999;
}

.dataset-format {
  background: #f5f5f5;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
}

.dataset-size {
  background: #f5f5f5;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
}

.dataset-date {
  margin-left: auto;
}

.dataset-actions {
  display: flex;
  gap: 10px;
}

.download-btn,
.detail-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 13px;
  display: flex;
  align-items: center;
  gap: 6px;
  transition: all 0.3s;
}

.download-btn {
  background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
  color: #fff;
}

.download-btn:hover {
  background: linear-gradient(135deg, #283593 0%, #1a237e 100%);
}

.detail-btn {
  background: #f5f5f5;
  color: #333;
}

.detail-btn:hover {
  background: #e0e0e0;
}

/* 空状态 */
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 80px 20px;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.empty-state svg {
  color: #ddd;
  margin-bottom: 20px;
}

.empty-state h3 {
  color: #666;
  font-size: 20px;
  margin: 0 0 10px 0;
}

.empty-state p {
  color: #999;
  font-size: 14px;
  margin: 0;
}

/* 响应式 */
@media (max-width: 1024px) {
  .dataset-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .page-header h1 {
    font-size: 24px;
  }

  .meta-info {
    flex-direction: column;
    gap: 8px;
  }

  .section-title h2 {
    font-size: 20px;
  }
}
</style>
