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
    <head>
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

**Adding a Task**

- Setup template inheritance by creating base.html and using extends in other html pages

- Import materialize css and icons in the ```<head>``` tag in base.html

    ```html
    <!--Import Google Icon Font-->
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <!-- Compiled and minified CSS -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0-rc.2/css/materialize.min.css">
    ```

- Add the block content placeholders and import materialize javascript and jQuery before the closing ```</body>``` tag in base.html

    ```html
    <body>

        {% block content %}
        {% endblock %}

        <!-- jQuery -->
        <script src=https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js></script>
        <!-- Compiled and minified JavaScript -->
        <script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0-rc.2/js/materialize.min.js"></script>

    </body>
    ```

- Create addtask.html with a form to post new task data

- Add functionality to submit form data:

    - In addtask.html
    ```html
    <form action="{{url_for('insert_task')}}" method="POST" class="col s12">
        <!-- form inputs -->
        ...
        <button type="submit">Add Task</button>
    </form>
    ```

    - In app.py
    ```python
    @app.route('/insert_task', methods=['POST'])
    def insert_task():
        tasks = mongo.db.tasks
        tasks.insert_one(request.form.to_dict())
        return redirect(url_for('get_tasks'))
    ```

**Editing a Task**

- Add static directory and style.css

    ```bash
    mkdir static
    mkdir static/css
    touch static/css/style.css
    ```

    - In base.html

    ```html
    <head>
        ...
        <link rel="stylesheet" href="{{url_for('static', filename='css/style.css')}}">
    </head>
    ```

- Create edit and done buttons on for each task on tasks.html

- Create edit_task route and template and connect edit button to function in app.py

    - In app.py

        ```python
        @app.route('/edit_task/<task_id>')
        def edit_task(task_id):
            the_task = mongo.db.tasks.find_one({"_id": ObjectId(task_id)})
            all_categories = mongo.db.categories.find()
            return render_template('edittask.html', task=the_task, categories=all_categories)
        ```

    - In tasks.html

        ```html
        <a href="{{url_for('edit_task', task_id=task._id)}}" class="waves-effect waves-light btn btn-small blue">Edit</a>
        ```
- Bind data to edit task form

- Update task in the database on submit edit task form

    - In app.py

        ```python
        @app.route('/update_task/<task_id>', methods=['POST'])
        def update_task(task_id):
            tasks = mongo.db.tasks
            tasks.update( { '_id': ObjectId(task_id) },
            {
                'task_name': request.form.get('task_name'),
                'category_name': request.form.get('category_name'),
                'task_description': request.form.get('task_description'),
                'due_date': request.form.get('due_date'),
                'is_urgent': request.form.get('is_urgent')
            })
            return redirect(url_for('get_tasks'))
        ```
    - In edittask.html

        ```html
        <form action="{{url_for('update_task', task_id=task._id)}}" method="POST" class="col s12">
        ```

**Deleting a Task**

- Connect the done button to the delete_task function

    ```python
    @app.route('/delete_task/<task_id>')
    def delete_task(task_id):
        mongo.db.tasks.remove({ '_id': ObjectId(task_id) })
        return redirect(url_for('get_tasks'))
    ```

    ```html
    <a href="{{url_for('delete_task', task_id=task._id)}}" class="waves-effect waves-light btn btn-small">Done</a>
    ```
