<template>
  <div class="vld-parent">
    <loading
      :active="isLoading"
      :can-cancel="false"
      :color="viewmodel.color"
      :loader="viewmodel.loader"
      :width="viewmodel.width"
    ></loading>
  </div>
</template>

<script>
// Import component
import { ref, watch } from 'vue'
import Loading from 'vue3-loading-overlay/dist/index'
import 'vue3-loading-overlay/dist/vue3-loading-overlay.css'

export default {
  components: { Loading },
  props: {
    loadingNow: Boolean
  },
  setup(props) {
    const viewmodel = ref({
      loader: 'dots',
      color: '#3fb883',
      bgColor: '#f2edd2',
      width: 80
    })

    const isLoading = ref(props.loadingNow)
    const fullPage = ref(true)
    // const onCancel = () => {
    //   console.log('User cancelled the loader.')
    //   //because the props is single flow direction, you need to set isLoading status normally.
    //   isLoading.value = false
    // }
    watch(
      () => props.loadingNow,
      (newValue) => {
        isLoading.value = newValue
      }
    )
    return {
      isLoading,
      fullPage,
      viewmodel
    }
  }
}
</script>
