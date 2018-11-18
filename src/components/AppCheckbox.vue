<template>
    <label class="g-checkbox">
        <input type="checkbox" :checked="value" @change="$emit('input', $event.target.checked)">
        Только прямые рейсы
    </label>
</template>

<script>
export default {
  name: 'app-checkbox',
  props: {
    value: {
      type: Boolean,
      required: true,
    },
  },
};
</script>
