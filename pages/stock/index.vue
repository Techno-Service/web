<template>
  <master>
    <card title = "Add To Stock">
      <divider orientation = "left">Item</divider>
      <div class="row">
        <div class="col s12 l4 m6">
          <coc-input
            v-model = "input.name"
            :scope = "['add-stock']" 
            :rules = "{ HasValue: true }"
            placeholder = "Item Name.." />        	
        </div>
        <div class="col s12 l4 m6">
          <coc-input
            v-model = "input.category"
            :scope = "['add-stock']"
            :rules = "{ HasValue: true }"
            placeholder = "Item Category.." />        	
        </div>
        <div class="col s12 l4 m6">
          <coc-input
            :scope = "['add-stock']"  
            :rules = "{
              HasValue: true,
              IsNumericString: true
            }"
            placeholder = "Item Price.." 
            filters = "ToInt" 
            @filter = "input.price = $event"/>        	
        </div>
        <div class="col s12 l4 m6 coc-margin-top-10px right">
          <i-switch 
            v-model = "input.external" 
            class = "right coc-margin-top-10px"/>   
          <span class="coc-padding-x-5px right">
            <span>External Resources</span><br>
            <small class="right">(salaries, taxs, etc..)</small>
          </span>
        </div>
      </div>
      <divider orientation = "left">Provider</divider>
      <div
        v-coc-loading.ivu-icon.ivu-icon-ios-checkmark-circle.coc-success-text.text-super = "input.external"
        coc-loader-text = "No Provider"
        coc-loader-classes = "coc-success-text coc-text-lg-1 block"
        class="row">
        <div class="col s12 l4 m6">
          <coc-input
            v-model = "input.vendor.name"
            :scope = "['add-stock']" 
            :rules = "{ HasValue: true }"
            placeholder = "Provider Name.." />        	
        </div>
        <div class="col s12 l4 m6">
          <coc-input
            v-model = "input.vendor.phone"
            :scope = "['add-stock']" 
            :rules = "{ HasValue: true }"
            placeholder = "Provider Phone.." />        	
        </div>
        <div class="col s12 l4 m6">
          <coc-input
            v-model = "input.vendor.address"
            :scope = "['add-stock']"  
            :rules = "{ HasValue: true }"
            placeholder = "Provider Address.." />        	
        </div>
      </div>
      <divider orientation = "left">Extra Info</divider>
      <div class="row">
        <div class="col s12 l4 m6">
          <coc-input
            :scope = "['add-stock']"  
            :rules = "{
              HasValue: true,
              IsNumericString: true
            }"
            placeholder = "Points.." 
            @filter = "input.points = $event"/>         
        </div>
      </div>
      <div class="row">
        <coc-button
          :scope = "['add-stock']"
          placeholder = "Submit"
          class = "right"
          local />
      </div>
    </card>
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
      brands,
      input: {
        name: '',
        category: '',
        points: 0,
        external: false,
        price: 0,
        vendor: {
          name: '',
          phone: '',
          address: ''
        }
      }
    }
  },
  methods: {
    handleFilter(e) {
      console.log(typeof e)
    }
  }
}
</script>

<style lang="css" scoped>
</style>
