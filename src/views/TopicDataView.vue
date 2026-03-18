<template>
  <div class="topic-data-page">
    <!-- 页面标题 -->
    <section class="page-header">
      <div class="container">
        <h1>专题数据</h1>
        <p>整合文旅融合领域专题数据资源，提供多维度数据查询与分析服务</p>
      </div>
    </section>

    <!-- 筛选栏 -->
    <section class="filter-section">
      <div class="container">
        <div class="filter-bar">
          <div class="filter-group">
            <label>搜索：</label>
            <input
              v-model="searchKeyword"
              type="text"
              placeholder="输入关键词搜索..."
              @input="handleSearch"
            />
          </div>
          <div class="filter-group">
            <label>分类：</label>
            <select v-model="selectedCategory" @change="handleFilter">
              <option value="all">全部分类</option>
              <option value="culture">文化遗产</option>
              <option value="tourism">旅游资源</option>
              <option value="ecology">生态环境</option>
              <option value="economy">旅游经济</option>
            </select>
          </div>
          <div class="filter-group">
            <label>排序：</label>
            <select v-model="sortBy" @change="handleSort">
              <option value="latest">最新</option>
              <option value="popular">最热</option>
              <option value="downloads">下载量</option>
            </select>
          </div>
        </div>
      </div>
    </section>

    <!-- 数据列表 -->
    <main class="main-content">
      <div class="container">
        <div v-if="filteredData.length > 0" class="data-grid">
          <div
            v-for="item in paginatedData"
            :key="item.id"
            class="data-card"
            @click="handleCardClick(item)"
          >
            <div class="card-image">
              <img :src="item.image" :alt="item.title" />
              <span class="card-category">{{ getCategoryName(item.category) }}</span>
            </div>
            <div class="card-content">
              <h3 class="card-title">{{ item.title }}</h3>
              <p class="card-description">{{ truncateText(item.description, 80) }}</p>
              <div class="card-meta">
                <span><i class="fas fa-calendar"></i> {{ item.date }}</span>
                <span><i class="fas fa-download"></i> {{ item.downloads }}</span>
              </div>
            </div>
          </div>
        </div>

        <!-- 空状态 -->
        <div v-else class="empty-state">
          <svg xmlns="http://www.w3.org/2000/svg" width="80" height="80" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <circle cx="11" cy="11" r="8"></circle>
            <path d="m21 21-4.35-4.35"></path>
          </svg>
          <h3>暂无数据</h3>
          <p>当前筛选条件下没有找到相关数据</p>
          <button class="reset-btn" @click="handleResetFilters">重置筛选条件</button>
        </div>

        <!-- 分页 -->
        <div v-if="totalPages > 1" class="pagination">
          <button :disabled="currentPage === 1" class="page-btn" @click="currentPage--">
            &lt; 上一页
          </button>
          <span class="page-info">{{ currentPage }} / {{ totalPages }}</span>
          <button :disabled="currentPage === totalPages" class="page-btn" @click="currentPage++">
            下一页 &gt;
          </button>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

// 响应式数据
const searchKeyword = ref('')
const selectedCategory = ref('all')
const sortBy = ref('latest')
const currentPage = ref(1)
const pageSize = ref(9)

