<template>
  <div class="container">
    <form class="card" @submit.prevent="createPerson">
      <h2>Работа с базой данных</h2>

      <div class="form-control">
        <label for="name">Введите имя</label>
        <input type="text" id="name" v-model.trim="name">
      </div>

      <button class="btn primary" :disabled="name.length === 0">Создать</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name:''
    }
  },
  methods: {
    createPerson() {}

  }
}
</script>

<style>

</style>
