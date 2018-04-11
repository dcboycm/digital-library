<template>
  <div id="app">
    <div class="panel-heading">
      <h3 class="panel-title">Current Library</h3>
    </div>
      <div class="panel-body">
      <table class="table table-striped">
        <thead>
          <tr>
            <th>Title</th>
            <th>Author</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="book in books"
            :key="book.id">
            <td><a :href="book.url">{{book.title}}</a></td>
            <td>{{book.author}}</td>
            <td><span class="glyphicon glyphicon-trash" aria-hidden="true"
              v-on:click="removeBook(book)"></span></td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3 class="panel-title">Add New Books</h3>
      </div>
      <div class="panel-body">
         <form id="form" class="form-inline" v-on:submit.prevent="addBook">
          <md-input-container class="form-group">
            <label for="bookTitle">Title:</label>
            <input type="text" id="bookTitle" class="form-control" v-model="newBook.title">
          </md-input-container>
          <md-input-container class="form-group">
            <label for="bookAuthor">Author:</label>
            <input type="text" id="bookAuthor" class="form-control" v-model="newBook.author">
          </md-input-container>
          <md-input-container class="form-group">
            <label for="bookUrl">Url:</label>
            <input type="text" id="bookUrl" class="form-control" v-model="newBook.url">
          </md-input-container>
          <md-button>
            <input type="submit" class="btn btn-primary" value="Add Book">
          </md-button>
        </form>
      </div>
    </div>
    <router-view/>
  </div>
</template>

<script>
import Firebase from 'firebase'
import toastr from 'toastr'

let config = {
  apiKey: 'AIzaSyCa0-rEsc1a3bjJh9dH26eX-bJKeOwp8U0',
  authDomain: 'vuejs-firebase-01-8572b.firebaseapp.com',
  databaseURL: 'https://vuejs-firebase-01-8572b.firebaseio.com',
  projectId: 'vuejs-firebase-01-8572b',
  storageBucket: 'vuejs-firebase-01-8572b.appspot.com',
  messagingSenderId: '697820390287'
}

let app = Firebase.initializeApp(config)
let db = app.database()
let booksRef = db.ref('books')

export default {
  data () {
    return {
      newBook: {
        title: '',
        author: '',
        url: 'http://'
      }
    }
  },
  methods: {
    addBook: function () {
      booksRef.push(this.newBook)
      this.newBook.title = ''
      this.newBook.author = ''
      this.newBook.url = 'http://'
    },
    removeBook: function (book) {
      booksRef.child(book['.key']).remove()
      toastr.success('Book removed successfully')
    }
  },
  firebase: {
    books: booksRef
  },
  name: 'App'
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
