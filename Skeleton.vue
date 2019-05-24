<template>
    <div :class="sknCls">
        <div class="placeholder-wrap">
            <slot name="placeholder"></slot>
        </div>
        <div class="content-wrap" style="opacity: 0">
            <slot></slot>
        </div>
    </div>
</template>

<style lang="stylus" scoped>
prefix = '.screen-skeleton'

{prefix}
    height 100%
    position relative
    overflow hidden

    .placeholder-wrap
        position absolute
        top 0
        left 0
        right 0
        bottom 0
        z-index 10
        overflow hidden
        opacity 0

    &.loading
        .placeholder-wrap
            opacity 1

    &.fade
        .placeholder-wrap
            transition opacity .3s

    &.show
        .content-wrap
            opacity 1 !important
</style>

<script>
export default {
    /* eslint-disable fecs-properties-quote */
    name: 'Skeleton',
    props: {
        /**
         * 展示类型
         */
        type: {
            type: String,
            default: 'full'
        },

        /**
         * 是否显示加载动画(首屏需改进)
         */
        active: {
            type: Boolean,
            default: true
        },

        /**
         * 骨架图切换实体fade效果
         */
        fade: {
            type: Boolean,
            default: false
        },

        /**
         * 是否显示骨架
         */
        loading: {
            type: Boolean,
            default: true
        }
        /* eslint-enable fecs-properties-quote */
    },

    data() {
        return {
            // showLoading: this.loading // okam初始化顺序不对？
            // 控制骨架图至少展示1s
            showLoading: true,
            // 和placeholderHideOrder有关
            showPlaceholder: true,
            // 1200ms之后是否有fade效果
            showFade: false,
            // loading开始时间
            startTime: Date.now()
        };
    },

    watch: {
        // 骨架图至少展示1.2秒，防止主界面因资源没加载完抖动
        loading(val) {
            if (!val) {
                const remain = 1200 - (Date.now() - this.startTime);
                // chrome 和 ios 设置delay < 0 没问题，但安卓会不触发。
                setTimeout(() => this.showLoading = val, remain >= 0 && remain || 0);
            }
            else {
                this.showLoading = val;
            }
        }
    },

    computed: {
        sknCls() {
            return [
                'screen-skeleton',
                this.showLoading ? 'loading' : 'show',
                this.fade ? 'fade' : ''
            ];
        }
    },

    created() {
        this.showLoading = true;
    }
};
</script>
