<p align="center">
  <img height="124" src="https://i.ibb.co/McgsCqf/vue-stories.png" alt="vue-stories">
</p>

<h1 align="center">
  Vue Stories
</h1>

<p align="center">
  ✌🏻Social media like stories and status for <a href="https://vuejs.org/">Vue.js</a>.
  <br>
</p>
<br>
<p align="center">
  <a href="https://npmjs.org/package/vue-stories">
    <img alt="Repo Size" src="https://img.shields.io/github/repo-size/MustafaPatharia/vue-stories?color=%2342b883" />
</a>
  <a href="https://npmjs.org/package/vue-stories">
    <img alt="Last Commit" src="https://img.shields.io/github/last-commit/MustafaPatharia/vue-stories?color=%2342b883" />
</a>
  </a>
  <a href="https://npmjs.org/package/vue-stories">
    <img alt="issues" src="https://img.shields.io/github/issues/MustafaPatharia/vue-stories?color=%2342b883" />
</a>
  <a href="https://npmjs.org/package/vue-stories">
    <img alt="Licence" src="https://img.shields.io/npm/l/vue-stories?color=%2342b883" />
</a>
</p>

## Installation  🚀

**npm**
```shell
npm install vue-stories
```
**yarn**
```shell
yarn add vue-stories
```
**Import**
```js
import VueStories from 'vue-stories';
```


## Usage👨‍💻

```vue
<template>
  <div @click="openStories">
  
  <VueStories
    :avatarImage=""
    :avatarTitle=""
    :stories="[]" 
    :isPause=true 
    :header=true
    :isSave=true
    ref="vueStories"
    v-on:onNextFrame="actionOnNext"
    v-on:onPreviousFrame="actionOnPrevious"
    v-on:onComplete="actionOnComplete"
    v-on:onFullscreen="actionOnFullscreen"
    v-on:onSave="actionOnSave"
/>
</template>

<script>
  import VueStories from 'vue-stories';

  export default {
    name: 'App',
    
    components: { VueStories },
    
    data() {
      return {
      
        strories:[
          {
            id: String || Number ,
            url: String ,
            title: String ,
            subTitle: String,
            styles:{
              backgroundSize:'contain',  
          }
        ]
      }
    },
    
    methods: {
      //open vue stories
      openStories(){
           this.$refs.vueStories.toggle_fullscreen(true)
      },
      
      actionOnNext(id)
      actionOnPrevious(id)
      actionOnComplete(id)
      actionOnFullscreen(status)
      actionOnSave(status, id)
    }
  };
</script>
```

**Props**

| Prop | Type | Description | Default value |
| ------ | ------ | ------ | ------ |
`avatarImage` | `String` | Image for your avatar | `/assets/sample.png`
`avatarTitle` | `String` | Title fo your avatar | `''`
`stories` | `Array` | List of the stories | `[]`
`isPause` | `Boolean` | Pause the story or not | `true`
`header` | `Boolean` | Show the header or not | `true`
`isSave` | `Boolean` | Show the save button or not | `true`

**Events**

| Event name | Usage |
| ------ | ------ |
`onNextFrame` | Returns id of the previous frame |
`onPreviousFrame` | Returns the id of the next frane |
`onComplete` | Returns the id of the last frame |
`onSave` | Returns true or false with the current frame id |

---

## Support the project ⭐

If you like my work please share and give a star







