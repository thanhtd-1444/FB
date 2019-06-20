<template>
<div>
  <v-flex xs12 sm6 d-flex>
    <v-select
      :items="accounts"
      label="Select Group"
      v-model="selectedItem"
      item-text="name"
      :return-object="true"
    ></v-select>
  </v-flex>
  <v-btn color="info" @click="getListPost(selectedItem)">Get List Posts</v-btn>  
  <h3>
       {{ selectedItem.name }}
  </h3>
  <div class="word-wrap">
    {{ selectedItem.access_token }}
  </div>

  <div class="mt30">
    <h3>List Posts of Page</h3>
    <ul>
      <li v-for="post in posts" :key="post.id">
        <p>{{ post.message }}</p>
      </li>
    </ul>
  </div>

  <h3>Create a Post</h3>
  <div class="post-form">
    <textarea v-model="postContent.message"></textarea>
    <input type="file"
       id="avatar" name="avatar"
       accept="image/png, image/jpeg">
    <v-btn @click="createAPost()">Post</v-btn>
  </div>
</div>


</template>

<script>
import axios from 'axios'

const TOKEN = 'EAAFxptITfoQBAOhwQkqBrI8L5TZAhBMlrmXijJw1acYPwAXGcvbPWk2arNbBSxQEtNLWUdbvvBGmNcswCbgCqDz88ZAvTOIsStysd9w4BqKS0NbvIeGQiL5TEUwpAHbGUOY3fyGaj2Wsxj6yTNxP9ZBzdvOxA2iiQEcT5iARlW0NGruo2ZA6OYgJ2V7tqtEtCPfIISBq14H8qWHkSOGM'
const baseURL = 'https://graph.facebook.com/v3.3'
export default {
  data() {
    return {
      accounts: [],
      selectedItem: {
      },
      posts: [],
      postContent: {
        message: '',
        source: ''
      }
    }
  },

  mounted() {
    this.getListAccount();
  },

  methods: {
    getListAccount() {
      axios.get(`${baseURL}/me/accounts?access_token=${TOKEN}`)
        .then((res) => {
          this.accounts = res.data.data
        })
    },

    getListPost(page) {
      const token = page.access_token
      const pageId = page.id
      axios.get(`${baseURL}/${pageId}/published_posts?access_token=${token}`)
        .then((res) => {
          this.posts = res.data.data
        })
    },

    // Create a Post with Photo
    // Post /page_id/photos/url="url"

    //Create Post Video Video Exist in FB
    // Post /page_id/videos
      //accent_token
      //crossposted_video_id: id_video can dang
      //description : string
      //title: title of videos
    // type: multu-part.form-data
    //key: source:

    createAPost() {
      const token = this.selectedItem.access_token
      const pageId = this.selectedItem.id
      axios.post(`${baseURL}/${pageId}/feed?access_token=${token}`, {
        message: this.postContent.message,
        source: "https://www.youtube.com/watch?v=sfaHV2Pu-is",
        status_type: 1,
        attachments: {
          type: 'video',
          media_type: 'video',
          media: {
            source: 'https://www.youtube.com/watch?v=sfaHV2Pu-is'
          }
        }
      })
        .then((res) => {
          console.log(res)
        })
        .catch((error) => {
          console.log(error)
        })
    }
  }
  
}
</script>

<style>
.word-wrap {
  white-space: -moz-pre-wrap !important;  /* Mozilla, since 1999 */
  white-space: -webkit-pre-wrap; /*Chrome & Safari */ 
  white-space: -pre-wrap;      /* Opera 4-6 */
  white-space: -o-pre-wrap;    /* Opera 7 */
  white-space: pre-wrap;       /* css-3 */
  word-wrap: break-word;       /* Internet Explorer 5.5+ */
  word-break: break-all;
  white-space: normal;
}
</style>
