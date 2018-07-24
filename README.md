# data_centric_dev_mini_project
Python, MongoDb, mLab, Flask, Materialize

*Developer: sarahloh*

App available at: https://mlab.com/databases/sl_task_manager

[1. Project Brief](#1-project-brief)

[2. Learning Outcomes](#2-learning-outcomes)

[3. Technologies](#3-technologies-and-dependencies)

[4. Workflow](#4-workflow)


### 1 Project Brief
*Create a Task Manager application*

***Navbar***
- Home
- New task
- Manage categories

***Home page***
- List of tasks (each with a task name, due date, description)
- Title: Tasks
- Buttons: edit, mark complete, add new task

***Add Task page***
- Inputs: category, name, description, due date (calendar), is urgent
- Submit: add task

***Edit Task page***
- Title: Edit Task
- Inputs: category, name, description, due date (calendar), is urgent
- Submit: update task

***Categories page***
- Title: Categories
- List of categories (each with category name)
- Buttons: delete, edit, new category

***Edit Category page***
- Title: Edit Category
- Inputs: name
- Buttons: update category

***New Category page***
- Title: New Category
- Inputs: name
- Buttons: add category


### 2 Learning Outcomes

- Understand how to make CRUD calls to MongoDB via a web framework (i.e. Flask)
- Understand the benefits of creating HTML based user interfaces to demonstrate CRUD calls
- Understand the value of good UX by creating an application using a visual framework (i.e. Materialize)


### 3 Technologies and Dependencies

- [HTML5](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5)
- [CSS3](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS3)
- [Materialize](https://materializecss.com)
- [Python3](https://www.python.org)
- [pip3](https://pip.pypa.io/en/stable/)
- [Flask](http://flask.pocoo.org)
- [flask-pymongo](http://flask-pymongo.readthedocs.io/en/latest/)
- [MongoDB](https://www.mongodb.com)
- [mLab](https://mlab.com)
- [Heroku](http://heroku.com)


### 4 Workflow

**Create new database on mlab.com**

- db name: sl_task_manager
- collections: categories, tasks

- Add a document to categories
    ```json
    {
        "category_name": "Home"
    }
    ```

- Add a document to tasks
    ```json
    {
        "category_name": "Home",
        "task_name": "Buy milk",
        "task_decription": "Get 2 ltrs of milk",
        "is_urgent": "false",
        "due_date": "28 August, 2018"
    }
    ```


**Create a Flask application**

- Install Flask
    ```bash
    sudo pip3 install flask
    ```

- Create app.py
    ```python
    import os
    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def hello():
        return 'Hello World ... again'

    if __name__ == '__main__':
        app.run(host=os.getenv('IP'),
                port=os.getenv('PORT'),
                debug=True)
    ```

**Deploy to Heroku**

- Create new app on heroku.com (app name: sl-task-manager)

- Use git and Heroku CLI to setup and push to heroku remote

    ```bash
    heroku login
    heroku apps
    heroku git:remote -a sl-task-manager
    pip3 freeze > requirements.txt
    echo web: python app.py > Procfile
    # push to heroku
    git add .
    git commit -m 'Add requirements.txt and Procfile'
    git push heroku master
    # start the app
    heroku ps:scale web=1
    ```

- Set environment variables on heroku.com

- App running on https://sl-task-manager.herokuapp.com/


**Connect Flask to MongoDB**

Connect the database and flask application by using a database URL. To help Flask talk to Mongo, install a library called flask-pymongo.

- Create a user for the database on mlab.com

- Install flask-pymongo and update requirements.txt

    ```bash
    pip3 install flask-pymongo
    pip3 freeze > requirements.txt
    ```

- Create environment variable to store MongoDB URI (locally for development and on heroku.com)

    ```bash
    export MONGO_URI=mongodb://<dbuser>:<dbpassword>@ds247121.mlab.com:47121/sl_task_manager
    ```

- Connect to the database from app.py and create a get_tasks view

    ```python
    import os
    from flask import Flask , render_template, redirect, request, url_for
    from flask_pymongo import PyMongo

    app = Flask(__name__)

    app.config["MONGO_DBNAME"] = 'sl_task_manager'
    app.config["MONGO_URI"] = os.getenv('MONGO_URI')

    mongo = PyMongo(app)

    @app.route('/')
    @app.route('/get_tasks')
    def get_tasks():
        """
        Display the tasks colection on tasks.html
        """
        return render_template("tasks.html", tasks=mongo.db.tasks.find())
    ```

- Create a templates directory with tasks.html using Jinja to loop through the collection

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Task Manager</title>
    </head>
    <body>
        {% for task in tasks %}
        {{task.task_name}}
        {{task.category_name}}
        {{task.task_description}}
        {{task.due_date}}
        {{task.is_urgent}}
        {% endfor %}
    </body>
    </html>
    ```