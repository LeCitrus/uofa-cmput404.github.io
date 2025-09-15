Title: Django Lab
date: 2024-09-05
tags: labs
authors: Alireza Azimi, Mo Adel Abdelghany, William Qi
status: Published
summary: Lab Procedure, Lab Assignments, Lab Marking
----

[TOC]

---

# Description

The purpose of this lab is to familiarize you with setting up a virtual environment, developing with Django, and using NPM to incorporate external packages like an emoji picker and a markdown editor. By the end of this lab, you will build a simple wiki app where users can add emojis, likes, and edit content using Markdown.

At the end of this lab, you will have built a fully functioning web app with the following features:
- An emoji picker using the `emoji-mart` NPM package.
- A Markdown editor where users can write content and convert it to HTML.
- Content storage in a Django model using the ORM.
- A working wiki website
- A working like system for each wiki page
- Efficient serving of static files with WhiteNoise.

# Getting Started

# Get an Environment with a Recent Python Version

[Make sure you have a working development environment with these instructions!]({filename}/general/environment.md)


### Introduction to HTML and JavaScript

Before diving into Django and NPM, let’s go over some basics of HTML and JavaScript.

### HTML Basics

HTML is the structure of a webpage. It defines elements such as **headings** (`<h1>`, `<h2>`, etc.), **paragraphs** (`<p>`), and **divs** (`<div>`) to organize content.

#### Example HTML Structure:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Web Page</title>
</head>
<body>
    <h1>Welcome to My Web Page</h1>
    <div>
        <p>This is a paragraph inside a div.</p>
    </div>
</body>
</html>
```

- **Headings** help organize content by importance. Use `<h1>` for the most important heading and `<h6>` for the least important.
- **Divs** are containers that group elements together. You can style or manipulate all content inside a div.

### JavaScript Basics

JavaScript is used to add interactivity to a web page. You can manipulate the **DOM** (Document Object Model), which represents the structure of the page, to change content, styles, or even add new elements.

#### Example JavaScript:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JS Example</title>
</head>
<body>
    <h1 id="heading">Hello, World!</h1>
    <button id="changeTextBtn">Change Text</button>

    <script>
        const button = document.getElementById('changeTextBtn');
        button.addEventListener('click', function() {
            document.getElementById('heading').textContent = 'You clicked the button!';
        });
    </script>
</body>
</html>
```

---

## Prepare your Repo

1. Get the GitHub classroom link from Canvas, create your assignment, and clone it.
2. Create an appropriate `.gitignore` file to prevent unwanted files from being committed to your repository. 

