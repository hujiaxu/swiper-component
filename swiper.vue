
<template>
    <div class="swiper">
        <div
            class="swiper-container"
            @touchstart="touchstart"
            @touchmove="touchmove"
            @touchend="touchend"
        >
            <div 
                class="swiper-item"
                :class="activeIndex === index ? 'swiper-item-active' : ''"
                v-for="(item, index) in swiperArr"
                :key="index"
            >
                <slot>20210716</slot>
            </div>
        </div>
        <div class="dots" v-if="showDots">
            <div 
                class="dot"
                :class="activeIndex === index ? 'dot-active' : ''"
                v-for="(item, index) in swiperArr"
                :key="index"
            >
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'SwiperComponent',
        props: {
            showNext: { // 是否显示下一个 元素
                type: Boolean,
                default: true
            },
            showDots: { // 是否显示小圆点
                type: Boolean,
                default: true,
            }
        },
        data() {
            return {
                swiperArr: new Array(2),
                activeIndex: 0
            }
        },
        mounted() {
            this.el = document.querySelector('.swiper-container')           // 容器
            this.itemEls = document.querySelectorAll('.swiper-item')        // 普通元素
            this.itemActive = document.querySelector('.swiper-item-active') // 当前元素
            this.heightActive = this.itemActive.offsetHeight                // 当前元素的高度
            this.height = this.itemEls[1 - this.activeIndex].offsetHeight                      // 普通元素的高度
            this.width = this.itemEls[0].offsetWidth                        // 普通元素宽度，每次滑动的位移
            this.percent = (this.heightActive - this.height) / this.width   // 偏移距离的百分比
            this.length = this.itemEls.length                               // 元素的数量
            this.style = this.el.style                                      // 元素的样式，用于在滑动过程中改变，易于操作
            this.left = 0                                                   // 每次滑动开始计算 容器元素 距 视图的左边距
            this.minLeft = 0                                                // 左极限值，向右滑动时，容器元素 距 视图的最小左边距
            this.maxLeft = (this.length - 1) * this.width                   // 右极限值，向左滑动时，容器元素 距 视图的最大左边距
            this.state = 0                                                  // 判断状态，防止 意外触发 touchmove
            this.X = 0                                                      // 初始值，记录手指 touchstart 时的 x坐标
            this.moveX = 0                                                  // 过程变化值，手指滑动时 相对 this.X 的 偏移距离
            this.moveFlag = this.width / 3                                  // 偏移的闸阀，超过这个值才会进行 整个 width 距离的偏移，否则回到原位
            console.log(document.querySelector('.test'))
        },
        methods: {
            // 在 touchstart,touchmove,touchend三个函数中
            // 自变量：this.moveX，this.X
            // 因变量：this.left，this.style.left，this.activeIndex，
            touchstart(e) {
                this.state = 1
                this.left = this.el.offsetLeft
                const { clientX } = e.touches[0]
                this.X = clientX
            },
            touchmove(e) {
                if (this.state !== 1) {
                    return
                }   
                this.moveX = e.touches[0].clientX - this.X
                this.heightChange(this.moveX)
                this.style.left = this.left + this.moveX + 'px'             
            },
            touchend() {
                const moveX = Math.abs(this.moveX)
                const left = Math.abs(this.left)
                if (moveX < this.moveFlag) {
                    this.style.left = this.left + 'px'
                } else {
                    let step = Math.ceil(left / this.width)
                    if (this.moveX > 0) {
                        step--
                        if (step <= 0) {
                            step = 0
                        }
                        this.itemEls[0].style.marginLeft = 0 + 'px'
                        if (this.left <= 0 && Number.parseFloat(this.style.left) >= 0) {
                            this.style.left = 0 + 'px'
                        } else {
                            this.style.left = -step * this.width + 'px'
                        }
                    } else {
                        step++ 
                        if (step >= this.length - 1) {
                            step = this.length - 1
                        }
                        this.itemEls[0].style.marginLeft = '0.1%'
                        if (left >= this.maxLeft && Math.abs(Number.parseFloat(this.style.left)) >= this.maxLeft) {
                            this.style.left = this.left + 'px'
                        } else {
                            this.style.left = -step * this.width + 'px'
                        }
                    }
                    this.activeIndex = step
                    this.itemEls[this.activeIndex].style.height = this.heightActive + 'px'
                    this.itemEls[1 - this.activeIndex].style.height = this.height + 'px'
                }
            },
            vtouchend() {
                this.itemEls[this.activeIndex].style.height = this.heightActive + 'px'
                this.itemEls[1 - this.activeIndex].style.height = this.height + 'px'
                // 目前 左，右不同方向的滑动
                if (this.moveX > 0) { // 向右滑动
                    if (this.moveX < this.moveFlag) {
                        this.style.left = this.left + 'px'
                    } else if (this.moveX >= this.moveFlag) {
                        let step = Math.ceil(Math.abs(this.left) / this.width)
                        step--
                        this.itemEls[0].style.marginLeft = 0 + 'px'
                        if (this.left <= 0 && Number.parseFloat(this.style.left) >= 0) {
                            this.style.left = 0 + 'px'
                        } else {
                            this.style.left = -this.width * step + 'px'
                            this.activeIndex = Math.abs(step)
                        }
                    }
                }

                if (this.moveX < 0) { // 向左滑动
                    const moveX = Math.abs(this.moveX)
                    const left = Math.abs(this.left)
                    if (moveX < this.moveFlag) {
                        this.style.left = this.left + 'px'
                    } else if (moveX >= this.moveFlag) {
                        let step = Math.ceil(left / this.width)
                        step++
                        this.itemEls[0].style.marginLeft = '0.05%'
                        if (left >= this.maxLeft && Math.abs(Number.parseFloat(this.style.left)) >= this.maxLeft) {
                            this.style.left = this.left + 'px'
                        } else {
                            this.style.left = -step * this.width + 'px'
                            this.activeIndex = step
                        }
                    }
                }
            },
            heightChange(movex) {
                const x = Math.abs(movex)
                const itemEl = this.itemEls[1 - this.activeIndex]
                const style = itemEl.style
                const styleActive = this.itemEls[this.activeIndex].style
                style.height = this.height + x * this.percent + 'px'
                styleActive.height = this.heightActive - x * this.percent + 'px'
                console.log(this.percent)
            }
        },
    }
