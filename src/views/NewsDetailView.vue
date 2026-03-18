<template>
    <div class="news-detail-page">
        <main class="news-detail-content">
            <div class="container">
                <div class="back-link">
                    <router-link to="/news" class="back-btn">
                        <span>&lt; 返回新闻列表</span>
                    </router-link>
                </div>
                
                <article class="news-article" v-if="newsDetail">
                    <div class="article-header">
                        <h1 class="article-title">{{ newsDetail.title }}</h1>
                        <div class="article-meta">
                            <span class="article-category">{{ getCategoryLabel(newsDetail.category) }}</span>
                            <span class="article-date">{{ newsDetail.date }}</span>
                            <span class="article-views">浏览：{{ newsDetail.views || 0 }}</span>
                        </div>
                    </div>
                    
                    <div class="article-image" v-if="newsDetail.image">
                        <img :src="newsDetail.image" :alt="newsDetail.title">
                    </div>
                    
                    <div class="article-body">
                        <p class="article-summary">{{ newsDetail.summary }}</p>
                        <div class="article-content" v-html="newsDetail.content"></div>
                    </div>
                    
                    <div class="article-footer">
                        <div class="article-tags">
                            <span class="tag">甘肃文旅</span>
                            <span class="tag">数据中心</span>
                            <span class="tag">文旅融合</span>
                        </div>
                        <div class="article-share">
                            <button class="share-btn">分享</button>
                            <button class="share-btn">收藏</button>
                        </div>
                    </div>
                </article>
                
                <div class="related-news" v-if="relatedNews.length > 0">
                    <h3>相关新闻</h3>
                    <div class="related-list">
                        <div 
                            class="related-item" 
                            v-for="news in relatedNews" 
                            :key="news.id"
                            @click="goToDetail(news.id)"
                        >
                            <div class="related-image" v-if="news.image">
                                <img :src="news.image" :alt="news.title">
                            </div>
                            <div class="related-info">
                                <h4>{{ news.title }}</h4>
                                <span class="related-date">{{ news.date }}</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </main>
    </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const allNews = ref([
    {
        id: 1,
        title: '甘肃文旅融合科学数据中心正式上线运行',
        summary: '甘肃文旅融合科学数据中心经过一年的建设和试运行，现已正式上线。该平台整合了全省文旅资源数据，为政府决策、科学研究和公众服务提供有力支撑。',
        content: `<p>甘肃文旅融合科学数据中心经过一年的建设和试运行，现已正式上线。该平台整合了全省文旅资源数据，为政府决策、科学研究和公众服务提供有力支撑。</p>
        <p>中心依托先进的大数据技术，构建了覆盖全省的文旅数据采集、存储、分析和应用体系。平台整合了旅游资源、游客行为、环境监测、文化遗产等多维度数据，实现了数据的实时监测和动态更新。</p>
        <p>在数据服务方面，中心提供了数据查询、统计分析、可视化展示等功能，用户可以通过平台获取各类文旅数据和分析报告。同时，中心还建立了完善的数据安全保障机制，确保数据的安全性和可靠性。</p>
        <p>未来，中心将继续完善数据资源体系，拓展数据应用场景，为甘肃文旅产业的高质量发展提供更加有力的数据支撑。</p>`,
        date: '2026-03-15',
        category: 'platform',
        image: 'https://picsum.photos/800/400?random=1',
        views: 1234
    },
    {
        id: 2,
        title: '全省文旅大数据平台建设推进会议召开',
        summary: '甘肃省文化和旅游厅召开全省文旅大数据平台建设推进会议，总结前期工作成果，部署下一阶段重点任务，推动文旅数字化转型。',
        content: `<p>甘肃省文化和旅游厅召开全省文旅大数据平台建设推进会议，总结前期工作成果，部署下一阶段重点任务，推动文旅数字化转型。</p>
        <p>会议指出，近年来甘肃省文旅大数据平台建设取得了显著成效，数据资源不断丰富，应用场景持续拓展，服务能力显著提升。但同时也要清醒地认识到，平台建设还存在数据共享不充分、应用深度不够等问题。</p>
        <p>会议强调，下一阶段要重点做好以下工作：一是完善数据标准体系，推动数据规范采集和共享；二是深化数据应用，开发更多实用的数据产品和服务；三是加强安全保障，建立健全数据安全管理制度；四是培养专业人才，提升数据分析和应用能力。</p>
        <p>各市州文旅部门负责人、相关企业代表参加了会议。大家纷纷表示，将认真贯彻落实会议精神，加快推进本地文旅大数据平台建设，为全省文旅产业发展贡献力量。</p>`,
        date: '2026-03-12',
        category: 'platform',
        image: 'https://picsum.photos/800/400?random=2',
        views: 987
    },
    {
        id: 3,
        title: '文旅融合科技创新项目获得省级表彰',
        summary: '甘肃省文旅融合科学数据中心申报的"基于大数据的文旅融合创新平台"项目荣获甘肃省科技进步奖，标志着中心在科技创新方面取得重要突破。',
        content: `<p>甘肃省文旅融合科学数据中心申报的"基于大数据的文旅融合创新平台"项目荣获甘肃省科技进步奖，标志着中心在科技创新方面取得重要突破。</p>
        <p>该项目构建了基于大数据的文旅融合创新平台，整合了全省文旅资源数据，实现了数据的实时监测、智能分析和可视化展示。平台采用先进的大数据技术，构建了完整的数据采集、存储、分析和应用体系，为政府决策、科学研究和公众服务提供了有力支撑。</p>
        <p>评审专家认为，该项目在技术创新、应用效果、社会效益等方面都达到了国内领先水平，具有很好的推广价值。项目的成功实施，为甘肃文旅产业的数字化转型提供了重要技术支撑，也为其他地区开展类似工作提供了有益借鉴。</p>
        <p>中心表示，将以此次获奖为新的起点，继续加大科技创新投入，不断提升平台的技术水平和服务能力，为甘肃文旅产业的高质量发展做出更大贡献。</p>`,
        date: '2026-03-10',
        category: 'activity',
        image: 'https://picsum.photos/800/400?random=3',
        views: 756
    },
    {
        id: 4,
        title: '数据中心发布2026年第一季度旅游趋势报告',
        summary: '报告显示，甘肃省第一季度旅游接待量同比增长15%，其中敦煌莫高窟、张掖丹霞等热门景区游客量创历史新高。',
        content: `<p>甘肃文旅融合科学数据中心发布2026年第一季度旅游趋势报告。报告显示，甘肃省第一季度旅游接待量同比增长15%，其中敦煌莫高窟、张掖丹霞等热门景区游客量创历史新高。</p>
        <p>从游客来源地看，省内游客占比45%，省外游客占比55%。省外游客主要来自陕西、宁夏、青海等周边省份，以及北京、上海、广州等经济发达地区。</p>
        <p>从旅游类型看，文化旅游占比最高，达到40%；其次是生态旅游，占比30%；休闲度假旅游占比20%，其他类型占比10%。</p>
        <p>从景区热度看，敦煌莫高窟、张掖丹霞、鸣沙山月牙泉等传统热门景区继续保持高人气，同时一些新兴景区如黄河三峡、天水麦积山等也受到游客青睐。</p>
        <p>报告预测，随着天气转暖和节假日增多，第二季度旅游市场将继续保持良好态势，预计接待量将比第一季度增长20%以上。</p>`,
        date: '2026-03-08',
        category: 'industry',
        image: 'https://picsum.photos/800/400?random=4',
        views: 2341
    },
    {
        id: 5,
        title: '《甘肃省文旅融合发展条例》正式实施',
        summary: '《甘肃省文旅融合发展条例》于3月1日起正式实施，为全省文旅融合发展提供了法律保障和制度支撑。',
        content: `<p>《甘肃省文旅融合发展条例》于3月1日起正式实施，为全省文旅融合发展提供了法律保障和制度支撑。</p>
        <p>条例共8章62条，从总则、规划与建设、资源保护、产业发展、服务保障、监督管理、法律责任等方面对文旅融合发展作出了全面规定。</p>
        <p>条例明确，县级以上人民政府应当将文旅融合发展纳入国民经济和社会发展规划，建立健全文旅融合发展协调机制。同时，条例对文旅资源的保护、开发和利用作出了具体规定，要求坚持保护优先、合理利用的原则。</p>
        <p>在产业发展方面，条例鼓励发展文旅融合新业态、新模式，支持文旅企业做大做强，推动文旅产业与其他产业融合发展。</p>
        <p>条例的实施，将为甘肃文旅产业的规范化、高质量发展提供有力的法律保障，也将为全国其他地区推进文旅融合发展提供有益借鉴。</p>`,
        date: '2026-03-05',
        category: 'policy',
        image: 'https://picsum.photos/800/400?random=5',
        views: 1876
    },
    {
        id: 6,
        title: '敦煌莫高窟数字化保护项目取得新进展',
        summary: '敦煌研究院与甘肃文旅融合科学数据中心合作，完成莫高窟第285窟高精度数字化采集，为文化遗产保护提供技术支持。',
        content: `<p>敦煌研究院与甘肃文旅融合科学数据中心合作，完成莫高窟第285窟高精度数字化采集，为文化遗产保护提供技术支持。</p>
        <p>莫高窟第285窟是西魏时期的重要洞窟，保存着精美的壁画和雕塑。由于年代久远，洞窟存在不同程度的病害，需要进行科学保护和修复。</p>
        <p>此次数字化采集采用了最新的三维扫描和高清摄影技术，获取了洞窟的完整三维模型和高清图像数据。这些数据不仅可以用于洞窟的保护研究，还可以通过数字技术向公众展示，让更多人了解和欣赏莫高窟的艺术价值。</p>
        <p>敦煌研究院表示，将继续与数据中心合作，推进莫高窟其他洞窟的数字化工作，争取在三年内完成所有重要洞窟的数字化采集。</p>
        <p>甘肃文旅融合科学数据中心表示，将充分发挥技术优势，为文化遗产数字化保护提供全方位支持，推动文化遗产的数字化传承和传播。</p>`,
        date: '2026-03-02',
        category: 'platform',
        image: 'https://picsum.photos/800/400?random=6',
        views: 1543
    },
    {
        id: 7,
        title: '甘肃省智慧旅游平台用户突破百万',
        summary: '甘肃省智慧旅游平台累计注册用户突破100万，平台提供的智能导览、在线预订等服务深受游客欢迎。',
        content: `<p>甘肃省智慧旅游平台累计注册用户突破100万，平台提供的智能导览、在线预订等服务深受游客欢迎。</p>
        <p>智慧旅游平台是甘肃文旅融合科学数据中心的重要组成部分，旨在通过数字技术提升游客体验。平台整合了全省景区、酒店、餐饮、交通等旅游资源，为游客提供一站式旅游服务。</p>
        <p>平台的主要功能包括：智能导览，根据游客偏好推荐旅游路线；在线预订，支持景区门票、酒店、餐饮等在线预订；实时信息，提供景区客流、天气等实时信息；社交分享，游客可以分享旅游体验和照片。</p>
        <p>数据显示，平台用户中，省内用户占60%，省外用户占40%。用户年龄主要集中在25-45岁，占比达到70%。</p>
        <p>平台负责人表示，将继续优化用户体验，丰富服务内容，争取年内用户突破200万，为更多游客提供便捷的智慧旅游服务。</p>`,
        date: '2026-02-28',
        category: 'industry',
        image: 'https://picsum.photos/800/400?random=7',
        views: 2109
    },
    {
        id: 8,
        title: '文旅融合创新大赛报名启动',
        summary: '2026年甘肃文旅融合创新大赛正式启动，面向全国征集文旅融合创新项目和创意方案，推动文旅产业高质量发展。',
        content: `<p>2026年甘肃文旅融合创新大赛正式启动，面向全国征集文旅融合创新项目和创意方案，推动文旅产业高质量发展。</p>
        <p>大赛由甘肃省文化和旅游厅主办，甘肃文旅融合科学数据中心承办，旨在发掘和培育文旅融合创新项目，推动文旅产业创新发展。</p>
        <p>大赛分为两个赛道：创新项目赛道，征集具有创新性和实用性的文旅融合项目；创意方案赛道，征集文旅融合的创意和设计方案。</p>
        <p>参赛对象包括企业、高校、科研机构、个人等。参赛项目或方案应围绕文旅融合主题，具有创新性、可行性和市场前景。</p>
        <p>大赛设置一等奖1名、二等奖2名、三等奖3名，获奖项目将获得资金支持和推广机会。同时，优秀项目将有机会获得投资机构的投资。</p>
        <p>报名截止时间为4月30日，决赛将于6月举行。欢迎广大创新者积极报名参赛，共同推动甘肃文旅产业创新发展。</p>`,
        date: '2026-02-25',
        category: 'activity',
        image: 'https://picsum.photos/800/400?random=8',
        views: 1321
    },
    {
        id: 9,
        title: '丝绸之路文化遗产数字化工程启动',
        summary: '丝绸之路文化遗产数字化工程在兰州启动，计划用三年时间完成甘肃段主要文化遗产的数字化采集和展示。',
        content: `<p>丝绸之路文化遗产数字化工程在兰州启动，计划用三年时间完成甘肃段主要文化遗产的数字化采集和展示。</p>
        <p>工程由甘肃省文化和旅游厅牵头，甘肃文旅融合科学数据中心具体实施，旨在通过数字化技术保护和传承丝绸之路文化遗产。</p>
        <p>工程将重点对甘肃段的重要文化遗产进行数字化采集，包括敦煌莫高窟、麦积山石窟、炳灵寺石窟、拉卜楞寺等。采集内容包括三维扫描、高清摄影、文字记录等。</p>
        <p>数字化成果将通过多种方式向公众展示，包括线上展览、虚拟现实体验、互动游戏等，让更多人了解和欣赏丝绸之路文化遗产的魅力。</p>
        <p>工程负责人表示，数字化是文化遗产保护的重要手段，也是文化遗产传播的有效途径。通过数字化工程，可以让文化遗产"活"起来，更好地发挥其社会价值和文化价值。</p>`,
        date: '2026-02-22',
        category: 'platform',
        image: 'https://picsum.photos/800/400?random=9',
        views: 1654
    },
    {
        id: 10,
        title: '甘肃省文旅产业投资洽谈会圆满举行',
        summary: '甘肃省文旅产业投资洽谈会在兰州举行，现场签约项目28个，总投资额达156亿元，为文旅产业发展注入新动力。',
        content: `<p>甘肃省文旅产业投资洽谈会在兰州举行，现场签约项目28个，总投资额达156亿元，为文旅产业发展注入新动力。</p>
        <p>洽谈会由甘肃省文化和旅游厅主办，吸引了来自全国各地的投资机构、文旅企业、金融机构等200多家单位参加。</p>
        <p>签约项目涵盖文旅景区开发、文旅综合体建设、文旅装备制造、文旅数字平台等多个领域。其中，投资额超过10亿元的项目有5个，超过5亿元的项目有12个。</p>
        <p>这些项目的落地，将有力推动甘肃文旅产业的转型升级，提升文旅产品的品质和竞争力，为经济社会发展注入新的活力。</p>
        <p>甘肃省文化和旅游厅表示，将继续优化营商环境，完善政策措施，为文旅产业发展创造良好条件。同时，欢迎更多投资者来甘肃投资兴业，共同推动文旅产业高质量发展。</p>`,
        date: '2026-02-20',
        category: 'industry',
        image: 'https://picsum.photos/800/400?random=10',
        views: 1987
    },
    {
        id: 11,
        title: '《甘肃省数字文旅发展三年行动计划》发布',
        summary: '甘肃省发布《数字文旅发展三年行动计划（2026-2028）》，明确数字文旅发展目标和重点任务，推动文旅产业数字化转型。',
        content: `<p>甘肃省发布《数字文旅发展三年行动计划（2026-2028）》，明确数字文旅发展目标和重点任务，推动文旅产业数字化转型。</p>
        <p>行动计划提出，到2028年，全省数字文旅基础设施更加完善，数字文旅应用更加广泛，数字文旅产业更加壮大，数字文旅发展水平达到全国先进水平。</p>
        <p>行动计划明确了六大重点任务：一是完善数字文旅基础设施，包括网络建设、数据中心建设、平台建设等；二是推进文旅资源数字化，包括景区数字化、文化遗产数字化、文创产品数字化等；三是发展数字文旅新业态，包括智慧旅游、在线旅游、数字文创等；四是培育数字文旅企业，支持企业做大做强；五是培养数字文旅人才，建设专业人才队伍；六是完善数字文旅政策，优化发展环境。</p>
        <p>行动计划还提出了一系列保障措施，包括加强组织领导、加大资金投入、强化考核评估等，确保各项任务落到实处。</p>`,
        date: '2026-02-18',
        category: 'policy',
        image: 'https://picsum.photos/800/400?random=11',
        views: 1432
    },
    {
        id: 12,
        title: '文旅融合数据中心与高校签署合作协议',
        summary: '甘肃文旅融合科学数据中心与兰州大学、西北师范大学等高校签署战略合作协议，共同推进文旅数据研究和人才培养。',
        content: `<p>甘肃文旅融合科学数据中心与兰州大学、西北师范大学等高校签署战略合作协议，共同推进文旅数据研究和人才培养。</p>
        <p>根据协议，双方将在以下几个方面开展合作：一是共建研究平台，联合开展文旅数据研究；二是共享数据资源，为高校教学科研提供数据支持；三是联合培养人才，共同培养文旅数据专业人才；四是共建实习基地，为学生提供实践机会。</p>
        <p>高校方面表示，将充分发挥学科和人才优势，为文旅数据研究提供智力支持，同时通过合作提升学生的实践能力。</p>
        <p>数据中心表示，将积极为高校提供数据和实践平台，支持高校开展教学科研工作，同时也希望通过合作吸引更多优秀人才加入文旅数据事业。</p>
        <p>此次合作，是产学研结合的有益探索，将有力推动文旅数据研究和人才培养，为甘肃文旅产业发展提供人才支撑。</p>`,
        date: '2026-02-15',
        category: 'platform',
        image: 'https://picsum.photos/800/400?random=12',
        views: 1123
    }
])