Place this `.gitignore` file in the root of your project. You can combine [this one](https://github.com/github/gitignore/blob/main/Python.gitignore), [this one](https://github.com/github/gitignore/blob/main/Node.gitignore), and [this one](https://github.com/django/django/blob/main/.gitignore) for your Django + Node project. Use `git status` to ensure you're not staging any unwanted files before committing.

## Create the Virtual Environment

A virtual environment is a tool for managing Python dependencies specific to your project. Each project may have different requirements, and a virtual environment helps you manage these dependencies independently.

### Windows OS

These commands are assuming you are using Linux or macOS. On Windows the commands need to be different.
We recommend using WSL2 to run Ubuntu in a VM.
You can install Ubuntu on Windows following [these instructions]({filename}http.md#ubuntu-on-windows).

### Setting up Python

Check your Python version with `--version`.

If you have multiple versions of Python installed, always specify the version number.
For example, on the lab machines there are multiple versions of Python installed.

```txt
hazelcam@innisfree:~>python --version
Python 2.7.18
hazelcam@innisfree:~>python3 --version
Python 3.8.10
hazelcam@innisfree:~>python3.8 --version
Python 3.8.10
hazelcam@innisfree:~>python3.11 --version
Python 3.11.10
```

So, to use Python 3.11 on the lab machines, we need to run it as `python3.11` not `python3` or `python`.

### Check for pip and venv/virtualenv:

To check if `venv` or `virtualenv` is installed:

```bash
python -m venv --help
python -m virtualenv --help
```

Either `venv` or `virtualenv` will work. 

If you don't have either, install `virtualenv` using pip.

```bash
python -m pip install --user virtualenv
```

If you don't have `pip` or `venv` or `virtualenv`, you need to install newer version of Python.

### Create Virtual Environment

Run `venv` or `virtualenv` and tell it to create a virtual environment directory, called `venv`.

Note the first `venv` or `virtualenv` is the name of the Python module.
The second `venv` is the name of a directory (folder) you'd like it to create.

```bash
python -m venv venv
# or
python -m virtualenv venv
```

### Activate Virtual Environment:

To activate the virtual environment:

```bash
source venv/bin/activate
```

If it is activated your command prompt should start with `(venv)` or whatever you called the directory virtualenv or venv made.

```text
hazelcam@innisfree:~>source venv/bin/activate
(venv) hazelcam@innisfree:~>
```

<p class="warning">Remember to never commit your virtual environment directory (folder) to git.
Make sure it is listed in the <code>.gitignore</code>.
</p>

## Installing Node.js and NPM

To install Node.js, download the LTS version from the [Node.js website](https://nodejs.org/en/download). The installation will include NPM as well, which we will use to manage JavaScript packages.

# Lab Instructions

## Django Setup

We will use [Django](https://www.djangoproject.com/), a high-level Python web framework. It simplifies web development by providing a built-in ORM, an admin panel, and much more. The [official documentation](https://docs.djangoproject.com/en/5.1/) is a valuable resource, and **you will find it very helpful** to reference it during this lab for anything that is unclear.

### Install Django

Make sure your virtual environment is created and activated as above.

```
echo "Django>=5.0.1" > requirements.txt
python -m pip install -r requirements.txt
```

**If you are encountering an issue with this command in regards to a library called `html5lib` you will need to update your pip version first.**
```
wget https://bootstrap.pypa.io/get-pip.py -O ./get-pip.py
python3.11 ./get-pip.py
```

### If you're using an Ubuntu VM (such as UTM or WSL)

You will need to point your browser to the VM's IP address, not `127.0.0.1` or `localhost`.
Here is an example from an Ubuntu WSL install on Windows:

```txt
hazelcam@Roxanne:~$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet 10.255.255.254/32 brd 10.255.255.254 scope global lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:8c:d5:00 brd ff:ff:ff:ff:ff:ff
    inet 172.23.110.83/20 brd 172.23.111.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::215:5dff:fe8c:d500/64 scope link
       valid_lft forever preferred_lft forever
```

In this case the VM's address is `172.23.110.83`.
So later when I want to see my Django site,
I will need to put `http://172.23.110.83:8000/` into my browser, instead of `localhost`.

### Follow the Django Tutorial

This is optional (not graded), but it will help you get familiar with Django. 
<https://docs.djangoproject.com/en/5.1/intro/tutorial01/>

### Start a new Django project

```
django-admin startproject lab2
cd lab2
python manage.py runserver 0.0.0.0:8000
```

You should see a "Congratulations!" page when visiting `localhost:8000`.

### Creating a Django Webapp

We will create a Django webapp called `wiki` to build a simple wiki app.

**Create a new app:**
```
python manage.py startapp wiki
```

**Modify `lab2/settings.py` to register your new wiki app to Django**
```py
INSTALLED_APPS = [
    'wiki', # Add this line!
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

**In `wiki/views.py`, add the following code:**

```py
from django.http import HttpResponse

def index(request):
    return HttpResponse("This is the wiki app.")
```

**Create a `urls.py` file inside `wiki` and add the following code:**
   
```py
from django.urls import path
from . import views

urlpatterns = [
    path("", views.index, name="index")
]
```

**Update `lab2/urls.py` to include the new app:**

```py
from django.urls import include, path
urlpatterns = [
    path("wiki/", include("wiki.urls")),
    path("admin/", admin.site.urls),
]
```

## Adding HTML Templates

Django allows you to render HTML templates. Let’s create an HTML page to display a homepage.

1. Inside the `wiki` directory, create a `templates` directory.
2. Inside `templates`, create an `index.html` file with the following boilerplate:
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Wiki App</title>
    </head>
    <body>
        <h1>Welcome to your wiki!</h1>
    </body>
</html>
```

3. Update `wiki/views.py` to render the template:
```py
from django.shortcuts import render

def index(request):
    return render(request, "index.html")
```

Now, visiting `localhost:8000/wiki/` will display the HTML template.

## Adding Markdown Editor

In this section, we will integrate a **Markdown editor** so that users can write content in Markdown format and see it rendered as HTML in the browser. We'll use the popular NPM package `marked` for parsing Markdown content and converting it to HTML.

### Steps to Add the Markdown Editor:

Install the `marked` and `esbuild` packages in the root directory of your repository:
```bash
npm install --save-dev marked
npm install --save-dev esbuild
```

Create a new directory in the root directory called `webapp` and create a `markdown-editor.js` file in it.

Update your `markdown-editor.js` file to the following:
```js
import { marked } from "marked"; // Import the markdown converter

// Handle Markdown conversion and rendering
document.getElementById('convert-btn').addEventListener('click', e => {
  e.preventDefault();   // Prevents native functionality for this event
  const markdownText = document.getElementById('markdown-editor').value;
  const htmlOutput = marked(markdownText); // Convert markdown to HTML
  document.getElementById('markdown-output').innerHTML = htmlOutput; // Add the generated HTML code to the output div element
});
```

This file (`webapp/markdown-editor.js`) depends on a node dependency and needs to be bundled with a tool called `esbuild` to make it ready to be able to run on a browser.

In your **base repository directory**, run the following command to use `esbuild` to bundle your `markdown-editor.js` file.
`npx esbuild ./webapp/markdown-editor.js --bundle --minify --sourcemap --outfile=./lab2/wiki/static/markdown-editor.min.js`

This command bundles your `markdown-editor.js` file and all the imports that it needs into one huge file called `markdown-editor.min.js`. It is now ready to be included into your Django application!

### Django Integration

While we do have the JavaScript for our markdown editor, we still need the HTML for the editor! Create a new HTML file called `editor.html` in `lab2/wiki/templates/` and add the following content:

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>New Wiki Page</title>
</head>
<body>
    <h1>Welcome to the Wiki App!</h1>

    <div>
        <h3>Markdown Editor</h3>
        <textarea id="markdown-editor" rows="10" cols="50" placeholder="Write in Markdown..."></textarea>
        <button id="convert-btn">Convert to HTML</button>
    </div>

    <h3>Output</h3>
    <div id="markdown-output"></div> <!-- Rendered HTML output -->
    
    <script src="{% static 'markdown-editor.min.js' %}"></script> <!-- Load bundled JS -->
</body>
</html>
```

Add a new view function that will render this template in `wiki/views.py`
```py
def editor(request):
    return render(request, "editor.html")
```

In `wiki/urls.py`, add the new route for our markdown editor:

```py
from django.urls import path
from . import views

urlpatterns = [
    path("", views.index, name="index"),
    path("add/", views.editor, name="add"),
]
```

If you navigate to `localhost:8000/wiki/add/` you should see your markdown editor!
Examples of how to use markdown can be found [here](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) if you would like to test it out yourself.

We now have a visible markdown editor, but we have no way of actually storing data yet. So let’s store the content users create in a Django model.

Update `wiki/models.py` to create a model for storing user-created pages:

```python
from django.db import models

class Page(models.Model):
    id = models.AutoField(primary_key=True)
    title = models.CharField(max_length=200)
    content = models.TextField()  # Markdown content
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"#{self.id} - {self.title}"
```

After creating the model, run `python3.11 manage.py makemigrations` and `python3.11 manage.py migrate` to create/update and apply our database schema. This will additionally create a `db.sqlite3` file within your project directory, which stores all of your database data as an SQLite3 database.

### TASK - Backend For Storing Wiki Pages

Replace the contents of `wiki/templates/editor.html` with this:
```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>New Wiki Page</title>
</head>
<body>
    <h1>Welcome to the Wiki App!</h1>

    <form id="save-form" method="POST" action="/wiki/save/">
        <input type="text" name="title" id="page-title" placeholder="Page Title"><br>
        <textarea id="markdown-editor" name="content" rows="10" cols="50"></textarea><br>
        <button id="convert-btn">Convert to HTML</button><br>
        <br>
        <button id="save-btn" type="submit">Save Page</button>
        {% csrf_token %}
    </form>

    <h3>Output</h3>
    <div id="markdown-output"></div> <!-- Rendered HTML output -->
    
    <script src="{% static 'markdown-editor.min.js' %}"></script> <!-- Load bundled JS -->
</body>
</html>
```

**Your task** is to create the backend logic for handling the submission of the add wiki page. Your logic **MUST** use the `Page` model that we created earlier in the lab and your logic **MUST** be run when sending a POST request to `/wiki/save/`. It **MUST** redirect to `/wiki/` after saving the page.

## Displaying Wiki Pages

While it's great that we can store wiki pages, we need a way to access them ourselves.

### Homepage

Change the `index.html` template in `wiki/templates`. Replace its contents with the following code snippet in it:
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>My Wiki | Home</title>
        <meta charset="UTF-8">
    </head>
    <body>
        <h1>My Wiki Pages</h1>
        <a href="/wiki/add/">Add Article</a><br>
        <ul>
            {% for page in pages %}
                <li><a href="{{ page.url }}">{{ page.title }}</a></li>
            {% endfor %}
        </ul>
    </body>
</html>
```

Modify `wiki/views.py` to replace the `index` function and also add a new function:
```py
from django.shortcuts import render, get_object_or_404
from django.urls import reverse
from .models import Page

def index(request):
    pages = []

    page_objects = Page.objects.all()
    for page in page_objects:
        pages.append({
            "title": page.title,
            "url": reverse("view", kwargs={ "id": page.id })
        })
    return render(request, "index.html", { "pages": pages })

def view_page(request, id):
    page = get_object_or_404(Page, pk=id)
    return render(request, "page.html", { "title": page.title, "content": page.content, "id": id })
```

Modify `wiki/urls.py` to include our new view function.
```py
path("page/<int:id>/", views.view_page, name="view")
```

### Wiki Page

Create a new file `wiki/templates/page.html` and paste the following snippet in:
```html
{% load static %}
<!DOCTYPE html>
<html>
    <head>
        <title>{{ title }}</title>
    </head>
    <body> 
        <div id="content" style="display: none;">{{ content }}</div>
        <div>
            <a href="/wiki/">&lt; Go Back</a>
        </div>
    </body>
    <script src="{% static 'markdown-renderer.min.js' %}"></script> <!-- Load bundled JS -->
</html>
```

In the `webapp` folder, create a new file called `markdown-renderer.js` and paste the following snippet in:
```js
import { marked } from "marked"; // Import the markdown converter

// Handle rendering
window.addEventListener('load', () => {
    const markdownText = document.getElementById('content').innerHTML;
    const htmlOutput = marked(markdownText);
    const contentDiv = document.getElementById('content');
    contentDiv.innerHTML = htmlOutput;
    contentDiv.style.display = 'block';
});
```

Use `esbuild` to transpile it with this command and then your website should now have a functional wiki!
`npx esbuild ./webapp/markdown-renderer.js --bundle --minify --sourcemap --outfile=./lab2/wiki/static/markdown-renderer.min.js`

## Serving Static Files with WhiteNoise

Now that we have a Django app with Markdown editing and content storage, we need to configure our static hosting middleware. In a production environment, Django will not export static files which is why we need to use a static middleware. Changing the `DEBUG` variable in `lab2/settings.py` to `False`, running `python3 manage.py runserver`, and then navigating to `localhost:8000/wiki/add/` will not allow you to preview any Markdown code. (note that you will also have to update `ALLOWED_HOSTS` to `['*']` if you would like to see it yourself) We can use **WhiteNoise** (a static file middleware) to resolve this. You can learn more about [WhiteNoise here](https://whitenoise.readthedocs.io/en/latest/django.html). 

1. Install WhiteNoise:
    ```
    pip install whitenoise
    ```

2. Navigate to the root directory in your repository and add WhiteNoise to your `requirements.txt`
    
    ```
    pip freeze > requirements.txt
    ```

3. Update `lab2/settings.py` to use WhiteNoise:

        :::python
        MIDDLEWARE = [
            # ...
            'django.middleware.security.SecurityMiddleware',
            'whitenoise.middleware.WhiteNoiseMiddleware',  # MAKE SURE IT'S AFTER THE SECURITY MIDDLEWARE!
            # ...
        ]

        STATIC_URL = '/static/'
        STATIC_ROOT = BASE_DIR / 'staticfiles'

4. Run `collectstatic` to gather all static files:

        :::python
        python manage.py collectstatic

5. Restart the Django server, and now WhiteNoise will handle serving static files, including the JavaScript bundles.

## TASK - Emoji Picker

Add a new file to `webapp` called `emoji-editor.js` and paste the following code in it:
```js
import { Picker } from "emoji-mart"; // Import the emoji picker

const pickerOptions = { onEmojiSelect: (emoji) => {
  const textarea = document.getElementById('markdown-editor');
  textarea.value += emoji.native; // Add selected emoji to the editor
}};
const picker = new Picker(pickerOptions);
document.getElementById('emoji-picker').appendChild(picker); // Add emoji picker to the DOM
```

Navigate to the root directory in your repository and then install `emoji-mart` from NPM.
```bash
npm install --save-dev emoji-mart
```

Update `wiki/templates/editor.html` to the following:
```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>New Wiki Page</title>
</head>
<body>
    <h1>Welcome to the Wiki App!</h1>

    <div id="emoji-picker"></div>
    <br>

    <form id="save-form" method="POST" action="/wiki/save/">
        <input type="text" name="title" id="page-title" placeholder="Page Title"><br>
        <textarea id="markdown-editor" name="content" rows="10" cols="50"></textarea><br>
        <button id="convert-btn">Convert to HTML</button><br>
        <br>
        <button id="save-btn" type="submit">Save Page</button>
        {% csrf_token %}
    </form>

    <h3>Output</h3>
    <div id="markdown-output"></div> <!-- Rendered HTML output -->
    
    <script src="{% static 'markdown-editor.min.js' %}"></script>
    <script src="{% static 'emoji-editor.min.js' %}"></script>
</body>
</html>
```

**Your task** is to transpile `emoji-editor.js` into `wiki/static/emoji-editor.min.js` using `esbuild`. After bundling, Check `localhost:8000/wiki/add/` to confirm that there is a working emoji picker.

## TASK - Adding Likes

Update `wiki/templates/page.html` to the following:
```html
{% load static %}
<!DOCTYPE html>
<html>
    <head>
        <title>{{ title }}</title>
    </head>
    <body> 
        <div>
            <form action="/wiki/page/{{ id }}/like/" method="POST">
                <input type="submit" value="Like Post ({{ like_count }} likes)" />
                {% csrf_token %}
            </form>
        </div>
        <br>
        <div id="content" style="display: none;">{{ content }}</div>
        <div>
            <a href="/wiki/">&lt; Go Back</a>
        </div>
    </body>
    <script src="{% static 'markdown-renderer.min.js' %}"></script> <!-- Load bundled JS -->
</html>
```

**Your task** is to update the backend code to store likes and when each like was created. It should pass a `like_count` to the context dictionary when calling `render` in the `view_page` view. The route and logic to add a like should be accessible when a POST request is sent to `/wiki/page/<id>/like/`. After liking the page, it must redirect to the same wiki page.

If you are not sure what to do, consult the Django Tutorial and the Documentation linked above!

## TASK - Displaying Likes

Add a new file in `wiki/templates/` called `likes.html` and paste in the following:
```html
<!DOCTYPE html>
<html>
    <head>
        <title>{{ title }} | Likes</title>
    </head>
    <body>
        <h1>Likes for {{ title }}</h1>
        <ol>
            {% for like_date in likes %}
                <li>{{ like_date }}</li>
            {% endfor %}
        </ol>
        <br>
        <a href="/wiki/page/{{ id }}/">&lt; Go Back</a>
    </body>
</html>
```

Replace the contents of `wiki/templates/page.html` with
```html
{% load static %}
<!DOCTYPE html>
<html>
    <head>
        <title>{{ title }}</title>
    </head>
    <body> 
        <div>
            <form action="/wiki/page/{{ id }}/like/" method="POST">
                <input type="submit" value="Like Post ({{ like_count }} likes)" /><br><br>
                <a href="/wiki/page/{{ id }}/likes/">View All Likes</a>
                {% csrf_token %}
            </form>
           
        </div>
        <br>
        <div id="content" style="display: none;">{{ content }}</div>
        <div>
            <a href="/wiki/">&lt; Go Back</a>
        </div>
    </body>
    <script src="{% static 'markdown-renderer.min.js' %}"></script> <!-- Load bundled JS -->
</html>
```

**Your task** is to render a list of every single like a specific wiki page received and when they were created. This route should be accessible at `/wiki/page/<id>/likes/`, and it should render the `likes.html` template.

When rendering the `likes.html` template, you will need to pass in to the context argument the following:
```json
{
    "id": 0,        // id of the wiki page
    "title": "",    // title of the wiki page,
    "likes": []     // list of STRING representations of the date of each like that was received for this specific wiki page
}
```

## Making sure your `.gitignore` is good and your repo is clean

Generally there are two kinds of files:

* Files which are rebuilt/generate/download with the standard build/deploy steps should NOT be in the repo and SHOULD be in `.gitignore`.
* Files which are modified by developer or only generated once at project initialization SHOULD be in the repo and should NOT be in `.gitignore`.

The standard build/deploy steps for a C project would be something like `configure`, `make`, `make install`. For Java in CMPUT 301 that would be rebuilding from the IDE. For Django+NPM we have commands like `virtualenv venv`, `pip install -r requirements.txt`, `npm install` (with no arguments), `django-admin migrate` and `manage.py collectstatic`. 

Generally commands like `npm install --save-dev somedependency` modifies the `package.json` and `package.lock` files, so their results need to be committed, and they're only run once (not every time we build/deploy). The same thing applies to `django-admin makemigrations`, which generates the migrations files that are needed to migrate an old database schema. They would not be necessary if you plan on deleting the entire database and its contents (all users, all posts...) every time you update your app, which is not usually the case.

It's not immediately clear whether the static files for the admin site should be committed or not, and it's not immediately clear to me either. However, the test remains the same: will a standard build/deploy command like `collectstatic` recreate them? If so, we should add them to `.gitignore` and remove them from our repository. So, if my `STATIC_ROOT` is set to `staticfiles` then `collectstatic` will create files such as `./staticfiles/admin/img/icon-no.svg`.

And, if I delete it, and run `collectstatic` again, it reappears. So I should ignore `./staticfiles/admin/img/icon-no.svg` and remove it from my repository.

## Requirements

* Must use Python3
* Must run on Ubuntu (Use the undergrad lab machines, for example the ones in CSC 2-29 or install an Ubuntu VM to check this)
* A working Django 5 application
    * using the latest Django version from [PyPI](https://pypi.org/)
        * downloaded with pip into a virtual environment
    * with a wiki homepage that displays all wiki pages created at `/wiki/`
    * with a wiki page creation page at `/wiki/add/`
        * must have a markdown editor and emoji picker
        * must have used `esbuild` to transpile the markdown editor/emoji picker into the code
        * when saving a wiki page, it should make a POST Request to `/wiki/save/` and then redirect to `/wiki/`
    * with individual wiki pages that display their respective content in Markdown at `/wiki/page/<id>/`
        * must have the functionality to add a like to a wiki page when a POST request is sent to `/wiki/page/<id>/like/`
            * must redirect back to the same wiki page that was liked
        * must have a likes list page that shows when each like for that specific page was created at `/wiki/page/<id>/likes/`
    * using Django's ORM system to store wiki pages and likes
    * with correctly setup WhiteNoise middleware
* A git repository that does not contain built (compiled, transpiled, bundled) or downloaded artifacts, including but not limited to:
    * `virtualenv` `venv` or other virtual environments, etc.
    * `.pyc` files, `__pycache__` directories.
    * `node_modules`
    * `*.min.js`
    * `*.min.js.map`
    * Databases like `db.sqlite3` or any other databases.
        * The `db.sqlite3` database *should never leave your computer.* It is for local development only.
* Your git repository SHOULD contain:
    * The code you worked on during the lab.

## Conclusion and Submission

Please confirm you have completed **the four tasks** in this lab and the [requirements](#requirements).

- Creating the backend for storing wiki pages
- Adding an emoji picker to the markdown editor
- Adding support for liking wiki pages
- Adding support for viewing all the likes a wiki page received (and when they were created)

### Submission Instructions

Ensure your repository contains only the necessary files, and your `.gitignore` excludes built files (`venv/`, `node_modules/`, `*.min.js`, `*.min.js.map`, `db.sqlite3`, etc).

Make sure you push to GitHub classroom **BEFORE the deadline!** You will not be able to push after that!

Submit a link to your repo in the form `https://github.com/uofa-cmput404/s25-labsignment-django-yourgithubname/commit/bunch-of-numbers` on eClass. **Do not** submit a link to a branch, a file, or the clone URL.

<p class="warning">If you do not submit a link to your COMMIT on Canvas on time using the correct format above, you will get a zero.</p>

You can submit and then resubmit as many times as you want before the deadline, so submit early and often.

---

### Further Tips
- If you encounter issues with static files, ensure your file paths are correct and that Django’s `STATIC_URL` is configured properly.
- Consult the [Django Documentation](https://docs.djangoproject.com/en/5.1/).