</script>

<style>
    body {
        padding: 0;
        margin: 0;
    }
</style>
<style scoped>
        /* overflow: hidden; 没有加overflow: hidden; 为什么swiper的高度会很低 */
    .swiper {
        overflow: hidden;
        position: relative;
        margin: 0 auto;
        height: 600px;
        padding: 30px 0;
        width: 50vw;
        background: #fff;
    }
    .swiper .swiper-container {
        position: relative;
        display: flex;
        align-items: center;
        left: 0;
        width: 10000%;
        padding: 15px 0;
        transition: left .2s ease;
    }
    .swiper .swiper-container .swiper-item {
        width: 0.9%;
        margin-right: 0.05%;
        background: #acacac;
        height: 270px;
        transition: all .2s ease;

        text-align: center;
        color: #fff;
        font-size: 30px;
        line-height: 10;
    }
    .swiper .swiper-container .swiper-item-active {
        height: 300px;
        background: #adadad;
        transition: all .2s ease;
        box-shadow: 2px 2px 19px rgba(0, 0, 0, 0.3);
    }
    .swiper .dots {
        position: absolute;
        top: 370px;
        left: 50%;
        transform: translateX(-50%);
        display: flex;
        justify-content: space-around;
        width: 50%;
        height: 20px;
    }
    .swiper .dots .dot {
        width: 20px;
        height: 20px;
        border-radius: 10px;
        background: #ccc;
    }
    .swiper .dots .dot-active {
        width: 40px;
        background: #aaa;
    }
</style>