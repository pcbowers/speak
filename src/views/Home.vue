<template>
  <div class="home">
    <Speak />
  </div>
</template>

<script>
// @ is an alias to /src
import Speak from '@/components/Speak'

export default {
  name: 'Home',
  components: {
    Speak
  }
}
</script>
