<template>
  <master v-coc-loading = "isLoading">
    <div v-if = "user && user.roles.indexOf('admin') !== -1">
      <div class="row">
        <p class="coc-text-bold coc-text-md-2 coc-padding-y-10px">Select Time Range</p>
        <div 
          class="col" 
          style="width: calc( 100% - 100px )">
          <DatePicker 
            v-model = "input.date"
            :start-date="$moment().toDate()" 
            size = "large"
            type="daterange" 
            placement="bottom-end" 
            placeholder="Select date" 
            style="width: 100%"/>
        </div>
        <div 
          class="col coc-padding-x-0" 
          style="width: 100px">
          <i-button
            :loading = "isLoading"
            size = "large"
            type = "primary"
            icon = " knocks-merge2"
            class = "coc-text-bold"
            long
            @click = "getData">Pull</i-button>
        </div>
      </div>
      <div class="row">
        <div class="col l3 s12 coc-error-text center coc-text-lg-1 coc-text-bold">
          <div class="col s12 coc-border-1 coc-standard-border-radius coc-padding-y-10px">
            <icon type = "md-arrow-round-up" />
            Outgoing<br>
            {{ stockImports }} {{ $store.state.core.app.currency }}
          </div>
        </div>
        <div class="col l3 s12 coc-success-shade-3-text center coc-text-lg-1 coc-text-bold">
          <div class="col s12 coc-border-1 coc-standard-border-radius coc-padding-y-10px">
            <icon type = "md-arrow-round-down" />
            Incoming<br>
            {{ stockExports + labors }} {{ $store.state.core.app.currency }}
          </div>
        </div>
        <div
          :class = "[
            {'coc-success-text': profitPercentage > 30},
            {'coc-error-text': profitPercentage <= 30}
          ]"
          class="col l3 s12 center coc-text-lg-1 coc-text-bold">
          <div class="col s12 coc-border-1 coc-standard-border-radius coc-padding-y-10px">
            <icon type = " knocks-moneybag" />
            Profit<br>
            {{ totalProfit }} {{ $store.state.core.app.currency }}
          </div>
        </div>
        <div
          :class = "[
            {'coc-success-text': profitPercentage > 30},
            {'coc-error-text': profitPercentage <= 30}
          ]"
          class="col l3 s12 center coc-text-lg-1 coc-text-bold">
          <div class="col s12 coc-border-1 coc-standard-border-radius coc-padding-10px">
            <icon type = " knocks-percent" />
            Percentage<br>
            {{ profitPercentage | CocToFixedTwo }}%
          </div>
        </div>
      </div>
      <div class="row">
        <div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-car-5-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Spare Parts Overhead
                <br><small>Total cost you spent for resailable spare parts.</small>
              </div>
              <div class = "right col"> {{ stockImports - externals }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-car-7-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Utilities & External Overhead
                <br><small>Promotions, Taxes, salaries and other operational costs.</small>
              </div>
              <div class = "right col"> {{ externals }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg coc-error-shade-2-text">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-car-check-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Total Overhead
                <br>
                <small>
                  All the spent money during this period <small style="font-weight: 200 !important">(Spare Parts, Taxes, Salaries, ...etc)</small>
                </small>
              </div>
              <div class = "right col"> {{ stockImports }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-car-sales-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Stock Sales
                <br>
                <small>Total revenue for stock.</small>
              </div>
              <div class = "right col"> {{ stockExports }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-fix-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Labors
                <br><small>All the labors for each operation.</small>
              </div>
              <div class = "right col"> {{ labors }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-truck-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Out Sourced Parts
                <br><small>Spare Parts that were brought from outside your inventory.</small>
              </div>
              <div class = "right col"> {{ outSourcedParts }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-investing-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Jobs Revenue
                <br><small>Total customers payments for jobs.</small>
              </div>
              <div class = "right col"> {{ jobsWithOutSourcedParts }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-business-and-finance-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Locale Jobs Profits
                <br><small>Total customer payments for your locale parts and labors.</small>
              </div>
              <div class = "right col"> {{ jobsWithOutSourcedParts - outSourcedParts }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
          <div v-coc-mouse-over = "'coc-text-md-2 coc-smooth-font-size'">
            <div class = "row coc-text-bold coc-text-lg coc-success-text">
              <div class="col coc-margin-top-15px">
                <span class = "tcsc-cost-icon coc-text-lg-1" />
              </div>
              <div class="col">
                Total Profits
                <br><small>Stock revenue compined with labors.</small>
              </div>
              <div class = "right col"> {{ totalProfit }} {{ $store.state.core.app.currency }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <card v-else>
      <p class="center coc-text-super coc-error-text coc-text-bold"><icon type = "ios-alert-outline" /><br>UNAUTHORIZED</p>
    </card>
  </master>
</template>

<script>
import Master from '~/components/common/master.vue'
export default {
  name: 'AnalyticsIndex',
  components: {
    Master
  },
  head() {
    return {
      title: this.$store.state.core.app
        ? `${this.$store.state.core.app.title} | Financial Analytics`
        : 'Financial Analytics'
    }
  },
  data() {
    return {
      isLoading: false,
      hasError: false,
      stockImports: 0,
      stockExports: 0,
      externals: 0,
      labors: 0,
      totalProfit: 0,
      profitPercentage: 0,
      outSourcedParts: 0,
      jobsWithOutSourcedParts: 0,
      stock: [],
      jobs: [],
      input: {
        ignore_external: 'yes',
        category: [],
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
    this.getData()
  },
  methods: {
    getData() {
      this.isLoading = true
      this.isLoading = false
      this.$axios({
        method: 'get',
        url: '/job',
        params: { ...this.encodedQuery() }
      })
        .then(jobsRes => {
          this.jobs = jobsRes.data.jobs
          this.$axios({
            method: 'get',
            url: '/move',
            params: { ...this.encodedQuery() }
          })
            .then(stockRes => {
              this.stock = stockRes.data.move
              this.isLoading = false
              this.hasError = false
              this.handleGetDataSuccess()
            })
            .catch(() => {
              this.isLoading = false
              this.hasError = true
            })
        })
        .catch(() => {
          this.isLoading = false
          this.hasError = true
        })
    },
    encodedQuery(dateLabel = 'created_at', input = this.input) {
      const final = this.$_.cloneDeep(input)
      // if (final.external !== undefined) {
      //   final.external = final.external ? 'yes' : 'no'
      // }
      // if (final.ignore_external !== undefined) {
      //   final.ignore_external = final.ignore_external ? 'yes' : 'no'
      // }
      if (final.page > 0) {
        final.page = final.page - 1
      }
      if (final.date && final.date.join) {
        final.date = final.date.join(',')
      } else {
        final.date = null
      }
      if (final.category && final.category.join) {
        final.category = final.category.join(',')
      } else {
        final.category = null
      }
      if (final.brand && final.brand.join) {
        final.brand = final.brand.join(',')
      } else {
        final.brand = null
      }
      if (final.model && final.model.join) {
        final.model = final.model.join(',')
      } else {
        final.model = null
      }
      if (final.release && final.release.join) {
        final.release = final.release.join(',')
      } else {
        final.release = null
      }
      if (final.price && final.price.join) {
        final.price = final.price.join(',')
      } else {
        final.price = null
      }
      final.stats = 'yes'
      return final
    },
    handleGetDataSuccess() {
      console.log('seeeya')
      this.stockImports = this.$_.sumBy(this.stock, s => s.item.import_price)
      this.stockExports = this.$_.sumBy(this.stock, s => s.item.price)
      this.externals = this.$_.sumBy(
        this.stock.filter(s => s.item.external),
        s => s.price
      )
      this.labors = this.$_.sumBy(
        this.$coc.CielChilds(this.jobs, j => j.operations),
        o => o.fees
      )
      this.outSourcedParts = this.$_.sumBy(
        this.$coc
          .CielChilds(this.jobs, j => j.operations)
          .filter(o => !o.makeMove),
        o => o.price
      )
      this.jobsWithOutSourcedParts = this.$_.sumBy(
        this.$coc.CielChilds(this.jobs, j => j.operations),
        o => o.price + o.fees
      )
      this.totalProfit = this.stockExports - this.stockImports + this.labors
      this.profitPercentage = (this.totalProfit / this.stockImports) * 100
    }
  }
}
</script>

<style lang="css" scoped>
.anltc-card {
	height: 200px;
}
.card-div{
  max-height: 130px;
  overflow-y: auto;
  border-top: 2px solid #fff;
}
</style>
