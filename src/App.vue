<template>
  <div id="app">
    <section class="hero is-bold is-fullheight ">
     <!-- <div class="hero-head">
        <nav class="navbar">
          <div class="container">
            <div class="navbar-brand">
              <a class="navbar-item">
              </a>
            </div>
          </div>
        </nav>
      </div>
      -->
      <div class="container">
        <h1 class="title is-1">
          PES
        </h1>
        <h2 class="subtitle">
         <strong>P</strong>rotein <strong>E</strong>nrichment <strong>S</strong>ystem
        </h2>
      </div>
      
       <!-- Hero content: will be in the middle -->
      <div class="hero-body">
        <div class="container has-text-centered">
          <div class="row">
            <div class="columns">
              <div class="column is-half is-offset-one-quarter">
                <parameters-form></parameters-form>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="hero-foot">
        <div class="container">
          <div class="content has-text-centered">
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import ParametersForm from './components/ParametersForm'

export default {
  name: 'app',
  components: {
    ParametersForm
  },
  data () {
    return {
    }
  }
}
</script>

<style lang="sass" src="bulma"></style>
<style lang="sass">
  $fa-font-path: '../node_modules/font-awesome/fonts/';
  @import '../node_modules/font-awesome/scss/font-awesome';
</style>
