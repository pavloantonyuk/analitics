<template>
    <div class="AnalyticsExtended analytics"
         id="analytics-extended"
    >
        <page-header
                title="Analytics"
                :breadcrumbs="breadcrumbs"
                :bordered="false"
        />
        <div class="page-content">
            <div class="row analytics__row">
                <analytics-panel
                        title="total volunteers"
                        :amount="{ num: analytics.cards.totals.volunteers }"
                        amount-comment="Volunteers"
                        :chart-data="analytics.cards.charts.volunteers"
                        color="#9069dd"
                        list-title="volunteers"
                        :top-list="topVolunteers"
                        units="hours"
                />
                <analytics-panel
                        title="total hours"
                        :amount="{ num: analytics.cards.totals.hours }"
                        amount-comment="Hours Contributed"
                        :chart-data="analytics.cards.charts.hours"
                        color="#ff9800"
                        list-title="opportunuties"
                        :top-list="topOpportunities"
                        units="hours"
                />
                <analytics-panel
                        title="cash donations"
                        :amount="{ num: analytics.cards.totals.dollars, currency: true }"
                        amount-comment="USD"
                        :chart-data="analytics.cards.charts.dollars"
                        color="#36c842"
                        list-title="donors"
                        :top-list="topCashDonors"
                        units="dollars"
                />
                <analytics-panel
                        title="tec donations"
                        :amount="{ num: analytics.cards.totals.tec }"
                        amount-comment="TEC"
                        :chart-data="analytics.cards.charts.tec"
                        color="#5bc0de"
                        list-title="donors"
                        :top-list="topTecDonors"
                        units="tec"
                />
            </div>
            <div class="row analytics__row">
                <div class="col-12 analytics__col">
                    <div class="panel analytics__panel analytics-list">
                        <div class="analytics-list__head">
                            <div class="analytics-list__left">
                                <h2 class="analytics-list__head-title"
                                    ref="AnalyticsVolunteer"
                                    @click="changeComponent"
                                >
                                    volunteers
                                </h2>
                                <h2 class="analytics-list__head-title"
                                    ref="AnalyticsOpportunity"
                                    @click="changeComponent"
                                >
                                    opportunities
                                </h2>
                            </div>
                            <div class="analytics-list__right">
                                <download-csv
                                        class="download-csv"
                                        :data="dataToExport"
                                        :fields="analyticsFields"
                                        type="csv"
                                        :name="nameOfFile"
                                >
                                    <div class="analytics-list__head-sub-title">
                                        Export to CSV
                                    </div>
                                    <i class="icon icon md-file-text analytics-list__head-icon"/>
                                </download-csv>
                            </div>
                            <div class="analytics-list__tab-line">
                                <div class="analytics-list__tab-line-active"
                                     ref="tabLine"
                                />
                            </div>
                        </div>
                        <component :is="showFilterComponent"
                                   :select-skill="skills"
                                   :select-opportunity="opportunities"
                                   @change-filters-opportunity="changeFilterOpportunity"
                                   @change-filters-volunteer="changeFilterVolunteer"
                                   :status="status"
                                   @change-range="changeRange"
                        />
                        <comment :is="showComponent"
                                 :volunteers="filteredDataVolunteers"
                                 :opportunities="filteredDataOpportunities"
                        />
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
import AnalyticsPanel from './AnalyticsPanel.vue';
import AnalyticsOpportunity from './AnalyticsOpportunity.vue';
import AnalyticsVolunteer from './AnalyticsVolunteer.vue';
import FiltersVolunteer from './FiltersVolunteer.vue';
import FiltersOpportunity from './FiltersOpportunity.vue';