const newsDetail = ref(null)

const categoryMap = {
    platform: '平台动态',
    policy: '政策法规',
    industry: '行业资讯',
    activity: '活动通知'
}

const getCategoryLabel = (category) => {
    return categoryMap[category] || '新闻'
}

const relatedNews = computed(() => {
    if (!newsDetail.value) return []
    return allNews.value
        .filter(news => news.id !== newsDetail.value.id && news.category === newsDetail.value.category)
        .slice(0, 3)
})

onMounted(() => {
    const newsId = parseInt(route.params.id)
    newsDetail.value = allNews.value.find(news => news.id === newsId)
    
    if (!newsDetail.value) {
        router.push('/news')
    }
})

const goToDetail = (id) => {
    router.push(`/news/${id}`)
    window.scrollTo({ top: 0, behavior: 'smooth' })
}
</script>

<style scoped>
.news-detail-page {
    min-height: 100vh;
    background: #f5f5f5;
}

.news-detail-content {
    padding: 40px 0;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 15px;
}

.back-link {
    margin-bottom: 20px;
}

.back-btn {
    display: inline-block;
    padding: 8px 16px;
    background: #fff;
    color: #666;
    text-decoration: none;
    border-radius: 4px;
    transition: all 0.3s;
    box-shadow: 0 2px 5px rgba(0,0,0,0.05);
}

