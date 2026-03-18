<template>
    <div class="model-resources-page">
        <!-- 页面标题区域 -->
        <section class="page-header">
            <div class="container">
                <div class="header-content">
                    <h1>模型资源</h1>
                    <p>提供文旅融合领域的分析模型、评价方法和决策支持工具，支持文化遗产保护、旅游资源开发、旅游经济分析等方面的研究与应用</p>
                </div>
            </div>
        </section>

        <!-- 搜索和筛选区域 -->
        <section class="search-section">
            <div class="container">
                <div class="search-box">
                    <input
                        v-model="searchKeyword"
                        type="text"
                        placeholder="搜索文化遗产、旅游流量、资源评价、经济分析等模型..."
                        @keyup.enter="handleSearch"
                    />
                    <button class="search-btn" @click="handleSearch">
                        <i class="fas fa-search"></i> 搜索
                    </button>
                </div>
                <div class="filter-tags">
                    <span class="filter-label">分类：</span>
                    <button
                        v-for="category in categories"
                        :key="category.id"
                        :class="['tag-btn', { active: selectedCategory === category.id }]"
                        @click="selectCategory(category.id)"
                    >
                        {{ category.name }}
                        <span class="count">({{ category.count }})</span>
                    </button>
                </div>
            </div>
        </section>

        <!-- 模型列表区域 -->
        <section class="models-section">
            <div class="container">
                <div class="section-header">
                    <h2>
                        {{ selectedCategory === 'all' ? '全部模型' : getCategoryName(selectedCategory) }}
                        <span class="result-count">共 {{ filteredModels.length }} 个模型</span>
                    </h2>
                    <div class="view-toggle">
                        <button
                            :class="['view-btn', { active: viewMode === 'grid' }]"
                            @click="viewMode = 'grid'"
                        >
                            <i class="fas fa-th"></i>
                        </button>
                        <button
                            :class="['view-btn', { active: viewMode === 'list' }]"
                            @click="viewMode = 'list'"
                        >
                            <i class="fas fa-list"></i>
                        </button>
                    </div>
                </div>

                <!-- 网格视图 -->
                <div v-if="viewMode === 'grid'" class="models-grid">
                    <ModelCard
                        v-for="model in paginatedModels"
                        :key="model.id"
                        :model="model"
                        @show-info="handleShowInfo"
                    />
                </div>

                <!-- 列表视图 -->
                <div v-else class="models-list">
                    <div
                        v-for="model in paginatedModels"
                        :key="model.id"
                        class="model-list-item"
                    >
                        <div class="model-thumbnail">
                            <img :src="model.thumbnail" :alt="model.title" />
                            <span class="model-category">{{ getCategoryName(model.category) }}</span>
                        </div>
                        <div class="model-info">
                            <h3>{{ model.title }}</h3>
                            <p class="model-description">{{ model.description }}</p>
                            <div class="model-meta">
                                <span><i class="fas fa-user"></i> {{ model.author }}</span>
                                <span><i class="fas fa-download"></i> {{ model.downloads }}</span>
                                <span><i class="fas fa-calendar"></i> {{ model.date }}</span>
                            </div>
                        </div>
                        <div class="model-actions">
                            <button class="btn-primary">
                                <i class="fas fa-download"></i> 下载
                            </button>
                            <button class="btn-secondary">
                                <i class="fas fa-info-circle"></i> 详情
                            </button>
                        </div>
                    </div>
                </div>

                <!-- 空状态 -->
                <div v-if="filteredModels.length === 0" class="empty-state">
                    <i class="fas fa-inbox"></i>
                    <p>暂无相关模型</p>
                </div>

                <!-- 分页 -->
                <div v-if="filteredModels.length > pageSize" class="pagination">
                    <button
                        :disabled="currentPage === 1"
                        class="page-btn"
                        @click="currentPage--"
                    >
                        <i class="fas fa-chevron-left"></i> 上一页
                    </button>
                    <span class="page-info">{{ currentPage }} / {{ totalPages }}</span>
                    <button
                        :disabled="currentPage === totalPages"
                        class="page-btn"
                        @click="currentPage++"
                    >
                        下一页 <i class="fas fa-chevron-right"></i>
                    </button>
                </div>

                <!-- 模型详情弹窗 -->
                <div v-if="showDialog" class="dialog-overlay" @click="closeDialog">
                    <div class="dialog-content" @click.stop>
                        <button class="dialog-close" @click="closeDialog">
                            <i class="fas fa-times"></i>
                        </button>
                        <div class="dialog-header">
                            <img :src="selectedModel.thumbnail" :alt="selectedModel.title" class="dialog-image" />
                            <div class="dialog-header-info">
                                <h2>{{ selectedModel.title }}</h2>
                                <span class="dialog-category">{{ getCategoryName(selectedModel.category) }}</span>
                            </div>
                        </div>
                        <div class="dialog-body">
                            <div class="dialog-section">
                                <h3>模型描述</h3>
                                <p>{{ selectedModel.description }}</p>
                            </div>
                            <div class="dialog-info-grid">
                                <div class="info-item">
                                    <i class="fas fa-user"></i>
                                    <span class="label">作者：</span>
                                    <span>{{ selectedModel.author }}</span>
                                </div>
                                <div class="info-item">
                                    <i class="fas fa-download"></i>
                                    <span class="label">下载量：</span>
                                    <span>{{ selectedModel.downloads }}</span>
                                </div>
                                <div class="info-item">
                                    <i class="fas fa-calendar"></i>
                                    <span class="label">发布日期：</span>
                                    <span>{{ selectedModel.date }}</span>
                                </div>
                            </div>
                            <div class="dialog-section">
                                <h3>标签</h3>
                                <div class="dialog-tags">
                                    <span
                                        v-for="(tag, index) in selectedModel.tags"
                                        :key="index"
                                        class="dialog-tag"
                                    >
                                        {{ tag }}
                                    </span>
                                </div>
                            </div>
                        </div>
                        <div class="dialog-footer">
                            <button class="btn-primary" @click="handleDownloadFromDialog">
                                <i class="fas fa-download"></i> 下载模型
                            </button>
                            <button class="btn-secondary" @click="closeDialog">
                                关闭
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import ModelCard from '../components/ModelCardComponent.vue'

