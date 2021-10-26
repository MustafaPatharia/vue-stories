<template>
  <div :style="styles.vue_stories">
    <div class="stories_box">
        <div v-if="header" >
            <HeaderComponent v-on:fullscreen="toogle_fullscreen" :img="avatarImage" :title="avatarTitle" :subTitle="avatarSubTitle" :outside="titleOutside" :isFullscreen="fullscreen"></HeaderComponent>
        </div>
    </div>
    <button v-if="fullscreenMode" id="fullscreen_button" @click.stop="toogle_fullscreen()">
      <span v-if="fullscreen" class="material-icons md-36">cancel</span>
    </button>

    <div id="stories_container" :ref="'stories_container'" :style="styles.stories_container_style" @click="toogle_pause()">
      <button id="left_frame_button" :style="styles.left_frame_button" @click="previous_frame()">
        <span class="material-icons md-52" style="transform:rotate(90deg)">expand_circle_down</span>
      </button>
      <button  id="right_frame_button" :style="styles.right_frame_button" @click="next_frame()">
        <span class="material-icons md-52" style="transform:rotate(-90deg)">expand_circle_down</span>
      </button>


      <div id="stories_progress_container" :style="styles.stories_progress_container">
        <div :style="styles.stories_progress_box" v-for="(story,index) in stories" v-bind:key="index" @click.stop="progress_click(index)">
          <div :ref="'progress'+index" :style="progress(index,story.duration)" class="stories_progress">
          </div>
        </div>
      </div>

      <video v-show="showVideo" @click.stop="toogle_video_pause()" :ref="'video_source'" :style="styles.video_frame_style" src="" autoplay muted></video>
      
      
      <div :style="styles.seemore_box" :ref="'see_more'">
        <div :style="styles.seemore_box_inner">
          <div :style="styles.seemore" @click.stop="seemore_click()" title="See More">
            <a style="padding:4px;">
               <span v-if="!arrowClick" :style="styles.arrow_style" class="material-icons md-48">keyboard_arrow_up</span>
               <span v-else :style="styles.arrow_style" class="material-icons md-48">keyboard_arrow_down</span>
            </a>
          </div>
          <p :style="styles.see_more_text">See More</p>
        </div>
        <div :style="styles.see_more_content" :is="render_seemore_component">
          
        </div>
      </div>
    </div>
  </div>
</template>

<script>
 import HeaderComponent from './HeaderComponent';
