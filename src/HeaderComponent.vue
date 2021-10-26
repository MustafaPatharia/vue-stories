<template>
  <div class="stories_box_heading" :ref="'header'" @click="fullscreen()">
    <div class="box-wrapper">
      <div class="img-wrapper">
        <img :src="img" :alt="title">
      </div>
      <div v-if="isFullscreen" class="detail-wrapper">
        <h2 class="title">{{title}}</h2>
        <h4 class="subtitle">{{subTitle}}</h4>
      </div>
    </div>
    <h3 class="mt-1" v-if="!isFullscreen && outside">{{title}}</h3>
  </div>
</template>

<script>
export default {
  props:['img','title','subTitle','outside','isFullscreen'],
  methods:{
    fullscreen(){
      this.isFullscreen = !this.isFullscreen;
      this.$emit('fullscreen', this.isFullscreen)
    }
  }
}
</script>

<style lang="scss" scoped>
.stories_box_heading{
  font-family:Arial, Helvetica, sans-serif;
  margin-top: 1%;
  width: fit-content;
  cursor: pointer;
  text-align: center;
}
.box-wrapper{
  display:flex
}
.img-wrapper{
  width: 90px;
  height: 90px;
  overflow: hidden;
  border-radius: 50%;
  img{
    object-fit: cover;
    height: inherit;
    width: inherit;
    margin:0;
  }
}
.detail-wrapper{
  margin-left:16px;
  margin-top:8px;
  .subitle{
    font-size: 16px;
  }
  h2,h4{
    color: #fff;
  }
  .title{
    font-size:24px;
  }
}
</style>