// 视图模式
const viewMode = ref('grid')
const searchKeyword = ref('')
const selectedCategory = ref('all')
const currentPage = ref(1)
const pageSize = ref(6)

// 详情弹窗
const showDialog = ref(false)
const selectedModel = ref(null)

// 显示详情弹窗
const handleShowInfo = (model) => {
    selectedModel.value = model
    showDialog.value = true
}

// 关闭详情弹窗
const closeDialog = () => {
    showDialog.value = false
}

// 从弹窗下载
const handleDownloadFromDialog = () => {
    alert(`正在下载 ${selectedModel.value.title}...`)
    closeDialog()
}

// 分类数据 - 文旅融合数据分类
const categories = ref([
    { id: 'all', name: '全部', count: 12 },
    { id: 'heritage', name: '文化遗产', count: 3 },
    { id: 'tourism', name: '旅游资源', count: 3 },
    { id: 'culture', name: '文化研究', count: 2 },
    { id: 'economy', name: '旅游经济', count: 2 },
    { id: 'protection', name: '文物保护', count: 1 },
    { id: 'folklore', name: '民俗文化', count: 1 }
])

// 模型数据 - 甘肃省文旅融合科学数据中心
const models = ref([
    {
        id: 1,
        title: '文化遗产价值评估模型',
        description: '基于多指标体系的文化遗产价值综合评估模型，从历史价值、艺术价值、科学价值、社会价值四个维度构建评价指标体系，采用层次分析法（AHP）确定权重，为文化遗产保护与利用提供科学依据。适用于甘肃境内石窟、遗址、古建筑等文化遗产的价值评估。',
        category: 'heritage',
        author: '文化遗产保护研究中心',
        downloads: 2850,
        date: '2024-03-15',
        thumbnail: 'https://images.unsplash.com/photo-1548013146-72479768bada?w=800&h=500&fit=crop',
        tags: ['文化遗产', '价值评估', 'AHP', '指标体系']
    },
    {
        id: 2,
        title: '旅游流量预测模型',
        description: '基于时间序列分析和机器学习的旅游流量预测模型，整合历史游客数据、节假日效应、气候因素、促销活动等多源数据，采用LSTM神经网络进行流量预测。支持日度、周度、月度多时间粒度预测，为景区管理、资源配置提供决策支持。',
        category: 'tourism',
        author: '旅游大数据实验室',
        downloads: 3420,
        date: '2024-03-12',
        thumbnail: 'https://images.unsplash.com/photo-1469854523086-cc02fe5d8800?w=800&h=500&fit=crop',
        tags: ['旅游预测', '时间序列', 'LSTM', '机器学习']
    },
    {
        id: 3,
        title: '敦煌莫高窟游客承载量评估模型',
        description: '针对敦煌莫高窟特殊文物保护需求开发的游客承载量评估模型，综合考虑洞窟空间容量、微环境影响、文物安全等因素，建立多层次承载量评估体系。通过动态监测游客活动对洞窟温湿度、CO2浓度、微生物的影响，确定最优游客承载量，实现文物保护与旅游发展的平衡。',
        category: 'protection',
        author: '敦煌研究院',
        downloads: 4180,
        date: '2024-03-10',
        thumbnail: 'https://images.unsplash.com/photo-1508804185872-d7badad00f7d?w=800&h=500&fit=crop',
        tags: ['莫高窟', '承载量', '文物保护', '游客管理']
    },
    {
        id: 4,
        title: '丝路文化旅游资源评价模型',
        description: '基于丝绸之路文化带特色的旅游资源评价模型，从资源价值、开发条件、市场潜力、环境影响四个维度构建评价指标。重点评估甘肃段丝绸之路的石窟文化、长城文化、彩陶文化、红色文化等特色旅游资源，为文旅产品开发、线路设计提供科学依据。',
        category: 'tourism',
        author: '丝绸之路文化研究中心',
        downloads: 2690,
        date: '2024-03-08',
        thumbnail: 'https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=800&h=500&fit=crop',
        tags: ['丝绸之路', '旅游资源', '评价体系', '文化带']
    },
    {
        id: 5,
        title: '旅游经济贡献度分析模型',
        description: '分析旅游业对区域经济贡献度的计量经济模型，基于投入产出表和旅游卫星账户（TSA）方法，测算旅游业的直接贡献、间接贡献和诱导贡献。评估旅游业对GDP、就业、税收的拉动作用，为旅游产业政策制定、投资决策提供量化支撑。',
        category: 'economy',
        author: '旅游经济研究所',
        downloads: 2340,
        date: '2024-03-05',
        thumbnail: 'https://images.unsplash.com/photo-1553729459-efe14ef6055d?w=800&h=500&fit=crop',
        tags: ['旅游经济', '贡献度', '投入产出', 'TSA']
    },
    {
        id: 6,
        title: '非物质文化遗产数字化保护模型',
        description: '结合数字技术的非物质文化遗产保护传承模型，通过三维扫描、动作捕捉、虚拟现实等技术手段，对甘肃花儿、皮影戏、庆阳香包、临夏砖雕等非遗项目进行数字化记录和保存。构建非遗数字资源库和展示平台，实现非遗的活态传承和传播推广。',
        category: 'culture',
        author: '非物质文化遗产保护中心',
        downloads: 3150,
        date: '2024-03-03',
        thumbnail: 'https://images.unsplash.com/photo-1599930113854-d6d7fd521f10?w=800&h=500&fit=crop',
        tags: ['非遗保护', '数字化', 'VR技术', '文化传承']
    },
    {
        id: 7,
        title: '游客行为分析模型',
        description: '基于大数据和位置服务的游客行为分析模型，整合手机信令、GPS轨迹、景区闸机、消费记录等多源数据，分析游客的来源地分布、移动轨迹、停留时长、消费偏好等行为特征。识别游客群体画像和行为模式，为精准营销、产品优化、服务提升提供数据支撑。',
        category: 'tourism',
        author: '旅游行为分析实验室',
        downloads: 3890,
        date: '2024-03-01',
        thumbnail: 'https://images.unsplash.com/photo-1476514525535-07fb3b4ae5f1?w=800&h=500&fit=crop',
        tags: ['游客行为', '大数据', '位置服务', '用户画像']
    },
    {
        id: 8,
        title: '文旅融合发展评价模型',
        description: '评价文化与旅游产业融合发展水平的综合模型，从资源融合、产品融合、市场融合、产业融合四个维度构建评价指标体系。采用熵值法和TOPSIS方法进行综合评价，量化文旅融合发展的协调度和融合度，为政策制定和产业规划提供评估工具。',
        category: 'economy',
        author: '文旅融合研究中心',
        downloads: 2760,
        date: '2024-02-28',
        thumbnail: 'https://images.unsplash.com/photo-1520483601560-389dff434fdf?w=800&h=500&fit=crop',
        tags: ['文旅融合', '评价模型', 'TOPSIS', '协调发展']
    },
    {
        id: 9,
        title: '长城文化遗产保护规划模型',
        description: '针对甘肃境内长城遗址（包括明长城、汉长城等）的保护规划模型，整合遥感监测、GIS空间分析、风险评估等技术，评估长城遗址的保存状况、病害特征、环境风险。建立分级分类保护体系，制定科学合理的保护措施和展示利用方案。',
        category: 'heritage',
        author: '长城保护研究中心',
        downloads: 1980,
        date: '2024-02-25',
        thumbnail: 'https://images.unsplash.com/photo-1508804052814-cd3ba865a116?w=800&h=500&fit=crop',
        tags: ['长城保护', '遗产规划', 'GIS', '风险评估']
    },
    {
        id: 10,
        title: '红色旅游资源开发潜力评价模型',
        description: '评价红色旅游资源开发潜力的专门模型，以甘肃丰富红色文化资源（会宁会师旧址、南梁革命纪念馆、高台烈士陵园等）为对象，从资源价值、开发条件、市场认知、教育功能等方面构建评价体系。为红色旅游产品开发、线路设计、研学旅行提供科学依据。',
        category: 'culture',
        author: '红色文化研究中心',
        downloads: 2230,
        date: '2024-02-22',
        thumbnail: 'https://images.unsplash.com/photo-1520483601560-389dff434fdf?w=800&h=500&fit=crop',
        tags: ['红色旅游', '资源评价', '开发潜力', '研学旅行']
    },
    {
        id: 11,
        title: '民俗文化活态传承模型',
        description: '研究甘肃民俗文化（如裕固族风情、藏族文化、回族文化、陇南民俗等）活态传承的模型，从传承人保护、文化空间维护、传承方式创新、社区参与等角度构建传承机制。探索民俗文化与现代旅游、教育、创意产业融合发展的路径，促进传统文化在当代社会的传承发展。',
        category: 'folklore',
        author: '民俗文化研究所',
        downloads: 1850,
        date: '2024-02-20',
        thumbnail: 'https://images.unsplash.com/photo-1545569341-9eb8b30979d9?w=800&h=500&fit=crop',
        tags: ['民俗文化', '活态传承', '文化保护', '社区参与']
    },
    {
        id: 12,
        title: '智慧旅游推荐系统模型',
        description: '基于协同过滤和内容推荐算法的智慧旅游推荐系统，根据游客的偏好特征、历史行为、实时位置等信息，智能推荐个性化的旅游景点、线路、美食和住宿。整合甘肃文旅资源数据库，提供定制化的旅游规划服务，提升游客体验和满意度。',
        category: 'tourism',
        author: '智慧旅游实验室',
        downloads: 4560,
        date: '2024-02-18',
        thumbnail: 'https://images.unsplash.com/photo-1469854523086-cc02fe5d8800?w=800&h=500&fit=crop',
        tags: ['推荐系统', '协同过滤', '个性化', '智慧旅游']
    }
])

