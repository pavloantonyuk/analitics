<template>
    <div class="Analytics analytics">
        <page-header
                title="Analytics"
                :breadcrumbs="breadcrumbs"
                :bordered="true"
        >
            <p slot="right"
               class="analytics__upgrade"
            >
                Want more data with advanced filtering capabilities?&#8194;
                <a class="analytics__upgrade--strong"
                   :href="upgradeURL"
                >Upgrade Now!</a>
            </p>
        </page-header>
        <div class="page-content">
            <div class="row analytics__row">
                <div class="col-xs-12 col-sm-6 col-md-3 col-lg-3 analytics__col">
                    <div class="panel analytics-panel">
                        <i class="icon md-assignment-account analytics-panel__icon"/>
                        <div class="analytics-panel__title">
                            {{ numberFormatted(analytics.cards.totals.volunteers) }}

                        </div>
                        <div class="analytics-panel__sub-title">
                            volunteers have contributed to your organization this year!
                        </div>
                    </div>
                </div>
                <div class="col-xs-12 col-sm-6 col-md-3 col-lg-3 analytics__col">
                    <div class="panel analytics-panel">
                        <i class="icon md-time analytics-panel__icon"/>
                        <div class="analytics-panel__title">
                            {{ numberFormatted(analytics.cards.totals.hours) }}
                            <span class="analytics-panel__title-hrs">hrs</span>
                        </div>
                        <div class="analytics-panel__sub-title">
                            have been contributed to your organization by volunteers this year!
                        </div>
                    </div>
                </div>
                <div class="col-xs-12 col-sm-6 col-md-3 col-lg-3 analytics__col">
                    <div class="panel analytics-panel">
                        <i class="icon md-money analytics-panel__icon"/>
                        <div class="analytics-panel__title">
                            {{ numberFormatted(analytics.cards.totals.dollars, true) }}
                        </div>
                        <div class="analytics-panel__sub-title">
                            have been donated to your organization by
                            <span class="analytics-panel__sub-title--strong">
                                {{ analytics.cards.totals.cashDonors }}&nbsp;donors
                            </span>
                            this year!
                        </div>
                    </div>
                </div>
                <div class="col-xs-12 col-sm-6 col-md-3 col-lg-3 analytics__col">
                    <div class="panel analytics-panel">
                        <i class="icon md-grain analytics-panel__icon"/>
                        <div class="analytics-panel__title">
                            {{ numberFormatted(analytics.cards.totals.tec) }}
                            <span class="analytics-panel__title-tec">tec</span>
                        </div>
                        <div class="analytics-panel__sub-title">
                            have been donated to your organization by
                            <span class="analytics-panel__sub-title--strong">
                                {{ analytics.cards.totals.tecDonors }}&nbsp;donors
                            </span> this year!
                        </div>
                    </div>
                </div>
            </div>
            <div class="row analytics__row">
                <div class="col-12 analytics__col">
                    <div class="panel analytics__panel analytics-list">
                        <div class="analytics-list__head">
                            <div class="analytics-list__left">
                                <h2 class="analytics-list__head-title">
                                    volunteer data
                                </h2>
                            </div>
                            <div class="analytics-list__right">
                                <download-csv
                                        class= "download-csv"
                                        :data= "analytics.lists.volunteers"
                                        :fields= "analyticsFields"
                                        type= "csv"
                                        name= "volunteers.csv"
                                >
                                    <div class="analytics-list__head-sub-title">
                                        Export to CSV
                                    </div>
                                    <i class="icon icon md-file-text analytics-list__head-icon"/>
                                </download-csv>
                            </div>
                        </div>
                        <div class="analytics-table">
                            <tr class="analytics-table__head">
                                <th class="analytics-table__head-item
                                           analytics-table__head-item--ava"/>
                                <th class="analytics-table__head-item
                                           analytics-table__head-item--name"
                                >name</th>
                                <th class="analytics-table__head-item
                                           analytics-table__head-item--address"
                                >address</th>
                                <th class="analytics-table__head-item
                                           analytics-table__head-item--phone"
                                >phone</th>
                                <th class="analytics-table__head-item
                                           analytics-table__head-item--email"
                                >email</th>
                                <th class="analytics-table__head-item
                                           analytics-table__head-item--birthday"
                                >birthday</th>
                                <th class="analytics-table__head-item
                                           analytics-table__head-item--gender"
                                >gender</th>
                            </tr>
                            <tr class="analytics-table__line"
                                v-for="item in paginationDataVolunteer[currentPage - 1]"
                                :key="item.id"
                            >
                                <th class="analytics-table__line-item
                                           analytics-table__line-item--ava">
                                    <div class="analytics-table__ava"
                                         :style="{
                                             'background-image' : `url(${ item.profilePhoto })`
                                         }"
                                    />
                                </th>
                                <th class="analytics-table__line-item
                                           analytics-table__line-item--name">
                                    {{ item.name }}
                                </th>
                                <th class="analytics-table__line-item
                                           analytics-table__line-item--address">
                                    {{ item.address ? item.address : 'N/A' }}
                                </th>
                                <th class="analytics-table__line-item
                                           analytics-table__line-item--phone">
                                    {{ item.phone ? item.phone : 'N/A' }}
                                </th>
                                <th class="analytics-table__line-item
                                           analytics-table__line-item--email">
                                    {{ item.email ? item.email : 'N/A' }}
                                </th>
                                <th class="analytics-table__line-item
                                           analytics-table__line-item--birthday">
                                    {{ item.birthday ? dateFormatted(item.birthday) : 'N/A' }}
                                </th>
                                <th class="analytics-table__line-item
                                           analytics-table__line-item--gender">
                                    {{ item.gender ? item.gender : 'N/A' }}
                                </th>
                            </tr>
                            <div class="analytics-table__pagination"
                                 v-if="analytics.lists.volunteers.length > recordPerPage"
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
                                        analytics.lists.volunteers.length
                                    }} total records
                                </div>
                                <el-pagination
                                        layout="prev, next"
                                        :page-size="recordPerPage"
                                        :total="analytics.lists.volunteers.length"
                                        :current-page.sync="currentPage"
                                        @current-change="handleCurrentChange"
                                />
                            </div>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>
