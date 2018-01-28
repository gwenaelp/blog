I needed to manipulate github repositories with javascript. To read some files, modify them, and create some commits once files were modified. All of this, directly from the browser, without any server-side code.

#### Github-js to the rescue !

[Github-js](https://github.com/github-tools/github) is a node package wrapping Github API. It makes handling Github API a breeze ! As the API handles cross domain requests, You can build web applications that does not even require a web server.

This post will cover a basic use case :

- Retreiving the readme file
- Modifying it into the browser
- Creating a commit and pushing it to Github

The webapp will be made with Vue.js (sorry guys, i love so much this framework... :D)

#### Project initialization

Follow the following commands in a terminal

```sh
# Follow the install wizard and configure the project how you like it.
# Please install the router if you want to follow the next steps)
$ vue init webpack github-admin

# Enter the project
$ cd github-admin

# Install github-api
$ npm i github-api --save

#create two components for the sake of this walkthrough
$ touch src/components/Editor.vue src/components/Login.vue

# Launch the development server
$ npm run dev
```

#### Routes

In this project, we need only two routes :
- login ('/')
- editor ('/editor')

Here is the configuration of the router

##### src/router/index.js

```javacript
import Vue from 'vue';
import Router from 'vue-router';
import Login from '@/components/Login';
import Editor from '@/components/Editor';

Vue.use(Router);

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Login',
      component: Login,
    }, {
      path: '/editor',
      name: 'Editor',
      component: Editor,
    },
  ],
});

```

#### Creating the login form

The login form is made of 3 inputs (username, password, repository), and one login button.

At login, we create the github object and store it globally (not a good practice but it's for the sake of simplicity). Then we change the route to go to the editor.

```html
<template>
  <div>
    <h1>Login form</h1>
    <div>
      <span class="formLabel">Username</span>
      <input type="text" name="username" v-model="username"/>
    </div>
    <div>
      <span class="formLabel">Password</span>
      <input type="password" name="password" v-model="password"/>
    </div>
    <div>
      <span class="formLabel">Repository</span>
      <input type="text" name="repository" v-model="repository"/>
    </div>
    <button @click="login">Login</button>
  </div>
</template>

<script>
import GitHub from 'github-api';

export default {
  name: 'Login',
  data() {
    return {
      username: '',
      password: '',
      repository: '',
    };
  },
  methods: {
    login() {
      window.github = new GitHub({
        username: this.username,
        password: this.password,
        auth: 'basic',
      });

      window.repository = github.getRepo(this.username, this.repository);

      this.$router.push('/editor');
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.formLabel {
  display: block;
  margin-top: 8px;
}
</style>
```

#### Creating the editor component

This editor tries to load the readme of the project at its initialization, and then store the content in the ```readme``` property.

We use the ```repository``` object defined previously by the login form.
If this object does not exists, we redirect to the login page.


The save method creates a commit with the modified file.

```javascript
<template>
  <div>
    <h1>Edit Readme</h1>
      <div>
        From this form you can edit the readme of the project.
      </div>
      <div>
        <textarea v-model="readme" placeholder="Content of the Readme file"></textarea>
      </div>
    <button @click="save">Save changes</button>
  </div>
</template>
<script>
export default {
  name: 'Editor',
  data() {
    // Redirect to login if the user did not log in
    if (!window.repository) {
      this.$router.push('/');
    }

    window.repository.getContents('master', 'README.md', false, (err, responseText) => {
      if (err) {
        console.error(err);
      }

      // The content is encoded with base64, so you need to use atob() to get it.
      this.readme = atob(responseText.content);
    });

    return {
      readme: '',
    };
  },
  methods: {
    save() {
      window.repository.writeFile(
        'master', // Branch you want to push the file to
        'README.md', // File path
        this.readme, // Content
        'edit readme from the webapp', // Commit message
        (err) => {
          // Callback
          if (err) {
            console.error(err);
          } else {
            alert('File edited sucessfully.');
          }
        },
      );
    },
  },
};
</script>
```

You now have a frontend served statically that allows you to edit one file on github. This walkthrough ends here. It's the first step to build more complex web applications. You can think about developping a wiki system, a contact editor... Everything is possible, you just need to continue this example !

You can find the full project [here](https://github.com/gwenaelp/blog-example-github-editor) and a demo served on github pages [here](https://gwenaelp.github.io/blog-example-github-editor/dist/).
