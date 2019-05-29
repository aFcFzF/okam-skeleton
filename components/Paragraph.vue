<template>
    <div :class="['skeleton-item', active ? 'active' : '', split ? 'line' : '']">
        <!-- background-position-x 不支持computed -->
        <p
            class="title gray row"
            v-if="title"
            :style="{
                width: titleWidth,
                backgroundPositionX: activeBgPos + '%',
                transition: activeBgPos === 0 ? 'background linear 800ms' : ''
            }"
        >
        </p>
        <div :class="['content']" >
            <div :class="['content-item', vertical ? 'vertical' : '']" v-for="col of colsCount" :key="'__index__' + col" :style="colSyl">
                <div
                    :class="['avatar', 'gray', shape, size, align]"
                    v-if="avatar"
                    :style="{
                        backgroundPositionX: activeBgPos + '%',
                        transition: activeBgPos === 0 ? 'background linear 800ms' : ''
                    }"
                ></div>
                <ul :class="['paragraph', align]" v-if="paragraph">
                    <li
                        :class="['row', 'gray', align]"
                        v-for="(v, idx) of rowsCount"
                        :key="'_row_' + v"
                        :style="{
                            marginTop: !vertical && idx === 0 ? '0px' : false,
                            width: rowsWdt[v],
                            backgroundPositionX: activeBgPos + '%',
                            transition: activeBgPos === 0 ? 'background linear 800ms' : ''
                        }"
                    ></li>
                </ul>
            </div>
        </div>
    </div>
</template>

<style lang="stylus" scoped>
$gray = #f2f2f2

.skeleton-item
    padding 24px 0

    .gray
        background $gray

    &.active
        .gray
            background linear-gradient(90deg, #f2f2f2 25%, #e6e6e6 37%, #f2f2f2 63%)
            background-size 400% 100%
            background-position-y 50%
    .row
        height 48px
        margin 15px 0
        line-height 1.2

    .title
        margin 0
        margin-bottom 30px

    .content
        display flex
        flex-flow row nowrap
        justify-content space-between

        .content-item
            display flex
            align-items flex-start
            flex-shrink 0

            &.vertical
                flex-direction column
                align-items center

                .avatar
                    margin 0

                .paragraph
                    width 100%
                    // align-items center

                    .row
                        margin-top 30px

                        &.center
                            align-self center

                        &.end
                            align-self flex-end

            .avatar,
            .paragraph
                display flex
                flex-direction column
                flex-grow 1
                flex-shrink 0
                align-self stretch

                &.center
                    align-self center

                &.end
                    align-self flex-end

            .avatar
                flex-grow 0
                width 120px
                height 120px
                border-radius 50%
                margin-right 25px

                &.small
                    width 90px
                    height 90px

                &.large
                    width 150px
                    height 150px

                &.square
                    border-radius 0

                &.fillet
                    border-radius 15px

    &.line
        border-bottom solid 3px $gray

    &.gap
        border-bottom solid 30px $gray

</style>

<script>
export default {
    name: 'Paragraph',
    /* eslint-disable fecs-properties-quote */
    props: {
        title: {
            type: Boolean,
            default: false
        },
        titleWidth: {
            type: String,
            default: '90px'
        },
        vertical: {
            type: Boolean,
            default: false
        },
        avatar: {
            type: Boolean,
            default: false
        },
        align: {
            type: String,
            default: 'start',
            validator(val) {
                return ['start', 'center', 'end'].includes(val);
            }
        },
        paragraph: {
            type: Boolean,
            default: true
        },
        rows: {
            type: Number,
            default: 1
        },
        rowsWidth: {
            type: String | Array,
            default: '100%'
        },
        cols: {
            type: Number,
            default: 1
        },
        colsWidth: {
            type: String | Array,
            default: '100%'
        },
        split: {
            type: Boolean,
            default: false
        },
        active: {
            type: Boolean,
            default: false
        },
        size: {
            type: String,
            validator(val) {
                return ['large', 'small', 'default'].includes(val);
            },
            default: 'default'
        },
        shape: {
            type: String,
            defalut: 'circle',
            validator(val) {
                return ['circle', 'square', 'fillet'].includes(val);
            }
        }
    },
    /* eslint-enable fecs-properties-enable */

    data() {
        return {
            // active动画相关
            activeBgPos: 100,
            activeTimer: null
        };
    },

    computed: {
        rowsCount() {
            // okam的v-for number标识符 似乎无效，转换下
            return Array.from({length: this.rows}, (_, i) => i);
        },
        rowsWdt() {
            const wdt = this.rowsWidth;
            let per = '100%';
            const r = Array.from({length: this.rows});
            if (typeof wdt === 'string') {
                per = wdt;
            }
            else if (Array.isArray(wdt)) {
                return wdt;
            }
            return r.fill(per);
        },
        colsCount() {
            return Array.from({length: this.cols}, (_, i) => i);
        },
        colsWdt() {
            const wdt = this.colsWidth;
            let per = '100%';
            const r = Array.from({length: this.cols});
            if (typeof wdt === 'string') {
                per = wdt;
            }
            else if (Array.isArray(wdt)) {
                return wdt;
            }
            return r.fill(per);
        },
        colSyl() {
            return {
                // 如果有多列，则增加间隔
                'flex-basis': this.cols === 1 ? 1 : Math.floor((100 - 10) / this.cols) + '%',
                'flex-grow': this.cols > 1 ? 0 : 1
            };
        }
    },

    watch: {
        active(val) {
            if (!val) {
                clearInterval(this.activeTimer);
                this.activeBgPos = 100;
            }
            else {
                this.startActive();
            }
        }
    },

    methods: {
        startActive() {
            this.activeTimer = setInterval(() => {
                const val = this.activeBgPos;
                this.activeBgPos = val === 100 ? 0 : 100;
            }, 800);
        }
    },

    mounted() {
        this.active && this.startActive();
    }
};
</script>