import SeemoreComponent from './SeemoreComponent';
export default {

  props:{
      stories:Array,
      loop:Boolean,
      titleOutside:Boolean,
      header:Boolean,
      isPause:Boolean,
      fullscreenMode:Boolean,
      avatarImage:String,
      avatarTitle:String,
      avatarSubTitle:String,
  },
  components:{ HeaderComponent,SeemoreComponent},

  data(){
    return {
      styles:{
        stories_container_style:{
          display: 'none',
          width:this.width+'px',
          height:this.height+'px',
          backgroundSize:"cover",
          backgroundRepeat:"no-repeat",
          backgroundColor:"black",
          backgroundPosition:"center",
          backgroundImage:"",
          color:"white",
          position:'relative',
          left:'',
          marginLeft:'',
          zIndex:'3500',
          top:'',
          boxShadow: '5px 5px 10px gray',
        },
        default_stories_container_style:{
          width:this.width+'px',
          height:this.height+'px',
          backgroundSize:"cover",
          backgroundRepeat:"no-repeat",
          backgroundColor:"black",
          backgroundPosition:"center",
          color:"white",
          position:'relative',
          left:'',
          marginLeft:'',
          zIndex:'3500',
          top:'',
          boxShadow: '5px 5px 10px gray',
        },
        stories_progress_box:{
          margin: '2px',
          height: '3px',
          background: 'grey',
          cursor: 'pointer',
          width :(100/this.stories.length)+'%',
        },
        stories_progress_container:{
          display: 'flex',
          padding: '10px 10px',
        },
        video_frame_style:{
          width:"99.9%",
          height:"96%",
          objectFit:"cover",
        },
        default_video_frame_style:{
          width:"99.9%",
          height:"96%",
          objectFit:"cover",
        },
        seemore:{
          height: '100%',
          width: '100%',
          cursor:'pointer',
        },
        seemore_box:{
          display: 'flex',
          justifyContent: 'center',
          position: 'absolute',
          width: '100%',
          height: '15%',
          background: 'transparent',
          bottom: '0',
          overflow:'hidden',
          animation:'',
          animationFillMode:'',
        },
        seemore_box_inner:{
          display: 'table',
          margin:'0 auto',
          width: '70px',
          textAlign:'center',
        },
        arrow_style:{
          color:'white',
        },
        see_more_content:{
          display:'none',
          padding:'10px',
        },
        see_more_text:{
          margin: '0px',
          fontFamily: 'monospace',
        },
        vue_stories:{
          position:'',
          left:'',
          top:'',
          width:'',
          height:'',
          backgroundColor:'',
          zIndex:'',
        },
        left_frame_button:{
          display:'none',
        },
        right_frame_button:{
          display:'none',
        },
      },
      frame: 0,
      pauseState:false,
      fullscreen:false,
      showVideo:false,
      width:350, 
      height:600,
      seemoreClick:false,
      arrowClick:false,
      render_seemore_component:'',
      render_header_component:'',
    }
  },
  mounted(){
    this.start_progress();
  },

  methods:{

    previous_frame(){

      this.stop_all_animation();
      if(this.frame == 0){
        for(var i = 0; i < this.stories.length; i++){
          this.$refs['progress'+i][0].style.width = '100%';
        }
        // this.$refs['box'+this.frame][0].style.display = 'none';
        this.frame = this.stories.length-1;
      }
      else{
        for(var i = 0; i <= this.frame; i++){
          this.$refs['progress'+i][0].style.width = '100%';
        }
        for(var i = this.frame-1; i <= this.frame; i++){
          this.$refs['progress'+i][0].style.width = '0%';
        }
        // this.$refs['box'+this.frame][0].style.display = 'none';
        this.frame--;
      }
      this.$emit('onPreviousFrame', true)
      this.set_frame_url();
      setTimeout(()=>{
        this.set_frame_duration();
      },500);
    },

    next_frame(){
      this.stop_all_animation();
      if(this.frame == this.stories.length-1){
        for(var i = 0; i < this.stories.length; i++){
          this.$refs['progress'+i][0].style.width = '0%';
        }
        // this.$refs['box'+this.frame][0].style.display = 'none';
        this.frame = 0;
      }
      else{
        for(var i = 0; i < this.frame+1; i++){
          this.$refs['progress'+i][0].style.width = '100%';
        }
        // this.$refs['box'+this.frame][0].style.display = 'none';
        this.frame++;
      }
      this.$emit('onNextFrame', true)
      this.set_frame_url();
      setTimeout(()=>{
        this.set_frame_duration();
      },500);
    },

    toogle_fullscreen(value){
     
      if(value){
        this.styles.stories_container_style.display = 'block'
        this.styles.left_frame_button.display = '';
        this.styles.right_frame_button.display = '';

        this.styles.vue_stories.position = 'absolute';
        this.styles.vue_stories.zIndex = '3000';
        this.styles.vue_stories.left = '0px';
        this.styles.vue_stories.top = '0px';
        this.styles.vue_stories.width = '100%';
        this.styles.vue_stories.height = '100%';
        this.styles.vue_stories.backgroundColor = 'rgba(0,0,0,0.5)';

        this.styles.stories_container_style.width = (screen.width*0.27)+'px';
        this.styles.stories_container_style.height = (screen.height*0.83)+'px';
        this.styles.stories_container_style.position = 'absolute';
        this.styles.stories_container_style.top = '3%';
        this.styles.stories_container_style.left = '50%';
        this.styles.stories_container_style.marginLeft = '-'+((screen.width*0.27)/2)+'px';

        this.styles.default_stories_container_style.width = (screen.width*0.27)+'px';
        this.styles.default_stories_container_style.height = (screen.height*0.83)+'px';
        this.styles.default_stories_container_style.position = 'absolute';
        this.styles.default_stories_container_style.top = '3%';
        this.styles.default_stories_container_style.left = '50%';
        this.styles.default_stories_container_style.marginLeft = '-'+((screen.width*0.27)/2)+'px';
        
        let smallerContainerwidth = 365

        if(screen.width <= 1024  && screen.width >= 486){
          this.styles.stories_container_style.width = (smallerContainerwidth)+'px';
          this.styles.stories_container_style.marginLeft = '-'+(smallerContainerwidth/2)+'px';
          this.styles.default_stories_container_style.width = (smallerContainerwidth)+'px';
          this.styles.default_stories_container_style.marginLeft = '-'+(smallerContainerwidth/2)+'px';
          this.styles.stories_container_style.marginTop = '96px';

        }
        this.fullscreen = true;
        // this.$emit('fullscreen', false);
      }
      else{
        this.styles.stories_container_style.display = 'none'
        this.styles.left_frame_button.display = 'none';
        this.styles.right_frame_button.display = 'none';

        this.styles.vue_stories.position = '';
        this.styles.vue_stories.zIndex = '';
        this.styles.vue_stories.left = '';
        this.styles.vue_stories.top = '';
        this.styles.vue_stories.width = '';
        this.styles.vue_stories.height = '';
        this.styles.vue_stories.backgroundColor = '';
        this.fullscreen = false;
      }
    }, 

    seemore_click(){
      if(this.seemoreClick){
        this.play_frame();
        this.styles.arrow_style.color = 'white';
        this.styles.seemore_box.height = '15%';
        this.styles.seemore_box.background = 'transparent';
        this.styles.seemore_box.animation = 'slidedown 1s ease';
        this.styles.seemore_box.animationFillMode = 'forwards';
        this.styles.see_more_content.display = 'none';
        this.seemoreClick = false;
        this.arrowClick = false;
        this.$emit('seemore', false);
      }
      else{
        this.pause_frame();
        this.styles.arrow_style.color = 'black';
        this.styles.seemore_box.height = '100%';
        this.styles.seemore_box.background = 'white';
        this.styles.seemore_box.animation = 'slideup 1s ease';
        this.styles.seemore_box.animationFillMode = 'forwards';
        this.styles.see_more_content.display = '';
        this.render_seemore_component = this.stories[this.frame].seemore;
        this.seemoreClick = true;
        this.arrowClick = true;
        this.$emit('seemore', true);
      }
    },

    stop_all_animation(){
      for(var i = 0; i < this.stories.length; i++){
        this.$refs['progress'+i][0].style.width = '0%';
        this.$refs['progress'+i][0].style.animation = '';
        this.$refs['progress'+i][0].style.animationFillMode = '';
      }
    },

    progress_click(index){
      this.stop_all_animation();
      for(var i = 0; i < index; i++){
        this.$refs['progress'+i][0].style.width = '100%';
      }
      // this.$refs['box'+this.frame][0].style.display = 'none';
      this.frame = index;
      this.set_frame_url();
      setTimeout(()=>{
        this.set_frame_duration();
      },500)
    },

    toogle_video_pause(){
      if(this.pauseState){
        this.play_frame();
      }
      else{
        this.pause_frame();
      }
    },

    pause_frame(){
      this.pauseState = true;
      this.$refs['progress'+this.frame][0].style.animationPlayState = 'paused';
      this.$refs['video_source'].pause();
    },

    play_frame(){
      this.pauseState = false;
      this.$refs['progress'+this.frame][0].style.animationPlayState = '';
      this.$refs['video_source'].play();
    },

    toogle_pause(){
      if(this.isPause){
        if(this.pauseState){
          this.play_frame();
        }
        else{
          this.pause_frame();
        }
      }
    },

    progress(index,duration){
      if(index == 0){
        if(duration){
          var duration = duration/1000;
        }
        else{
          var duration = 2000/1000;
        }
        return {
          animation:'start_progress '+duration+'s',
          animationFillMode:'forwards',
        };
      }
    },

    stories_box(index){
      if(index == 0){
        return 'display:block;';
      }
      return 'display:none;';
    },

    display_see_more(){
      this.styles.seemore_box.animation = '';
      if(this.stories[this.frame].seemore){
        this.styles.seemore_box.display = 'block';
      }
      else{
        this.styles.seemore_box.display = 'none';
      }
    },

    set_frame_duration(){
      if(this.stories[this.frame].duration){
        var duration = this.stories[this.frame].duration/1000;
      }
      else{
        var duration = 2000/1000;
      }
      this.$refs['progress'+this.frame][0].style.animation = 'start_progress '+duration+'s';
      this.$refs['progress'+this.frame][0].style.animationFillMode = 'forwards';
    },

    set_frame_url(){
      // this.display_header();
      if(this.stories[this.frame].url){
        if(this.stories[this.frame].type == "image"){
          Object.assign(this.styles.stories_container_style,this.styles.default_stories_container_style);
          this.showVideo = false;
          this.styles.stories_container_style.backgroundImage = "url("+this.stories[this.frame].url+")";
          if(this.stories[this.frame].styles){
            Object.assign(this.styles.stories_container_style,this.stories[this.frame].styles);
          }
        }
        else{
          Object.assign(this.styles.stories_container_style,this.styles.default_stories_container_style);
          this.styles.stories_container_style.backgroundImage = "";
          Object.assign(this.styles.video_frame_style,this.styles.default_video_frame_style);
          this.$refs['video_source'].src = this.stories[this.frame].url;
          this.styles.video_frame_style.height = (100-(document.getElementById('stories_progress_container').offsetHeight/this.height)*100)+"%";
          if(this.stories[this.frame].styles){
            Object.assign(this.styles.video_frame_style,this.stories[this.frame].styles);
          }
          this.showVideo = true;
        }
      }
      else{
        Object.assign(this.styles.stories_container_style,this.styles.default_stories_container_style);
        this.styles.stories_container_style.backgroundImage = "";
        if(this.stories[this.frame].styles){
          Object.assign(this.styles.stories_container_style,this.stories[this.frame].styles);
        }
        this.showVideo = false;
        // this.$refs['box'+this.frame][0].style.display = 'block';
      }
      this.display_see_more();
    },

    start_progress(){
      this.set_frame_url();
      for (var i=0; i<this.stories.length; i++){
          this.$refs['progress'+i][0].addEventListener("animationend", ()=>{
          this.frame++;
          if(this.frame <= this.stories.length-1){
            this.$emit('onNextFrame', true)
            this.set_frame_url();
            this.set_frame_duration();
          }
          else{
            this.$emit('onComplete',true);
            this.toogle_fullscreen();
            this.stop_all_animation();
            this.frame = 0
            this.set_frame_duration();
          }
        });
      }
    },
  },


};
</script>

