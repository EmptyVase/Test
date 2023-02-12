<!-- eslint-disable vue/require-v-for-key -->
<!-- eslint-disable vue/no-deprecated-filter -->
<template>
  <div>
    <h1>Vue <b>Table test</b> 📄</h1>
    <hr>

    <label>Users per page</label>
    <select v-model="pageSizeModel">
      <option value="3">3</option>
      <option value="5">5</option>
      <option value="10">10</option>
      <option value="25">25</option>
    </select>

    <table>
      <thead>
        <tr>
          <th :class="{ sorted: sort === 'id' }" @click="sortBy('id')">
            Место
          </th>
          <th :class="{ sorted: sort === 'login' }" @click="sortBy('login')">
            Логин</th>
          <th :class="{ sorted: sort === 'order' }" @click="sortBy('order')" >
            Подтверждённые заказы
          </th>
          <th :class="{ sorted: sort === 'status' }" @click="sortBy('status')" >
            статус
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in  sortedUsers" :key="user">
          <td>{{ user.id }}</td>
          <td>{{ user.login  }}</td>
          <td>{{ user.order  }}</td>
          <td>{{ user.status}}</td>
        </tr>
      </tbody>
    </table>

    <div class="controls">
      <button class="btn btn-sm" :disabled="!hasPage(-1)" @click="prevPage">←</button> 
      <button class="btn btn-sm" :disabled="!hasPage(1)"  @click="nextPage">→</button>
    </div>

    <hr>
    <h2>Новый пользователь </h2>
    <form v-on:submit.prevent="addUser">
      <input type="text" v-model="newUser.login" placeholder="Логин" required>
      <input type="int" v-model="newUser.order" placeholder="Подтверждённые заказы" required>
      <input type="text" v-model="newUser.status" placeholder="Статус" required>
      <button type="submit" class="btn">Add user</button>
    </form>
  </div>
</template>

<script>
export default {
data: () => ({
    users: [
			{ id: 0,  login: 'smith@gmail.com',     order: 312    ,  status: 'Ценитель красоты'},
      { id: 1,  login: 'lenin@gmail.com',     order: 120  ,  status: 'Поставщик аксессуаров'},
      { id: 2,  login: 'mask@gmail.com',     order: 98    , status: 'Конкурент миндздрава'},
      { id: 3,  login: 'dog@mail.ru',       order: 64 ,  status: 'рыбак'},
      { id: 4,  login: 'nightmare@mail.ru',    order: 34 , status: 'охотник'},
      { id: 5,  login: 'cat@mail.ru',   order: 1  ,  status: 'Ценитель красоты'},
    ],
    newUser: { login: '', order: '', status: '' },
    sort: 'id',
    sortDir:'asc',
    page: 0,
    pageSize: 25
  }),
  methods: {
    addUser: function() {
      let login    = this.newUser.login.trim().toLowerCase();
      let order = this.newUser.order.trim().toLowerCase();
      let status = this.newUser.status.trim().toLowerCase();
      if (login && order && status) {  
        this.users.push({
          id:      this.users.length,
          login:    login,
          order: order,
          status: status
        });
        this.sortDir = 'desc'; this.sortBy('id'); // Default sorting
      }
      this.newUser.login = this.newUser.order = this.newUser.status = '';
    },
    sortBy: function(s) {
      if (s === this.sort) {
        this.sortDir = (this.sortDir === 'asc') ? 'desc' : 'asc';
      } else {
        this.sortDir = 'asc';
      }
      this.sort = s;
    },
    isActiveSort: function(s) { 
      return this.sort === s; 
    },
    hasPage: function(dir) {
      if (dir === -1 && (this.page > 0)) return true;
      if (dir ===  1 && (((this.page+1)*this.pageSize) < this.users.length)) return true;
      return false;
    },
    prevPage: function() {
      if (this.hasPage(-1)) this.page--;
    },
    nextPage: function() {
      if (this.hasPage(1)) this.page++;
    }
  },
  computed: {
    sortedUsers: function() {
      // eslint-disable-next-line vue/no-side-effects-in-computed-properties
      return this.users.sort((a, b) => {
        let dir = (this.sortDir === 'asc') ? 1 : -1;
        if (a[this.sort] < b[this.sort]) {
          return -1 * dir;
        } else if (a[this.sort] > b[this.sort]) {
          return  1 * dir;
        } else {
          return 0;
        }
      }).filter((row, idx) => {
        let s = this.page*this.pageSize;
        let e = (this.page+1)*this.pageSize;
        return (idx >= s && idx < e);
    });
    },
    pageSizeModel: {
      get() {
        return this.pageSize;
      },
      set(v) {
        this.pageSize = v;
        this.page = 0;
      }
    }
  },

}


</script>

<style>
  @import "@/css/style.styl"
</style>