.back-btn:hover {
    background: #1a237e;
    color: #fff;
}

.news-article {
    background: #fff;
    border-radius: 8px;
    padding: 40px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
    margin-bottom: 40px;
}

.article-header {
    margin-bottom: 30px;
    padding-bottom: 20px;
    border-bottom: 1px solid #e0e0e0;
}

.article-title {
    font-size: 32px;
    color: #1a237e;
    font-weight: 600;
    margin: 0 0 20px 0;
    line-height: 1.4;
}

.article-meta {
    display: flex;
    gap: 20px;
    align-items: center;
}

.article-category {
    display: inline-block;
    padding: 4px 12px;
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
    font-size: 12px;
    border-radius: 12px;
}

.article-date,
.article-views {
    font-size: 14px;
    color: #999;
}

.article-image {
    width: 100%;
    margin-bottom: 30px;
    border-radius: 8px;
    overflow: hidden;
}

.article-image img {
    width: 100%;
    height: auto;
    display: block;
}

.article-body {
    font-size: 16px;
    line-height: 1.8;
    color: #333;
}

.article-summary {
    font-size: 18px;
    color: #1a237e;
    font-weight: 500;
    margin-bottom: 20px;
    line-height: 1.6;
}

.article-content {
    margin-top: 20px;
}