// 筛选后的模型列表
const filteredModels = computed(() => {
    let result = models.value

    // 按分类筛选
    if (selectedCategory.value !== 'all') {
        result = result.filter(model => model.category === selectedCategory.value)
    }

    // 按关键词搜索
    if (searchKeyword.value.trim()) {
        const keyword = searchKeyword.value.toLowerCase()
        result = result.filter(model =>
            model.title.toLowerCase().includes(keyword) ||
            model.description.toLowerCase().includes(keyword) ||
            model.tags.some(tag => tag.toLowerCase().includes(keyword))
        )
    }

    return result
})

// 分页后的模型列表
const paginatedModels = computed(() => {
    const start = (currentPage.value - 1) * pageSize.value
    const end = start + pageSize.value
    return filteredModels.value.slice(start, end)
})

// 总页数
const totalPages = computed(() => {
    return Math.ceil(filteredModels.value.length / pageSize.value)
})

// 获取分类名称
const getCategoryName = (categoryId) => {
    const category = categories.value.find(c => c.id === categoryId)
    return category ? category.name : '未知'
}

// 选择分类
const selectCategory = (categoryId) => {
    selectedCategory.value = categoryId
    currentPage.value = 1
}

// 搜索
const handleSearch = () => {
    currentPage.value = 1
}

