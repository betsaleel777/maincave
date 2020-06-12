<template>
  <b-navbar toggleable="lg" type="dark" variant="warning">
    <b-navbar-brand href=""
      ><nuxt-link id="dash" to="/">MainCave</nuxt-link></b-navbar-brand
    >

    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav>
        <li class="nav-item">
          <nuxt-link to="/Produits" class="nav-link">Produits</nuxt-link>
        </li>
        <li class="nav-item">
          <nuxt-link to="/Approvisionnements" class="nav-link"
            >Approvisionnements</nuxt-link
          >
        </li>
        <li class="nav-item">
          <nuxt-link to="/Ventes" class="nav-link">Ventes</nuxt-link>
        </li>
      </b-navbar-nav>

      <!-- Right aligned nav items -->
      <b-navbar-nav class="ml-auto">
        <!-- <b-nav-form>
          <b-form-input
            size="sm"
            class="mr-sm-2"
            placeholder="Search"
          ></b-form-input>
          <b-button size="sm" class="my-2 my-sm-0" type="submit"
            >Search</b-button
          >
        </b-nav-form> -->

        <b-nav-item-dropdown right>
          <!-- Using 'button-content' slot -->
          <template v-slot:button-content>
            <em>User</em>
          </template>
          <b-dropdown-item href="#">Profile</b-dropdown-item>
          <b-dropdown-item href="#">Sign Out</b-dropdown-item>
        </b-nav-item-dropdown>
      </b-navbar-nav>
    </b-collapse>
  </b-navbar>
</template>

<script>
// eslint-disable-next-line standard/object-curly-even-spacing
export default {}
</script>

<style scoped></style>
