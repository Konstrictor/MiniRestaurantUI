<template>
  <center>
    <form>
      <input type="text" placeholder="Keyword"
      	id="id_username" v-model="keyword" autofocus>
      <button type="reset" @click="keyword=''">X</button>
    </form>
  </center>
</template>

<script>
export default {
	data(){
		return {
			keyword : ""
		}
	},
	watch:{
		keyword(val){
			this.$emit("changed", val);
		}
	},
};
</script>
<style scoped>
</style>
