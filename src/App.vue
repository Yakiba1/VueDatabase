<template>
  <div class="container">

    <AppAlert
        :alert="alert"
        @close="alert = null">
    </AppAlert>

    <form class="card" @submit.prevent="createPerson">
      <h2>Работа с базой данных</h2>

      <div class="form-control">
        <label for="name">Введите имя</label>
        <input type="text" id="name" v-model.trim="name">
      </div>

      <button class="btn primary" :disabled="name.length === 0">Создать</button>
    </form>

    <AppLoader v-if="loading"></AppLoader>

    <AppPeopleList
        v-else
        @remove="RemovePeople"
        @load="loadPeople"
        :people="people">
    </AppPeopleList>
  </div>
</template>

<script>
import AppPeopleList from "@/components/AppPeopleList.vue";
import axios from "axios";
import AppAlert from "@/components/AppAlert.vue";
import AppLoader from "@/components/AppLoader.vue";

export default {
  components: {AppLoader, AppAlert, AppPeopleList},
  data() {
    return {
      name:'',
      people: [],
      alert: null,
      loading: false,
    }
  },
  mounted() {
    this.loadPeople()
  },
  methods: {
    async createPerson() {
      const response = await fetch('https://vue-with-http-50965-default-rtdb.firebaseio.com/people.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json' // Явно указываем тип данных для отправки
        },
        body: JSON.stringify({ // В body те данные которые мы отправляем на сервер. JSON.stringify стерелизуем данные
          firstName: this.name
        })
      })
      const fireBaseData = await response.json()
      this.people.push({ //добавляем созданных людей в список при нажатии кнопки создать
        firstName: this.name,
        id: fireBaseData.name
      })
      this.name = ''
    },
    async loadPeople() {
      try {
        this.loading = true
        const {data} = await axios.get('https://vue-with-http-50965-default-rtdb.firebaseio.com/people.json')
        if (!data) {
          throw new Error('No person data')
        }
        setTimeout(() => {
          this.people = Object.keys(data).map(key => { // Вернет криптографические ключи. Map на каждой итерации получаем ключ где он будет равняться (oisagorjwerihw)
            return { // возвращает новый массив
              id: key,
              firstName: data[key].firstName, // или ...data[key] если много ключей
            }
          })
          this.loading = false
        },1500)
      }catch(e) {
        this.alert = {
          type: 'danger',
          title: 'Ошибка',
          text:e.message
        }
        this.loading = false
      }
    },
    async RemovePeople (id) {//Удоляем  по id при клике на конкретного человека из базы данных
      try {
        const name = this.people.find(person => person.id === id).firstName
        await axios.delete(`https://vue-with-http-50965-default-rtdb.firebaseio.com/people/${id}.json`)
        this.people = this.people.filter(person =>  person.id !== id) //Удоляем  по id при клике на конкретного человека из масива
        //this.people.filter(person =>  person.id !== id) меняем массив people с помощю метода filter (person =>  person.id !== id) где на каждой итерации
        // принимаем обект person person.id !== id и говорим что нам
        // нужно оставить в масиве только те элементы у готорых person.id !== id
        this.alert = {
          type: 'primary',
          title: 'Успешно!',
          text: `Потльзователь "${name}" был удален`
        }
      }catch (e) {

      }
    }
  }
}
</script>

<style>

</style>