</template>

<script>
import moment from 'moment';
import DownloadCsv from '../../../../../../node_modules/vue-json-excel/JsonExcel.vue';
import PageHeader from '../../common/navigation/page-header.vue';

const Analytics =  {
    name: 'analytics',
    components: {
        PageHeader,
        DownloadCsv,
    },
    props: {
        breadcrumbs: {
            type: Array,
            default() {
                return [{
                    href: '#',
                    label: '',
                }];
            },
        },
        analytics: {
            type: Object,
            default() {
                return [];
            },
        },
    },
    data() {
        return {
            analyticsFields: {
                name: 'name',
                address: 'address',
                phone: 'phone',
                email: 'email',
                birthday: 'birthday',
                gender: 'gender',
            },
            currentPage: 1,
            recordPerPage: 10,
            upgradeURL: `${location.protocol}//${location.host}/nonprofits/${
                window.nonprofit.id
            }/settings/profile/billing`,
        };
    },
    methods: {
        handleCurrentChange(val) {
            this.currentPage = val;
        },
        numberFormatted(_num, currency = false) {
            if (currency) {
                return _num.toLocaleString('en-US', {
                    style: 'currency',
                    currency: 'USD',
                    currencyDisplay: 'symbol',
                    minimumFractionDigits: 0,
                });
            }
            return _num.toLocaleString('en-US');
        },
        dateFormatted(_date) {
            return _date ? moment(_date).format('MM/DD/YYYY') : '';
        },
    },
    computed: {
        paginationDataVolunteer() {
            const pagination = [];
            for (let i = 0; i < this.analytics.lists.volunteers.length; i += this.recordPerPage) {
                pagination.push(this.analytics.lists.volunteers.slice(i, i + this.recordPerPage));
            }
            return pagination;
        },
    },
};

export default Analytics;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .analytics {
        &__upgrade {
            @include fnt($text-light, 14px, $weight-light, center);
            display: flex;
            align-items: flex-end;
            margin: 0;
            &--strong {
                @include fnt($blue, 14px, $weight-semibold, center);
            }
        }
    }
    .analytics-panel {
        align-items: center;
        display: flex;
        flex-direction: column;
        justify-content: center;
        padding: 32px 20px;
        height: 264px;

        &__icon {
            @include fnt($blue, 78px, $weight-normal, center);
        }
        &__title {
            @include fnt($text-light, 36px, $weight-light, center);
            line-height: 1;
            padding: 16px 0;
        }
        &__title-hrs {
            @include fnt($text-light, 12px, $weight-light, center);
            margin-left: -8px;
        }
        &__title-tec {
            @include fnt($text-light, 12px, $weight-light, center);
            margin-left: -8px;
            text-transform: uppercase;
        }
        &__sub-title {
            @include fnt($text-light, 12px, $weight-light, center);
            &--strong {
                @include fnt($text-strong, 12px, $weight-semibold, center);
            }
        }
    }
    .analytics-list {
        &__head {
            padding: 0 32px;
            height: 64px;
            display: flex;
            align-items: center;
            justify-content: space-between;
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
        &__head-title {
            @include fnt($text, 1rem, $weight-semibold, left);
            text-transform: capitalize;
            margin: 0;
        }
        &__head-sub-title {
            @include fnt($blue, 1rem, $weight-semibold, right);
            margin: 0;
            cursor: pointer;
            transition: all .3s;
            &:hover {
                text-decoration: underline;
                color: $blue-hover;
            }
        }
        &__head-icon {
            @include fnt($blue, 1.5rem, $weight-semibold, right);
            padding: 0 0 0 6px;
        }
        &__table {}

    }

    .analytics-table {
        padding: 0 32px 20px;
        width: 100%;
        &__head {
            display: flex;
            align-items: center;
            height: 44px;
            width: 100%;
        }
        &__ava {
            height: 24px;
            width: 24px;
            border-radius: 50%;
            background: center center/cover no-repeat $grey-lighter;
        }
        &__head-item {
            @include fnt($text-lighter, 12px, $weight-semibold, left);
            text-transform: uppercase;

            display: flex;
            align-items: center;
            justify-content: flex-start;
            height: 100%;
            &--ava {
                width: 4%;
            }
            &--name {
                width: 18%;
            }
            &--address {
                width: 28%;
            }
            &--phone {
                width: 16%;
            }
            &--email {
                width: 18%;
            }
            &--birthday {
                width: 10%;
            }
            &--gender {
                width: 6%;
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
            &--ava {
                width: 4%;
            }
            &--name {
                width: 18%;
            }
            &--address {
                width: 28%;
            }
            &--phone {
                width: 16%;
            }
            &--email {
                width: 18%;
            }
            &--birthday {
                width: 10%;
            }
            &--gender {
                width: 6%;
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
    .download-csv {
        display: flex;
    }

</style>

<style lang="scss" rel="stylesheet/scss">
    @import '../../../../sass/variables/td_theme';
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
</style>