// 监听筛选条件变化，重置页码
watch([selectedCategory, searchKeyword], () => {
    currentPage.value = 1
})
</script>

<script>
export default {
    name: 'ModelResourcesView'
}
</script>

<style scoped>
.model-resources-page {
    min-height: 100vh;
    background: #f5f5f5;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 15px;
}

/* 页面标题区域 */
.page-header {
    background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
    padding: 60px 0 40px;
    color: #fff;
    text-align: center;
}

.header-content h1 {
    font-size: 36px;
    margin-bottom: 15px;
    font-weight: 600;
}

.header-content p {
    font-size: 16px;
    opacity: 0.9;
    max-width: 800px;
    margin: 0 auto;
    line-height: 1.6;
}

/* 搜索区域 */
.search-section {
    background: #fff;
    padding: 30px 0;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
    margin-bottom: 30px;
}

.search-box {
    display: flex;
    gap: 15px;
    margin-bottom: 20px;
}

.search-box input {
    flex: 1;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    transition: border-color 0.3s;
}

.search-box input:focus {
    outline: none;
    border-color: #005da6;
}

.search-btn {
    padding: 12px 30px;
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 14px;
    font-weight: 500;
    transition: all 0.3s;
}

.search-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0,93,166,0.3);
}

.filter-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    align-items: center;
}

