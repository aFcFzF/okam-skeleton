<template>
    <div :class="sknCls">
        <div class="placeholder-wrap" v-if="placeholder && showPlaceholder">
            <slot name="placeholder"></slot>
        </div>
        <div class="screen-skeleton-wrap">
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
        width 100%
        height 100%
        z-index -1

    &-wrap
        opacity 1

    &.loading
        {prefix}-wrap
            opacity 0

    &.fade
        {prefix}-wrap
            transition opacity .3s
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

        placeholder: {
            type: Boolean,
            default: false
        },

        /**
         * 是否显示加载动画
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
         * 延迟骨架消失,单位ms。场景：骨架完全贴合且具有过渡效果
         */
        delaySknHide: {
            type: Number,
            default: 0
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
                // this.showFade = this.fade;
                const remain = 1200 - (Date.now() - this.startTime);
                // chrome 和 ios 设置delay < 0 没问题，但安卓会不触发。
                setTimeout(() => {
                    this.showLoading = val;
                    setTimeout(() => this.showPlaceholder = val, this.delaySknHide);
                }, remain >= 0 && remain || 0);
            }
            else {
                this.showLoading = this.showPlaceholder = val;
            }
        }
    },

    computed: {
        sknCls() {
            return [
                'screen-skeleton',
                this.showLoading ? 'loading' : '',
                this.fade ? 'fade' : ''
            ];
        }
        // sknSyl() {
        //     const pathResolve = url => (/^http/.test(url) ? url : '../../' + url);
        //     const bg = this.placeholder ? pathResolve(this.placeholder) : 'unset';
        //     return {
        //         'background-image': this.showLoading ? `url(${bg})` : 'unset',
        //         'background-size': this.showLoading ? '100% auto' : 'unset'
        //     };
        // }
    },

    created() {
        this.showLoading = true;
        this.showPlaceholder = this.placeholder;
        // this.showFade = false;
    }
};
</script>
