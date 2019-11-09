<template>
  <master>
    <div>
      <div class="row">
        <span class = "coc-text-bold coc-text-md-2">
          Stock
          <span v-if = "stock.stock && stock.stock.length">({{ stock.stock.length }})</span>
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
                <Divider 
                  class = "coc-margin-y-0" 
                  orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text">Item Data</span> </Divider>
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Part Name</p>
                  <coc-input
                    v-model = "input.part"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { part: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stock.filter(s => !!s.name), j => j.name).map(j => j.name)"
                    icon = "ios-search"
                    placeholder = "Search by Part Name.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
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
                        value = "instock"
                        label = "In Stock"/>
                      <Option
                        value = "outofstock"
                        label = "Out Of Stock"/>
                    </template>
                  </coc-select>
                </div>
                <div class="col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Entery Time</p>
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
                  <p class = "coc-content-text coc-text-normal-2">Price Range</p>
                  <Slider 
                    v-model="input.price"
                    :step = "config.price.step"
                    :max = "config.price.max" 
                    range 
                    show-stops/>
                </div>
                <div class = "col s12 l6">
                  <div class="right">
                    <i-switch 
                      v-model = "input.external"
                      :disabled = "input.ignore_external" 
                      light-model
                      class = "right coc-margin-top-10px"/>   
                    <span class="coc-padding-x-5px right">
                      <span>External Resources</span><br>
                      <small class="right">(salaries, taxs, etc..)</small>
                    </span>
                  </div>
                  <div class="coc-padding-x-20px">
                    <i-switch 
                      v-model = "input.ignore_external" 
                      light-model
                      class = "right coc-margin-top-10px"/>   
                    <span class="coc-padding-x-5px right">
                      <span>Select All</span><br>
                      <small class="right">(select all externals and not externals)</small>
                    </span>
                  </div>
                </div>
              </div>
              <Divider 
                class = "coc-margin-y-0" 
                orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text">Vendor Data</span> </Divider>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Vendor Phone</p>
                  <coc-input
                    v-model = "input.phone"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { phone: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stock.filter(s => !!s.vendor), j => j.vendor.phone).map(j => j.vendor.phone)"
                    icon = "ios-search"
                    placeholder = "Search by Phone.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Vendor Name</p>
                  <coc-input
                    v-model = "input.name"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/stock', params: { name: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stock.filter(s => !!s.vendor), j => j.vendor.name).map(j => j.vendor.name)"
                    icon = "ios-search"
                    placeholder = "Search by vendor name.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    light-model />
                </div>
              </div>
              <Divider 
                class = "coc-margin-y-0" 
                orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text">Car Compatibility</span> </Divider>
              <div class="row coc-house-keeper">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Car Brand</p>
                  <coc-select
                    v-model = "input.brand"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { brandac: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stocks, j => j.car.brand).map(j => j.car.brand)"
                    icon = "ios-search"
                    placeholder = "Search by Car Brand.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    filterable
                    multiple
                    light-model />
                </div>
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Car Model</p>
                  <coc-select
                    v-model = "input.model"
                    :autocomplete-remote = "model => ({ method: 'get', url: '/job', params: { modelac: model.val, limit: 5 } } )"
                    :autocomplete-map-response = "(res) => $_.uniqBy(res.stocks, j => j.car.model).map(j => j.car.model)"
                    icon = "ios-search"
                    placeholder = "Search by car model.."
                    class = "coc-house-keeper"
                    allow-autocomplete
                    clearable
                    filterable
                    multiple
                    light-model />
                </div>
                <div class="col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Realse Year</p>
                  <coc-select
                    v-model = "input.release"
                    :rules = "{ HasValue: true }"
                    :data = "generateYears()"
                    placeholder = "Release Year"
                    icon = "ios-calendar"
                    size = "large"
                    filterable
                    clearable
                    multiple
                    light-model />
                </div>
              </div>
              <Divider 
                class = "coc-margin-y-0" 
                orientation = "left"><span class = "coc-text-bold coc-info-shade-4-text">Sorting</span> </Divider>
              <div class="row">
                <div class = "col s12 l6">
                  <p class = "coc-content-text coc-text-normal-2">Sort By</p>
                  <coc-select
                    v-model = "input.sort"
                    placeholder = "Sort By.."
                    light-model>
                    <template slot-scope = "{options}">
                      <Option
                        value = "created_at"
                        label = "Date"/>
                      <Option
                        value = "price"
                        label = "Price"/>
                      <Option
                        value = "status"
                        label = "Status"/>
                    </template>
                  </coc-select>
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
                  <span class="coc-text-lg">Filter</span>
                </Button>
              </div>
            </div>
          </div>
        </div>
        <Divider 
          class = "coc-margin-y-0" 
          orientation = "right">Search Settings</Divider>
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
      <table
        v-if = "isMounted"
        class = "coc-full-width">
        <tr>
          <th 
            v-for = "(col, c) in columns" 
            :key = "c"
            class = "coc-primary-bg coc-padding-y-10px coc-background-text">
            {{ col.title }}
          </th>
        </tr>
        <tr 
          v-for = "(stock, s) in stock.stock" 
          :key = "s"
          class = "coc-border-border center coc-info-hover-tint-8-bg">
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">{{ stock.name }}</td>
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">{{ stock.category }}</td>
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">{{ stock.price }}</td>
          <td class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px">{{ stock.count }}</td>
          <td 
            class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px" 
            style=" width: 300px;">
            <coc-collapse
              v-if = "stock.car_compatibility && stock.car_compatibility.length"
              :title = "`(${stock.car_compatibility.length}) car/s are compatible`"
              :animations = "{'enter':'animated fadeIn','leave':'animated slideOutUp hidden'}"
              icon = "tcsc-motor-1-icon coc-text-lg-1"
              toggler-class = "row house-keeper pointer transparent coc-border-0"
              regular-class = "coc-text-body coc-primary-text"
              active-class = "coc-text-md-1 coc-primary-text">
              <CellGroup slot = "content">
                <Cell 
                  v-for = "(car, c) in stock.car_compatibility" 
                  :key = "c" 
                  :label="car.model" 
                  :extra = "car.release"
                  class = "animated slideInLeft">
                  <div class = "row coc-house-keeper">
                    <coc-avatar
                      :source = "`/snaps/brands/png/${car.brand.split(' ').join('-').toLowerCase()}.png`"
                      scale = "25px"
                      class = "col coc-padding-x-2px"/>
                    <span class="col coc-margin-x-0px">
                      {{ car.brand | CocCapitalizeName }}
                    </span>
                  </div>
                </Cell>
              </CellGroup>
            </coc-collapse>
            <i v-else><small>NONE</small></i>
          </td>
          <td
            class="coc-border-bottom-1 coc-border-0 coc-border-border coc-padding-y-10px"
            style = "text-align: left !important">
            <p
              v-if = "stock.vendor"
              class = "coc-padding-left-5px">
              <Icon type = "ios-person" /> {{ stock.vendor.name }} <br>
              <a :href="`tel:${stock.vendor.phone}`"><Icon type = "ios-call" /> {{ stock.vendor.phone }}</a> <br>
              <Icon type = "ios-navigate" /> {{ stock.vendor.address }} <br>
            </p>
            <i v-else><small>NONE</small></i>
          </td>
        </tr>
        <tr v-if = "stock.stock && stock.stock.length">
          <td />
          <td />
          <td class="center coc-text-bold">Total {{ $_.sumBy(stock.stock, o => o.price) }} LE</td>
          <td />
        </tr>
      </table>
    </div>
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
  </master>
