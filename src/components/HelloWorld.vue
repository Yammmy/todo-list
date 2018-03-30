<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <hr>
    <div>
      <ul>
        <li v-for="user in users">
          {{user.firstname}} {{user.lastname}}
        </li>
      </ul>
    </div>
    <hr>
    <div>
      <input type="text" v-model="input_val">
    </div>
    <div>
      <span> {{input_val}} </span>
    </div>
    <hr>
    <div>
      <button class="btn btn-primary" v-on:click="counter++">
        You've clicked the button {{counter}} times
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      msg: 'Welcome to Your Vue.js App',
      users: [
        {firstname: 'Bob', lastname: "Dylen"},
        {firstname: 'Kidney', lastname: "Sourl"},
        {firstname: 'Runby', lastname: "Fuel"}
      ],
      input_val: '',
      counter: 0
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: inside;
  /* padding: 0; */
}
/* li {
  display: inline-block;
  margin: 0 10px;
} */
a {
  color: #42b983;
}
</style>
