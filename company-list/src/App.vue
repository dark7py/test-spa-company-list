<script>
import CompaniesTable from '@/components/CompaniesTable.vue';
export default {
  name: "App",
  components: {
    CompaniesTable
  }
}
</script>

<template>
  <div id="app">
    <header>
      <h1 class="header">Тестовое задание</h1>
    </header>

    <main>
      <CompaniesTable/>
    </main>
  </div>
</template>

<style>
.header {
  font-size: 50px;
}
</style>
