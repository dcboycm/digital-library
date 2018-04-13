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
          <md-table-cell><md-button class="md-icon-button" @click="removeBook(book)"><md-icon>delete</md-icon></md-button></md-table-cell>
          <md-table-cell><md-button class="md-icon-button" @click="editBook(book)"><md-icon>mode_edit</md-icon></md-button></md-table-cell>
        </md-table-row>
      </md-table>
    </div>

    <!-- Old Add a Book Form -->
    <div class="form">
      <form id="form" novalidate class="" v-on:submit.prevent="addBook">
        <md-card class="md-layout-item">
          <md-card-header>
            <div class="md-title">Add a Book</div>
          </md-card-header>

          <md-field>
            <label for="bookTitle">Title:</label>
            <md-input type="text" id="bookTitle" class="form-control" v-model="newBook.title"/>
          </md-field>
          <md-field>
            <label for="bookAuthor">Author:</label>
            <md-input type="text" id="bookAuthor" class="form-control" v-model="newBook.author"/>
          </md-field>
          <md-field>
            <label for="bookUrl">Url:</label>
            <md-input type="text" id="bookUrl" class="form-control" v-model="newBook.url"/>
          </md-field>
          <md-button type="submit" class="md-raised">Add Book</md-button>
        </md-card>
      </form>
    </div>

    <!-- Add a Book Form -->
    <!-- <div>
      <form novalidate class="md-layout" @submit.prevent="validateUser">
        <md-card class="md-layout-item md-size-50 md-small-size-100">
          <md-card-header>
            <div class="md-title">Users</div>
          </md-card-header>

          <md-card-content>
            <div class="md-layout md-gutter">
              <div class="md-layout-item md-small-size-100">
                <md-field :class="getValidationClass('firstName')">
                  <label for="first-name">First Name</label>
                  <md-input name="first-name" id="first-name" autocomplete="given-name" v-model="form.firstName" :disabled="sending" />
                  <span class="md-error" v-if="!$v.form.firstName.required">The first name is required</span>
                  <span class="md-error" v-else-if="!$v.form.firstName.minlength">Invalid first name</span>
                </md-field>
              </div>

              <div class="md-layout-item md-small-size-100">
                <md-field :class="getValidationClass('lastName')">
                  <label for="last-name">Last Name</label>
                  <md-input name="last-name" id="last-name" autocomplete="family-name" v-model="form.lastName" :disabled="sending" />
                  <span class="md-error" v-if="!$v.form.lastName.required">The last name is required</span>
                  <span class="md-error" v-else-if="!$v.form.lastName.minlength">Invalid last name</span>
                </md-field>
              </div>
            </div>

            <div class="md-layout md-gutter">
              <div class="md-layout-item md-small-size-100">
                <md-field :class="getValidationClass('gender')">
                  <label for="gender">Gender</label>
                  <md-select name="gender" id="gender" v-model="form.gender" md-dense :disabled="sending">
                    <md-option></md-option>
                    <md-option value="M">M</md-option>
                    <md-option value="F">F</md-option>
                  </md-select>
                  <span class="md-error">The gender is required</span>
                </md-field>
              </div>

              <div class="md-layout-item md-small-size-100">
                <md-field :class="getValidationClass('age')">
                  <label for="age">Age</label>
                  <md-input type="number" id="age" name="age" autocomplete="age" v-model="form.age" :disabled="sending" />
                  <span class="md-error" v-if="!$v.form.age.required">The age is required</span>
                  <span class="md-error" v-else-if="!$v.form.age.maxlength">Invalid age</span>
                </md-field>
              </div>
            </div>

            <md-field :class="getValidationClass('email')">
              <label for="email">Email</label>
              <md-input type="email" name="email" id="email" autocomplete="email" v-model="form.email" :disabled="sending" />
              <span class="md-error" v-if="!$v.form.email.required">The email is required</span>
              <span class="md-error" v-else-if="!$v.form.email.email">Invalid email</span>
            </md-field>
          </md-card-content>

          <md-progress-bar md-mode="indeterminate" v-if="sending" />

          <md-card-actions>
            <md-button type="submit" class="md-primary" :disabled="sending">Create user</md-button>
          </md-card-actions>
        </md-card>

        <md-snackbar :md-active.sync="userSaved">The user {{ lastUser }} was saved with success!</md-snackbar>
      </form>
    </div> -->
    <router-view/>

    <!-- Delete Confirmation Modal -->
    <md-dialog
      v-if="showDeleteConfirm"
      :md-active.sync="showDeleteConfirm"
      :md-fullscreen="false"
    >
      <md-dialog-title>Delete this Book?</md-dialog-title>

      <div class="md-dialog-content">You cannot <strong>undo</strong> this action.</div>

      <md-dialog-actions>
        <md-button class="md-default" @click="showDeleteConfirm = false">No</md-button>
        <md-button class="md-accent" @click="removeBook(book)">Yes</md-button>
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
      showDeleteConfirm: false
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
</style>
