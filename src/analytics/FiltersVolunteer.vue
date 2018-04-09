<template>
    <div class="FiltersVolunteer analytics-filters">
        <div class="analytics-filters__left">
            <div class="analytics-filters__item">
                <div class="analytics-filters__header">Opportunity</div>
                <el-select v-model="filterOpportunity"
                           placeholder="Select an Opportunity"
                           style="width: 160px"
                           @change="changeFilters"
                >
                    <el-option
                            v-for="item in selectOpportunity"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                    />
                </el-select>
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
            >
                <div class="analytics-filters__header">Column</div>
                <el-select v-model="filterColumn"
                           placeholder="Select a Column"
                           style="width: 120px"
                           @change="changeFilters"
                >
                    <el-option
                            v-for="item in selectColumn"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                    />
                </el-select>
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
                 v-if="filterColumn !== 'gender' && filterColumn !== 'skill'"
            >
                <div class="analytics-filters__header">Operator</div>
                <el-select v-model="filterOperator"
                           placeholder="Select an Operator"
                           style="width: 80px"
                           @change="changeFilters"
                >
                    <el-option
                            v-for="item in selectOperator"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                    />
                </el-select>
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
                 v-if="filterColumn !== 'gender' && filterColumn !== 'skill'"
            >
                <div class="analytics-filters__header">Value</div>
                <el-input v-model="filterValue"
                          @change="changeFilters"
                          placeholder="Please input"
                          style="width: 80px"
                />
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
                 v-if="filterColumn === 'gender'"
            >
                <div class="analytics-filters__header">Gender</div>
                <el-select v-model="filterGender"
                           placeholder="Select an Operator"
                           style="width: 80px"
                           @change="changeFilters"
                >
                    <el-option
                            v-for="item in selectGender"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                    />
                </el-select>
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
                 v-if="filterColumn === 'skill'"
            >
                <div class="analytics-filters__header">Skill</div>
                <el-select v-model="filterSkill"
                           placeholder="Select a Skill"
                           style="width: 160px"
                           @change="changeFilters"
                >
                    <el-option
                            v-for="(item, index) in skillsSorted"
                            :key="index"
                            :label="item.label"
                            :value="item.value"
                    />
                </el-select>
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 16px">
                <div class="analytics-filters__header"
                     style="opacity: 0"
                >Clear</div>
                <el-button @click="clearFilters">
                    Clear
                </el-button>
            </div>
        </div>
        <div class="analytics-filters__right">
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
            >
                <div class="analytics-filters__header">Start Range</div>
                <el-date-picker
                        style="width: 120px"
                        v-model="startDate"
                        type="date"
                        format="MM/dd/yyyy"
                        placeholder="Pick a start date"
                        :clearable="false"
                />
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
            >
                <div class="analytics-filters__header">End Range</div>
                <el-date-picker
                        style="width: 120px"
                        v-model="endDate"
                        type="date"
                        format="MM/dd/yyyy"
                        placeholder="Pick a end date"
                        :picker-options="pickerOptionsEndDate"
                        :clearable="false"
                />
            </div>

            <div class="analytics-filters__item"
                 style="margin-left: 16px">
                <div class="analytics-filters__header"
                     style="opacity: 0"
                >Apply</div>
                <button style="width: 63px"
                        class="analytics-filters__button"
                        :class="{
                            'analytics-filters__button--error': status.error,
                            'analytics-filters__button--success': status.success,
                        }"
                        @click="changeRange"
                >
                    <i class="fa fa-exclamation-circle"
                       v-if="status.error"
                    />
                    <i class="fa fa-check-circle"
                       v-else-if="status.success"
                    />
                    <i class="fa fa-spinner fa-spin"
                       v-else-if="status.loading"
                    />
                    <span v-else>Apply</span>
                </button>
            </div>
        </div>
    </div>
</template>

<script>
import moment from 'moment';