</template>

<script>
import AddStock from '~/components/stock/add.vue'
import Master from '~/components/common/master'
export default {
  name: 'Index',
  components: {
    AddStock,
    Master
  },
  data() {
    return {
      isMounted: false,
      isLoading: true,
      stock: { stock: [] },
      columns: [
        {
          title: 'Part',
          key: 'name'
        },
        {
          title: 'Category',
          key: 'category'
        },
        {
          title: 'Price',
          key: 'price'
        },
        {
          title: 'Count',
          key: 'count'
        },
        {
          title: 'Car Compiatablity',
          key: 'car_compatibility'
        },
        {
          title: 'Vendor',
          key: 'vendor'
        }
      ],
      config: {
        drawer: false,
        price: {
          step: 100,
          max: 7000
        }
      },
      pagination: null,
      formattedQuery: {},
      input: {
        ignore_external: true,
        status: null,
        date: null,
        price: [0, 6900],
        phone: '',
        products: [],
        sort: 'created_at',
        desc: 'yes',
        page: 0,
        limit: 10
      }
    }
  },
  computed: {
    user() {
      return this.$store.state.core.auth
    }
  },
  mounted() {
    this.formatQuery(this.$route.query)
    setTimeout(() => {
      this.isMounted = true
    }, 1000)
  },
  methods: {
    askForLogin() {
      this.$root.$emit('askForLogin')
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
    getStock() {
      this.$router.push({
        path: '/stock',
        query: this.encodedQuery()
      })
      this.$axios({
        method: 'get',
        url: '/stock',
        params: { ...this.formattedQuery }
      }).then(res => {
        this.stock = res.data
        this.config.drawer = false
        this.isLoading = false
        this.pagination = this.$_.omit(res.data, ['stocks'])
        this.$router.push({
          path: '/stock',
          query: this.encodedQuery()
        })
      })
    },
    decodedQuery(query) {
      const final = this.$_.cloneDeep(query)
      if (final.external !== undefined) {
        final.external = final.external === 'yes'
      }
      if (final.ignore_external !== undefined) {
        final.ignore_external = final.ignore_external === 'yes'
      }
      if (final.page !== undefined) {
        final.page = parseInt(final.page, 10) - 1
      }
      // if (final.date) {
      //   final.date = final.date.split(',')
      // }
      if (final.brand) {
        final.brand = final.brand.split(',')
      }
      if (final.model) {
        final.model = final.model.split(',')
      }
      if (final.release) {
        final.release = final.release.split(',')
      }
      if (final.price) {
        final.price = final.price.split(',')
      }
      return final
    },
    encodedQuery(input = this.input) {
      const final = this.$_.cloneDeep(input)
      if (final.external !== undefined) {
        final.external = final.external ? 'yes' : 'no'
      }
      if (final.ignore_external !== undefined) {
        final.ignore_external = final.ignore_external ? 'yes' : 'no'
      }
      if (final.page > 0) {
        final.page = final.page - 1
      }
      // if (final.date) {
      //   final.date = final.date.join(',')
      // }
      if (final.brand) {
        final.brand = final.brand.join(',')
      }
      if (final.model) {
        final.model = final.model.join(',')
      }
      if (final.release) {
        final.release = final.release.join(',')
      }
      // if (final.price) {
      //   final.price = final.price.join(',')
      // }
      return final
    },
    formatQuery(input = this.input) {
      this.formattedQuery = this.encodedQuery(input)
      this.getStock()
    }
  }
}
</script>

<style lang="css" scoped>
</style>
