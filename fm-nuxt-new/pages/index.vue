<template>
  <div class="mt-16">
    <div v-if="!userHasTeams">
      <home-choice-team />
    </div>
    <div v-else-if="userHasTeams && nextMatch !== '' && nextMatch.first_team !== null">
      <div class="d-flex justify-center mb-5">
        Prochain Match
      </div>
      <div>
        <soccer-game :next-match="nextMatch" />
      </div>
    </div>
    <div v-else>
      <h1 class="d-flex justify-center mb-5 light-green">
        Bientôt des nouveaux match
      </h1>
    </div>
  </div>
</template>

<script>
import HomeChoiceTeam from '@/pages/home-choice-team'
import SoccerGame from '@/components/SoccerGame/SoccerGame'
import auth from '@/mixins.js/auth'
import user from '@/mixins.js/user'
import Cookies from 'js-cookie'

export default {
  name: 'Index',
  components: { SoccerGame, HomeChoiceTeam },
  mixins: [auth, user],
  data () {
    return {
      userHasTeams: null,
      user: null,
      nextMatch: { first_team: null, second_team: null, fm_date: null }
    }
  },
  async created () {
    await this.getUser()
    this.userHasTeams = this.user[0].team && this.user[0].team.tu_taken === 1 &&
       this.user[0].team.tu_year === 2021 &&
    this.user[0].team.tu_active === 1
      ? 1
      : 0
    Cookies.set('userInfo', JSON.stringify(this.user[0]), { expires: 365 })
    if (this.userHasTeams) {
      await this.getNextMatch()
    }
  },
  methods: {
    async getUser () {
      await this.$axios.get('http://localhost/api/users/' + this.cookiesUser.id).then((res) => {
        this.user = res.data
      })
    },
    async getNextMatch () {
      await this.$axios.get('http://localhost/api/fm/' + this.cookiesUser.id + '/' + this.getUserInfo.team.team.team_division + '/2021/next-match').then((res) => {
        this.nextMatch = res.data
      })
    }
  }
}
</script>
