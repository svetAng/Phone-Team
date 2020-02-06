<template>
  <f7-page>
    <f7-navbar title="Days Open" back-link="Back" sliding></f7-navbar>
    <f7-block inner>
      <p>Please select days</p>

      <f7-list form class="theme-red">
        <f7-list-item checkbox name="my-checkbox" v-model="monday" title="Monday" checked></f7-list-item>
        <f7-list-item checkbox name="my-checkbox" v-model="tuesday" title="Tuesday" checked></f7-list-item>
        <f7-list-item checkbox name="my-checkbox" v-model="wednesday" title="Wednesday" checked></f7-list-item>
        <f7-list-item checkbox name="my-checkbox" v-model="thursday" title="Thursday" checked></f7-list-item>
        <f7-list-item checkbox name="my-checkbox" v-model="friday" title="Friday" checked></f7-list-item>
        <f7-list-item checkbox name="my-checkbox" v-model="saturday" title="Saturday"></f7-list-item>
        <f7-list-item checkbox name="my-checkbox" v-model="sunday" title="Sunday"></f7-list-item>
      </f7-list>
    </f7-block>
  </f7-page>
</template>

<script>
export default {
  data() {
    return {
      monday: true,
      tuesday: true,
      wednesday: true,
      thursday: true,
      friday: true,
      saturday: false,
      sunday: false,
    }
  },
  beforeDestroy() {
    this.$emit('test');
  }
}
</script>
