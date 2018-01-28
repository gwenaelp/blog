
For a long time, I searched for a way to make simple 3/4 pages websites with these goals in mind :
- Requiring no maintainance or support whatsoever
- Easy to deliver and backup
- Providing to website admins a simple way to edit their content
- Light, easy to understand for website developpers
- Hackable and easily improvable admin

It looks like quite an exigeant list of requirements, but you can also add that now I only want to focus on what I do the most efficiently : front-end development.

#### Let's start building a static cms system (with a static admin as well) !

-- What? Is this possible?
-- Yep.

I use to deploy all my web apps projects on github pages. Let's do that again.

The principle is simple. I would like to have one or several templates for the website, and then, for each page, add the content into the template and generate all the pages.

###### Template (templates/main.html)

```html
<!DOCTYPE html>
<html>
<head>
  <title></title>
</head>
<body>
  <header>Website header</header>
  {{{content}}}
  <footer>Website footer</footer>
</body>
</html>
```

###### Content (fragments/index.html)

```html
Welcome to the index page!
```

Now take a look at the folder architecture of the github repository

###### Repository folder architecture

```sh
├── templates
│   └── main.html     (Main template)
├── fragments
│   └── index.html    (Content of the index page)
├── index.html        (Generated index file)
└── vue-git-cms.json  (Config file for the pages generator)
```

###### Configuration file (vue-git-cms.json)

The config file is here to describe which page has to be generated with which template and which fragment.

```javascript
{
  "pages": [{
    "template": "templates/main.html",
    "fragment": "fragments/index.html",
    "destination": "index.html"
  }]
}
```

#### Admin webapp and page generator

Now we need to allow website administrator to edit pages, and to publish changes to the repo. Using the files declared above, you can create an user interface the way you want, but actually I prefer to develop a web app served statically, deployed to github pages. This way, I have no back-end to maintain, no deployment issues, a good uptime, and some backups :D.

We can actually use the github API directly through cross domain ajax requests, to retreive and push some informations to the repository.

> For more information, I'll let you have a look here :
> https://github.com/github-tools/github

What I did is a web app that asks your github credentials, and the repo to edit. It then opens the config file, build a menu with the list of fragments that can be edited by the administrator. Then, when the admin clicks on a fragment, it opens a wysiwyg editor.
Once the admin has made the changes he want and clicked the "publish" button, the web app retreives the template used for that page, generate the complete compiled page, push the changes (fragment and compiled page) into the repo.

![Draft of the admin webpage](https://image.ibb.co/e6Rotb/git_cms_admin.png "Draft of the admin webpage")

#### Conclusion notes

This is just a draft of a simple system, and there is a lot of room for improvements.
