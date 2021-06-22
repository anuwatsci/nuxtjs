<template>
  <div class="container">
    <div>  
    <Logo />  
      <h4 class="title">
        casino-test
      </h4>
      <div class="link">
        <b-overlay :show="show" rounded="sm">
          <label>Balance {{user.balance}} THB.</label>
          <b-form inline>
            <label>จำนวนเงิน:</label>
            <b-form-input type="number" v-model="user.deposit_amount"></b-form-input>
            <b-button type="button" variant="primary" @click="deposit">ฝาก</b-button>
          </b-form>
          <b-form inline style="margin-top:10px;">
            <label>จำนวนเงิน:</label>
            <b-form-input type="number" v-model="user.withdraw_amount"></b-form-input>
            <b-button type="button" variant="primary" @click="withdraw">ถอน</b-button>
          </b-form>
          <b-form inline style="margin-top:10px;">
            <a :href="this.linkgame" v-if="this.show"><img src="~/assets/img/sa-gaming.png" width="100" alt=""></a>
          </b-form>
          <b-form inline style="margin-top:10px;">
            <b-button type="button" variant="info" @click="logout">ออกจากระบบ</b-button>
          </b-form>
        </b-overlay>
      </div>
    </div>
  </div>
</template>

<script>
export default { 
  created() {
    const auth = String(this.$cookies.get('userState'))
      if(auth == 'undefined' || auth==''){
          this.$router.push('/') 
      }
  },data: () => {
    return {
      user: {
        id: '',
        balance: 0,
        deposit_amount: 0,
        withdraw_amount: 0,
        gameid: 0
      },
      games: [{}],
      linkgame: '',
      show:false
    }
  },
  mounted () {
    this.user.id = this.$cookies.get('userState')
    if (this.user.id !== null) {
      this.checkbalace()
    }
  },
  methods: {
    async deposit () {
      this.show = false
      await this.$axios.post('/users/doposit', this.user).then(function (res) {
        // console.log(res)
      })
      this.checkbalace() 
    },
    async withdraw () {
      this.show = false
      await this.$axios.post('/users/withdraw', this.user).then(function (res) {
        // console.log(res)
      })
      this.checkbalace()
    },
    async checkbalace () {
      const resp = await this.$axios.get('/users/balance/' + this.user.id).then(res => res.data)
      // console.log(resp)
      if (resp.status === 'true') {
        this.user.balance = resp.text
      }
      this.user.deposit_amount = 0
      this.user.withdraw_amount = 0
      this.getlink(6)
    },
    async getgame () {
      const game = await this.$axios.get('/games/' + this.user.id).then(res => res.data)
      this.games = JSON.parse(game.substring(game.indexOf('['), game.length))
    },
    async getlink (idgame) {
      this.user.gameid = idgame
      const link = await this.$axios.post('/games/urlgame', this.user).then(res => res.data)
      this.linkgame = link
      this.show = true
    }, 
    logout () {
      this.$cookies.remove('userState')
      this.$router.push('/')
    },
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
} 
.link {
  padding-top: 15px;
}
</style>
