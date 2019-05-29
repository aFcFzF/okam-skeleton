<template>
    <div :class="sknCls">
        <div class="placeholder-wrap" ref="wrap">
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

    .placeholder-wrap
        position absolute
        top 0
        left 0
        right 0
        bottom 0
        z-index 10
        opacity 0
        visibility hidden

    &.loading
        .placeholder-wrap
            visibility visible
            opacity 1

    &.fade
        .placeholder-wrap
            transition opacity .3s linear, visibility .3s .2s

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
         * 展示类型
         * 0. 首屏 1.
         */
        type: {
            type: String,
            default: 0
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
            // 控制骨架图至少展示.4s
            showLoading: true,
            // loading开始时间
            startTime: Date.now(),
            // 控制子placeholder动画时间
            activeBgPos: 100,
            activeTimer: null
        };
    },

    watch: {
        // 骨架图至少展示400毫秒，防止主界面因资源没加载完抖动
        loading(val) {
            if (!val) {
                const remain = 400 - (Date.now() - this.startTime);
                // chrome 和 ios 设置delay < 0 没问题，但安卓会不触发。
                setTimeout(() => {
                    this.showLoading = val;
                    this.$emit('statusChanged', val);
                }, remain > 0 ? remain : 0);
            }
            else {
                this.showLoading = val;
                this.$emit('statusChanged', val);
            }
        },
        active(val) {
            if (!val) {
                clearInterval(this.activeTimer);
                this.activeBgPos = 100;
                this.$broadcast('activeChanged.Placeholder', false);
                this.$broadcast('activeBgPosChanged.Placeholder', 100);
            }
            else {
                this.startActive();
                this.$broadcast('activeChanged.Placeholder', true);
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

    methods: {
        startActive() {
            this.activeTimer = setInterval(() => {
                const val = this.activeBgPos;
                const pos = this.activeBgPos = val === 100 ? 0 : 100;
                this.$emit('bgPosChange', pos);
                this.$broadcast('activeBgPosChanged.Placeholder', pos);
            }, 800);
        }
    },

    created() {
        this.showLoading = true;
        this.active && this.startActive();
    }
};
</script>
