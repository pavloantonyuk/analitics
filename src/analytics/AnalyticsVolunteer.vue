<template>
    <div class="AnalyticsVolunteer analytics-table">
        <div cellspacing="0"
             width="100%"
        >
            <div class="analytics-table__head">
                <div class="analytics-table__head-item ava"/>
                <div class="analytics-table__head-item name">name</div>
                <div class="analytics-table__head-item email">email</div>
                <div class="analytics-table__head-item hours">hours</div>
                <div class="analytics-table__head-item dollars">donations ($)</div>
                <div class="analytics-table__head-item tec">donations (tec)</div>
                <div class="analytics-table__head-item birthday">birthday</div>
                <div class="analytics-table__head-item gender">gender</div>
                <div class="analytics-table__head-item chevron"/>
            </div>
            <div v-for="(item, index) in paginationDataVolunteer[currentPage - 1]"
                 :key="index"
            >
                <div class="analytics-table__line">
                    <div class="analytics-table__line-item ava">
                        <div class="analytics-table__ava"
                             :style="{'background-image' : `url(${ item.ava })`}"
                        />
                    </div>
                    <div class="analytics-table__line-item name">
                        {{ item.name }}
                    </div>
                    <div class="analytics-table__line-item email"
                         :class="{ 'analytics-table__line-item--n-a': !item.email}"
                    >
                        {{ item.email ? item.email : 'N/A' }}
                    </div>
                    <div class="analytics-table__line-item hours">
                        {{ numberFormatted(item.hours) }}
                    </div>
                    <div class="analytics-table__line-item dollars">
                        {{ numberFormatted(item.dollars, true) }}
                    </div>
                    <div class="analytics-table__line-item tec">
                        {{ numberFormatted(item.tec) }} TEC
                    </div>
                    <div class="analytics-table__line-item birthday"
                         :class="{ 'analytics-table__line-item--n-a': !item.birthday}"
                    >
                        {{ item.birthday ? item.birthday : 'N/A' }}
                    </div>
                    <div class="analytics-table__line-item gender"
                         :class="{ 'analytics-table__line-item--n-a': !item.gender}"
                    >
                        {{ item.gender ? item.gender : 'N/A' }}
                    </div>
                    <div class="analytics-table__line-item
                                analytics-table__line-item--right chevron"
                         @click="handleExpandItem(item.id)"
                    >
                        <i class="fa fa-chevron-down"
                           :class="{'chevron--expand' : expandItem === item.id}"
                        />
                    </div>
                </div>
                <expand-volunteer
                        v-if="expandItem === item.id"
                        :volunteer="item"
                />
            </div>

            <div class="analytics-table__foot">
                <div class="analytics-table__foot-item ava"/>
                <div class="analytics-table__foot-item name"/>
                <div class="analytics-table__foot-item analytics-table__foot-item--right totals">
                    totals
                </div>
                <div class="analytics-table__foot-item hours">
                    {{ numberFormatted(totalHours) }}
                </div>
                <div class="analytics-table__foot-item dollars">
                    {{ numberFormatted(totalDollars, true) }}
                </div>
                <div class="analytics-table__foot-item tec">
                    {{ numberFormatted(totalTec) }} TEC
                </div>
                <div class="analytics-table__foot-item birdivday"/>
                <div class="analytics-table__foot-item gender"/>
                <div class="analytics-table__foot-item chevron"/>
            </div>
            <div class="analytics-table__pagination"
                 v-if="volunteers.length > recordPerPage"
            >
                <div class="analytics-table__pagination-text">
                    {{
                        (recordPerPage * (currentPage - 1)) + 1
                    }} - {{
                        paginationDataVolunteer.length === currentPage
                            ? recordPerPage * (currentPage - 1)
                                + paginationDataVolunteer[currentPage - 1].length
                            : recordPerPage * currentPage
                    }} of {{
                        volunteers.length
                    }} total records
                </div>
                <el-pagination
                        layout="prev, next"
                        :page-size="recordPerPage"
                        :total="volunteers.length"
                        :current-page.sync="currentPage"
                        @current-change="handleCurrentChange"
                />
            </div>
        </div>
    </div>
</template>

<script>
import ExpandVolunteer from './ExpandVolunteer.vue';

const AnalyticsVolunteer = {
    name: 'analytics-volunteer',
    components: {
        ExpandVolunteer,
    },
    props: {
        volunteers: {
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
    },
    computed: {
        totalHours() {
            let amount = 0;
            this.volunteers.forEach((item) => { amount += item.hours; });
            return amount;
        },
        totalDollars() {
            let amount = 0;
            this.volunteers.forEach((item) => { amount += item.dollars; });
            return amount;
        },
        totalTec() {
            let amount = 0;
            this.volunteers.forEach((item) => { amount += item.tec; });
            return amount;
        },
        paginationDataVolunteer() {
            const pagination = [];
            for (let i = 0; i < this.volunteers.length; i += this.recordPerPage) {
                pagination.push(this.volunteers.slice(i, i + this.recordPerPage));
            }
            return pagination;
        },
    },
};
export default AnalyticsVolunteer;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .analytics-table {
        padding: 0 32px 28px;
        width: 100%;

        &__ava {
            height: 24px;
            width: 24px;
            border-radius: 50%;
            background: center center/cover no-repeat $grey-lighter;
        }

        &__head, &__foot {
            display: flex;
            align-items: center;
            height: 44px;
            width: 100%;
        }
        &__foot {
            border-top: 1px solid $border;
        }

        &__head-item, &__foot-item {
            @include fnt($text-lighter, 10px, $weight-light, left);
            text-transform: uppercase;

            display: flex;
            align-items: center;
            justify-content: flex-start;
            height: 100%;
            &--right {
                text-align: right;
                justify-content: flex-end;
                padding-right: 8px;
            }
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
            &--right {
                text-align: right;
            }
            &--n-a {
                color: $text-lighter; // #b5b5b5
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
    .ava, .gender, .chevron {
        width: 4%;
    }
    .chevron {
        cursor: pointer;
        &--expand {
            transform: rotate(180deg);
        }
    }
    .fa {
        transition: all .3s;
    }
    .name {
        width: 20%;
    }
    .email, .totals {
        width: 26%;
    }
    .hours, .dollars, .tec {
        width: 10%;
    }
    .birthday {
        width: 12%;
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
        .el-input__inner, .el-input__inner:focus, .el-input__inner.focus,
        .el-textarea__inner, .el-textarea__inner:focus,
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
