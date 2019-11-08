<template>
  <div>
    <nuxt />
  </div>
</template>
<script>
export default {
  async created() {
    this.$axios
      .get('/app')
      .then(res => {
        this.$store.dispatch('setApp', res.data)
      })
      .catch(() => {
        // console.log(err)
      })
    const auth = this.$cookies.get('auth') || null
    if (!auth) {
      // this.$store.state.user.auth = null
    } else {
      this.$store.dispatch('setAuth', auth)
    }
    if (this.$store.state.core.auth) {
      this.$axios.defaults.headers.common[
        'Authorization'
      ] = this.$store.state.core.auth.token
    }
  }
}
</script>
<style>
@font-face {
  font-family: 'Niramit';
  src: url('/fonts/Niramit/Niramit-Regular.ttf');
  font-weight: 250;
  font-style: normal;
}
@font-face {
  font-family: 'Niramit';
  src: url('/fonts/Niramit/Niramit-Bold.ttf');
  font-weight: bold;
  font-style: normal;
}
@font-face {
  font-family: 'Niramit';
  src: url('/fonts/Niramit/Niramit-BoldItalic.ttf');
  font-weight: bold;
  font-style: italic;
}
@font-face {
  font-family: 'Niramit';
  src: url('/fonts/Niramit/Niramit-LightItalic.ttf');
  font-weight: light;
  font-style: italic;
}
@font-face {
  font-family: 'Niramit';
  src: url('/fonts/Niramit/Niramit-Light.ttf');
  font-weight: light;
  font-style: normal;
}
html,
body,
p,
h1,
h2,
h3,
h4,
h5,
h6,
span,
li,
button,
div {
  font-family: 'Niramit', sans-serif;
}
html {
  font-family: 'Niramit', sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
}

.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}

.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}

.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #35495e;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
}

.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}
</style>
