<template>
    <div id="app">
        <HeaderComponent />
        <CarouselComponent />
        <DataOverviewComponent />
        <NewsSectionComponent />
        <DataResourcesComponent />
        <SpecialTopicComponent />
        <ModelResourcesComponent />
        <SciencePopularizationComponent />
        <PlatformIntroComponent />
        <FooterComponent />
    </div>
</template>

<script>
import HeaderComponent from './components/HeaderComponent.vue'
import CarouselComponent from './components/CarouselComponent.vue'
import DataOverviewComponent from './components/DataOverviewComponent.vue'
import NewsSectionComponent from './components/NewsSectionComponent.vue'
import DataResourcesComponent from './components/DataResourcesComponent.vue'
import SpecialTopicComponent from './components/SpecialTopicComponent.vue'
import ModelResourcesComponent from './components/ModelResourcesComponent.vue'
import SciencePopularizationComponent from './components/SciencePopularizationComponent.vue'
import PlatformIntroComponent from './components/PlatformIntroComponent.vue'
import FooterComponent from './components/FooterComponent.vue'

export default {
    name: 'App',
    components: {
        HeaderComponent,
        CarouselComponent,
        DataOverviewComponent,
        NewsSectionComponent,
        DataResourcesComponent,
        SpecialTopicComponent,
        ModelResourcesComponent,
        SciencePopularizationComponent,
        PlatformIntroComponent,
        FooterComponent
    }
}
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Microsoft YaHei', Arial, sans-serif;
    background-color: #f5f5f5;
    color: #333;
    line-height: 1.6;
}

a {
    text-decoration: none;
    color: #333;
    transition: color 0.3s;
}

a:hover {
    color: #005da6;
}

ul {
    list-style: none;
}

#app {
    min-height: 100vh;
}
</style>
