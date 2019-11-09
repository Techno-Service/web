<template>
  <master>
    <div class="row">
      <Card>
        <p 
          slot = "title">
          <icon type = "ios-add"/>
          <span class="coc-subcolor-text">Create Job</span>
        </p>
        <div class="row">
          <div class="col l4 m6 s12">
            <coc-input
              ref = "clientPhone"
              v-model = "input.client.phone"
              :scope = "['create-job']"
              :rules = "{ HasValue: { active: true } }"
              :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { phone: model.val, limit: 5 }})"
              :autocomplete-map-response = "res => $_.uniqBy(res.jobs, j => j.client.phone).map(o => o.client.phone)"
              labeled
              placeholder = "Client Phone"
              icon = "ios-phone-portrait"
              size = "large"
              light-model
              allow-autocomplete
              @coc-enter = "handleAutocompleteSelect"
              @coc-select = "handleAutocompleteSelect" />
          </div>
          <div class="col l4 m6 s12">
            <coc-input
              v-model = "input.client.name"
              :scope = "['create-job']"
              :rules = "{ HasValue: { active: true } }"
              labeled
              placeholder = "Client Name"
              icon = "ios-person"
              size = "large"
              light-model />
          </div>
          <div class="col l4 m6 s12">
            <coc-input
              v-if = "brands && brands.length && isMounted"
              v-model = "input.car.brand"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              :data = "brands"
              :icon = "input.car.brand && input.car.brand.length ? null : 'ios-color-filter'"
              :style = "input.car.brand && input.car.brand.length ? 'width: calc( 100% - 40px )' : 'width: 100%'"
              labeled
              placeholder = "Car Brand"
              size = "large"
              class = "col"
              light-model
              allow-autocomplete />
            <coc-avatar
              v-if = "input.car.brand && input.car.brand.length"
              slot = "suffix"
              :source = "`/snaps/brands/png/${input.car.brand.split(' ').join('-').toLowerCase()}.png`"
              scale = "30px"
              class = "col coc-margin-left-4px coc-margin-top-25px"/>
          </div>
          <div class="col l4 m6 s12">
            <coc-input
              v-model = "input.car.model"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              labeled
              placeholder = "Car Model"
              icon = "ios-car"
              size = "large"
              light-model />
          </div>
          <div class="col l4 m6 s12">
            <coc-select
              v-model = "input.car.release"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              :data = "generateYears()"
              labeled
              placeholder = "Release Year"
              icon = "ios-calendar"
              size = "large"
              filterable
              clearable
              light-model />
          </div>
          <div class="col l4 m6 s12">
            <coc-input
              v-model = "input.car.kilometers"
              :scope = "['create-job']"
              :rules = "{ HasValue: true, IsNumericString: true, NumberGreaterThan: 0 }"
              labeled
              placeholder = "Kilometers"
              icon = "ios-speedometer"
              size = "large"
              light-model />
          </div>
          <!-- <div class="col l4 m6 s12">
            <coc-input
              v-model = "input.car.chase"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              labeled
              placeholder = "Chase Number"
              icon = "ios-barcode"
              size = "large"
              light-model />
          </div> -->
          <div class="col l4 m6 s12">
            <coc-input
              v-model = "input.reciptionist"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              labeled
              placeholder = "Reciptionist"
              icon = "ios-ionitron"
              size = "large"
              light-model />
          </div>
          <div class="col l4 m6 s12 right">
            <coc-button
              :scope = "['create-job']"
              :request = "{ xdata: input, url: '/job' }"
              placeholder = "Create"
              icon = "ios-add-circle"
              class = "right coc-margin-top-25px"
              size = "large"
              reset
              @coc-submit-accepted = "handleResult"/>
          </div>
        </div>
      </Card>
      <!-- <Card v-else>
        <p class = "coc-text-title coc-error-text center">
          <Icon type = "ios-alert-outline"/>
          You Are Not Logged In
        </p>
        <br>
        <p class="center">
          <button-group>
            <Button
              type = "default"
              size = "large"
              shape = "circle"
              style = "width: 120px"
              @click = "askForLogin">
              Login
            </Button>
          </button-group>
        </p>
      </Card> -->
    </div>
  </master>
</template>

<script>
import Master from '~/components/common/master'
import brands from '~/plugins/brands'
export default {
  name: 'Index',
  components: {
    Master
  },
  data() {
    return {
      isMounted: false,
      brands,
      input: {
        reciptionist: '',
        car: {
          brand: '',
          model: '',
          release: '',
          chase: '--',
          kilometers: ''
        },
        client: {
          name: '',
          phone: ''
        }
      }
    }
  },
  computed: {
    user() {
      return this.$store.state.core.auth
    }
  },
  mounted() {
    setTimeout(() => {
      this.isMounted = true
    }, 1000)
    setTimeout(() => {
      if (this.user) {
        this.input.reciptionist = this.user.name
      }
    }, 500)
    this.$root.$on('LoggedIn', p => {
      setTimeout(() => {
        if (this.user) {
          this.input.reciptionist = p.name
        }
      }, 500)
    })
  },
  methods: {
    askForLogin() {
      this.$root.$emit('askForLogin')
    },
    handleResult(e) {
      this.$store.dispatch('setRunningJobs', [
        ...this.$store.state.jobs.running,
        e.meta.response
      ])
      setTimeout(() => {
        this.input.reciptionist = this.user.name
      }, 900)
    },
    generateYears() {
      let i
      let result = []
      const year = parseInt(this.$moment().format('YYYY'), 10) + 1
      for (i = 0; i < 100; i += 1) {
        result.push((year - i).toString())
      }
      return result
    },
    handleAutocompleteSelect(e) {
      if (!e || !e.length) return
      const response = this.$refs.clientPhone.autocompleteRetriever.response.jobs.filter(
        c => c.client.phone === e
      )
      if (response.length) {
        this.input.client = response[response.length - 1].client
        this.input.car = response[response.length - 1].car
      }
    }
  }
}
</script>

<style lang="css" scoped>
</style>
