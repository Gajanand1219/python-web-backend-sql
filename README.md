# python-web-backend-sql
“A Python backend using Flask/FastAPI with SQLAlchemy to interact with an SQL database. "




# Python Web Backend with SQL Database

This guide explains how to set up a Python backend for a website that connects to an SQL database. We will use Flask or FastAPI for the backend and SQLAlchemy for database interaction.

## Requirements
- Python 3.x
- Flask or FastAPI
- SQLAlchemy (for database interaction)
- SQLite (or MySQL/PostgreSQL)

## Steps

## 1. Set up a Python Web Framework


### Create a virtual environment and activate it:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows, use .env\Scriptsctivate
   ```



#### Flask (Lightweight and Beginner-Friendly)
Install Flask:
```bash
pip install flask
```

#### FastAPI (High-Performance API)
Install FastAPI and Uvicorn:
```bash
pip install fastapi uvicorn
```

### 2. Install Database Libraries
You need libraries to interact with your SQL database. SQLAlchemy is the most common ORM for interacting with relational databases.

#### Install SQLAlchemy:
```bash
pip install sqlalchemy
```

#### Database Connectors
- For **SQLite** (no installation required, comes with Python)
- For **MySQL**: 
  ```bash
  pip install mysql-connector-python
  ```
- For **PostgreSQL**:
  ```bash
  pip install psycopg2
  ```

### 3. Set Up a Database Connection

#### Example using Flask & SQLAlchemy with SQLite
Create a Python file (`app.py`):
```python
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# Database configuration (SQLite in this case)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///mydatabase.db'
db = SQLAlchemy(app)

# Define a simple model
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100))

# Route to get all users
@app.route('/users', methods=['GET'])
def get_users():
    users = User.query.all()
    return jsonify([user.name for user in users])

# Route to add a new user
@app.route('/users', methods=['POST'])
def add_user():
    name = request.json.get('name')
    new_user = User(name=name)
    db.session.add(new_user)
    db.session.commit()
    return jsonify({"message": "User added!"}), 201

if __name__ == '__main__':
    db.create_all()  # Create tables if they don't exist
    app.run(debug=True)
```

#### Example using FastAPI & SQLAlchemy with SQLite
Create a Python file (`app.py`):
```python
from fastapi import FastAPI
from pydantic import BaseModel
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

app = FastAPI()

# Database configuration
SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

# Define a simple model
class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)

# Create the database tables
Base.metadata.create_all(bind=engine)

# Define a Pydantic model for input validation
class UserCreate(BaseModel):
    name: str

# Route to get all users
@app.get("/users")
def get_users():
    db = SessionLocal()
    users = db.query(User).all()
    return users

# Route to add a new user
@app.post("/users")
def add_user(user: UserCreate):
    db = SessionLocal()
    db_user = User(name=user.name)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user
```

### 4. Run the Server
For **Flask**, run the server:
```bash
python app.py
```

For **FastAPI**, run the server:
```bash
uvicorn app:app --reload
```

### 5. Test API Endpoints
- For **Flask**, visit `http://127.0.0.1:5000/users`.
- For **FastAPI**, visit `http://127.0.0.1:8000/users`.
Use a tool like Postman to test your API.

### 6. Add More Routes and Logic
You can extend this backend to include more routes, implement full CRUD operations (Create, Read, Update, Delete), and handle more complex database interactions.

## Conclusion
This guide demonstrated how to set up a basic backend using Python with Flask or FastAPI and connect it to an SQL database using SQLAlchemy. You can extend this setup to create a fully functional web application.

