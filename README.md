# Recipe Web App

This project is a web app that manages and interacts with a database that contains informatino about recipes, users, and ingredients. We used Flask and SQLAlchemy.

In order to run the app, you need to run the following commands in the terminal:

```pip install virtualenv```

```python -m venv venv```

MAC: ```source venv/bin/activate```, WINDOWS: ```.\venv\Scripts\activate```


```pip install flask```

```pip install flask-sqlalchemy```

```pip install sqlalchemy_views```

```python app.py```

Once you open the web app, just create a new user to make it possible to log in.


### ORM

Since we used an ORM tool, we will now briefly show that we know how our webapp interacts with our database with a few examples:

**Select:** To display all recipes, we use the following code to perform a select query to retrive all recipes from the database

```python
recipes = Recipe.query.all()
```

**Insert:** To add a new user to the database, we use the following code to perform an insert:

```python
def add_user():
    if request.method == 'POST':
        username = request.form['username']
        name = request.form['name']
        password = request.form['password']
        new_user = User(username=username, name=name, password=password)
        db.session.add(new_user) 
        db.session.commit()
        return redirect(url_for('login'))
    return render_template('add_user.html')
```

**Update:** We use the following code to either add a new rating or update a user's existing rating in the database:

```python
@app.route('/user/<string:user_id>/rate_recipe/<int:recipe_id>', methods=['POST'])
    def rate_recipe(user_id, recipe_id):
        rating_value = int(request.form['rating'])
        existing_rating = Rating.query.filter_by(user_id=user_id, recipe_id=recipe_id).first()
        
        if existing_rating:
            existing_rating.rating = rating_value  # Update query to update an existing rating
        else:
            new_rating = Rating(user_id=user_id, recipe_id=recipe_id, rating=rating_value)
            db.session.add(new_rating)  # Insert query to add a new rating if it doesn't exist
        
        db.session.commit()
        return redirect(url_for('recipe', recipe_id=recipe_id, user_id=user_id))
```

**Delete:** Although it is not intended for the user to use, we created this method to enable us to wipe the database (useful when we were testing) :

```python
def wipe_db():
    db.drop_all()  # Delete query to drop all tables in the database
    db.create_all()
    return "Database wiped and recreated!"
```