<template>
  <div>
    <div class="d-flex justify-content-between align-items-end">
      <h1>
        <span class="d-inline d-md-none">Report</span>
        <span class="d-none d-md-inline">Site Report</span>
      </h1>
    </div>
    <Website
      :key="website.id"
      :website="website"
      class="mt-3"
    />
  </div>
</template>

<script>
import Website from '../components/website'

export default {
  name: 'ReportsShow',
  components: {
    Website
  },
  computed: {
    website () {
      return this.$store.getters.websites[0]
    }
  }
}
</script>
