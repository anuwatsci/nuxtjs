<template>
  <div>
    <b-form >


              <b-form-group  id="input-group-1" label="เบอร์โทร:"  label-for="input-tel"  label-cols="6" label-cols-lg="6">
                <b-form-input id="input-tel" v-model="user.tel"  required></b-form-input>
              </b-form-group>

              <b-form-group id="input-group-2" label="รหัสผ่าน:" label-for="input-password" label-cols="6" label-cols-lg="6">
                <b-form-input type="password" id="input-password" v-model="user.password"  required ></b-form-input>
              </b-form-group>
              <div class="row">
                <div class="col-md-12 text-center">
                  <h4 class="text-danger">{{response}}</h4>
                </div>
              </div>
              <b-button type="button" variant="primary" @click="signin">Login</b-button>
              <b-button type="reset" variant="info" @click="register">Signup</b-button>  
    </b-form> 
  </div>
</template> 

<script>
  export default { 
    data() {
      return {
        user: {
          tel: '',
          password: '',
          member_id: '', 
        }, 
        response: ''
      }
    },
    created() { 
      const auth = String(this.$cookies.get('userState'))
      if(auth.length > 0 && auth !='undefined'  ){
          this.response = 'กำลังไปหน้า dashboard....'
          this.$router.push('/dashboard') 
      }
    },
    methods: { 
      async signin () {
        const resstr = await this.$axios.post('/users/auth', this.user).then(res => res.data)
        if (resstr.status === 'false') {
          this.response = 'เบอร์โทรหรือรหัสผ่านไม่ถูกต้อง'
        } else if (resstr.status === 'true') {
          this.$cookies.set('userState', resstr.text, '1d')
          this.response = 'กำลังไปหน้า dashboard....'
          this.$router.push('/dashboard')
        }
      },
      async register () {
      const resstr = await this.$axios.post('/users', this.user).then(res => res.data)
      if (resstr.status === 'true') {
        this.signin()
        this.response = resstr.text
      } else {
        this.response = resstr.text
      }
    }, 
    }
  }
</script>