.filter-label {
    font-weight: 500;
    color: #666;
    margin-right: 10px;
}

.tag-btn {
    padding: 8px 16px;
    border: 1px solid #e0e0e0;
    background: #fff;
    border-radius: 20px;
    cursor: pointer;
    font-size: 14px;
    transition: all 0.3s;
    color: #666;
}

.tag-btn:hover {
    border-color: #005da6;
    color: #005da6;
}

.tag-btn.active {
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
    border-color: transparent;
}

.tag-btn .count {
    font-size: 12px;
    opacity: 0.8;
}

/* 模型列表区域 */
.models-section {
    padding: 30px 0 60px;
}

.section-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 30px;
}

.section-header h2 {
    font-size: 24px;
    color: #1a237e;
    font-weight: 600;
}

.result-count {
    font-size: 14px;
    color: #666;
    font-weight: 400;
    margin-left: 10px;
}

.view-toggle {
    display: flex;
    gap: 10px;
}

.view-btn {
    width: 40px;
    height: 40px;
    border: 1px solid #e0e0e0;
    background: #fff;
    border-radius: 8px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #666;
    transition: all 0.3s;
}

.view-btn:hover {
    border-color: #005da6;
    color: #005da6;
}

.view-btn.active {
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
    border-color: transparent;
}

