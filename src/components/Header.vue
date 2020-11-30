<template>
<div class="container-fluid" style="padding: 0; background:#3587dac2;"> 
<div class="d-flex justify-content-center">
  
  <!-- <div class="container-fluid"> 
    <div class="row"> -->
        <!--
      <div class="col">
        <b-icon
          icon="paperclip"
          scale="6"
          rotate="45"
          color="blue"
          flip-h="true"
          shift-v="3"
          shift-h="0"
        >
        </b-icon>
      </div>
      -->
        <div class="col">
          <b-iconstack font-scale="2">
            <b-icon stacked icon="square"></b-icon>
            <b-icon stacked icon="dot" shift-h="-3" shift-v="4"></b-icon>
            <b-icon stacked icon="dot" shift-h="-3"></b-icon>
            <b-icon stacked icon="dot" shift-h="-3" shift-v="-4"></b-icon>
            <b-icon stacked icon="dot" shift-h="3" shift-v="4"></b-icon>
            <b-icon stacked icon="dot" shift-h="3"></b-icon>
            <b-icon stacked icon="dot" shift-h="3" shift-v="-4"></b-icon>
          </b-iconstack>
          <b-iconstack font-scale="2" rotate="70">
            <b-icon stacked icon="square"></b-icon>
            <b-icon stacked icon="dot" shift-h="-3" shift-v="4"></b-icon>
            <b-icon stacked icon="dot" shift-h="-3" shift-v="-4"></b-icon>
            <b-icon stacked icon="dot" shift-h="0" shift-v="0"></b-icon>
            <b-icon stacked icon="dot" shift-h="3" shift-v="4"></b-icon>
            <b-icon stacked icon="dot" shift-h="3" shift-v="-4"></b-icon>
          </b-iconstack>
          <b-iconstack font-scale="2" rotate="75">
            <b-icon stacked icon="square"></b-icon>
            <b-icon stacked icon="dot" shift-h="-3" shift-v="4"></b-icon>
            <b-icon stacked icon="dot" shift-h="-3" shift-v="-4"></b-icon>
            <b-icon stacked icon="dot" shift-h="3" shift-v="4"></b-icon>
            <b-icon stacked icon="dot" shift-h="3" shift-v="-4"></b-icon>
          </b-iconstack>
        </div>
        <div class="col">
          <h4>{{ titulo }}</h4>
        </div>
        <div class="col">
          <b-button>
            <b-icon icon="question-circle"> </b-icon>
          </b-button>
        </div>
      </div>
      <div class="row">

      <div>  
     </div>
 <!--  </div> -->
 
</template>

<script>
export default {
  data() {
    return {
      counter: 0
    };
  },
  props: ["titulo"],
  methods: {}
};
</script>

<style scoped>
.container-fluid {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
  background: #3587dac2;
}
</style>