// 专题数据
const allData = ref([
  {
    id: 1,
    title: '甘肃省文化遗产资源专题数据库',
    description: '整合甘肃省内石窟寺、古遗址、古墓葬、古建筑等不可移动文物资源数据，以及可移动文物、非物质文化遗产等文化遗产资源数据。',
    category: 'culture',
    date: '2026-03-15',
    downloads: 2850,
    image: 'https://picsum.photos/400/300?random=1'
  },
  {
    id: 2,
    title: '丝绸之路（甘肃段）文化遗产数据专题',
    description: '聚焦丝绸之路甘肃段沿线文化遗产资源，包括敦煌莫高窟、天水麦积山、永靖炳灵寺等石窟寺，以及嘉峪关、汉长城等遗址数据。',
    category: 'culture',
    date: '2026-03-12',
    downloads: 3420,
    image: 'https://picsum.photos/400/300?random=2'
  },
  {
    id: 3,
    title: '甘肃省 A 级旅游景区专题数据',
    description: '涵盖甘肃省所有 A 级旅游景区的基础信息、游客接待量、旅游收入、服务质量评价等数据，支持旅游市场分析和规划决策。',
    category: 'tourism',
    date: '2026-03-10',
    downloads: 4180,
    image: 'https://picsum.photos/400/300?random=3'
  },
  {
    id: 4,
    title: '黄河文化（甘肃段）专题数据库',
    description: '围绕黄河流经甘肃的兰州、白银、临夏等地区，整合黄河文化遗址、民俗风情、生态景观等数据资源。',
    category: 'culture',
    date: '2026-03-08',
    downloads: 2690,
    image: 'https://picsum.photos/400/300?random=4'
  },
  {
    id: 5,
    title: '甘肃省旅游经济统计专题数据',
    description: '提供甘肃省及各地州市旅游经济统计数据，包括旅游总收入、接待游客量、旅游投资、旅游就业等指标数据。',
    category: 'economy',
    date: '2026-03-05',
    downloads: 2340,
    image: 'https://picsum.photos/400/300?random=5'
  },
  {
    id: 6,
    title: '甘肃特色民俗文化专题数据库',
    description: '收录甘肃各民族民俗文化数据，包括裕固族、藏族、回族、东乡族等少数民族的节庆、歌舞、手工艺、饮食等民俗文化资源。',
    category: 'culture',
    date: '2026-03-03',
    downloads: 3150,
    image: 'https://picsum.photos/400/300?random=6'
  },
  {
    id: 7,
    title: '甘肃省旅游生态环境监测专题数据',
    description: '整合重点旅游景区生态环境监测数据，包括空气质量、水质监测、噪声监测、植被覆盖等生态环境指标。',
    category: 'ecology',
    date: '2026-03-01',
    downloads: 3890,
    image: 'https://picsum.photos/400/300?random=7'
  },
  {
    id: 8,
    title: '长征国家文化公园（甘肃段）专题数据',
    description: '围绕长征国家文化公园甘肃段建设，整合红军长征在甘肃的革命遗址、纪念设施、红色故事等红色文化资源数据。',
    category: 'culture',
    date: '2026-02-28',
    downloads: 2760,
    image: 'https://picsum.photos/400/300?random=8'
  },
  {
    id: 9,
    title: '甘肃省智慧旅游平台运行监测数据',
    description: '基于甘肃省智慧旅游平台，整合游客行为数据、在线预订数据、投诉建议数据、平台运行数据等。',
    category: 'tourism',
    date: '2026-02-25',
    downloads: 1980,
    image: 'https://picsum.photos/400/300?random=9'
  },
  {
    id: 10,
    title: '河西走廊文化旅游带专题数据库',
    description: '聚焦河西走廊五市（武威、金昌、张掖、酒泉、嘉峪关）文化旅游资源，整合历史文化、自然景观、民俗风情等数据。',
    category: 'tourism',
    date: '2026-02-22',
    downloads: 2230,
    image: 'https://picsum.photos/400/300?random=10'
  },
  {
    id: 11,
    title: '甘肃省文旅产业投资专题数据',
    description: '收录甘肃省文旅产业投资项目数据，包括项目基本信息、投资规模、建设进度、经济效益等，支持产业投资分析。',
    category: 'economy',
    date: '2026-02-20',
    downloads: 1850,
    image: 'https://picsum.photos/400/300?random=11'
  },
  {
    id: 12,
    title: '甘南草原生态旅游专题数据库',
    description: '整合甘南藏族自治州草原生态旅游资源数据，包括草原景观、藏族文化、生态旅游项目、环境监测等数据。',
    category: 'ecology',
    date: '2026-02-18',
    downloads: 4560,
    image: 'https://picsum.photos/400/300?random=12'
  }
])

// 计算属性
const filteredData = computed(() => {
  let result = [...allData.value]

  // 按分类筛选
  if (selectedCategory.value !== 'all') {
    result = result.filter(item => item.category === selectedCategory.value)
  }

  // 按关键词搜索
  if (searchKeyword.value.trim()) {
    const keyword = searchKeyword.value.toLowerCase().trim()
    result = result.filter(item =>
      item.title.toLowerCase().includes(keyword) ||
      item.description.toLowerCase().includes(keyword)
    )
  }

  // 排序
  if (sortBy.value === 'latest') {
    result.sort((a, b) => new Date(b.date) - new Date(a.date))
  } else if (sortBy.value === 'popular') {
    result.sort((a, b) => b.downloads - a.downloads)
  } else if (sortBy.value === 'downloads') {
    result.sort((a, b) => b.downloads - a.downloads)
  }

  return result
})

