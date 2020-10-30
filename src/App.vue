<template>
  <div id="app">
    <div class="sidenav">
      <a class="nav-link active" href="/">Anasayfa</a>
      <a v-for="(day, index) in getAllDays()" :key="index" class="nav-link active"
      @click.prevent="showSelectedDay(day)" href="#" >
        {{day}}
      </a>
    </div>
    <div v-if="showForm" class="container main">
      <h1 class="text-center">ZAYIFLATACAM OLUM SENİ</h1>
      <form @submit.prevent="addFood()">
        <div class="form-group">
          <label for="food">Ne yedin lan yine ⁉</label>
          <input v-model="food.name" type="text" class="form-control" id="food">
          <small id="foodJoke">Söyle söyle kızmıcam 😊😊</small>
        </div>
        <div class="form-group">
          <label for="calories">Kaç kaloriydi peki gülüm ❓❓</label>
          <input v-model="food.calories" type="text" class="form-control" id="calories">
        </div>
        <button type="submit" class="btn btn-primary">Ekle Bakuyim</button>
      </form>
    </div>
    <div class="container">
      <h1 v-if="!showForm" class="text-center">{{selectedDay}} TARİHİNDE NE YEDİM❓</h1>
      <ul class="list-group mt-3">
        <li v-for="(food, index) in getFoodsOfGivenDay()" :key="index" class="list-group-item">
          <button type="button" class="btn btn-danger btn-sm" @click="removeFood(index)">Sil
          </button>
          {{title(food.name)}} - {{food.calories}}
          </li>
      </ul>
      <h3 class="text-right mt-3">TOPLAM KALORİ: {{totalCalories}}</h3>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    days: {
    },
    food: {
      name: '',
      calories: '',
    },
    showForm: true,
    selectedDay: '',
    totalCalories: 0,
  }),
  mounted() {
    if (localStorage.days) {
      this.days = JSON.parse(localStorage.days);
    }
    const today = new Date().toLocaleDateString();
    if (!(today in this.days)) {
      this.days[today] = { foods: [], total: 0 };
    }
    this.selectedDay = today;
    this.totalCalories = this.days[this.selectedDay].total;
  },
  methods: {
    addFood() {
      const today = new Date().toLocaleDateString();
      this.days[today].foods.push(this.food);
      this.days[today].total += +this.food.calories;
      this.totalCalories = this.days[today].total;
      localStorage.days = JSON.stringify(this.days);
      this.food = {};
    },
    title(str) {
      return str.trim().split(' ').map((word) => word[0].toUpperCase() + word.substring(1).toLowerCase()).join(' ');
    },
    getFoodsOfGivenDay() {
      try {
        const dayToShow = this.selectedDay || new Date().toLocaleDateString();
        return this.days[dayToShow].foods;
      } catch {
        return [];
      }
    },
    getAllDays() {
      return Object.keys(this.days);
    },
    showSelectedDay(day) {
      this.showForm = false;
      this.selectedDay = day;
      this.totalCalories = this.days[this.selectedDay].total;
    },
    removeFood(index) {
      this.days[this.selectedDay].total -= +this.days[this.selectedDay].foods[index].calories;
      this.totalCalories = this.days[this.selectedDay].total;
      this.days[this.selectedDay].foods.splice(index, 1);
      localStorage.days = JSON.stringify(this.days);
    },
  },
};
</script>

<style >
.sidenav {
  height: 100%;
  width: 15%;
  position: fixed;
  z-index: 1;
  top: 0;
  left: 0;
  overflow-x: hidden;
  padding-top: 20px;
}
</style>
