<template>
    <div  class="FiltersOpportunity analytics-filters">
        <div class="analytics-filters__left">
            <div class="analytics-filters__item">
                <div class="analytics-filters__header">Column</div>
                <el-select v-model="filterColumn"
                           placeholder="Select a Column"
                           style="width: 160px"
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
                 v-if="filterColumn !== 'status'"
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
                 v-if="filterColumn !== 'filledPrc' && filterColumn!== 'status'"
            >
                <div class="analytics-filters__header">Value</div>
                <el-input v-model="filterValue"
                          placeholder="Please input"
                          style="width: 160px"
                          @change="changeFilters"
                />
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
                 v-if="filterColumn === 'filledPrc'"
            >
                <div class="analytics-filters__header">Value</div>
                <el-select v-model="filterFilled"
                           placeholder="Select a value"
                           style="width: 160px"
                           @change="changeFilters"
                >
                    <el-option
                            v-for="item in selectFilled"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                    />
                </el-select>
            </div>
            <div class="analytics-filters__item"
                 style="margin-left: 8px"
                 v-if="filterColumn === 'status'"
            >
                <div class="analytics-filters__header">Status</div>
                <el-select v-model="filterStatus"
                           placeholder="Select a value"
                           style="width: 160px"
                           @change="changeFilters"
                >
                    <el-option
                            v-for="item in selectStatus"
                            :key="item.value"
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
                <el-button
                        @click="clearFilters"
                >Clear</el-button>
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

const FiltersOpportunity = {
    name: 'filters-opportunity',
    props: {
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
            filterColumn: '',
            selectColumn: [{
                value: 'status',
                label: 'Status',
            }, {
                value: 'volunteers',
                label: 'Volunteers (Total)',
            }, {
                value: 'hours',
                label: 'Hours (Total)',
            }, {
                value: 'shiftsAmount',
                label: 'Shifts',
            }, {
                value: 'positions',
                label: 'Positions (Total)',
            }, {
                value: 'filledPrc',
                label: 'Positions Filled',
            }, {
                value: 'app',
                label: 'Applications (Total)',
            }, {
                value: 'pending',
                label: 'Pending Applications',
            }],
            filterFilled: '',
            selectFilled: [{
                value: '25',
                label: '25%',
            }, {
                value: '50',
                label: '50%',
            }, {
                value: '75',
                label: '75%',
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
            filterStatus: '',
            selectStatus: [{
                value: 'active',
                label: 'Active',
            }, {
                value: 'inactive',
                label: 'Inactive',
            }, {
                value: 'draft',
                label: 'Draft',
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
            this.filterColumn = '';
            this.filterFilled = '';
            this.filterOperator = '';
            this.filterStatus = '';
            this.filterValue = 100;
            this.changeFilters();
        },
        changeFilters() {
            this.$emit('change-filters-opportunity', {
                filterColumn: this.filterColumn,
                filterFilled: this.filterFilled,
                filterOperator: this.filterOperator,
                filterStatus: this.filterStatus,
                filterValue: this.filterValue,
            });
        },
    },
    computed: {
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
export default FiltersOpportunity;

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
            align-items: center;
            background-color: transparent;
            border-color: $border;
            border-radius: 4px;
            cursor: pointer;
            display: flex;
            justify-content: center;
            line-height: 1;
            outline:none;
            padding: 10px 15px;
            transition: all .3s;
            white-space: nowrap;

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
