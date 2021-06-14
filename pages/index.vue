<template>
  <div class="container">
    <div>{{ currentUser.name }} {{ currentUser.surname }}</div>
    <h1>Бросить вызов</h1>
    <label>
      <select>
        <option v-for="user in users">{{ user.name }} {{ user.surname }}</option>
      </select>
    </label>
    <button @click="makeCall">Бросить вызов!</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      callsNodeId: 'c573c1dd-1555-4079-8eb3-25d0ffaf8d8d'
    }
  },
  async asyncData({$axios, route}) {
    const users = await $axios.$get('http://localhost:8000/api/get-users', {
      params: {
        token: route.query.token,
        mapId: route.query.mapid
      }
    });
    const currentUser = await $axios.$get('http://localhost:8000/api/get-current-user', {
      params: {
        token: route.query.token
      }
    });

    const usersRemapped = users.data.filter((user) => {
      return user.user_id !== currentUser.data.user_id
    })
    return {
      users: usersRemapped,
      currentUser: currentUser.data
    }
  },
  async mounted() {
    const data = await this.$axios.$get('http://localhost:8000/api/get-maps', {
      params: {
        token: this.$route.query.token,
        mapId: this.$route.query.mapid
      }
    });

    console.log(data);
  },
  methods: {
    async makeCall() {
      await this.$axios.$post('http://localhost:8000/api/new-node', {
        properties: {
          global: {
            title: "Еще заголовок"
          }
        },
        parent: this.callsNodeId,
        mapId: this.$route.query.mapid
      }, {
        params: {
          token: this.$route.query.token,
          mapId: this.$route.query.mapid
        }
      })
    }
  }
}
</script>

<style>
.container {

}

.title {
  font-family: 'Quicksand',
  'Source Sans Pro',
  -apple-system,
  BlinkMacSystemFont,
  'Segoe UI',
  Roboto,
  'Helvetica Neue',
  Arial,
  sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