const paginatedData = computed(() => {
  const start = (currentPage.value - 1) * pageSize.value
  const end = start + pageSize.value
  return filteredData.value.slice(start, end)
})

const totalPages = computed(() => Math.ceil(filteredData.value.length / pageSize.value))

// 方法
const getCategoryName = (category) => {
  const categories = {
    culture: '文化遗产',
    tourism: '旅游资源',
    ecology: '生态环境',
    economy: '旅游经济'
  }
  return categories[category] || category
}

const truncateText = (text, maxLength) => {
  if (text.length <= maxLength) return text
  return text.substring(0, maxLength) + '...'
}

const handleSearch = () => {
  currentPage.value = 1
}

const handleFilter = () => {
  currentPage.value = 1
}

const handleSort = () => {
  currentPage.value = 1
}

const handleCardClick = (item) => {
  router.push(`/topic-data/${item.id}`)
}

const handleResetFilters = () => {
  searchKeyword.value = ''
  selectedCategory.value = 'all'
  sortBy.value = 'latest'
  currentPage.value = 1
}
</script>

<style scoped>
.topic-data-page {
  min-height: 100vh;
  background: #f5f5f5;
}

/* 页面标题 */
.page-header {
  background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
  color: #fff;
  padding: 60px 0;
  text-align: center;
}

.page-header h1 {
  font-size: 36px;
  margin: 0 0 15px 0;
  font-weight: 600;
}

.page-header p {
  font-size: 16px;
  max-width: 800px;
  margin: 0 auto;
  opacity: 0.9;
  line-height: 1.6;
}

/* 筛选栏 */
.filter-section {
  background: #fff;
  padding: 20px 0;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
}

.filter-bar {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
}

.filter-group {
  display: flex;
  align-items: center;
  gap: 10px;
}

.filter-group label {
  font-size: 14px;
  color: #666;
  font-weight: 500;
  white-space: nowrap;
}

.filter-group input,
.filter-group select {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
  color: #333;
  background: #fff;
  transition: border-color 0.3s;
}

.filter-group input:focus,
.filter-group select:focus {
  outline: none;
  border-color: #1a237e;
}

/* 主内容 */
.main-content {
  padding: 40px 0;
  min-height: 600px;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 15px;
}

/* 数据网格 */
.data-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 25px;
  margin-bottom: 30px;
}

/* 数据卡片 */
.data-card {
  background: #fff;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  cursor: pointer;
  transition: transform 0.3s, box-shadow 0.3s;
}

.data-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.12);
}

.card-image {
  position: relative;
  height: 200px;
  overflow: hidden;
}

.card-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s;
}

.data-card:hover .card-image img {
  transform: scale(1.1);
}

.card-category {
  position: absolute;
  top: 10px;
  left: 10px;
  background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
  color: #fff;
  padding: 4px 12px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 500;
}

.card-content {
  padding: 20px;
}

.card-title {
  font-size: 16px;
  color: #1a237e;
  margin: 0 0 10px 0;
  font-weight: 600;
  line-height: 1.4;
}

.card-description {
  font-size: 14px;
  color: #666;
  line-height: 1.5;
  margin: 0 0 15px 0;
}

.card-meta {
  display: flex;
  justify-content: space-between;
  font-size: 13px;
  color: #999;
}

.card-meta i {
  margin-right: 4px;
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
  margin: 0 0 20px 0;
}

.reset-btn {
  padding: 10px 24px;
  background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.3s;
}

.reset-btn:hover {
  background: linear-gradient(135deg, #283593 0%, #1a237e 100%);
}

/* 分页 */
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin-top: 30px;
}

.page-btn {
  padding: 8px 16px;
  background: #fff;
  border: 1px solid #ddd;
  color: #333;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.3s;
}

.page-btn:not(:disabled):hover {
  background: #1a237e;
  color: #fff;
  border-color: #1a237e;
}

.page-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.page-info {
  padding: 8px 16px;
  color: #666;
  font-size: 14px;
}

/* 响应式 */
@media (max-width: 1024px) {
  .data-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .page-header h1 {
    font-size: 28px;
  }

  .filter-bar {
    flex-direction: column;
    gap: 15px;
  }

  .data-grid {
    grid-template-columns: 1fr;
  }
}
</style>