const AnalyticsExtended = {
    name: 'analytics-extended',
    components: {
        PageHeader,
        DownloadCsv,
        AnalyticsPanel,
        AnalyticsOpportunity,
        AnalyticsVolunteer,
        FiltersVolunteer,
        FiltersOpportunity,
    },
    props: {
        breadcrumbs: {
            type: Array,
            default() {
                return [{
                    href: '#',
                    label: 'Dashboard',
                }, {
                    label: 'Analytics',
                }];
            },
        },
        analytics: {
            type: Object,
            default() {
                return [];
            },
        },
        rangeUrl: {
            type: String,
            required: true,
        },
    },
    data() {
        return {
            showComponent: 'AnalyticsVolunteer',
            showFilterComponent: 'FiltersVolunteer',
            filtersOpportunity: {},
            filtersVolunteer: {},
            skills: [],
            opportunities: [],
            lists: {},
            status: {
                loading: false,
                success: false,
                error: false,
            },
        };
    },
    methods: {
        changeRange(newRange) {
            this.status = Object.assign({}, { loading: true });
            this.$http.get(this.rangeUrl, { params: newRange })
                .then(({ data }) => {
                    this.status = Object.assign({}, { success: true });
                    this.lists = data.lists;
                    setTimeout(() => {
                        this.status = Object.assign({}, {
                            loading: false,
                            success: false,
                            error: false,
                        });
                    }, 1000);
                })
                .catch((error) => {
                    this.status = Object.assign({}, { error: true });
                    console.log('error ', error);
                    setTimeout(() => {
                        this.status = Object.assign({}, {
                            loading: false,
                            success: false,
                            error: false,
                        });
                    }, 1000);
                });
        },
        changeFilterOpportunity(obj) {
            this.filtersOpportunity = obj;
        },
        changeFilterVolunteer(obj) {
            this.filtersVolunteer = obj;
        },
        numberFormatted(num, currency = false) {
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
        dateFormatted(_date) {
            return _date ? moment(_date).format('MM/DD/YYYY') : '';
        },
        changeLine(rf) {
            this.$refs.tabLine.style.left = `${this.$refs[rf].offsetLeft}px`;
            this.$refs.tabLine.style.width = `${this.$refs[rf].offsetWidth}px`;
        },
        changeComponent() {
            this.showComponent = this.showComponent === 'AnalyticsVolunteer'
                ? 'AnalyticsOpportunity'
                : 'AnalyticsVolunteer';
            this.showFilterComponent = this.showComponent === 'AnalyticsVolunteer'
                ? 'FiltersVolunteer'
                : 'FiltersOpportunity';
            this.changeLine(this.showComponent);
        },
        positionsAmount(shift) {
            let amount = 0;
            shift.forEach((item) => {
                amount += item.positions.total;
            });
            return amount;
        },
        filledAmount(shift) {
            let amount = 0;
            shift.forEach((item) => {
                amount += item.positions.filled;
            });
            return amount;
        },
        filledPrc(shift) {
            const positionsAmount = this.positionsAmount(shift);
            return positionsAmount
                ? Math.ceil((this.filledAmount(shift) / positionsAmount) * 100)
                : 0;
        },
        addSkill(skill) {
            const strSkill = skill.trim().toLowerCase();
            if (this.skills.findIndex(item => item.value === strSkill) === -1) {
                this.skills.push({
                    value: strSkill,
                    label: skill,
                });
            }
        },
        addOpportunities(opp) {
            const strOpp = opp.trim().toLowerCase();
            if (this.opportunities.findIndex(item => item.value === strOpp) === -1) {
                this.opportunities.push({
                    value: strOpp,
                    label: opp,
                });
            }
        },
        getSkills(skills) {
            let str = '';
            skills.forEach((item) => {
                this.addSkill(item.name);
                str += `${item.name} (${item.skillLevel}), `;
            });
            return str;
        },
        getOpportunities(opp) {
            let str = '';
            opp.forEach((item) => {
                this.addOpportunities(item.title);
                str += `${item.title}, `;
            });
            return str;
        },
        getOpportunityStatus(status) {
            if (status.active) {
                return 'active';
            } else if (status.inactive) {
                return 'inactive';
            }
            return 'draft';
        },
    },
    computed: {
        nameOfFile() {
            if (this.showComponent === 'AnalyticsVolunteer') {
                return 'volunteers.csv';
            }
            return 'opportunities.csv';
        },
        dataToExport() {
            return this.showComponent === 'AnalyticsVolunteer'
                ? this.filteredDataVolunteers
                : this.filteredDataOpportunities;
        },
        analyticsFields() {
            if (this.showComponent === 'AnalyticsVolunteer') {
                return {
                    Name: 'name',
                    Address: 'address',
                    Phone: 'phone',
                    Email: 'email',
                    Hours: 'hours',
                    'Donations ($)': 'dollars',
                    'Donations (TEC)': 'tec',
                    Skills: 'skillsStr',
                    Birthday: 'birthday',
                    Gender: 'gender',
                    Opportunities: 'opportunitiesStr',
                    Occurrences: 'occurrences',
                };
            }
            return {
                Name: 'name',
                Status: 'status',
                Volunteers: 'volunteers',
                'Hours worked': 'hours',
                Shifts: 'shiftsAmount',
                Positions: 'positions',
                'Pos. filled': 'filled',
                'Filled %': 'filledPrc',
                Applications: 'app',
                'Pending apps.': 'pending',
            };
        },
        dataVolunteers() {
            return this.lists.volunteers.map((item) => {
                const year = Number(moment().format('YYYY'));
                return {
                    id: item.id,
                    ava: item.profilePhoto,
                    name: item.name,
                    address: item.address,
                    phone: item.phone,
                    email: item.email,
                    hours: item.hours,
                    dollars: item.dollars,
                    tec: item.tec,
                    birthday: this.dateFormatted(item.birthday),
                    gender: item.gender,
                    skills: item.skills,
                    age: year - moment(item.birthday).format('YYYY'),
                    skillsStr: this.getSkills(item.skills),
                    resume: item.resume,
                    opportunities: item.opportunities,
                    opportunitiesStr: this.getOpportunities(item.opportunities),
                    occurrences: item.occurrences,
                };
            });
        },
        dataOpportunities() {
            return this.lists.opportunities.map((item) => {
                const opp = {
                    id: item.id,
                    name: item.title,
                    status: this.getOpportunityStatus(item.status),
                    shifts: item.shifts,
                    volunteers: item.volunteers,
                    hours: item.hours,
                    shiftsAmount: item.shifts.length,
                    positions: this.positionsAmount(item.shifts),
                    filled: this.filledAmount(item.shifts),
                    filledPrc: this.filledPrc(item.shifts),
                    app: item.applications,
                    pending: item.pendingApplications,
                };
                return opp;
            });
        },
        filteredDataVolunteers() {
            return this.dataVolunteers.filter((item) => {
                let gender = true;
                let skill = true;
                let amount = true;
                let opportunity = true;
                //
                const filters = this.filtersVolunteer;

                if (filters.filterColumn === 'skill' && filters.filterSkill) {
                    skill = item.skills.some(i =>
                        i.name.toLowerCase().trim() === filters.filterSkill);
                }
                if (filters.filterOpportunity) {
                    opportunity = item.opportunities.some(i =>
                        i.title.toLowerCase().trim() === filters.filterOpportunity);
                }
                if (filters.filterColumn === 'gender' && filters.filterGender) {
                    gender = String(item.gender).toLowerCase().trim() === filters.filterGender;
                }
                if (filters.filterColumn !== 'skill'
                    && filters.filterColumn !== 'gender'
                    && filters.filterColumn !== 'duplicated'
                    && filters.filterValue) {
                    if (filters.filterOperator === '>') {
                        amount = item[filters.filterColumn] > filters.filterValue;
                    } else if (filters.filterOperator === '<') {
                        amount = item[filters.filterColumn] < filters.filterValue;
                    } else if (filters.filterOperator === '=') {
                        amount = Number(item[filters.filterColumn]) === Number(filters.filterValue);
                    }
                }
                return gender && skill && amount && opportunity;
            });
        },
        filteredDataOpportunities() {
            return this.dataOpportunities.filter((item) => {
                let status = true;
                let amount = true;
                let filled = true;
                const filters = this.filtersOpportunity;

                if (filters.filterStatus) {
                    status = item.status === filters.filterStatus;
                }
                if (filters.filterColumn
                    && filters.filterColumn === 'filledPrc') {
                    if (filters.filterOperator === '>') {
                        filled = item.filledPrc > filters.filterFilled;
                    } else if (filters.filterOperator === '<') {
                        filled = item.filledPrc < filters.filterFilled;
                    } else if (filters.filterOperator === '=') {
                        filled = Number(item.filledPrc) === Number(filters.filterFilled);
                    }
                }
                if (filters.filterColumn
                    && filters.filterColumn !== 'filledPrc'
                    && filters.filterColumn !== 'status') {
                    if (filters.filterOperator === '>') {
                        amount = item[filters.filterColumn] > filters.filterValue;
                    } else if (filters.filterOperator === '<') {
                        amount = item[filters.filterColumn] < filters.filterValue;
                    } else if (filters.filterOperator === '=') {
                        amount = Number(item[filters.filterColumn]) === Number(filters.filterValue);
                    }
                }
                return status && amount && filled;
            });
        },
        topVolunteers() {
            const topVolunteers = this.analytics.lists.volunteers.map((item) => {
                const vol = {
                    id: item.id,
                    name: item.name,
                    hours: item.hours,
                    ava: item.profilePhoto,
                };
                return vol;
            });
            topVolunteers.sort((a, b) => {
                if (a.hours < b.hours) return 1;
                return -1; // if (a.hours > b.hours) return -1;
            });
            return topVolunteers.slice(0, 3);
        },
        topOpportunities() {
            const topOpportunities = this.analytics.lists.opportunities.map((item) => {
                const opp = {
                    id: item.id,
                    name: item.title,
                    hours: item.hours,
                    inactive: item.status.inactive,
                    active: item.status.active,
                    draft: item.status.draft,
                };
                return opp;
            });
            topOpportunities.sort((a, b) => {
                if (a.hours < b.hours) return 1;
                return -1; // if (a.hours > b.hours) return -1;
            });
            return topOpportunities.slice(0, 3);
        },
        topCashDonors() {
            const topCashDonors = this.analytics.lists.volunteers.map((item) => {
                const vol = {
                    id: item.id,
                    name: item.name,
                    dollars: item.dollars,
                    ava: item.profilePhoto,
                };
                return vol;
            });
            topCashDonors.sort((a, b) => {
                if (a.dollars < b.dollars) return 1;
                return -1; // if (a.dollars > b.dollars) return -1;
            });
            return topCashDonors.slice(0, 3);
        },
        topTecDonors() {
            const topTecDonors = this.analytics.lists.volunteers.map((item) => {
                const vol = {
                    id: item.id,
                    name: item.name,
                    tec: item.tec,
                    ava: item.profilePhoto,
                };
                return vol;
            });
            topTecDonors.sort((a, b) => {
                if (a.tec < b.tec) return 1;
                return -1; // if (a.tec > b.tec) return -1;
            });
            return topTecDonors.slice(0, 3);
        },
    },
    created() {
        this.lists = this.analytics.lists;
    },
    mounted() {
        this.changeLine(this.showComponent);
    },
};

export default AnalyticsExtended;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .analytics {
        &__row {
        }
        &__col {
        }
        &__panel {
        }
    }

    .analytics-list {
        &__head {
            // padding: 0 32px;
            height: 64px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            position: relative;
        }
        &__left {
            // padding: 0 32px;
            display: flex;
            align-items: center;
        }
        &__right {
            padding: 0 32px;
            display: flex;
            align-items: center;
            justify-content: flex-end;
        }
        &__head-title {
            @include fnt($text, 1rem, $weight-semibold, left);
            cursor: pointer;
            margin: 0;
            padding: 0 32px;
            text-transform: capitalize;
        }
        &__head-title + &__head-title {
            //margin-left: 22px;
        }
        &__head-sub-title {
            @include fnt($blue, 1rem, $weight-semibold, right);
            cursor: pointer;
            margin: 0;
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
        &__tab-line {
            background-color: $border;
            bottom: 0;
            height: 2px;
            position: absolute;
            transition: all .3s;
            width: 100%;
        }
        &__tab-line-active {
            background-color: $primary;
            height: 2px;
            position: absolute;
            transition: all .3s;
        }

    }

    .download-csv {
        display: flex;
    }
</style>
