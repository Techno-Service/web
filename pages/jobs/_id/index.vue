<template>
  <master>
    <div 
      v-coc-loading = "(!job && !fetchError)" 
      class="row"
      style="min-height: 40vh">
      <div 
        v-if = "job" 
        class = "row">
        <coc-avatar
          v-if = "job.car.brand && job.car.brand.length"
          :source = "`/snaps/brands/png/${job.car.brand.split(' ').join('-').toLowerCase()}.png`"
          scale = "100px"
          class = "col coc-margin-left-4px coc-margin-top-5px"/>
        <span 
          class="col coc-margin-x-0px coc-text-title" 
          style="margin-top: 20px">{{ `${ job.car.brand } / ${ job.car.model }` | CocCapitalizeName }}</span>
      </div>
      <Card v-if = "job">
        <p 
          slot = "title">
          <icon type = "ios-create"/>
          <span class="coc-subcolor-text">Edit Job</span>
        </p>
        <div class="row">
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Client Phone</label>
            <coc-input
              ref = "clientPhone"
              v-model = "input.client.phone"
              :scope = "['create-job']"
              :rules = "{ HasValue: { active: true } }"
              :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { phone: model.val, limit: 5 }})"
              :autocomplete-map-response = "res => $_.uniqBy(res.jobs, j => j.client.phone).map(o => o.client.phone)"
              placeholder = "Client Phone"
              icon = "ios-phone-portrait"
              size = "large"
              light-model
              allow-autocomplete
              @coc-enter = "handleAutocompleteSelect"
              @coc-select = "handleAutocompleteSelect" />
          </div>
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Client Name</label>
            <coc-input
              v-model = "input.client.name"
              :scope = "['create-job']"
              :rules = "{ HasValue: { active: true } }"
              placeholder = "Client Name"
              icon = "ios-person"
              size = "large"
              light-model />
          </div>
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Car Brand</label>
            <coc-input
              v-model = "input.car.brand"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              :data = "brands"
              :icon = "input.car.brand && input.car.brand.length ? null : 'ios-color-filter'"
              :style = "input.car.brand && input.car.brand.length ? 'width: calc( 100% - 40px )' : 'width: 100%'"
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
              class = "col coc-margin-left-4px coc-margin-top-5px"/>
          </div>
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Car Model</label>
            <coc-input
              v-model = "input.car.model"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              placeholder = "Car Model"
              icon = "ios-car"
              size = "large"
              light-model />
          </div>
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Car Release Year</label>
            <coc-select
              v-model = "input.car.release"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              :data = "generateYears()"
              placeholder = "Release Year"
              icon = "ios-calendar"
              size = "large"
              filterable
              clearable
              light-model />
          </div>
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Car Kilometers</label>
            <coc-input
              v-model = "input.car.kilometers"
              :scope = "['create-job']"
              :rules = "{ HasValue: true, IsNumericString: true, NumberGreaterThan: -1 }"
              placeholder = "Kilometers"
              icon = "ios-speedometer"
              size = "large"
              light-model />
          </div>
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Car Chase Number</label>
            <coc-input
              v-model = "input.car.chase"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              placeholder = "Chase Number"
              icon = "ios-barcode"
              size = "large"
              light-model />
          </div>
          <div class="col l6 m6 s12">
            <label class="coc-subcolor-text coc-text-small">Reciptionist</label>
            <coc-input
              v-model = "input.reciptionist"
              :scope = "['create-job']"
              :rules = "{ HasValue: true }"
              placeholder = "Reciptionist"
              icon = "ios-ionitron"
              size = "large"
              light-model />
          </div>
          <div class="col s12">
            <label class="coc-subcolor-text coc-text-small">Complain</label>
            <coc-input
              v-model = "input.complain"
              :scope = "['create-job']"
              :rules = "{ HasValue: { active: false } }"
              placeholder = "Complain..."
              size = "large"
              type = "textarea"
              light-model />
          </div>
          <div class="col s12">
            <label class="coc-subcolor-text coc-text-small">Notes</label>
            <coc-input
              v-model = "input.notes"
              :scope = "['create-job']"
              :rules = "{ MaxLength: 9000 }"
              placeholder = "Notes..."
              size = "large"
              type = "textarea"
              light-model />
          </div>
          <div class="col s12">
            <label class="coc-subcolor-text coc-text-small">Working Status</label>
            <coc-select
              v-model = "input.status"
              :scope = "['create-job']"
              :rules = "{ MaxLength: 9000 }"
              :data = "['running', 'postponed', 'finished']"
              placeholder = "Notes..."
              size = "large"
              light-model />
          </div>
          <Divider orientation = "left">Time Entry</Divider>
          <div 
            class="col s12 animated slideInUp">
            <table class = "full-width coc-border-1 coc-border-border coc-light-background-bg">
              <tr class = "coc-border-bg coc-content-text">
                <th class="coc-padding-y-10px">Time In</th>
                <th class="coc-padding-y-10px">Time Leave</th>
              </tr>
              <tr
                class = "coc-primary-hover-bg coc-secondary-hover-text coc-smooth-bg-color full-width center coc-border-bottom-1 animated slideInLeft">
                <td> {{ $moment(job.timein).fromNow() }} ({{ $moment(job.timein).format('D/M/YY h:m A') }}) </td>
                <td>{{ job.timeleave ? `${$moment(job.timeleave).fromNow()}  (${ $moment(job.timeleave).format('D/M/YY h:m A')})` : 'Not Finished Yet' }}</td>
              </tr>
            </table>
            <br>
          </div>
          <Divider orientation = "left">Operations / Parts</Divider>
          <div class="col s12">
            <div class="col s12 coc-background-bg coc-margin-y-10px coc-padding-y-10px coc-border-border coc-border-1 coc-standard-border-radius">
              <p class = "coc-text-subtitle">Add a Part / Operation</p>
              <div class="col s12 l4">
                <coc-input
                  ref = "partInput"
                  v-model = "externalInput.part"
                  :scope = "['add-part']"
                  :rules = "{ HasValue: true }"
                  placeholder = "Part / Operation"
                  icon = "ios-cog"
                  light-model
                  @coc-enter = "handleAutocompleteSelectPart"
                  @coc-select = "handleAutocompleteSelect" />
              </div>
              <div class="col s12 l3">
                <coc-input
                  v-model = "externalInput.price"
                  :scope = "['add-part']"
                  :rules = "{ HasValue: true, IsNumericString: true, NumberGreaterThan: -1 }"
                  placeholder = "Price"
                  icon = "ios-pricetag"
                  light-model />
              </div>
              <div class="col s12 l3">
                <coc-input
                  v-model = "externalInput.fees"
                  :rules = "{ IsNumericString: true, NumberGreaterThan: -1 }"
                  :scope = "['add-part']"
                  placeholder = "Fees"
                  icon = "ios-cash"
                  light-model />
              </div>
              <div class="col s12 l2">
                <coc-button
                  :scope = "['add-part']"
                  type = "default"
                  placeholder = "Add"
                  icon = "ios-add-circle"
                  class = "right coc-padding-y-4px"
                  local
                  reset
                  @coc-validation-passed = "handleAddPart" />
              </div>
              <div 
                v-if = "input.operations && input.operations.length" 
                class="col s12 animated slideInUp">
                <table class = "full-width coc-border-1 coc-border-border coc-light-background-bg">
                  <tr class = "coc-border-bg coc-content-text">
                    <th class="coc-padding-y-10px">Time In</th>
                    <th class="coc-padding-y-10px">Timeout</th>
                  </tr>
                  <tr
                    class = "coc-primary-hover-bg coc-secondary-hover-text coc-smooth-bg-color full-width center coc-border-bottom-1 animated slideInLeft">
                    <td> {{ $moment(job.timein).fromNow() }} {{ $moment(job.timein).format('D/M/YY h:m A') }} </td>
                    <td>{{ job.timeleave ? $moment(job.timeleave).fromNow() : 'Not Finished Yet' }}</td>
                  </tr>
                </table>
                <Divider orientation = "left">Operations / Installed Parts</Divider>
                <table class = "full-width coc-border-1 coc-border-border coc-light-background-bg">
                  <tr class = "coc-border-bg coc-content-text">
                    <th class="coc-padding-y-10px">Part</th>
                    <th class="coc-padding-y-10px">Price</th>
                    <th class="coc-padding-y-10px">Fees</th>
                    <th class="coc-padding-y-10px">Total</th>
                    <th class="coc-padding-y-10px">Actions</th>
                  </tr>
                  <tr 
                    v-for = "(operation, o) in input.operations" 
                    :key = "o"
                    class = "coc-primary-hover-bg coc-secondary-hover-text coc-smooth-bg-color full-width center coc-border-bottom-1 animated slideInLeft">
                    <td> {{ operation.part }} </td>
                    <td> {{ operation.price | CocTrimExtraZeros }} LE</td>
                    <td> {{ operation.fees | CocTrimExtraZeros }} LE</td>
                    <td> {{ operation.price + operation.fees | CocTrimExtraZeros }} LE</td>
                    <td>
                      <Button
                        icon = "md-trash coc-error-text coc-text-body"
                        class = "coc-margin-y-3px"
                        type = "text"
                        @click = "input.operations.splice( o, 1)"/>
                    </td>
                  </tr>
                </table>
                <table class = "full-width coc-border-1 coc-border-border coc-light-background-bg">
                  <tr class="center full-width coc-border-bg coc-content-text">
                    <th class="coc-padding-y-10px">Total Prices</th>
                    <th class="coc-padding-y-10px">Total Fees</th>
                    <th class="coc-padding-y-10px">Total</th>
                  </tr>
                  <tr class="center full-width">
                    <td class="coc-padding-y-10px">{{ $_.sum( input.operations.map( o => parseFloat(o.price, 10) ) ) | CocTrimExtraZeros }} LE</td>
                    <td class="coc-padding-y-10px">{{ $_.sum( input.operations.map( o => parseFloat(o.fees, 10) ) ) | CocTrimExtraZeros }} LE</td>
                    <td class="coc-padding-y-10px">
                      {{
                        $_.sum( input.operations.map( o => parseFloat(o.price, 10) ) ) +
                          $_.sum( input.operations.map( o => parseFloat(o.fees, 10) ) )
                      | CocTrimExtraZeros }} LE
                    </td>
                  </tr>
                </table>
              </div>
            </div>
          </div>
          <div class="col s12">
            <coc-button
              :scope = "['create-job']"
              :request = "{ xdata: formatInput(), url: `/job/${job._id}`, method: 'put' }"
              :validation-tolerence-time = "1200"
              placeholder = "Save"
              icon = "ios-checkmark-circle"
              class = "right coc-margin-top-3px"
              size = "large"
              @coc-submit-accepted = "handleResult"/>
          </div>
          <div class="col s12 coc-margin-top-10px coc-padding-x-0">
            <Button
              icon = "ios-trash"
              type = "text"
              class = "coc-error-text right coc-padding-bottom-0"
              @click = "deleteJob">Delete Job</Button>
          </div>
        </div>
      </Card>
      <Card v-else-if = "fetchError">
        <h3 class = "coc-text-title coc-content-text center">
          <Icon type = "ios-alert-outline coc-text-lg-2" /><br>
          <span class = "coc-text-lg-2">404</span><br>
          <span>Job Not Found</span>
        </h3>
      </Card>
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
  head() {
    return {
      title: this.job ? `Job | ${this.job.job_no}` : 'Job Not Found'
    }
  },
  data() {
    return {
      brands,
      fetchError: null,
      job: null,
      externalInput: {
        part: '',
        price: '',
        fees: ''
      },
      input: {
        notes: '',
        complain: '',
        reciptionist: '',
        car: {
          brand: '',
          model: '',
          release: '',
          chase: '',
          kilometers: ''
        },
        client: {
          name: '',
          phone: ''
        }
      }
    }
  },
  mounted() {
    this.getJob()
  },
  methods: {
    getJob() {
      this.$axios
        .get(`/job/${this.$route.params.id}`)
        .then(res => {
          this.fetchError = null
          this.job = res.data
          this.input = this.$_.cloneDeep(res.data)
          this.input.notes = this.input.notes ? this.input.notes : ' '
          this.input.complain = this.input.complain ? this.input.complain : ' '
          document.title = `Techno-Service | Job #${res.data.job_no}`
        })
        .catch(err => {
          this.fetchError = err
          this.job = null
        })
    },
    deleteJob() {
      this.$axios({ url: `/job/${this.job._id}`, method: 'delete' }).then(
        res => {
          this.$router.push('/jobs')
        }
      )
    },
    handleResult(e) {
      this.$root.$emit('updateRunningJobs')
      this.job = e.meta.response
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
    processExternalInput(operation = this.externalInput) {
      return {
        part: operation.part,
        price: parseFloat(operation.price, 10),
        fees: parseFloat(operation.fees, 10)
      }
    },
    handleAddPart() {
      this.input.operations.push(this.processExternalInput())
      this.$refs.partInput.focus()
    },
    formatInput() {
      const temp = this.$_.cloneDeep(this.input)
      temp.operations = temp.operations.map(o => this.processExternalInput(o))
      return this.$_.pick(temp, [
        'car',
        'client',
        'reciptionist',
        'complain',
        'notes',
        'status',
        'operations'
      ])
    },
    handleAutocompleteSelect(e) {
      const response = this.$refs.clientPhone.autocompleteRetriever.response.jobs.filter(
        c => c.client.phone === e
      )
      if (response.length) {
        this.input.client = response[response.length - 1].client
        this.input.car = response[response.length - 1].car
      }
    },
    // handleAutocompleteSelectPart(e) {
    //   const response = this.$refs.partInput.autocompleteRetriever.response.jobs.filter(
    //     c => c.operations.filter(p => p.part === e).length
    //   )
    //   if (response.length) {
    //     this.externalInput = response[response.length - 1].filter(
    //       p => p.part === e
    //     )[0]
    //   }
    // },
    merge(arrayOfArrays) {
      let i
      let j
      const result = []
      for (i = 0; i < arrayOfArrays.length; i += 1) {
        for (j = 0; j < arrayOfArrays[i].length; j += 1) {
          result.push(arrayOfArrays[i][j])
        }
      }
      return result
    }
    // mapPartsAutoComplete(res) {
    //   const allOperations = this.merge(res.jobs.map(j => j.operations))
    //   return this.$_.uniqBy(allOperations, o => o.part).map(p => p.part)
    // }
  }
}
</script>

<style lang="css" scoped>
</style>
