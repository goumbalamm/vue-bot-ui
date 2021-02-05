<template lang="pug">
<div class="header">
  <span class="title">ADN Bot UI Integration</span>
  <input type="button" @click="closeBot" value="X" class="close-button">
</div>
</template>
<script>
export default {
  props: {
    botTitle: {
      type: String,
      default: 'Chatbot'
    },
    closeBot: Function
  }
}
</script>
<style scoped>
.close-button{
  margin-left: 15px;
  color: #5B5BCE;
  float: right;
  background-color: white;
  border-radius: 10px;
  color: #5B5BCE;
  border: #5B5BCE solid 2px;
  text-decoration:none;
  font-size: 20px;
  margin-bottom:10px;
  margin-right:15px;
  outline:none;
  cursor: pointer;
}
.header {
  background-color: white;
  margin-top: 10px;
}
.title{
  margin-left: 15px; color: #5B5BCE;
  font-size: 26px
}

</style>
