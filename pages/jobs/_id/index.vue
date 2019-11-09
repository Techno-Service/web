<template>
  <master :on-print = "onPrint">
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
          :scale = "onPrint ? '30px' : '100px'"
          class = "col coc-margin-left-4px coc-margin-top-5px"/>
        <span 
          :class = "onPrint ? 'coc-text-md' : 'coc-text-title'"
          :style="onPrint ? 'margin-top: 10px; font-weight: bold' : 'margin-top: 20px'" 
          class="col coc-margin-x-0px">{{ `${ job.car.brand } / ${ job.car.model }` | CocCapitalizeName }}</span>
        <button-group
          v-if = "!onPrint"
          class = "right">
          <Button   
            :type = "onPrint ? 'primary' : 'default'"         
            :size = "onPrint ? 'small' : 'large'"
            icon = "ios-print-outline"
            @click = "print" />
          <Button
            :size = "onPrint ? 'small' : 'large'"
            icon = "ios-refresh"
            @click = "getJob" />
        </button-group>
      </div>
      <component
        v-if = "job"
        :is = "onPrint ? 'DIV' : 'Card'">
        <p 
          v-if = "!onPrint" 
          slot = "title">
          <icon :type = "onPrint ? 'ios-information-circle-outline' : 'ios-create'"/>
          <span class="coc-subcolor-text">{{ onPrint ? 'Job Details' : 'Edit Job' }}</span>
        </p>
        <div class="row">
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Client Phone</label>
            <coc-input
              ref = "clientPhone"
              v-model = "input.client.phone"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: { active: true } }"
              :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { phone: model.val, limit: 5 }})"
              :autocomplete-map-response = "res => $_.uniqBy(res.jobs, j => j.client.phone).map(o => o.client.phone)"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Client Phone"
              icon = "ios-phone-portrait"
              light-model
              allow-autocomplete
              @coc-enter = "handleAutocompleteSelect"
              @coc-select = "handleAutocompleteSelect" />
          </div>
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Client Name</label>
            <coc-input
              v-model = "input.client.name"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: { active: true } }"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Client Name"
              icon = "ios-person"
              light-model />
          </div>
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Car Make</label>
            <coc-input
              v-model = "input.car.brand"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: true }"
              :data = "brands"
              :icon = "input.car.brand && input.car.brand.length ? null : 'ios-color-filter'"
              :style = "input.car.brand && input.car.brand.length ? 'width: calc( 100% - 40px )' : 'width: 100%'"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Car Make"
              class = "col"
              light-model
              allow-autocomplete />
            <coc-avatar
              v-if = "input.car.brand && input.car.brand.length"
              slot = "suffix"
              :source = "`/snaps/brands/png/${input.car.brand.split(' ').join('-').toLowerCase()}.png`"
              :scale = "onPrint ? '20px' : '30px'"
              class = "col coc-margin-left-4px coc-margin-top-5px"/>
          </div>
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Car Model</label>
            <coc-input
              v-model = "input.car.model"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: true }"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Car Model"
              icon = "ios-car"
              light-model />
          </div>
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Car Release Year</label>
            <coc-select
              v-model = "input.car.release"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: true }"
              :data = "generateYears()"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Release Year"
              icon = "ios-calendar"
              filterable
              clearable
              light-model />
          </div>
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Car Kilometers</label>
            <coc-input
              v-model = "input.car.kilometers"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: true, IsNumericString: true, NumberGreaterThan: -1 }"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Kilometers"
              icon = "ios-speedometer"
              light-model />
          </div>
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Car Chase Number</label>
            <coc-input
              v-model = "input.car.chase"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: true }"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Chase Number"
              icon = "ios-barcode"
              light-model />
          </div>
          <div class="col s6">
            <label class="coc-subcolor-text coc-text-small">Reciptionist</label>
            <coc-input
              v-model = "input.reciptionist"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: true }"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Reciptionist"
              icon = "ios-ionitron"
              light-model />
          </div>
          <div class="col s12">
            <label class="coc-subcolor-text coc-text-small">Complain</label>
            <coc-input
              v-model = "input.complain"
              :scope = "['create-edit-job']"
              :rules = "{ HasValue: { active: false } }"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Complain..."
              type = "textarea"
              light-model />
          </div>
          <div class="col s12">
            <label class="coc-subcolor-text coc-text-small">Notes</label>
            <coc-input
              v-model = "input.notes"
              :scope = "['create-edit-job']"
              :rules = "{ MaxLength: 9000 }"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Notes..."
              type = "textarea"
              light-model />
          </div>
          <div class="col s12">
            <label class="coc-subcolor-text coc-text-small">Working Status</label>
            <coc-select
              v-model = "input.status"
              :scope = "['create-edit-job']"
              :rules = "{ MaxLength: 9000 }"
              :data = "['running', 'postponed', 'finished']"
              :hide-status = "onPrint"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Notes..."
              light-model />
          </div>
          <Divider 
            v-if = "!onPrint" 
            orientation = "left">Time Entry</Divider>
          <div 
            v-if = "!onPrint"
            class="col s12 animated slideInUp">
            <table class = "full-width coc-border-1 coc-border-border coc-light-background-bg">
              <tr class = "coc-border-bg coc-content-text">
                <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Entry Time</th>
                <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Departure Time</th>
              </tr>
              <tr
                class = "coc-primary-hover-bg coc-secondary-hover-text coc-smooth-bg-color full-width center coc-border-bottom-1 animated slideInLeft">
                <td> <span v-if = "!onPrint">{{ $moment(job.timein).fromNow() }}</span> ({{ $moment(job.timein).format('D/M/YYYY h:m A') }})</td>
                <td>
                  <span v-if = "!onPrint">{{ job.timeleave && job.status.toLowerCase() === 'finished' ? `${$moment(job.timeleave).fromNow()}` : '' }}</span>
                  {{ job.timeleave && job.status.toLowerCase() === 'finished' ? ` (${ $moment(job.timeleave).format('D/M/YYYY h:m A')})` : 'Not Finished Yet' }}
                </td>
              </tr>
            </table>
            <br>
          </div>
          <div 
            v-if = "onPrint"
            class="col s12 coc-margin-top-5px">
            <label class="coc-subcolor-text coc-text-small block">Timing</label>
            <div class="col s6">
              <div class = "coc-padding-y-3px coc-margin-y-0">Entry Time: <small>{{ $moment(job.timein).format('D/M/YYYY h:m A') }}</small></div>
            </div>
            <div class="col s6">
              <div class = "coc-padding-y-3px coc-margin-y-0">Departure Time: 
                <small>{{ job.timeleave && job.status.toLowerCase() === 'finished' ? $moment(job.timeleave).format('D/M/YYYY h:m A') : 'Not Finished Yet' }}</small>
              </div>
            </div>
          </div>
          <Divider 
            v-if = "!onPrint"
            orientation = "left">Operations / Parts</Divider>
          <div class="col s12">
            <div
              :class = "[
                { 'coc-margin-y-10px coc-padding-y-10px coc-background-bg coc-border-border coc-border-1 coc-standard-border-radius': !onPrint },
                { 'coc-margin-y-3px coc-padding-y-3px coc-padding-x-0': onPrint } ]"
              class="col s12">
              <p 
                :class = "{ hidden: onPrint }" 
                class = "coc-text-subtitle">Add a Part / Operation</p>
              <div
                :class ="{ hidden: onPrint }"
                class="row coc-house-keeper">
                <div 
                  v-if = "partAutocompleteResponse" 
                  class="col s4 animated fadeIn">
                  <label
                    :class = "[
                      { 'coc-success-text': partAutocompleteResponse.count > 0 },
                      { 'coc-error-text': partAutocompleteResponse.count <= 0 }
                    ]"
                    class="coc-text-sm coc-text-bold">Available In Stock: {{ partAutocompleteResponse.count }}</label>
                </div>
                <div 
                  v-if = "partAutocompleteResponse" 
                  class="col s4 animated fadeIn">
                  <label
                    :class = "[
                      { 'coc-success-text': partAutocompleteResponse.count - externalInput.count > 0 },
                      { 'coc-error-text': partAutocompleteResponse.count - externalInput.count <= 0 }
                    ]"
                    class="coc-text-sm coc-text-bold">Available After Export: {{ partAutocompleteResponse.count - externalInput.count }}</label>
                </div>
              </div>
              <div 
                :class = "{ hidden: onPrint }" 
                class="col l4 s12">
                <coc-input
                  ref = "partInput"
                  v-model = "externalInput.name"
                  :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { part: model.val, limit: 5 }})"
                  :autocomplete-map-response = "res => $_.uniqBy(res.stock, j => j.name).map(o => o.name)"
                  :scope = "['add-part']"
                  :rules = "{ HasValue: true }"
                  :hide-status = "onPrint"
                  placeholder = "Part / Operation"
                  icon = "ios-cog"
                  light-model
                  clearable
                  allow-autocomplete
                  @input = "handleAutocompleteSelectPart"
                  @coc-enter = "handleAutocompleteSelectPart"
                  @coc-select = "handleAutocompleteSelectPart" />
              </div>
              <div 
                :class = "{ hidden: onPrint }" 
                class="col l4 s12">
                <coc-input
                  v-model = "externalInput.price"
                  :scope = "['add-part']"
                  :rules = "{ HasValue: true, IsNumericString: true, NumberGreaterThan: -1 }"
                  :hide-status = "onPrint"
                  placeholder = "Price"
                  icon = "ios-pricetag"
                  light-model />
              </div>
              <div 
                :class = "{ hidden: onPrint }" 
                class="col l4 s12">
                <coc-input
                  v-model = "externalInput.fees"
                  :rules = "{ IsNumericString: true, NumberGreaterThan: -1 }"
                  :scope = "['add-part']"
                  :hide-status = "onPrint"
                  placeholder = "Fees"
                  icon = "ios-cash"
                  light-model />
              </div>
              <div 
                :class = "{ hidden: onPrint }" 
                class="col s12 coc-house-keeper">
                <div class = "col l4 s12">
                  <label class="coc-text-sm">Count</label><br>
                  <input-number
                    v-model = "externalInput.count" 
                    :min = "1"
                    placeholder = "Count"
                    class = "coc-full-width" />
                </div>
                <div class = "col l4 s12">
                  <br>
                  <i-switch v-model = "externalInput.makeMove" />
                  <label class="coc-text-sm">Export From Stock</label>
                </div>
                <div 
                  :class = "{ hidden: onPrint }" 
                  class="col l4 s12">
                  <coc-button
                    :scope = "['add-part']"
                    :class = "{ hidden: onPrint }"
                    type = "default"
                    placeholder = "Add"
                    icon = "ios-add-circle"
                    class = "right coc-padding-y-4px coc-margin-y-10px"
                    local
                    reset
                    @coc-validation-passed = "handleAddPart" />
                </div>
              </div>
              <div 
                v-if = "input.operations && input.operations.length" 
                class="col s12 animated slideInUp">
                <Divider 
                  v-if = "!onPrint" 
                  orientation = "left">Operations / Installed Parts</Divider>
                <label 
                  v-else 
                  class="coc-subcolor-text coc-text-small block">Operations / Installed Parts</label>
                <table class = "full-width coc-border-1 coc-border-border coc-light-background-bg">
                  <tr class = "coc-border-bg coc-content-text">
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Part</th>
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Price</th>
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Fees</th>
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Count</th>
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Total</th>
                    <th
                      v-if = "!onPrint"
                      :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Actions</th>
                  </tr>
                  <tr 
                    v-for = "(operation, o) in input.operations" 
                    :key = "o"
                    class = "coc-primary-hover-bg coc-secondary-hover-text coc-smooth-bg-color full-width center coc-border-bottom-1 animated slideInLeft">
                    <td>
                      <Button
                        v-if = "operation.makeMove"
                        :to = "{
                          path: '/stock',
                          query: {
                            part: operation.name
                          }
                        }"
                        type = "text">{{ operation.name }}</Button>
                      <span v-else> {{ operation.name }} </span>
                    </td>
                    <td> {{ operation.price | CocTrimExtraZeros }} LE</td>
                    <td> {{ operation.fees | CocTrimExtraZeros }} LE</td>
                    <td> {{ operation.count | CocTrimExtraZeros }}</td>
                    <td> {{ ((operation.price + operation.fees) * operation.count) | CocTrimExtraZeros }} LE</td>
                    <td v-if = "!onPrint">
                      <Button
                        icon = "ios-remove-circle-outline coc-error-text coc-text-lg"
                        class = "coc-margin-y-3px"
                        type = "text"
                        @click = "input.operations.splice( o, 1)"/>
                    </td>
                  </tr>
                </table>
                <table class = "full-width coc-border-1 coc-border-border coc-light-background-bg">
                  <tr class="center full-width coc-border-bg coc-content-text">
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Total Prices</th>
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Total Fees</th>
                    <th :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">Total</th>
                  </tr>
                  <tr class="center full-width">
                    <td :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">
                      {{ $_.sumBy(input.operations, operation => ((operation.price) * operation.count)) | CocTrimExtraZeros }} LE
                    </td>
                    <td :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]">
                      {{ $_.sumBy(input.operations, operation => ((operation.fees) * operation.count)) | CocTrimExtraZeros }} LE
                    </td>
                    <td
                      :class="[{'coc-padding-y-10px': !onPrint}, {'coc-padding-y-4px': onPrint}]"
                      class = "coc-text-bold">
                      {{
                        $_.sumBy(input.operations, operation => ((operation.price + operation.fees) * operation.count))
                      | CocTrimExtraZeros }} LE
                    </td>
                  </tr>
                </table>
              </div>
            </div>
          </div>
          <div class="col s12">
            <coc-button
              :scope = "['create-edit-job']"
              :request = "{ xdata: formatInput(), url: `/job/${job._id}`, method: 'put' }"
              :validation-tolerence-time = "1500"
              :class = "{ hidden: onPrint }"
              :size = "onPrint ? 'small' : 'large'"
              placeholder = "Save"
              icon = "ios-checkmark-circle"
              class = "right coc-margin-top-3px"
              @coc-submit-accepted = "handleResult"/>
          </div>
          <div 
            :class = "{ hidden: onPrint }" 
            class="col s12 coc-margin-top-10px coc-padding-x-0">
            <Button
              icon = "ios-trash"
              type = "text"
              class = "coc-error-text right coc-padding-bottom-0"
              @click = "deleteJob">Delete Job</Button>
          </div>
        </div>
      </component>
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
      partAutocompleteResponse: null,
      onPrint: false,
      fetchError: null,
      job: null,
      externalInput: {
        name: '',
        price: '',
        fees: '',
        count: 1,
        makeMove: false
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
    if (window) {
      const vm = this
      window.onfocus = () => {
        vm.onPrint = false
      }
    }
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
          this.job = {
            job_no: '1',
            reciptionist: 'Kerolos',
            car: {
              brand: 'Skoda',
              model: 'Octavia',
              release: '2010',
              chase: '123',
              kilometers: '73000'
            },
            client: {
              name: 'Amr',
              phone: '01011133122'
            },
            status: 'running',
            operations: []
          }
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
      if (this.job.status.toLowerCase() === 'finished') {
        this.$Message.destroy()
        this.$Notice.destroy()
        window.focus()
        setTimeout(() => {
          this.print()
        }, 1000)
      }
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
        ...operation,
        name: operation.name,
        price: parseFloat(operation.price, 10),
        fees: parseFloat(operation.fees, 10)
      }
    },
    handleAddPart() {
      this.input.operations.push(this.processExternalInput())
      this.$refs.partInput.focus()
      this.partAutocompleteResponse = null
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
    handleAutocompleteSelectPart() {
      setTimeout(() => {
        const ename = this.externalInput.name
        const response = this.$refs.partInput.autocompleteRetriever.response.stock.filter(
          c => c.name === ename
        )
        if (response.length) {
          this.partAutocompleteResponse = response[0]
          this.externalInput = { ...response[0], count: 1 }
          this.externalInput.countOnStock = response[0].count
          this.externalInput.makeMove = true
        } else {
          this.partAutocompleteResponse = null
          this.externalInput = {
            ...this.$_.pick(this.externalInput, ['name', 'price', 'fees']),
            makeMove: false
          }
        }
      }, 500)
    },
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
    },
    print() {
      if (this.onPrint) {
        this.onPrint = false
        return
      }
      this.onPrint = true
      setTimeout(() => {
        window.print()
      }, 500)
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
