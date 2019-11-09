<template>
  <master>
    <div class="row">
      <span class = "coc-text-bold coc-text-md-2">
        Jobs
        <span v-if = "jobs && jobs.length">({{ jobs.length }})</span>
      </span>
      <Button
        icon = "ios-funnel-outline"
        class = "right"
        @click = "config.drawer = true"/>
      <Button
        icon = "ios-refresh"
        class = "right coc-margin-x-5px"
        @click = "formatQuery"/>
    </div>
    <Drawer 
      v-model="config.drawer" 
      title="Filters" 
      width = "80%"
      closable>
      <div 
        v-if = "config.drawer" 
        class="row coc-house-keeper">
        <div class="row coc-section">
          <div class = "col s12  m12 coc-margin-0 coc-padding-x-5px">
            <div class="row coc-house-keeper">
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Search By Number</p>
                <coc-input
                  slot = "title"
                  v-model = "input.job_no"
                  icon = "ios-grid-outline"
                  placeholder = "Search By Job Number.."
                  light-model/>
              </div>
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Status</p>
                <coc-select
                  v-model = "input.status"
                  placeholder = "Status.."
                  light-model
                  clearable>
                  <template slot-scope = "{options}">
                    <Option
                      value = "running"
                      label = "Running"/>
                    <Option
                      value = "finished"
                      label = "Finished"/>
                    <Option
                      value = "postponed"
                      label = "Postponed"/>
                  </template>
                </coc-select>
              </div>
            </div>
            <div class="row coc-house-keeper">
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Sort</p>
                <coc-select
                  v-model = "input.sort"
                  placeholder = "Sort By.."
                  light-model>
                  <template slot-scope = "{options}">
                    <Option
                      value = "job_no"
                      label = "Job Number"/>
                    <Option
                      value = "created_at"
                      label = "Date"/>
                    <Option
                      value = "status"
                      label = "Status"/>
                  </template>
                </coc-select>
              </div>
              <div class="col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Time Range</p>
                <DatePicker 
                  v-model = "input.date"
                  :start-date="$moment().toDate()" 
                  type="daterange" 
                  placement="bottom-end" 
                  placeholder="Select date" 
                  style="width: 100%"/>
              </div>
            </div>
            <div class="row coc-house-keeper">
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Phone</p>
                <coc-input
                  v-model = "input.phone"
                  :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { phone: model.val, limit: 5 } } )"
                  :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.client.phone).map(j => j.client.phone)"
                  icon = "ios-search"
                  placeholder = "Search by Phone.."
                  class = "coc-house-keeper"
                  allow-autocomplete
                  clearable
                  light-model />
              </div>
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Client Name</p>
                <coc-input
                  v-model = "input.name"
                  :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { name: model.val, limit: 5 } } )"
                  :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.client.name).map(j => j.client.name)"
                  icon = "ios-search"
                  placeholder = "Search by client name.."
                  class = "coc-house-keeper"
                  allow-autocomplete
                  clearable
                  light-model />
              </div>
            </div>
            <div class="row coc-house-keeper">
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Car Brand</p>
                <coc-input
                  v-model = "input.brand"
                  :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { brand: model.val, limit: 5 } } )"
                  :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.car.brand).map(j => j.car.brand)"
                  icon = "ios-search"
                  placeholder = "Search by Phone.."
                  class = "coc-house-keeper"
                  allow-autocomplete
                  clearable
                  light-model />
              </div>
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Car Model</p>
                <coc-input
                  v-model = "input.model"
                  :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { model: model.val, limit: 5 } } )"
                  :autocomplete-map-response = "(res) => $_.uniqBy(res.jobs, j => j.car.model).map(j => j.car.model)"
                  icon = "ios-search"
                  placeholder = "Search by car model.."
                  class = "coc-house-keeper"
                  allow-autocomplete
                  clearable
                  light-model />
              </div>
            </div>
            <div class="row">
              <div class = "col s12 l6">
                <p class = "coc-content-text coc-text-normal-2">Price Range</p>
                <Slider 
                  v-model="input.price"
                  :step = "config.price.step"
                  :max = "config.price.max" 
                  range 
                  show-stops/>
              </div>
              <div class = "col s12 l6 right">
                <p class = "coc-content-text coc-text-normal-2">Arrangement</p>
                <button-group
                  shape = "circle"
                  class = "coc-full-width"
                  long>
                  <Button
                    :type = "input.desc === 'no' ? 'info' : 'default'"
                    icon = "md-arrow-round-up"
                    style = "width:50%"
                    @click = "input.desc = 'no'"/>
                  <Button
                    :type = "input.desc === 'yes' ? 'info' : 'default'"
                    icon = "md-arrow-round-down"
                    style = "width:50%"
                    @click = "input.desc = 'yes'"/>
                </button-group>
              </div>
            </div>
            <div class = "col s12">
              <Button 
                type = "primary" 
                icon = "ios-funnel-outline coc-text-lg"
                long 
                @click = "formatQuery()">
                <span class="coc-text-lg">Filter Jobs</span>
              </Button>
            </div>
          </div>
        </div>
      </div>
      <Divider orientation = "right">Search Settings</Divider>
      <p class = "coc-text-md-2">Price Range Maximum</p>
      <input-number 
        v-model = "config.price.max" 
        placeholder = "Price Range Maximum" 
        size = "large"
        style = "width: 100%" />
      <p class = "coc-text-md-2">Price Range Step</p>
      <input-number 
        v-model = "config.price.step" 
        placeholder = "Price Range Step" 
        size = "large"
        style = "width: 100%" />
    </Drawer>
    <div 
      v-coc-loading = "isLoading" 
      class="row">
      <div class="col s12 l4">
        <h3 class = "coc-info-text">Running</h3>
        <div class = "div col s6 coc-info-border coc-border-3" />
        <CellGroup class = "coc-background-bg job-status-section coc-full-width">
          <Cell 
            v-for = "(job, j) in jobs.filter(j => j.status === 'running')" 
            :key = "j" 
            :to = "`/jobs/${job.job_no}`"
            class = "animated slideInUp" >
            <div class = "row coc-house-keeper">
              <coc-avatar
                :source = "`/snaps/brands/png/${job.car.brand.split(' ').join('-').toLowerCase()}.png`"
                scale = "40px"
                class = "col"/>
              <span class="col coc-margin-top-5px">#{{ job.job_no }} {{ job.car.brand | CocCapitalizeName }}</span>
            </div>
            <div slot = "label">
              <br>
              <span>{{ job.car.model }}</span><br>
              <small>{{ job.client.name }} , Phone: {{ job.client.phone }} </small>
            </div>
            <div slot = "extra">
              <small class="right">{{ $moment(job.timein).fromNow() }}</small>
            </div>
          </Cell>
        </CellGroup>
        <div 
          v-if = "!jobs || !jobs.filter(j => j.status === 'running').length" 
          class = "coc-background-bg coc-content-text coc-padding-y-10px">
          <p class="center">
            <br>
            <Icon type = "ios-alert-outline" /><br>
            <span>No Jobs To Show</span>
            <br>
          </p>
        </div>
      </div>
      <div class="col s12 l4 ">
        <h3 class = "coc-success-text">Finished</h3>
        <div class = "div col s6 coc-success-border coc-border-3 " />
        <CellGroup class = "coc-background-bg job-status-section coc-full-width">
          <Cell 
            v-for = "(job, j) in jobs.filter(j => j.status === 'finished')" 
            :key = "j" 
            :to = "`/jobs/${job.job_no}`"
            class = "animated slideInUp" >
            <div class = "row coc-house-keeper">
              <coc-avatar
                :source = "`/snaps/brands/png/${job.car.brand.split(' ').join('-').toLowerCase()}.png`"
                scale = "40px"
                class = "col"/>
              <span class="col coc-margin-top-5px">#{{ job.job_no }} {{ job.car.brand | CocCapitalizeName }}</span>
            </div>
            <div slot = "label">
              <br>
              <span>{{ job.car.model }}</span><br>
              <small>{{ job.client.name }} , Phone: {{ job.client.phone }} </small>
            </div>
            <div slot = "extra">
              <small class="right">{{ $moment(job.timein).fromNow() }}</small>
            </div>
          </Cell>
        </CellGroup>
        <div 
          v-if = "!jobs || !jobs.filter( j => j.status === 'finished').length" 
          class = "coc-background-bg coc-content-text coc-padding-y-10px">
          <p class="center">
            <br>
            <Icon type = "ios-alert-outline" /><br>
            <span>No Jobs To Show</span>
            <br>
          </p>
        </div>
      </div>
      <div class="col s12 l4">
        <h3 class = "coc-warning-text">Postponed</h3>
        <div class = "div col s6 coc-warning-text coc-border-3" />
        <CellGroup class = "coc-background-bg job-status-section coc-full-width">
          <Cell 
            v-for = "(job, j) in jobs.filter(j => j.status === 'postponed')" 
            :key = "j" 
            :to = "`/jobs/${job.job_no}`"
            class = "animated slideInUp" >
            <div class = "row coc-house-keeper">
              <coc-avatar
                :source = "`/snaps/brands/png/${job.car.brand.split(' ').join('-').toLowerCase()}.png`"
                scale = "40px"
                class = "col"/>
              <span class="col coc-margin-top-5px">#{{ job.job_no }} {{ job.car.brand | CocCapitalizeName }}</span>
            </div>
            <div slot = "label">
              <br>
              <span>{{ job.car.model }}</span><br>
              <small>{{ job.client.name }} , Phone: {{ job.client.phone }} </small>
            </div>
            <div slot = "extra">
              <small class="right">{{ $moment(job.timein).fromNow() }}</small>
            </div>
          </Cell>
        </CellGroup>
        <div 
          v-if = "!jobs || !jobs.filter(j => j.status === 'postponed').length" 
          class = "coc-background-bg coc-content-text coc-padding-y-10px">
          <p class="center">
            <br>
            <Icon type = "ios-alert-outline" /><br>
            <span>No Jobs To Show</span>
            <br>
          </p>
        </div>
      </div>
      <div 
        style = " padding-top: 10px !important; margin-top: 5px; margin-bottom: 10px;" 
        class="col s12 ">
        <Page
          v-if = "pagination && jobs && jobs.length && input.page >= 0"
          :total="pagination.pages"
          :page-size = "1"
          :current="pagination.page + 1"
          :styles = "{margin: 'auto', display: 'block', width: 'fit-content'}"
          @on-change = "changePage"/>
      </div>
    </div>
  </master>