/* 网格视图 */
.models-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 20px;
}

/* 列表视图 */
.models-list {
    display: flex;
    flex-direction: column;
    gap: 20px;
}

.model-list-item {
    background: #fff;
    border-radius: 12px;
    padding: 20px;
    display: flex;
    gap: 20px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
    transition: all 0.3s;
}

.model-list-item:hover {
    box-shadow: 0 6px 20px rgba(0,0,0,0.1);
    transform: translateY(-2px);
}

.model-thumbnail {
    width: 200px;
    height: 125px;
    border-radius: 8px;
    overflow: hidden;
    position: relative;
    flex-shrink: 0;
}

.model-thumbnail img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.model-category {
    position: absolute;
    top: 10px;
    left: 10px;
    background: rgba(0,93,166,0.9);
    color: #fff;
    padding: 4px 12px;
    border-radius: 4px;
    font-size: 12px;
}

.model-info {
    flex: 1;
    display: flex;
    flex-direction: column;
}

.model-info h3 {
    font-size: 18px;
    color: #1a237e;
    margin-bottom: 10px;
    font-weight: 600;
}

.model-description {
    font-size: 14px;
    color: #666;
    line-height: 1.6;
    margin-bottom: 15px;
    flex: 1;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

.model-meta {
    display: flex;
    gap: 20px;
    font-size: 13px;
    color: #999;
}

.model-meta span {
    display: flex;
    align-items: center;
    gap: 5px;
}

.model-actions {
    display: flex;
    flex-direction: column;
    gap: 10px;
    justify-content: center;
}

.btn-primary,
.btn-secondary {
    padding: 10px 20px;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-size: 14px;
    font-weight: 500;
    transition: all 0.3s;
    white-space: nowrap;
}

.btn-primary {
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
}

.btn-primary:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0,93,166,0.3);
}

.btn-secondary {
    background: #f5f5f5;
    color: #666;
}

.btn-secondary:hover {
    background: #e0e0e0;
    color: #333;
}

/* 空状态 */
.empty-state {
    text-align: center;
    padding: 80px 0;
    color: #999;
}

.empty-state i {
    font-size: 64px;
    margin-bottom: 20px;
    color: #e0e0e0;
}

.empty-state p {
    font-size: 16px;
}

/* 分页 */
.pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 20px;
    margin-top: 40px;
}

.page-btn {
    padding: 10px 20px;
    border: 1px solid #e0e0e0;
    background: #fff;
    border-radius: 8px;
    cursor: pointer;
    font-size: 14px;
    color: #666;
    transition: all 0.3s;
}

.page-btn:hover:not(:disabled) {
    border-color: #005da6;
    color: #005da6;
}

.page-btn:disabled {
    opacity: 0.5;
    cursor: not-allowed;
}

.page-info {
    font-size: 14px;
    color: #666;
}

/* 响应式 */
@media (max-width: 1200px) {
    .models-grid {
        grid-template-columns: repeat(3, 1fr);
    }
}

@media (max-width: 992px) {
    .models-grid {
        grid-template-columns: repeat(2, 1fr);
    }

    .model-list-item {
        flex-direction: column;
    }

    .model-thumbnail {
        width: 100%;
        height: 180px;
    }

    .model-actions {
        flex-direction: row;
    }
}

@media (max-width: 768px) {
    .header-content h1 {
        font-size: 28px;
    }

    .models-grid {
        grid-template-columns: 1fr;
    }

    .section-header {
        flex-direction: column;
        align-items: flex-start;
        gap: 15px;
    }

    .filter-tags {
        gap: 8px;
    }

    .tag-btn {
        font-size: 13px;
        padding: 6px 12px;
    }
}

