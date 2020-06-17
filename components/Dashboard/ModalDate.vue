<template>
  <b-modal
    :ok-disabled="date === null || date === ''"
    id="modal"
    @ok="oldInventoryGet"
    title="Ancien bilans d'inventaire"
  >
    <v-select v-model="date" :options="dates"></v-select>
  </b-modal>
</template>

<script>
export default {
  name: 'ModalDate',
  props: ['dates'],
  data() {
    return {
      date: null
    }
  },
  methods: {
    oldInventoryGet() {
      location.href = `/inventaire/${this.date.label}`
    }
  }
}
</script>

<style lang="scss" scoped></style>