</template>

<script>
import Master from '~/components/common/master'
export default {
  name: 'Index',
  components: {
    Master
  },
  data() {
    return {
      isLoading: false,
      formattedQuery: {},
      pagination: null,
      jobs: [],
      config: {
        drawer: false,
        price: {
          step: 100,
          max: 7000
        }
      },
      input: {
        job_no: '',
        status: null,
        date: null,
        price: [0, 6900],
        phone: '',
        products: [],
        sort: 'job_no',
        desc: 'yes',
        page: 0,
        limit: 10
      }
    }
  },
  mounted() {
    this.formatQuery()
  },
  methods: {
    getJobs() {
      this.isLoading = true
      this.$axios({
        method: 'get',
        url: '/job',
        params: { ...this.formattedQuery }
      })
        .then(({ data }) => {
          this.jobs = data.jobs
          this.config.drawer = false
          this.isLoading = false
          this.pagination = this.$_.omit(data, ['jobs'])
        })
        .catch(res => {
          this.isLoading = false
          this.jobs = [
            {
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
            },
            {
              job_no: '2',
              reciptionist: 'Kerolos',
              car: {
                brand: 'Toyota',
                model: 'Corola',
                release: '2014',
                chase: '123',
                kilometers: '73000'
              },
              client: {
                name: 'Shenoda',
                phone: '01011133121'
              },
              status: 'finished',
              operations: []
            }
          ]
        })
    },
    encodedQuery(input = this.input) {
      const final = this.$_.cloneDeep(input)
      if (final.page > 0) {
        final.page = final.page - 1
      }
      if (final.date) {
        final.date = final.date.join(',')
      }
      if (final.price) {
        final.price = final.price.join(',')
      }
      return final
    },
    formatQuery() {
      this.formattedQuery = this.encodedQuery()
      this.getJobs()
      this.$router.push('/jobs', { query: this.formattedQuery })
    },
    changePage(e) {
      this.input.page = e
      this.formatQuery()
    }
  }
}
</script>

<style lang="css" scoped>
.job-status-section {
  max-height: 70vh;
  overflow-y: scroll
}
</style>