.article-content p {
    margin-bottom: 15px;
}

.article-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 40px;
    padding-top: 20px;
    border-top: 1px solid #e0e0e0;
}

.article-tags {
    display: flex;
    gap: 10px;
}

.tag {
    padding: 6px 14px;
    background: #f0f0f0;
    color: #666;
    font-size: 13px;
    border-radius: 4px;
}

.article-share {
    display: flex;
    gap: 10px;
}

.share-btn {
    padding: 8px 20px;
    border: 1px solid #e0e0e0;
    background: #fff;
    color: #666;
    font-size: 14px;
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.3s;
}

.share-btn:hover {
    border-color: #005da6;
    color: #005da6;
}

.related-news {
    background: #fff;
    border-radius: 8px;
    padding: 30px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
}

.related-news h3 {
    font-size: 24px;
    color: #1a237e;
    font-weight: 600;
    margin: 0 0 20px 0;
}

.related-list {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
}

.related-item {
    cursor: pointer;
    transition: transform 0.3s;
}

.related-item:hover {
    transform: translateY(-5px);
}

.related-image {
    width: 100%;
    height: 150px;
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 10px;
}

.related-image img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.related-info h4 {
    font-size: 15px;
    color: #333;
    font-weight: 500;
    margin: 0 0 5px 0;
    line-height: 1.4;
}

.related-date {
    font-size: 12px;
    color: #999;
}

@media (max-width: 1024px) {
    .related-list {
        grid-template-columns: repeat(2, 1fr);
    }
}

@media (max-width: 768px) {
    .news-article {
        padding: 20px;
    }
    
    .article-title {
        font-size: 24px;
    }
    
    .article-meta {
        flex-wrap: wrap;
    }
    
    .article-footer {
        flex-direction: column;
        gap: 15px;
    }
    
    .related-list {
        grid-template-columns: 1fr;
    }
}
</style>
