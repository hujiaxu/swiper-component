<template>
    <div>
        <swiper
        >
            
            <div 
                class="swiper-item"
                :class="activeIndex === index ? 'swiper-item-active' : ''"
                v-for="(item, index) in swiperArr"
                :key="index"
            >
                hujiaxu
            </div>
        </swiper>
    </div>
</template>

<script>
    import Swiper from './swiper.vue'
    export default {
        name: 'SwiperComponent',
        components: {
            Swiper
        },
        data() {
            return {
                swiperArr: new Array(2),
                activeIndex: 0
            }
        },
    }
</script>

<style lang="scss" scoped>

</style>