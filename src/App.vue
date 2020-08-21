<template>
  <div id="app">
    <div>
      Language : <b v-if="!$apollo.queries.language.loading">{{ language }}</b>
      <span v-else>Loading...</span><br />
    </div>
    Champions : <b v-if="!$apollo.queries.getChampions.loading"> {{ JSON.stringify(getChampions) }}</b>
    <span v-else>Loading...</span><br />
    Champion : <b v-if="!$apollo.queries.getChampionByName.loading"> {{ JSON.stringify(getChampionByName) }}</b>
    <span v-else>Loading...</span><br />
    <div>
      <h3>Example Mutation</h3>
      Name: <input v-model="name">
      Attack Damage: <input v-model.number="attack">
      <div>
        Data:
        {{ updatedChampion }}
      </div>
      <button @click="updateAttackDamage">Update Champion</button>
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import gql from 'graphql-tag'

export default {
  name: 'app',
  data() {
    return {
      name: '',
      attack: null,
      updatedChampion: null
    };
  },
  apollo: {
    // Simple query that will update the 'hello' vue property
    language: gql`{
      language
    }`,
    getChampions: gql`{
      getChampions { name attackDamage }
    }`,
    getChampionByName: {
      query: gql`query GetChampionByName($championName: String!) {
        getChampionByName(name: $championName) { name attackDamage }
      }`,
      variables: {
        championName: 'Ashe'
      },
      // Additional options here
      fetchPolicy: 'cache-and-network',
    }
  },
  methods: {
    updateAttackDamage () {
      const res = this.$apollo.mutate({
        mutation: gql`
        mutation UpdateAttackDamage($championName: String!, $attackDamage: Float) {
          updateAttackDamage(name: $championName, attackDamage: $attackDamage) {
            name
            attackDamage
          }
        }`,
        variables: {
          championName: this.name,
          attackDamage: this.attack
        }
      })
      res.then((data) => {
        const newUpdate = data.data .updateAttackDamage
        this.updatedChampion = newUpdate
        this.getChampionByName = newUpdate
      })
      
    },
  },
  // components: {
  //   HelloWorld
  // }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
