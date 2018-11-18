<template>
    <div id="app">
        <div class="g-wrap">
            <div class="app-filter">
                <app-select :airlines="allAirlinesWithDetailedInfo" @show-specific="showSpecific" :key="uniqueKey"></app-select>
                <app-checkbox v-model="directFlight"></app-checkbox>
            </div>
            <app-table :list="filteredOverall" @refresh="refreshTable" :key="uniqueKey"></app-table>
        </div>
    </div>
</template>

<script>
import json from '@/assets/data.json'
import AppSelect from '@/components/AppSelect'
import AppCheckbox from '@/components/AppCheckbox'
import AppTable from '@/components/AppTable'

export default {
    components: {
        AppSelect,
        AppCheckbox,
        AppTable
    },
    data() {
        return {
            flightsArray: json,
            directFlight: false,
            chosenAirlines: [],
            uniqueKey: 0
        }
    },
    computed: {
        //Фильтрация на прямой рейс
        filteredByDirect() {
            return this.directFlight ? this.flightsArray.filter(x => x.flights.length === 1) : this.flightsArray
        },
        //Фильтрация прямой рейс + выбранные авиакомпании
        filteredOverall() {
            return this.filteredByDirect.filter(x => x.flights.some(y => this.chosenAirlines.includes(y.airline.code)))
        },
        allAirlines() {
            let array = []
            this.flightsArray.map(x => {
                x.flights.forEach(y => {
                    array.push({
                        code: y.airline.code,
                        name: y.airline.name
                    })
                });
            })
            return array
        },
        // массив уникальных авиалиний для передачи в селект
        allAirlinesWithDetailedInfo() {
            const unique = new Set();
            const detailedArray = this.allAirlines.filter(entry => {
                if (unique.has(entry.code)) {
                    return false;
                }
                unique.add(entry.code);
                return true;
            })
            return detailedArray
        }
    },
    mounted() {
        this.chosenAirlines = [...new Set(this.allAirlines.map(item => item.code))]
    },
    methods: {
        showSpecific(val) {
            this.chosenAirlines = val
        },
        refreshTable() {
            this.chosenAirlines = [...new Set(this.allAirlines.map(item => item.code))]
            this.directFlight = false
            this.uniqueKey++ //Ре-рендерит компонент AppSelect, чтоб чекбоксы снова закрасились
        }
    }
}
</script>

<style lang="scss" scoped>
.app-filter {
    font-size: .875rem;
    margin-bottom: 1rem;
    color: #212C5B;
    display: flex;
}
</style>
