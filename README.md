# data_centric_dev_mini_project
Python, MongoDb, mLab, Flask, Materialize

*Developer: sarahloh*

[1. Project Brief](#1-project-brief)

[2. Learning Outcomes](#2-learning-outcomes)

[3. Technologies](#3-technologies-and-dependencies)

[4. Workflow](#4-workflow)

[5. Deployment](#5-deployment)

[6. Testing](#6-testing)

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
- [Flask](http://flask.pocoo.org)
- [MongoDB](https://www.mongodb.com)
- [mLab](https://mlab.com)


### 4 Workflow

**Create new database on mLab**

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


### 5 Deployment


### 6 Testing
