<template>
    <div class="datepicker paper-elevated inline-block pa-2">
        <div class="datepicker-calendar inline-block">
            <div class="datepicker-calendar--header text-subtitle1">
                <div class="datepicker-calendar--header__dates">
                    <span class="pick-month">

                        <button @click="onSubstractionMonth" class="month-change go-to-previous-month text-subtitle1">
                            <i class="ri-arrow-left-s-line"></i>
                        </button>
                        <span>
                            {{ months[selectedMonth] }}
                        </span>
                        <button @click="onAddMonth" class="month-change go-to-previous-month text-subtitle1">
                            <i class="ri-arrow-right-s-line"></i>
                        </button>
                    </span>
                    <span class="datepicker-calendar--header__dates year-and-month">
                        <span>
                            <select v-model="selectedMonth" class="text-subtitle1" name="" id="">
                                <option v-for="(month, i) in months" :key="i" :value="i">
                                    {{ month }}
                                </option>
                            </select>
                        </span>
                        <span class="pick-year">
                            <select v-model="selectedYear" class="text-subtitle1">
                                <option v-for="year in years" :value="year" :key="year">{{ year }}</option>
                            </select>
                        </span>
                    </span>
                </div>
                <div class="datepicker-calendar--header__days-row flex gap-3 my-2 justify-evenly text-center">
                    <div class="day-unit">ПН</div>
                    <div class="day-unit">ВТ</div>
                    <div class="day-unit">СР</div>
                    <div class="day-unit">ЧТ</div>
                    <div class="day-unit">ПТ</div>
                    <div class="day-unit">СБ</div>
                    <div class="day-unit">ВС</div>
                </div>
            </div>

            <div class="datepicker-calendar--body">
                <div class="datepicker-calendar--body__days-row 
                                                    flex justify-evenly text-subtitle2 gap-3">
                    <div class="pa-1" v-for="othersDay in monthFirstDayWeekDay(1).getDay()" :key="othersDay"></div>
                    <div v-for="day in monthDays.getDate()" :key="day" class="text-center day-unit pa-1"
                        :class="[{ active: activeDates(day) }]" @click="onSelectedDates(day)">{{ day }}</div>
                </div>
            </div>

            <button @click="onModelValue" class="emit-button">Сохранять</button>
        </div>
    </div>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from "vue";

export default defineComponent({
    props: {
        minYear: {
            type: Number,
            default: 1950
        },
        maxYear: {
            type: Number,
            default: 2050
        },
        weekDays: {
            type: [],
            default: ['']
        },
        isMulti: {
            type: Boolean,
            default: false
        }
    },
    emits: ['getValue'],
    setup(props, { emit }) {
        const months = ref([
            'Январь',
            'Февраль',
            'Март',
            'Aпрель',
            'Май',
            'Июнь',
            'Июль',
            'Август',
            'Сентябрь',
            'Октябрь',
            'Ноябрь',
            'Декабрь'
        ]);
        const selectedDates = ref<any>([]);
        const selectedMonth = ref(0);

        const selectedYear = ref(2023);
        const years = computed(() => {
            let arr = [];
            for (let i = props.minYear; i <= props.maxYear; i++) {
                arr.push(i);
            }
            return arr;
        })

        const getDate = (year: any, month: any, day: any) => {
            return new Date(year, month, day);
        }


        const monthDays = computed(() => {
            return getDate(selectedYear.value, selectedMonth.value, 0);
        });

        const monthFirstDayWeekDay = computed(() => {
            return (day: any) => getDate(selectedYear.value, selectedMonth.value, day);
        })

        const onAddMonth = () => {
            if (selectedMonth.value == 11) {
                selectedYear.value++;
                selectedMonth.value = 0;
                return;
            }
            selectedMonth.value++;
        }

        const onSubstractionMonth = () => {
            if (selectedMonth.value == 0) {
                selectedYear.value--;
                selectedMonth.value = 11;
                return;
            }
            selectedMonth.value--;
        }

        const onSelectedDates = (day: any) => {
            const date = getDate(selectedYear.value, selectedMonth.value, day).getTime();

            const isFound = selectedDates.value?.find((d: number) =>
                d == date
            );

            if (isFound) {
                selectedDates.value = selectedDates.value?.filter((d: number) =>
                    d != date
                );
            } else {
                if (props.isMulti) {
                    selectedDates.value = [...selectedDates.value, date];
                    selectedDates.value.sort();
                } else {
                    selectedDates.value = [date];
                }
            }


        }

        const activeDates = computed(() => (day: any) => {
            const isFound = selectedDates.value.toString().includes(monthFirstDayWeekDay.value(day).getTime())

            return isFound;

        })


        const onModelValue = () => {
            if(props.isMulti) {
                emit('getValue', selectedDates.value);
            } else {
                emit('getValue', selectedDates.value[0]);
            }
        }
        return {
            years,
            months,
            selectedMonth,
            selectedYear,
            monthDays,
            monthFirstDayWeekDay,
            onAddMonth,
            onSubstractionMonth,
            onSelectedDates,
            selectedDates,
            activeDates,
            onModelValue
        }
    }
})
</script>


<style lang="scss" scoped>
$white: white;
$primary: rgb(7, 144, 108);

.datepicker {
    &-calendar {
        &--header {

            &__dates {
                .pick-month {
                    display: flex;
                    justify-content: center;
                    align-items: center;
                }


                .year-and-month {
                    display: flex;
                    justify-content: space-around;
                }

                select {
                    border: none;
                    background: transparent;
                    outline: none;
                    font-size: 1.4rem;
                    padding: .1rem;
                    margin-bottom: 1rem;

                    option {
                        outline: none;
                        border: none;
                        appearance: none;
                        padding: .4rem;
                    }
                }

                button {
                    border: none;
                    outline: none;
                    padding: .4rem .8rem;
                    background: transparent;
                }
            }

            &__days-row {
                display: grid;
                grid-template-columns: repeat(7, 1fr);
                gap: 1rem;
            }
        }

        &--body {
            &__days-row {
                display: grid;
                gap: 1rem;
                grid-template-columns: repeat(7, 1fr);

                .day-unit {
                    height: 2rem;
                    width: 2rem;
                    display: block;
                    text-align: center;
                    padding: .4rem;
                    padding-top: .7rem;
                    border-radius: .4rem;
                    cursor: pointer;

                    a {
                        text-decoration: none;
                    }
                }

                & div:nth-child(7n) {
                    background: rgba(203, 75, 11, 0.567);
                }

                .active {
                    align-self: center;
                    padding-top: .7rem;
                    color: $white;
                    background: $primary !important;
                }
            }
        }

        .emit-button {
            float: right;
            background: $primary !important;
            color: $white;
            border: none;
            outline: none;
            &:hover {
                background-color: rgba($color: $primary, $alpha: .7) !important;
            }

            &:active {
                background-color: rgba($color: $primary, $alpha: .4) !important;
            }
        }
    }
}
</style>