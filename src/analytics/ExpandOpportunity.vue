<template>
    <div class="ExpandOpportunity expand-opportunity">
        <div class="expand-opportunity__line"
             v-for="(item, index) in shift"
             :key="index"
        >
            <div class="expand-opportunity__line-item status"/>
            <div class="expand-opportunity__line-item name">
                <div class="expand-opportunity__name">{{ item.name }}</div>
                <div class="expand-opportunity__date">
                    {{ item.dateStart === item.dateEnd
                        ? `${item.dateStart} | ${
                            getLocalTime(item.timeStart)
                        } - ${getLocalTime(item.timeEnd)}`
                        : `${item.dateStart} | ${
                            getLocalTime(item.timeStart)
                    } - ${item.dataEnd} | ${getLocalTime(item.timeEnd)}` }}
                </div>
            </div>
            <div class="expand-opportunity__line-item volunteers"
                 :class="{'expand-opportunity__line-item--n-a' : !item.volunteers}"
            >{{ item.volunteers }}</div>
            <div class="expand-opportunity__line-item hours"
                 :class="{'expand-opportunity__line-item--n-a' : !item.hours}"
            >{{ item.hours }}</div>
            <div class="expand-opportunity__line-item expand-opportunity__line-item--n-a shifts">
                N/A
            </div>
            <div class="expand-opportunity__line-item positions"
                 :class="{'expand-opportunity__line-item--n-a' : !item.positions.total}"
            >{{ item.positions.total }}</div>
            <div class="expand-opportunity__line-item filled">
                <el-progress
                        :percentage="filledPercentile(item)"
                        :status="filledStatus(item)"
                        :show-text="false"
                />
            </div>
            <div class="expand-opportunity__line-item expand-opportunity__line-item--n-a app">
                N/A
            </div>
            <div class="expand-opportunity__line-item expand-opportunity__line-item--n-a pending">
                N/A
            </div>
            <div class="expand-opportunity__line-item chevron"/>
        </div>
    </div>
</template>
<script>
import momentTZ from 'moment-timezone';

const ExpandOpportunity = {
    name: 'expand-opportunity',
    props: {
        shift: {
            type: Array,
            required: true,
        },
    },
    methods: {
        numberFormatted(num, currency = false) {
            if (currency) {
                return num.toLocaleString('en-US', {
                    style: 'currency',
                    currency: 'USD',
                    currencyDisplay: 'symbol',
                    minimumFractionDigits: 2,
                    maximumFractionDigits: 2,
                });
            }
            return num.toLocaleString('en-US');
        },
        getLocalTime(time) {
            return momentTZ.tz(momentTZ(time, 'h:mma'), 'UTC').tz(momentTZ.tz.guess()).format('h:mma');
        },
        filledPercentile(shift) {
            return Math.ceil((shift.positions.filled / shift.positions.total) * 100);
        },
        filledStatus(shift) {
            const percentile = this.filledPercentile(shift);
            if (percentile > 75) {
                return 'success';
            } else if (percentile <= 25) {
                return 'exception';
            }
            return '';
        },

    },
};

export default ExpandOpportunity;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .expand-opportunity {
        width: 100%;

        &__line {
            align-items: center;
            display: flex;
            height: 48px;
            justify-content: flex-start;
        }
        &__line-item {
            @include fnt($text-light, 12px, $weight-light, left);
            &--n-a {
                color: $text-lighter;
            }
        }
        &__name {
            @include fnt($text-light, 10px, $weight-semibold, left);
            overflow: hidden;
            padding-right: 8px;
            text-overflow: ellipsis;
            white-space: nowrap;
        }
        &__date {
            @include fnt($text-lighter, 10px, $weight-light, left);
        }
    }

    .status {
        width: 2%;
    }
    .name {
        width: 20%;
    }
    .volunteers, .hours, .app {
        width: 12%;
    }
    .shifts, .positions {
        width: 8%;
    }
    .filled  {
        width: 16%;
        padding-right: 16px;
    }
    .pending {
        width: 6%;
    }
    .chevron {
        width: 4%;
    }

</style>