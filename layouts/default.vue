<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" app right/>
    <v-toolbar app>
      <v-toolbar-title>Welcome {{ user.name }} </v-toolbar-title>
      <v-spacer/>
      <v-toolbar-side-icon @click.stop="drawer = !drawer"/>
    </v-toolbar>
    <v-content>
      <nuxt/> 
    </v-content>
  </v-app>
</template>

<script>
export default {
  computed: {
    user() { // สร้าง user ขึ้นมา สามารถนำไปใส่หน้าอื่น ๆ ได้
      return this.$store.state.user
    },    
    online: {
      get() {
        return this.$store.state.online
      },
      set(value) {
        this.$store.commit('setOnline', value)
      },
    },
    drawer: {
      get() {
        return this.$store.state.drawer
      },
      set(value) {
        this.$store.commit('setDrawer', value)
      },
    },
  },

  async created() {
  //      this.$sture.commit('setUser', {}) // save user    
      let ok = await this.$store.dispatch('loadUser') //losd user
      if(!ok){
        return this.$router.replace('/') // replace ไปหน้า login
      }
  },// created

  mounted() {
    this.$store.commit('setOnline', window.navigator.onLine)
    window.addEventListener('offline', this.toggleNetworkStatus)
    window.addEventListener('online', this.toggleNetworkStatus)
  },

  beforeDestroyed() {
    window.removeEventListener('offline', this.toggleNetworkStatus)
    window.removeEventListener('online', this.toggleNetworkStatus)
  },

  methods: {
    toggleNetworkStatus({ type }) {
      this.online = type === 'online'
    },
  },
}
</script>