/* 详情弹窗样式 */
.dialog-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.7);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1000;
    animation: fadeIn 0.3s ease-out;
}

@keyframes fadeIn {
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}

.dialog-content {
    background: #fff;
    border-radius: 12px;
    max-width: 700px;
    width: 90%;
    max-height: 90vh;
    overflow-y: auto;
    position: relative;
    animation: slideUp 0.3s ease-out;
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);
}

@keyframes slideUp {
    from {
        transform: translateY(50px);
        opacity: 0;
    }
    to {
        transform: translateY(0);
        opacity: 1;
    }
}

.dialog-close {
    position: absolute;
    top: 15px;
    right: 15px;
    width: 32px;
    height: 32px;
    border: none;
    background: rgba(0, 0, 0, 0.1);
    border-radius: 50%;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #666;
    font-size: 18px;
    transition: all 0.3s;
    z-index: 1;
}

.dialog-close:hover {
    background: rgba(0, 0, 0, 0.2);
    color: #333;
    transform: rotate(90deg);
}

.dialog-header {
    position: relative;
    margin-bottom: 25px;
}

.dialog-image {
    width: 100%;
    height: 250px;
    object-fit: cover;
    border-radius: 12px 12px 0 0;
}

.dialog-header-info {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    padding: 20px;
    background: linear-gradient(to top, rgba(0, 0, 0, 0.8), transparent);
    color: #fff;
}

.dialog-header-info h2 {
    font-size: 24px;
    margin-bottom: 8px;
    font-weight: 600;
}

.dialog-category {
    display: inline-block;
    background: rgba(0, 93, 166, 0.9);
    color: #fff;
    padding: 4px 12px;
    border-radius: 4px;
    font-size: 13px;
}

.dialog-body {
    padding: 0 25px 25px;
}

.dialog-section {
    margin-bottom: 20px;
}

.dialog-section h3 {
    font-size: 16px;
    color: #1a237e;
    margin-bottom: 10px;
    font-weight: 600;
}

.dialog-section p {
    font-size: 14px;
    color: #666;
    line-height: 1.8;
}

.dialog-info-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 15px;
    margin-bottom: 20px;
    padding: 15px;
    background: #f5f5f5;
    border-radius: 8px;
}

.info-item {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 13px;
    color: #666;
}

.info-item i {
    color: #005da6;
}

.info-item .label {
    font-weight: 500;
    color: #333;
}

.dialog-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
}

.dialog-tag {
    padding: 6px 14px;
    background: #e8f4f8;
    color: #005da6;
    border-radius: 20px;
    font-size: 13px;
    font-weight: 500;
}

.dialog-footer {
    display: flex;
    justify-content: flex-end;
    gap: 12px;
    padding: 20px 25px;
    border-top: 1px solid #f0f0f0;
}

.dialog-footer .btn-primary {
    padding: 10px 25px;
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-size: 14px;
    font-weight: 500;
    display: flex;
    align-items: center;
    gap: 8px;
    transition: all 0.3s;
}

.dialog-footer .btn-primary:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 93, 166, 0.4);
}

.dialog-footer .btn-secondary {
    padding: 10px 25px;
    background: #f5f5f5;
    color: #666;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-size: 14px;
    font-weight: 500;
    transition: all 0.3s;
}

.dialog-footer .btn-secondary:hover {
    background: #e0e0e0;
    color: #333;
}

/* 弹窗响应式 */
@media (max-width: 768px) {
    .dialog-content {
        width: 95%;
        max-height: 95vh;
    }

    .dialog-image {
        height: 180px;
    }

    .dialog-header-info h2 {
        font-size: 20px;
    }

    .dialog-info-grid {
        grid-template-columns: 1fr;
    }

    .dialog-footer {
        flex-direction: column;
    }

    .dialog-footer button {
        width: 100%;
    }
}
</style>