<style>
#fullscreen_button{
    position:absolute;
    right:3%;
    top:4%;
    outline:none;
    border:none;
    background-color:transparent;
    color:#BFBFBF;
    cursor:pointer;

  }
 #left_frame_button{
  position:absolute;
  left:-16%;
  top:50%;
  margin-top:-40px;
  outline:none;
  border:none;
  background-color:transparent;
  color:#BFBFBF;
  cursor:pointer;
}
#right_frame_button{
  position:absolute;
  right:-16%;
  top:50%;
  margin-top:-40px;
  outline:none;
  border:none;
  background-color:transparent;
  color:#BFBFBF;
  cursor:pointer;
}
#right_frame_button:hover , #left_frame_button:hover, #fullscreen_button:hover{
  color: white;
}

.md-36{
    font-size: 36px;
}
.md-48{
    font-size: 48px;
}
.md-52{
    font-size: 52px;
}
.stories_progress{
  background: white;
  width: 0%;
  height: 100%;
}
@keyframes start_progress {
  from{width: 0%;}
  to{width: 100%;}
}
.stories_box{
  padding: 1px 20px;
}
.stories_box_heading{
  font-family:Arial, Helvetica, sans-serif;
  margin-top: 10%;
}
.seemore{
  height: 100%;
  width: 100%;
  background: white;
  border: 1px solid white;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.seemore path{
  fill: black;
}
.seemore a{
  color: white;
  text-decoration: none;
  font-size: 12px;
  width: 100%;
  text-align: center;
}
.seemore_box{
  display: none;
  justify-content: center;
  position: absolute;
  width: 100%;
  height: 15%;
  background: transparent;
  bottom: 0;
}
@keyframes slideup {
  from{
    height: 15%;
    background: transparent;
  }
  to{
    height: 100%;
    background: white;
  }
}
@keyframes slidedown {
  from{
    height: 100%;
    background: white;
  }
  to{
    height: 15%;
    background: transparent;
  }
}
</style>