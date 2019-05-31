<template>
    <div :class="sknCls" :style="{height}">
        <div class="placeholder-wrap" ref="placeWrap">
            <slot name="placeholder"></slot>
        </div>
        <!-- 直接对内容opacity 0是防止内容先于骨架屏渲染出来 -->
        <div class="content-wrap" style="opacity: 0">
            <slot></slot>
        </div>
    </div>
</template>

<style lang="stylus" scoped>
clearfix()
    &::before,
    &::after
        content ''
        display table

    &::before
        clear both

    zoom 1

.screen-skeleton
    position relative
    clearfix()

    .placeholder-wrap
        position absolute
        top 0
        bottom 0
        left 0
        right 0
        z-index 9
        opacity 0
        visibility hidden

    &.loading
        .placeholder-wrap
            visibility visible
            opacity 1

    &.fade
        .placeholder-wrap
            transition opacity .3s linear, visibility .3s .2s

    &.overflow
        overflow hidden

    &.show
        overflow visible

        .content-wrap
            opacity 1 !important
</style>

<script>
export default {
    /* eslint-disable fecs-properties-quote */
    name: 'Skeleton',

    props: {
        /**
         * 是否显示骨架
         */
        loading: {
            type: Boolean,
            default: true
        },

        /**
         * 骨架屏切换实体fade效果
         */
        fade: {
            type: Boolean,
            default: false
        },

        /**
         * 只展示1屏
         */
        overflow: {
            type: Boolean,
            default: false
        },

        /**
         * 骨架屏大小, 默认全屏
         */
        height: {
            type: String,
            default: '100vh'
        },

        /**
         * 切换动画效果
         */
        active: {
            type: Boolean,
            default: false
        }
        /* eslint-enable fecs-properties-quote */
    },

    data() {
        return {
            // showLoading: this.loading // okam初始化顺序不对？
            // 控制骨架屏至少展示600ms
            showLoading: true,
            // loading开始时间
            startTime: 0,
            // fade第二帧加上去，避免首次出现
            showFade: false
        };
    },

    watch: {
        // 骨架屏至少展示600毫秒，防止主界面因资源没加载完抖动
        loading(val) {
            if (!val) {
                this.showFade = this.fade;
                const remain = 600 - (Date.now() - this.startTime);
                const delay = remain > 0 ? remain : 0;
                // chrome 和 ios 设置delay < 0 没问题，但安卓会不触发。
                setTimeout(() => {
                    this.showLoading = val;
                    this.$emit('statusChanged', val);
                }, delay);
            }
            else {
                this.showLoading = val;
                setTimeout(() => this.showFade = this.fade, 100);
                this.$emit('statusChanged', val);
            }
        },
        active(val) {
            this.$broadcast('activeChanged.Placeholder', val);
        }
    },

    computed: {
        sknCls() {
            return [
                'screen-skeleton',
                this.showLoading ? 'loading' : 'show',
                this.showFade ? 'fade' : '',
                this.overflow ? 'overflow' : ''
            ];
        }
    },

    created() {
        this.showLoading = this.loading;
        this.startTime = Date.now();
    },

    mounted() {
        // 挂载后添加fade效果，避免闪屏
        setTimeout(() => this.showFade = this.fade, 100);
    }
};
</script>
