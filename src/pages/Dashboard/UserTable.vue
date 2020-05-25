<template>
  <base-table :data="data" :columns="columns" thead-classes="text-primary"></base-table>
</template>
<script>
import { BaseTable } from "@/components";
export default {
  components: {
    BaseTable
  },
  props: {
    columns: Array,
    data: Array
  },
  computed: {
    table() {
      return this.$t("dashboard.usersTable");
    }
  }
};
</script>
<style>
</style>
