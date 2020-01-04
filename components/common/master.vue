<template>
  <div class="layout">
    <drawer
      v-model = "addStockModal"
      width = "90%"
      footer-hide>
      <add-stock
        v-bind = "stockData"
        class = "coc-margin-top-25px"
        @success = "handleAddSuccess" />
    </drawer>
    <Modal
      v-model = "authModal"
      footer-hide>
      <div class = "coc-margin-top-30px coc-margin-bottom-0">
        <h3 class="text-title coc-primary-text center">
          {{ authMode === 'login' ? 'Login' : 'Register' }}
        </h3>
        <transition
          enter-active-class = "animated zoomIn"
          leave-active-class = "animated zoomOut">
          <login
            v-if = "authMode === 'login'"
            @success = "authModal = false"/>
        </transition>
        <transition
          enter-active-class = "animated zoomIn"
          leave-active-class = "animated zoomOut">
          <register
            v-if = "authMode === 'register'"
            class = "coc-margin-bottom-20px"
            @success = "authModal = false"/>
        </transition>
        <div class="row">
          <a
            class = "coc-padding-left-0 no-outline right"
            @click = "authMode = authMode === 'login' ? 'register' : 'login'">
            {{ authMode === 'login' ? 'Create an account' : 'Already have an account? Login' }}
          </a>
        </div>
      </div>
    </Modal>
    <Layout>
      <Layout>
        <Sider 
          ref="side1" 
          v-model="isCollapsed"
          :collapsed-width="78" 
          :class = "[{hidden: onPrint}]"
          class = "coc-secondary-bg coc-border-0 coc-border-right-1 coc-border-border"
          hide-trigger 
          collapsible>
          <Menu 
            :class="menuitemClasses"
            active-name="jobs" 
            theme="light" 
            width="auto" 
            @on-select = "handleSidebarSelect">
            <menu-item name="jobs">
              <i class="tcsc-transportation-icon" />
              <span>Running</span>
            </menu-item>
            <menu-item name="stock">
              <i class="tcsc-inventory-1-icon" />
              <span>Add Stock</span>
            </menu-item>
            <menu-item name="moves">
              <i class="tcsc-stock-icon" />
              <span>Moves</span>
            </menu-item>
          </Menu>
          <div 
            v-if = "!isCollapsed && sidebarActive === 'jobs' && user" 
            class = "row coc-margin-top-30px">
            <Card 
              :padding="0" 
              title="Running Jobs" 
              icon="ios-options" 
              shadow 
              style="width: 100%;">
              <div class="row coc-divider-bg coc-margin-y-0 coc-padding-y-3px">
                <div class="col s12 coc-padding-x-5px">
                  <Input
                    v-model = "input.job.search"
                    placeholder = "Search.."
                    icon = "ios-search"
                    class = "coc-divider-bg" ></Input> <!-- eslint-disable-line -->  
                </div>
              </div>
              <CellGroup
                v-coc-loading = "loaders.jobs"
                v-if = "jobs.length"
                class = "side-jobs">
                <Cell 
                  v-for = "(job, j) in jobs" 
                  :key = "j" 
                  :label="job.car.model" 
                  :to = "$route.path == `/jobs/${job.job_no}` ? null : `/jobs/${job.job_no}`"
                  :selected = "$route.path == `/jobs/${job.job_no}`"
                  :extra = "$route.path == `/jobs/${job.job_no}` ? null : `#${job.job_no}`"
                  class = "animated slideInLeft">
                  <div class = "row coc-house-keeper">
                    <coc-avatar
                      :source = "`/snaps/brands/png/${job.car.brand.split(' ').join('-').toLowerCase()}.png`"
                      scale = "25px"
                      class = "col coc-padding-x-2px"/>
                    <span class="col coc-margin-x-0px">
                      {{ job.car.brand | CocCapitalizeName }}
                    </span>
                  </div>
                </Cell>
              </CellGroup>
              <CellGroup
                v-coc-loading = "loaders.jobs"
                v-if = "!jobs.length"
                class = "side-jobs">
                <Cell title = "No Matching Jobs Found"/>
              </CellGroup>
            </Card>
          </div>
        </Sider>
        <Layout>
          <Header :class = "[{hidden: onPrint}]">
            <Menu 
              mode="horizontal" 
              theme="dark" 
              active-name="1">
              <div class="layout-logo">
                <div class="row">
                  <Icon 
                    :class="rotateIcon" 
                    class = "coc-secondary-text col" 
                    type="ios-menu" 
                    size="33" 
                    @click.native="collapsedSider"/>
                  <nuxt-link to = "/">
                    <coc-avatar
                      v-coc-mouse-over="'pulse'"
                      v-coc-mouse-leave="'jello'"
                      :source="$coc.App.logo.invert"
                      scale = "40px"
                      class="logo col coc-padding-0 animated"/>
                  </nuxt-link>
                </div>
              </div>
              <div class="layout-nav">
                <menu-item name="1">
                  <nuxt-link
                    to = "/jobs"
                    class = "coc-secondary-text">
                    <Icon type="ios-build" />
                    Jobs
                  </nuxt-link>
                </menu-item>
                <menu-item name="2">
                  <nuxt-link
                    to = "/stock"
                    class = "coc-secondary-text">
                    <Icon type="ios-archive" />
                    Stock
                  </nuxt-link>
                </menu-item>
                <menu-item name="3">
                  <nuxt-link
                    to = "/analytics"
                    class = "coc-secondary-text">
                    <Icon type="ios-analytics" />
                    Analytics
                  </nuxt-link>
                </menu-item>
                <menu-item name="4">
                  <span 
                    v-if = "!user" 
                    class = " coc-text-normal-1 ivu-icon ivu-icon-ios-log-in hide-on-med-and-up" 
                    @click = "activateAuthModal" />
                  <span 
                    v-if = "!user" 
                    class = " coc-text-normal-1 hide-on-med-and-down" 
                    @click = "activateAuthModal"> Login </span>
                  <Dropdown v-else>
                    <Button
                      type="text" 
                      size = "large"
                      class="client-navbar-action coc-primary-hover-tint-9-bg coc-padding-x-10px">
                      <span class="coc-primary-text coc-text-normal-1"/>
                      <Avatar 
                        :icon="!user.avatar ? 'ios-person' : null"
                        :src="avatar" 
                        size="large"
                        class = "coc-primary-tint-9-bg coc-primary-tint-7-text coc-margin-y-0" />
                    </Button>
                    <DropdownMenu slot="list">
                      <DropdownItem style = "min-width: 150px">
                        <nuxt-link
                          class = "coc-primary-text coc-primary-hover-shade-4-text coc-text-normal-1 text coc-text-hover-normal-2 coc-smooth-font-size center full-width block"
                          to = "/profile">Profile</nuxt-link>
                      </DropdownItem>
                      <DropdownItem style = "min-width: 150px">
                        <a
                          class = "coc-primary-text coc-primary-hover-shade-4-text coc-text-normal-1 text coc-text-hover-normal-2 coc-smooth-font-size center full-width block"
                          @click = "logout">Logout</a>
                      </DropdownItem>
                    </DropdownMenu>
                  </Dropdown>
                </menu-item>
              </div>
            </Menu>
          </Header>
          <Content :style="{padding: '0 50px'}">
            <Breadcrumb
              v-if = "!onPrint"
              :style="{margin: '20px 0'}">
              <BreadcrumbItem 
                v-for = "(crumb, c) in analysisBreadcrump()" 
                :key = "c"
                :to = "`/${ analysisBreadcrump(false).splice( 0, c ).join('/') }`">
                {{ crumb | CocCapitalizeName }}
              </BreadcrumbItem>
            </Breadcrumb>
            <Card 
              v-else 
              class = "row coc-margin-top-5px animated fadeIn">
              <p class="coc-text-lg-1 coc-text-bold coc-margin-y-0 coc-padding-y-0">
                <coc-avatar
                  :source = "$coc.App.logo.primary"
                  scale = "50px"
                  class = "col coc-margin-right-10px coc-margin-y-0 coc-padding-y-0"/>
                Techno-Service
                <small class="right coc-text-body"> {{ $moment().format('D/M/YYYY h:m A') }} </small>
              </p>
              <p class="left">
                <span class="coc-text-md-2"><Icon type= "ios-call"/> 01008645544</span><br>
                <span class="coc-text-md-1"><Icon type= "ios-navigate"/> 9 Mohamed Refaat, El nozha el gededa</span>
              </p>
            </Card>
            <Card>
              <div style="min-height: 70vh;">
                <slot />
              </div>
            </Card>
          </Content>
          <Footer class="layout-footer-center">
            <span class = "">{{ $coc.App.brandName }} by {{ $coc.App.author }} {{ $moment().format('YYYY') }} &copy; </span>
            <span v-if = "onPrint">
              <br>
              <icon 
                type = "logo-twitter" 
                class = "blue-text" /> @mamr_moussa
            </span>
          </Footer>
        </Layout>
      </Layout>
    </Layout>
  </div>
