<template>
    <div class="AnalyticsVolunteer analytics-table">
        <div>
            <div class="analytics-table__head">
                <div class="analytics-table__head-item status"/>
                <div class="analytics-table__head-item name">name</div>
                <div class="analytics-table__head-item volunteers">volunteers</div>
                <div class="analytics-table__head-item hours">hours worked</div>
                <div class="analytics-table__head-item shifts">shifts</div>
                <div class="analytics-table__head-item positions">positions</div>
                <div class="analytics-table__head-item filled">pos. filled</div>
                <div class="analytics-table__head-item app">applications</div>
                <div class="analytics-table__head-item pending">pending apps.</div>
                <div class="analytics-table__head-item chevron"/>
            </div>
            <div v-for="(item, index) in paginationDataOpportunities[currentPage - 1]"
                 :key="index"
            >
                <div class="analytics-table__line">
                    <div class="analytics-table__line-item status">
                        <div :class="`analytics-table__status
                            analytics-table__status--${item.status}`"
                        />
                    </div>
                    <div class="analytics-table__line-item name">
                        {{ item.name }}
                    </div>
                    <div class="analytics-table__line-item volunteers">
                        {{ numberFormatted(item.volunteers) }}
                    </div>
                    <div class="analytics-table__line-item hours">
                        {{ numberFormatted(item.hours) }}
                    </div>
                    <div class="analytics-table__line-item shifts">
                        {{ numberFormatted(item.shiftsAmount) }}
                    </div>
                    <div class="analytics-table__line-item positions">
                        {{ numberFormatted(item.positions) }}
                    </div>
                    <div class="analytics-table__line-item filled">
                        <el-progress
                                v-if="item.shiftsAmount"
                                :percentage="item.filledPrc"
                                :status="filledStatus(item.filledPrc)"
                                :show-text="false"
                        />
                    </div>
                    <div class="analytics-table__line-item app">
                        {{ numberFormatted(item.app) }}
                    </div>
                    <div class="analytics-table__line-item pending">
                        {{ numberFormatted(item.pending) }}
                    </div>
                    <div class="analytics-table__line-item
                            analytics-table__line-item--right chevron"
                         @click="handleExpandItem(item.id)"
                    >
                        <i v-if="item.shifts.length"
                           class="fa fa-chevron-down"
                           :class="{'chevron--expand' : expandItem === item.id}"
                        />
                    </div>
                </div>
                <expand-opportunity
                        v-if="expandItem === item.id"
                        :shift="item.shifts"
                />
            </div>
            <div class="analytics-table__pagination"
                 v-if="opportunities.length > recordPerPage"
            >
                <div class="analytics-table__pagination-text">
                    {{
                        (recordPerPage * (currentPage - 1)) + 1
                    }} - {{
                        paginationDataOpportunities.length === currentPage
                            ? recordPerPage * (currentPage - 1)
                                + paginationDataOpportunities[currentPage - 1].length
                            : recordPerPage * currentPage
                    }} of {{
                        opportunities.length
                    }} total records
                </div>
                <el-pagination
                        layout="prev, next"
                        :page-size="recordPerPage"
                        :total="opportunities.length"
                        :current-page.sync="currentPage"
                        @current-change="handleCurrentChange"
                />
            </div>
        </div>
    </div>
</template>

<script>
import ExpandOpportunity from './ExpandOpportunity.vue';

const AnalyticsOpportunity = {
    name: 'analytics-opportunity',
    components: {
        ExpandOpportunity,
    },
    props: {
        opportunities: {
            type: Array,
            required: true,
        },
    },
    data() {
        return {
            expandItem: null,
            currentPage: 1,
            recordPerPage: 10,
        };
    },
    methods: {
        handleCurrentChange(val) {
            this.currentPage = val;
        },
        handleExpandItem(id) {
            this.expandItem = this.expandItem === id ? null : id;
        },
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
        filledStatus(percentile) {
            if (percentile > 75) {
                return 'success';
            } else if (percentile <= 25) {
                return 'exception';
            }
            return '';
        },
    },
    computed: {
        paginationDataOpportunities() {
            const pagination = [];
            for (let i = 0; i < this.opportunities.length; i += this.recordPerPage) {
                pagination.push(this.opportunities.slice(i, i + this.recordPerPage));
            }
            return pagination;
        },
    },
};

export default AnalyticsOpportunity;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .analytics-table {
        padding: 0 32px 28px;
        width: 100%;

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

        &__head {
            display: flex;
            align-items: center;
            height: 44px;
            width: 100%;
        }

        &__head-item {
            @include fnt($text-lighter, 10px, $weight-light, left);
            text-transform: uppercase;

            display: flex;
            align-items: center;
            justify-content: flex-start;
            height: 100%;
        }

        &__line {
            display: flex;
            align-items: center;
            justify-content: flex-start;
            height: 48px;
            border-top: 1px solid $border;
        }
        &__line-item {
            @include fnt($text, 12px, $weight-light, left);
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            &--right {
                text-align: right;
            }
        }
        &__pagination {
            display: flex;
            align-items: center;
            justify-content: flex-end;
            height: 48px;
        }
        &__pagination-text {
            @include fnt($text-lighter, 12px, $weight-light, right);
            font-style: italic;
            margin-right: 32px;
        }
    }

    .status {
        width: 2%;
        align-items: center;
        display: flex;
        height: 100%;
        justify-content: flex-start;
    }
    .name {
        width: 20%;
        padding-right: 8px;
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
        cursor: pointer;
        &--expand {
            transform: rotate(180deg);
        }
    }
    .fa {
        transition: all .3s;
    }

    .el-button {
        transition: all .3s;
        border-color: $border;
        color: $text-light;
        &:hover {
            border-color: $blue;
            color: $blue;
        }
    }
</style>

<style lang="scss" rel="stylesheet/scss">
    @import '../../../../sass/variables/td_theme.scss';

    .btn-outline-light {
        background-color: transparent;
        background-image: none;
        border-color    : $white;
        color           : $white;
        width           : 20%;
    }

    .el-pagination {
        & button:hover {
            color: $blue;
        }
        & .btn-next, & .btn-prev {
            border: none;
            background-color: $white-ter;
            border-radius: 2px;
            color: $grey-light;
            transition: all .3s;
            &.disabled {
                background-color: $white-ter;
                &:hover {
                    color: $grey-light;
                }
            }
        }
        .btn-next {
            margin-left: 4px;
        }
    }

    .analytics-filters {
        .el-input__inner, .el-textarea__inner {
            border-color: $border;
            color       : $grey-middle;
            font-weight : $weight-normal;
            resize      : none;
            &:focus {
                border-color: $blue;
            }
            &::-webkit-input-placeholder {
                color      : $grey-lighter;
                font-weight: 300;
            }
            &::-moz-placeholder {
                color      : $grey-lighter;
                font-weight: 300;
            }
            &:-moz-placeholder {
                color: $grey-lighter;
                font-weight: 300;
            }
            &:-ms-input-placeholder {
                color      : $grey-lighter;
                font-weight: 300;
            }
        }
        .el-select {
            width: 100%;
        }
        .el-input__icon, .el-select .el-input .el-input__icon {
            color: $grey-light;
        }
        .el-switch {
            margin-bottom: 0;
        }
    }

    #analytics-extended {
        .el-form-item__error {
            @include fnt($danger, $size-7, $weight-light, left);
            margin-top: 0;
        }
        .el-select-dropdown__item.selected {
            background-color: $white-bis;
            color: $primary;
        }
        .el-select-dropdown__item.selected.hover{
            background-color: $white-ter;
        }
        .el-select-dropdown.is-multiple .el-select-dropdown__item.selected {
            color: $primary;
        }
        .el-tag--primary, .el-tag__close el-icon-close {
            background-color: rgba($primary, .1);
            border-color    : rgba($primary, .2);
            color           : $primary;

        }
        .el-tag--primary .el-tag__close:hover {
            background-color: $primary;
        }
        .el-autocomplete-suggestion li:active {
            background-color: rgba($primary, .2);
        }
        .el-date-table td.today {
            background-color: $primary !important;
        }
        /** Remove bottom border overlapse */
        .el-input__inner,
        .el-input__inner:focus,
        .el-input__inner.focus,
        .el-textarea__inner,
        .el-textarea__inner:focus,
        .el-textarea__inner.focus {
            background-image: none;
        }
        .el-input.is-disabled .el-input__inner {
            background-color: transparent;
        }
        .el-input.is-disabled {
            .el-icon-caret-top {
                color: #dedede;
            }
            .el-input__icon {
                color: #dedede;
            }
        }
    }
</style>
