<template>
  <div id="app">

    <!-- Table of Current Books in Library -->
    <div class="md-title">Current Library</div>
      <div class="panel-body">
      <md-table md-card class="table">
        <md-table-row>
          <md-table-head>Title</md-table-head>
          <md-table-head>Author</md-table-head>
          <md-table-head>Delete</md-table-head>
          <md-table-head>Edit</md-table-head>
        </md-table-row>
        <md-table-row v-for="book in books"
          :key="book.id">
          <md-table-cell><a :href="book.url">{{book.title}}</a></md-table-cell>
          <md-table-cell>{{book.author}}</md-table-cell>
          <md-table-cell><md-button class="md-icon-button" @click="openModel(book)"><md-icon>delete</md-icon></md-button></md-table-cell>
          <md-table-cell><md-button class="md-icon-button" @click="editBook(book)"><md-icon>mode_edit</md-icon></md-button></md-table-cell>
        </md-table-row>
      </md-table>
    </div>

    <md-button class="md-primary md-raised" @click="addBookModal= true">Add a Book</md-button>

    <!-- Add book dialog -->
    <md-dialog
      :md-active.sync="addBookModal"
      :md-fullscreen="false" >
      <md-dialog-title>Add a Book</md-dialog-title>

      <md-content>
        <md-field>
          <label for="bookTitle">Title:</label>
          <md-input type="text" id="bookTitle" class="form-control" placeholder="Book Title" v-model="newBook.title"/>
        </md-field>
        <md-field>
          <label for="bookAuthor">Author:</label>
          <md-input type="text" id="bookAuthor" class="form-control" placeholder="Author" v-model="newBook.author"/>
        </md-field>
        <md-field>
          <label for="bookUrl">Url:</label>
          <md-input type="text" id="bookUrl" class="form-control" v-model="newBook.url"/>
        </md-field>
      </md-content>

      <md-dialog-actions>
        <md-button class="md-raised" @click="addBookModal = false">Close</md-button>
        <md-button class="md-raised md-primary" @click="addBook">Add Book</md-button>
      </md-dialog-actions>
    </md-dialog>
    <router-view/>

    <!-- Delete Confirmation Modal -->
    <md-dialog
      :md-active.sync="deleteBookModal"
      :md-fullscreen="false" >

      <md-dialog-title>Delete this Book?</md-dialog-title>

      <div class="md-dialog-content">You cannot <strong>undo</strong> this action.</div>

      <md-dialog-actions>
        <md-button class="md-raised" @click="deleteBookModal = false">No</md-button>
        <md-button class="md-raised md-primary" @click="removeBook()">Yes</md-button>
      </md-dialog-actions>
    </md-dialog>

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
      },
      deleteBookModal: false,
      addBookModal: false,
      bookToBeDeleted: null
    }
  },
  methods: {
    addBook: function () {
      booksRef.push(this.newBook)
      this.newBook.title = ''
      this.newBook.author = ''
      this.newBook.url = 'http://'
      this.addBookModal = false
    },
    openModel: function (book) {
      this.deleteBookModal = true
      this.bookToBeDeleted = book
    },
    removeBook: function () {
      booksRef.child(this.bookToBeDeleted['.key']).remove()
      this.deleteBookModal = false
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

.form label {
  text-align: center;
}

.form input {
  text-align: center;
}

.md-card {
  width: 80%;
  margin: 0px auto;
}

.md-table {
  width: 80%;
  margin: 25px auto;

}

.md-table-head {
  text-align: center;
}

.md-dialog-container {
  background: white;
}

.md-primary {
  color: white;
  background: #448aff;
}

.md-content {
  padding: 1rem;
}
</style>
