<template>
    <header class="header">
        <div class="header-top">
            <div class="container">
                <div class="logo">
                    <div class="logo-icon"></div>
                    <h1>甘肃文旅融合科学数据中心</h1>
                </div>
                <div class="header-links">
                    <a href="javascript:;">登录</a>
                    <a href="javascript:;">注册</a>
                    <a href="javascript:;">中文</a>
                </div>
            </div>
        </div>
        <nav class="main-nav">
            <div class="container">
                <ul>
                    <li v-for="item in navItems" :key="item.path">
                        <a :href="item.href" :class="{ active: currentItem === item.path }">{{ item.name }}</a>
                    </li>
                </ul>
            </div>
        </nav>
    </header>
</template>

<script>
export default {
    name: 'HeaderComponent',
    data() {
        return {
            currentItem: '首页',
            navItems: [
                { name: '首页', path: 'home', href: 'javascript:;' },
                { name: '综合新闻', path: 'news', href: 'javascript:;' },
                { name: '数据资源', path: 'data', href: 'javascript:;' },
                { name: '专题数据', path: 'topic', href: 'javascript:;' },
                { name: '模型资源', path: 'model', href: 'javascript:;' },
                { name: '科普专栏', path: 'popularization', href: 'javascript:;' },
                { name: '平台介绍', path: 'intro', href: 'javascript:;' }
            ]
        }
    }
}
</script>

<style scoped>
.header {
    background: linear-gradient(135deg, #1a237e 0%, #283593 100%);
    box-shadow: 0 2px 10px rgba(0,0,0,0.3);
}

.header-top {
    background: rgba(0,0,0,0.2);
    padding: 10px 0;
}

.header-top .container {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.logo {
    display: flex;
    align-items: center;
    color: #fff;
}

.logo-icon {
    width: 40px;
    height: 40px;
    margin-right: 15px;
    background: linear-gradient(135deg, #4fc3f7 0%, #00bcd4 100%);
    border-radius: 8px;
}

.logo h1 {
    font-size: 20px;
    font-weight: 600;
}

.header-links {
    display: flex;
    gap: 20px;
}

.header-links a {
    color: #fff;
    font-size: 14px;
}

.header-links a:hover {
    color: #4fc3f7;
}

.main-nav {
    background: #fff;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.main-nav .container {
    display: flex;
    justify-content: center;
}

.main-nav ul {
    display: flex;
    gap: 0;
}

.main-nav li {
    position: relative;
}

.main-nav li a {
    display: block;
    padding: 15px 25px;
    color: #333;
    font-weight: 500;
    font-size: 16px;
}

.main-nav li a:hover,
.main-nav li a.active {
    background: linear-gradient(135deg, #005da6 0%, #0079b8 100%);
    color: #fff;
}
</style>