const FiltersVolunteer = {
    name: 'filters-volunteer',
    props: {
        selectOpportunity: {
            type: Array,
            default() {
                return [];
            },
        },
        selectSkill: {
            type: Array,
            default() {
                return [];
            },
        },
        status: {
            type: Object,
            default() {
                return {
                    loading: false,
                    success: false,
                    error: false,
                };
            },
        },
    },
    data() {
        return {
            startDate: Date.now(),
            endDate: Date.now(),
            filterOpportunity: '',
            filterColumn: '',
            selectColumn: [{
                value: 'hours',
                label: 'Hours',
            }, {
                value: 'dollars',
                label: 'Donations ($)',
            }, {
                value: 'tec',
                label: 'Donations (TEC)',
            }, {
                value: 'age',
                label: 'Age',
            }, {
                value: 'skill',
                label: 'Skill',
            }, {
                value: 'gender',
                label: 'Gender',
            }, {
                value: 'occurrences',
                label: 'Occurrences',
            }],
            filterSkill: '',
            filterGender: '',
            selectGender: [{
                value: 'm',
                label: 'Male',
            }, {
                value: 'f',
                label: 'Female',
            }],
            filterOperator: '',
            selectOperator: [{
                value: '>',
                label: '>',
            }, {
                value: '<',
                label: '<',
            }, {
                value: '=',
                label: '=',
            }],
            filterValue: 100,
        };
    },
    methods: {
        changeRange() {
            this.$emit('change-range', {
                startRange: moment(this.startDate).format('YYYY-MM-DD'),
                endRange: moment(this.endDate).format('YYYY-MM-DD'),
            });
        },
        clearFilters() {
            this.filterOpportunity = '';
            this.filterColumn = '';
            this.filterSkill = '';
            this.filterOperator = '';
            this.filterGender = '';
            this.filterValue = 100;
            this.changeFilters();
        },
        changeFilters() {
            this.$emit('change-filters-volunteer', {
                filterOpportunity: this.filterOpportunity,
                filterColumn: this.filterColumn,
                filterSkill: this.filterSkill,
                filterOperator: this.filterOperator,
                filterGender: this.filterGender,
                filterValue: this.filterValue,
            });
        },
    },
    computed: {
        skillsSorted() {
            return this.selectSkill.sort((a, b) => {
                if (a.value > b.value) return 1;
                return -1;
            });
        },
        pickerOptionsEndDate() {
            const options = {
                disabledDate(time) {
                    return time.getTime() > Date.now();
                },
            };
            return options;
        },
    },
    mounted() {
        const thisDate = new Date();
        const lastYear = thisDate.getFullYear() - 1;
        this.startDate = thisDate.setFullYear(lastYear);
    },
};
export default FiltersVolunteer;
</script>

<style lang="scss" rel="stylesheet/scss" scoped>
    @import '../../../../sass/variables/td_theme';

    .analytics-filters {
        padding: 28px 32px 0;
        width: 100%;
        display: flex;
        justify-content: space-between;

        &__left {
            display: flex;
        }
        &__right {
            display: flex;
            justify-content: flex-end;
        },
        &__item {
        }
        &__header {
            @include fnt($text-lighter, 12px, $weight-light, left);
            padding-bottom: 12px;
            line-height: 1;
        }
        &__button {
            @include fnt($text-light, 14px, $weight-light, center);
            border-color: $border;
            background-color: transparent;
            outline:none;
            transition: all .3s;
            padding: 10px 15px;
            border-radius: 4px;
            line-height: 1;
            white-space: nowrap;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;

            &:hover {
                border-color: $blue;
                color: $blue;
            }
            &--success {
                border-color: $success;
                color: $success;
                &:hover {
                    border-color: $success;
                    color: $success;
                }
            }
            &--error {
                border-color: $danger;
                color: $danger;
                &:hover {
                    border-color: $danger;
                    color: $danger;
                }
            }
        }
    }
    .fa {
        width: 14px;
    }
</style>

<style lang="scss" rel="stylesheet/scss">
    @import '../../../../sass/variables/td_theme.scss';
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
</style>
