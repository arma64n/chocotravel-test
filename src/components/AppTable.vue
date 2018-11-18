<template>
    <section class="g-table" >
        <div class="g-table__header">
            <div class="g-table__row">
                <div class="g-table__heading" style="width: 6.875rem; box-sizing: content-box">Авиакомпания</div>
                <div class="g-table__heading mobile-hidden">Рейс</div>
                <div class="g-table__heading tablet-hidden" style="width: 9rem; box-sizing: content-box">Выбор времени</div>
                <div class="g-table__heading" >Вылет - посадка</div>
                <div class="g-table__heading g-table__heading--sortable xs-hidden" @click="sortBy('flightDuration')">
                    Время в пути
                    <span v-if="currentSort === 'flightDuration'">
                        <template v-if="ascending">&#9650;</template>
                        <template v-else>&#9660;</template>
                    </span>
                    <span v-else>&#x21D5;</span>
                </div>
                <div class="g-table__heading g-table__heading--sortable" @click="sortBy('price')" style="width: 11rem; box-sizing: content-box">
                    Цена
                    <span v-if="currentSort === 'price'">
                        <template v-if="ascending">&#9650;</template>
                        <template v-else>&#9660;</template>
                    </span>
                    <span v-else>&#x21D5;</span>
                </div>
            </div>
        </div>
        <div class="g-table__body" v-if="list.length">
            <div class="g-table__row g-table__row--wide" v-for="(item, index) in list" :key="index">
                <div class="g-table__item">
                    <template v-for="(flight, index) in item.flights" >
                        <img :src="`/airlines/${flight.airline.code}.png`" class="g-table__logo" :key="index"/>
                    </template>
                </div>
                <div class="g-table__item mobile-hidden">{{ item.flights.length > 1 ? "с пересадкой" : "прямой" }}</div>
                <div class="g-table__item tablet-hidden" style="width: 9rem; box-sizing: content-box">
                    <div class="underlined underlined--arrow">Выбрать другое время</div>
                    <div class="underlined">Поделиться</div>
                </div>
                <div class="g-table__item">
                    <div v-for="(time, index) in item.flights" :key="index">
                        <div><strong>{{ time.departureTime }}</strong> - {{time.arrivalTime}}</div>
                    </div>
                </div>
                <div class="g-table__item xs-hidden">{{ item.flightDuration | duration }}</div>
                <div class="g-table__item">
                    <button class="g-button">Купить за {{ item.price | tenge }}</button>
                </div>
            </div>
        </div>
        <div class="g-table__body g-table__body--empty" v-else>
            <p>Не найдено рейсов удовлетворяющих заданным условиям :(</p><br>
            <p>Может попробуете <a href="#" @click="$emit('refresh')">снова</a>?</p>
        </div>
    </section>
</template>

<script>
export default {
  name: 'app-table',
  props: {
    list: {
      type: Array,
      required: true,

    },
  },
  data() {
    return {
      ascending: true,
      currentSort: '',
    };
  },
  filters: {
    tenge(val) {
      if (!val) return '';
      return `${val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ')} ₸`;
    },
    duration(val) {
      const hours = Math.floor(val / 60);
      const mins = val % 60;
      return `${hours}ч ${mins}м`;
    },
  },
  methods: {
    // Сортировка полей
    sortBy(val) {
      this.currentSort = val;
      this.ascending = !this.ascending;
      return this.list.sort((a, b) => (this.ascending ? a[val] - b[val] : b[val] - a[val]));
    },
  },
};
</script>

<style lang="scss" scoped>
.underlined {
    border-bottom: 1px dotted #9EA3B7;
    cursor: pointer;
    display: inline-block;

    &--arrow {
        position: relative;

        &:after {
            content: '';
            position: absolute;
            display: inline-block;
            top: 40%;
            right: -.75rem;
            width: .35rem;
            height: .35rem;
            border-bottom: .5px solid #44ABD8;
            border-right: .5px solid #44ABD8;
            border-top: 0;
            border-left: 0;
            transform: rotate(45deg);
        }
    }
}
</style>
