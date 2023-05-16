# TODO APP WITH FLASK, PYTHON AND SQL-DATABASE
#### Video Demo:  https://youtu.be/ll0jLSjlMIE
#### Description: 

# Final-Project

TODO-App with FLASK, PYTHON AND SQL-DATABASE

# Introduction

The TODO-App is a task managing Web-Application to help its users to stay organized for their day-to-day activities.

The Todo-App can be used for multiple purposes such as shopping lists, taking notes or planning an event.

A user may add to the TODO task list, and view all the tasks they have to complete. If the task is completed, users can just click on the "Done" button next to the task, after clicking it will show as completed. Completed tasks can be deleted by the "Delete" button, however, users can have the option to leave the task alone. But, if the user tries to add a task without writing anything, the screen will come up as an error. Users can go back to fix this issue.

This is the final project for CS50: Introduction to Computer Science course.

# Steps
Install and Setup Flask
Define routes
Use templates
Use a Database 
Build TODO App functionality
Setup¶

# Create project with virtual environment
$ mkdir myproject
$ cd myproject
$ python3 -m venv venv
Activate it
$ . venv/bin/activate

Install Flask and Flask-SQLAlchemy which is used for our database.
$ pip install Flask
$ pip install Flask-SQLAlchemy

# Create the Hello World app¶
Create the app.py file and insert:
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == "__main__":
    app.run(debug=True)

For each url/route we want, we have to create a function and decorate it with @app.route('path/to/your/url'). In this case we only use a forward slash /because this is going to be our start page. Note that we set debug=True so we don't have to reload our server each time we make a change in our code.

# Add the database¶
We must import SQLAlchemy, set some database configuration parameters, create the database, and then create a model class for our todo items. Our model requires three entries: an id, a title, and a flag indicating whether or not the task has been completed. Before running our app, we also invoke db.create_all():

from flask import Flask, request
from flask import render_template
from flask import redirect
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///todo.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
db = SQLAlchemy(app)


class Task(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.Text)
    done = db.Column(db.Boolean, default=False)

    def __init__(self, content):
        self.content = content
        self.done = False

    def __repr__(self):
        return '<Content %s>' % self.content


db.create_all()

# Change the home page to list all the todos¶
Instead of just a string, we import render_template and render a proper html. The template file will be created in the following step. Replace hello_world with the following:

from flask import Flask, render_template

...

@app.route("/")
def home():
    todo_list = Todo.query.all()
    return render_template("base.html", todo_list=todo_list)

# Create the template file¶
In the root folder of our project, create a folder called templates. Flask is looking for this directory, so it has to be this name! Now, in this templates folder, create the base.html file and insert:

<body style="background-color: rgb(105, 104, 120)">
        <div class="label">
                <h1> TODO App...</h1>

                <form action="/task" method="post">
                    <p><input type="text" name="content"></p>
                    <input type="submit" value="Add task">
                </form>

                <ol>
                    {% for task in tasks %}
                        <li>
                            {% if task.done %} <strike> {% endif %}{{ task.content }} {% if task.done %} </strike>{% endif %}
                            <a href="/done/{{ task.id }}">Done</a>
                            <a href="/delete/{{ task.id }}">Delete</a>
                        </li>
                    {% endfor %}
                </ol>
            </div>
    </body

# Add routes to add, delete, and update new todos¶
Add this to your app.py:

@app.route('/')
def tasks_list():
    tasks = Task.query.all()
    return render_template('list.html', tasks=tasks)


@app.route('/task', methods=['POST'])
def add_task():
    content = request.form['content']
    if not content:
        return 'Error'

    task = Task(content)
    db.session.add(task)
    db.session.commit()
    return redirect('/')

@app.route('/delete/<int:task_id>')
def delete_task(task_id):
    task = Task.query.get(task_id)
    if not task:
        return redirect('/')

    db.session.delete(task)
    db.session.commit()
    return redirect('/')


@app.route('/done/<int:task_id>')
def resolve_task(task_id):
    task = Task.query.get(task_id)

    if not task:
        return redirect('/')
    if task.done:
        task.done = False
    else:
        task.done = True

    db.session.commit()
    return redirect('/')


if __name__ == '__main__':
    app.run(debug=True)

# How to run the application
Run the app¶
Run
$ python app.py
Now we can go to http://127.0.0.1:5000/ and inspect our first running app!


# Finally

Thank you to everyone who worked on CS50: Introduction to Computer Science for us. I finished the course because of [Brain Yu's](https://www.edx.org/bio/brian-yu) excellent lecture and his "great question."

Eamon Wong,
May 15th, 2023
