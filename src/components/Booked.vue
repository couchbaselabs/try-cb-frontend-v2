<template>
  <div class="container">
    <div class="row mt-3">
      <div class="col-lg">
        <!-- Nothing booked message -->
        <div class="alert alert-primary" v-if="booked.length == 0">
          <p><strong>You haven't purchased any flights yet!</strong></p>
          <p class="mb-0">Try purchasing some from your cart</p>
        </div>

        <!-- Results table, generated from :items data -->
        <b-table class="mt-3" :items="booked" :fields="fields" @update="update">
          <!-- Composite field to show flightpath as one item -->
          <template v-slot:cell(flightpath)="data">
              {{data.item.sourceairport}} -> {{data.item.destinationairport}}
          </template>
        </b-table>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: "booked",
  inject: ['API'],
  data() {
    return {
      // The fields and data for the Booked flights table
      fields: ["name", "flight", "date", "flightpath"],
      booked: []
    }
  },
  methods:{
    // Updates the booked flights table from the database
    // Triggered on reopening the Booked tab
    update: async function(){
      let vm = this
      console.log("updating...")
      this.API.callTenanted('GET', `user/${ this.$attrs.user.username }/flights`,{
        // JWT token supplied to authorize user
        headers: {'Authorization': "Bearer " + this.$attrs.user.token}
      }).then(response => {
        vm.booked.length = 0
        if(response.data){
          vm.booked.push(...response.data.data)
        } else {
          // TODO: Display ... something?
        }
        // Emit an event to log the request's context to the context component
        vm.$emit('logCtx',["Retrieved booked flights lists from server", response.data.context])
      })
    }
  }
}
</script>
