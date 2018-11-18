<template>
    <div class="g-select" v-click-outside="closeDropdown">
        <p @click="toggle" class="g-select__arrow" :class="{'g-select__arrow--transformed': isOpened}">Авиакомпании</p>
        <div v-show="isOpened" class="g-select__dropdown">
            <label class="g-select__item g-checkbox">
                <input type="checkbox" :checked="allButton" @change="selectAll">
                Все
            </label>
            <label v-for="airline in airlines" :key="airline.code" class="g-select__item g-checkbox">
                <input :value="airline.code" type="checkbox" v-model="selectedAirlines" @change="showSpecific">
                {{airline.name}}
            </label>
        </div>
    </div>
</template>

<script>
export default {
  name: 'app-select',
  props: {
    airlines: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      isOpened: false,
      selectedAirlines: [...new Set(this.airlines.map(item => item.code))],
    };
  },
  computed: {
    allButton() {
      return this.selectedAirlines.length === this.airlines.length;
    },
  },
  directives: {
    'click-outside': {
      bind(el, binding, vnode) {
        el.event = function (event) {
          if (!(el == event.target || el.contains(event.target))) {
            vnode.context[binding.expression](event);
          }
        };
        document.body.addEventListener('click', el.event);
      },
      unbind(el) {
        document.body.removeEventListener('click', el.event);
      },
    },
  },
  methods: {
    showSpecific() {
      this.$emit('show-specific', this.selectedAirlines);
    },
    toggle() {
      this.isOpened = !this.isOpened;
    },
    closeDropdown() {
      if (this.isOpened) {
        this.toggle();
      }
    },
    selectAll() {
      if (this.allButton) {
        this.selectedAirlines = [];
      } else {
        this.selectedAirlines = [...new Set(this.airlines.map(item => item.code))];
      }
      this.$emit('show-specific', this.selectedAirlines);
    },
  },
};
</script>

<style lang="scss" scoped>
.g-select {
    margin-right: 3.5rem;
}
</style>
