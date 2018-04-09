<template>
    <div class="AnalyticsPanel col-xs-12 col-sm-6 col-md-3 col-lg-3">
        <div class="panel analytics-panel">
            <header class="analytics-panel__header top-header">
                <div class="analytics-panel__left">
                    <h2 class="top-header__title">{{ title }}</h2>
                </div>
                <div class="analytics-panel__right">
                    <p class="top-header__sub-title"><em>last 12 months</em></p>
                </div>
            </header>
            <main class="analytics-panel__amount amount">
                <p class="amount__title">{{ numberFormatted(amount) }}
                    <span class="amount__comment">{{ amountComment }}</span>
                </p>
            </main>
            <div class="analytics-panel__chart"
                 ref="chart"
            >
                <analytics-chart
                        v-if="chartWidth"
                        :labels="labels"
                        :data="chartData"
                        :color="color"
                        :width="chartWidth"
                        :height="chartHeight"
                />
            </div>
            <div class="list">
                <div class="list__header">
                    <h2 class="top-header__title">top {{ listTitle }}</h2>
                </div>
                <div class="list__line"
                     v-for="(item, index) in topList"
                     :key="index"
                     :class="{'list__line--bordered': index}"
                >
                    <div class="list__left">
                        <div class="list__ava"
                             v-if="item.ava"
                             :style="{'background-image' : `url(${ item.ava })`}"
                        />
                        <div class="list__status"
                             v-else
                             :class="{
                                 'list__status--active': item.active,
                                 'list__status--inactive': item.inactive,
                                 'list__status--draft': item.draft,
                             }"
                        />
                        <div class="list__name">{{ item.name }}</div>
                    </div>
                    <div class="list__right">
                        <div class="list__amount"
                             v-if="units === 'hours'"
                        >
                            {{ numberFormatted({ num: item[units] }) }} hours
                        </div>
                        <div class="list__amount"
                             v-if="units === 'dollars'"
                        >
                            {{ numberFormatted({ num: item[units], currency: true }) }}
                        </div>
                        <div class="list__amount"
                             v-if="units === 'tec'"
                        >
                            {{ numberFormatted({ num: item[units] }) }} TEC
                        </div>
                    </div>
                </div>

            </div>

        </div>
    </div>
</template>

<script>
import moment from 'moment';
import AnalyticsChart from './AnalyticsChart.vue';

const AnalyticsPanel = {
    name: 'analytics-panel',
    components: {
        AnalyticsChart,
    },
    props: {
        title: {
            type: String,
            required: true,
        },
        amount: {
            type: Object,
            required: true,
        },
        amountComment: {
            type: String,
            required: true,
        },
        chartData: {
            type: Array,
            required: true,
        },
        color: {
            type: String,
            default: '#4053b5',
        },
        listTitle: {
            type: String,
            required: true,
        },
        topList: {
            type: Array,
            required: true,
        },
        units: {
            type: String,
            required: true,
        },
    },
    data() {
        return {
            chartWidth: null,
            chartHeight: 112,
        };
    },
    methods: {
        numberFormatted({ num, currency = false }) {
            if (currency) {
                return num.toLocaleString('en-US', {
                    style: 'currency',
                    currency: 'USD',
                    currencyDisplay: 'symbol',
                    minimumFractionDigits: 0,
                });
            }
            return num.toLocaleString('en-US');
        },
    },
    computed: {
        labels() {
            const lab = [];
            let m = moment().subtract(1, 'years').add(1, 'months');
            while (m.isSameOrBefore(moment(), 'month')) {
                lab.push(m.format('MMM').substr(0, 1).toLowerCase());
                m = m.add(1, 'months');
            }
            return lab;
        },
    },
    mounted() {
        const paddingLeft  = parseFloat(getComputedStyle(this.$refs.chart).paddingLeft);
        const paddingRight = parseFloat(getComputedStyle(this.$refs.chart).paddingRight);
        this.chartWidth = this.$refs.chart.clientWidth - paddingLeft - paddingRight;
    },
};
export default AnalyticsPanel;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .analytics-panel {
        align-items: center;
        display: flex;
        flex-direction: column;
        padding-top: 24px;

        &__header {
            display: flex;
            height: 20px;
            justify-content: space-between;
            width: 100%;
            padding: 0 20px;
        }
        &__amount {
            display: flex;
            width: 100%;
            padding: 32px 20px 0;
        }
        &__chart {
            display: flex;
            justify-content: center;
            width: 100%;

            border-bottom: 1px solid $border;
            padding: 18px 14px 28px 16px;
        }
        &__left {
            display: flex;
            align-items: center;
        }
        &__right {
            display: flex;
            align-items: center;
            justify-content: flex-end;
        }
    }
    .top-header {
        &__title {
            @include fnt($text, 14px, $weight-semibold, left);
            text-transform: uppercase;
            line-height: 1;
            margin: 0;
        }
        &__sub-title {
            @include fnt($text-lighter, 12px, $weight-light, right);
            margin: 0;
        }
    }
    .amount {
        &__title {
            @include fnt($text, 24px, $weight-light, left);
            line-height: 1;
            margin: 0;
        }
        &__comment {
            @include fnt($text-lighter, 12px, $weight-light, left);
        }
    }
    .list {
        padding: 16px 20px 12px;
        width: 100%;
        &__header {
            padding-bottom: 16px;
        }
        &__line {
            display: flex;
            align-items: center;
            justify-content: space-between;
            height: 48px;
            &--bordered {
                border-top: 1px solid $border;
            }
        }
        &__left {
            display: flex;
            align-items: center;
            width: 70%;
        }
        &__right {
            display: flex;
            align-items: center;
            justify-content: flex-end;
        }
        &__ava {
            height: 26px;
            width: 26px;
            border-radius: 50%;
            background: center center/cover no-repeat $grey-lighter;
        }
        &__status {
            height: 8px;
            width: 8px;
            border-radius: 50%;
            display: flex;
            flex: 0 0 auto;
            &--active {
                background-color: $green;
            }
            &--inactive {
                background-color: $red;
            }
            &--draft {
                background-color: $grey-middle;
            }
        }

        &__name {
            @include fnt($text, 12px, $weight-light, left);
            margin-left: 10px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        &__amount {
            @include fnt($text-lighter, 12px, $weight-light, right);
        }
    }

</style>