</template>
<script>
import config from '~/config'
import Login from '../auth/Login.vue'
import Register from '../auth/Register.vue'
import AddStock from '~/components/stock/add.vue'
export default {
  name: 'Master',
  components: {
    Login,
    Register,
    AddStock
  },
  props: {
    crumbs: {
      type: Array,
      default: null
    },
    onPrint: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      stockData: {
        mode: 'post',
        init: null
      },
      loaders: { jobs: false },
      events: new this.$coc.Event({ api: this.$root }),
      authMode: 'login',
      authModal: false,
      addStockModal: false,
      isCollapsed: false,
      sidebarActive: 'jobs',
      input: {
        job: {
          search: ''
        }
      }
    }
  },
  computed: {
    jobs() {
      return this.$store.state.jobs.running.filter(
        j =>
          j.job_no.toString().includes(this.input.job.search) ||
          j.car.brand
            .toLowerCase()
            .includes(this.input.job.search.toLowerCase()) ||
          j.car.model
            .toLowerCase()
            .includes(this.input.job.search.toLowerCase()) ||
          j.client.name
            .toLowerCase()
            .includes(this.input.job.search.toLowerCase()) ||
          j.client.phone
            .toLowerCase()
            .includes(this.input.job.search.toLowerCase())
      )
    },
    user() {
      return this.$store.state.core.auth
    },
    avatar() {
      if (this.user && this.user.avatar && this.user.auth_type !== 'local') {
        return this.user.avatar
      }
      return this.user && this.user.avatar
        ? `${config.baseURL}/users/${this.user._id}/avatar`
        : null
    },
    rotateIcon() {
      return ['menu-icon', this.isCollapsed ? 'rotate-icon' : '']
    },
    menuitemClasses() {
      return ['menu-item', this.isCollapsed ? 'collapsed-menu' : '']
    }
  },
  watch: {
    isCollapsed(e) {
      this.$emit('collapse', e)
      this.$emit(e ? 'sider-on' : 'sider-off')
    }
  },
  mounted() {
    const vm = this
    this.getJobs()
    this.events.OnMap({
      askForLogin(e) {
        if (e && e.mode && e.mode === 'register') {
          vm.authMode = 'register'
        } else {
          vm.authMode = 'login'
        }
        vm.authModal = true
      },
      updateRunningJobs() {
        vm.getJobs()
      },
      LoggedIn() {
        setTimeout(() => {
          vm.getJobs()
        }, 3000)
      },
      addStock(e) {
        if (e) {
          vm.stockData = e
        } else stockData = null
        vm.addStockModal = true
      }
    })
  },
  methods: {
    getJobs() {
      this.loaders.jobs = true
      this.$axios({
        method: 'get',
        url: '/job',
        params: { status: 'running', stats: 'yes' }
      })
        .then(({ data: { jobs } }) => {
          this.loaders.jobs = false
          this.$store.dispatch('setRunningJobs', jobs)
        })
        .catch(() => {
          this.loaders.jobs = false
        })
    },
    handleSidebarSelect(e) {
      this.sidebarActive = e
      this.isCollapsed = false
      if (e === 'jobs') {
        this.getJobs()
      } else if (e === 'stock') {
        this.stockData = {
          mode: 'post',
          init: null
        }
        this.addStockModal = true
      } else if (e === 'moves') {
        this.$router.push('/moves')
      }
    },
    collapsedSider() {
      this.$refs.side1.toggleCollapse()
    },
    activateAuthModal() {
      this.authModal = true
    },
    handleName(name) {
      if (!name) return ''
      return name.length > 11 ? name.split(' ')[0] : name
    },
    logout() {
      this.$cookies.set('auth', null, {
        path: '/',
        maxAge: 60 * 60 * 24 * 7
      })
      this.$axios.defaults.headers.common['Authorization'] = null
      this.$store.dispatch('setAuth', null)
    },
    handleAddSuccess(e) {
      this.stockData = {
        mode: 'post',
        init: null
      }
      this.addStockModal = false
      this.$root.$emit('addStockSuccess', e)
    },
    analysisBreadcrump(appendDashboard = true) {
      const psudoAppend = []
      if (appendDashboard) psudoAppend.push('Dashboard')
      {
        const parts =
          this.crumbs ||
          this.$route.path.split('/').filter(c => c !== '/' && c.length)
        return [...psudoAppend, ...parts]
      }
    }
  }
}
</script>
<style scoped>
.layout {
  position: relative;
  border-radius: 4px;
  overflow: hidden;
}
.layout-header-bar {
  background: #fff;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
}
.layout-logo-left {
  width: 90%;
  height: 30px;
  border-radius: 3px;
  margin: 15px auto;
}
.menu-icon {
  transition: all 0.3s;
}
.rotate-icon {
  transform: rotate(-90deg);
}
.menu-item span {
  display: inline-block;
  overflow: hidden;
  width: 69px;
  text-overflow: ellipsis;
  white-space: nowrap;
  vertical-align: bottom;
  transition: width 0.2s ease 0.2s;
}
.menu-item i {
  transform: translateX(0px);
  transition: font-size 0.2s ease, transform 0.2s ease;
  vertical-align: middle;
  font-size: 23px;
}
.collapsed-menu span {
  width: 0px;
  transition: width 0.2s ease;
}
.collapsed-menu i {
  transform: translateX(5px);
  transition: font-size 0.2s ease 0.2s, transform 0.2s ease 0.2s;
  vertical-align: middle;
  font-size: 22px;
}
.layout-logo {
  width: 100px;
  height: 30px;
  background: transparent;
  border-radius: 3px;
  float: left;
  position: relative;
  top: 15px;
  left: 20px;
}
.layout-nav {
  width: 420px;
  margin: 0 auto;
  margin-right: 20px;
}
.layout-footer-center {
  text-align: center;
}
.side-jobs {
  min-height: 50vh;
  max-height: 70vh;
  overflow-y: auto;
}
</style>
