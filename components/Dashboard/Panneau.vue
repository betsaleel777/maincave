<template>
  <div>
    <b-card :bg-variant="variant" text-variant="white">
      <template v-slot:header>
        <h6 class="mb-0">{{ titre }}</h6>
      </template>
      <b-card-text>
        <b-media right-align vertical-align="center">
          <template v-slot:aside>
            <h3>
              <b-badge :variant="variant" pill
                ><number
                  ref="nombre"
                  :to="valeur"
                  :format="formater"
                  :duration="5"
                  :delay="2"
                  easing="Power1.easeOut"
                ></number
              ></b-badge>
            </h3>
          </template>
          <h6 class="mt-0 mb-1">{{ libelle }}</h6>
        </b-media>
      </b-card-text>
    </b-card>
  </div>
</template>

<script>
export default {
  name: 'Panneau',
  props: ['variant', 'valeur', 'titre', 'libelle'],
  mounted() {
    this.$refs.nombre.play()
  },
  methods: {
    formater(nombre) {
      return `${nombre.toFixed(2)}`
    }
  }
}
</script>

<style lang="scss" scoped></style>
