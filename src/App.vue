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

    <AppPeopleList
        @load="loadPeople"
        :people="people">
    </AppPeopleList>
  </div>
</template>

<script>
import AppPeopleList from "@/components/AppPeopleList.vue";
import axios from "axios";

export default {
  components: {AppPeopleList},
  data() {
    return {
      name:'',
      people: []
    }
  },
  methods: {
    async createPerson() {
      const response = await fetch('https://vue-with-http-50965-default-rtdb.firebaseio.com/people.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          firstName: this.name
        })
      })
      const fireBaseData = await response.json()

      console.log(fireBaseData)
      this.name = ''
    },
    async loadPeople() {
      const {data} = await axios.get('https://vue-with-http-50965-default-rtdb.firebaseio.com/people.json')
      this.people = Object.keys(data).map(key => {
        return {
          id: key,
          firstName: data[key].firstName, // или ...data[key]
        }
      }) // Вернет криптографические ключи. Map на каждой итерации получаем ключ где он будет равняться (oisagorjwerihw)
    }
  }
}
</script>

<style>

</style>
