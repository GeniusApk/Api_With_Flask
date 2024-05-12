# Flask API Documentation

## Introduction
This Flask API project provides endpoints for managing users and products in a medical shop database. It allows users to create new accounts, retrieve user details, update user information, add new products, and modify product details.

## Setup
To run this project locally, follow these steps:

1. Clone the repository to your local machine.
2. Install Python and Flask if you haven't already.
3. Navigate to the project directory in your terminal.
4. Run the following command to install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
5. Run the following command to create the necessary database tables:
   ```bash
   python app.py
   ```

## Usage
To use the API endpoints, send HTTP requests to the specified endpoints using tools like Postman or cURL. Below are the details of each endpoint along with their descriptions, methods, parameters, and examples.


## How to Create Flask API with SQLite3
1. **Set Up Flask App**:
   - Import Flask and create an instance of the Flask app.
   - Define your API routes using `@app.route()` decorators.
     Example:
```python
from flask import Flask, request, jsonify
import sqlite3

app = Flask(__name__)

@app.route('/', methods=['GET'])
def main():
    return jsonify("Hello World")


if __name__ == "__main__":
    app.run(debug=True)
```

2. **Set Up SQLite Database**:
   - Import SQLite3 and establish a connection to your SQLite database.
   - Define functions to interact with the database (e.g., insert, update, select).
  ```python

def createTables():  # Define a function to create a user table if it doesn't already exist in the database.

    conn = sqlite3.connect("my_user.db")      # Connect to the SQLite database.
    cursor = conn.cursor()    # Create a cursor object to execute SQL queries.

    # Execute a SQL query to create the User table with specified columns if it doesn't exist.
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS User (
            Id INTEGER PRIMARY KEY AUTOINCREMENT,
            Name VARCHAR(255),
            Password VARCHAR(255),
            Address VARCHAR(255),
            PinCode VARCHAR(255),
            Email VARCHAR(255),
            Phone VARCHAR(255)
        )
    ''')
    # Commit the changes to the database.
    conn.commit()
    # Close the database connection.
    conn.close()
```
3. **Add Data Using Api**:
   - You can add user by using this
```python
    @app.route('/addUser', methods=['POST'])
def add_user():
    try:
        # Extract form data from the request.
        name = request.form['Name']
        password = request.form['Password']
        address = request.form['Address']
        pincode = request.form['PinCode']
        email = request.form['Email']
        phone = request.form['Phone']

        # Connect to the SQLite database.
        conn = sqlite3.connect("my_user.db")
        # Create a cursor object to execute SQL queries.
        cursor = conn.cursor()
        # Execute an SQL query to insert user data into the User table.
        cursor.execute("INSERT INTO User (Name, Password, Address, PinCode, Email, Phone) VALUES (?, ?, ?, ?, ?, ?)",
                       (name, password, address, pincode, email, phone))
        # Commit the changes to the database.
        conn.commit()
        # Close the database connection.
        conn.close()

        # Return a JSON response indicating successful user addition.
        return jsonify({"message": "User added successfully"}), 201
    except Exception as e:
        # Return a JSON response indicating any errors that occurred during user addition.
        return jsonify({"error": str(e)}), 400

```


4. **Read Data From DetaBase Using Api**:
   - If you want to add data in your database then you have to follow this steps
```python



# Define a route for fetching and displaying all users via a GET request.
@app.route('/showUsers', methods=['GET'])
def show_users():
    try:
        # Connect to the SQLite database.
        conn = sqlite3.connect("my_user.db")
        # Create a cursor object to execute SQL queries.
        cursor = conn.cursor()
        # Execute an SQL query to retrieve all users from the User table.
        cursor.execute("SELECT * FROM User")
        # Fetch all user records from the cursor.
        users = cursor.fetchall()
        # Close the database connection.
        conn.close()

        # Initialize an empty list to store user data dictionaries.
        user_list = []
        # Iterate through each user record fetched from the database.
        for user in users:
            # Create a dictionary representing user data with appropriate keys and values.
            user_dict = {
                "Id": user[0],
                "Name": user[1],
                "Password": user[2],
                "Address": user[3],
                "PinCode": user[4],
                "Email": user[5],
                "Phone": user[6]
            }
            # Append the user dictionary to the user list.
            user_list.append(user_dict)

        # Return a JSON response containing the list of user data.
        return jsonify(user_list), 200
    except Exception as e:
        # Return a JSON response indicating any errors that occurred during user retrieval.
        return jsonify({"error": str(e)}), 400

```
     
5. **Update data in Detabase Using Api**:
   - If you want to Update data in your database then you have to follow this steps 
```python



# Define a route for updating user information via a PUT request.
@app.route('/updateUser/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    try:
        # Connect to the SQLite database.
        conn = sqlite3.connect("my_user.db")
        # Create a cursor object to execute SQL queries.
        cursor = conn.cursor()

        # Prepare the SET part of the SQL query dynamically based on form data.
        set_values = []
        # Iterate through form data items.
        for key, value in request.form.items():
            # Check if the key is a valid field name in the User table.
            if key in ['Name', 'Password', 'Address', 'PinCode', 'Email', 'Phone']:
                # Append a string representing the key-value pair to the set_values list.
                set_values.append(f"{key}='{value}'")
        # Join the set_values list elements with commas to create the SET clause of the SQL query.
        set_clause = ", ".join(set_values)

        # Execute the SQL query to update user information.
        cursor.execute(f"UPDATE User SET {set_clause} WHERE Id=?", (user_id,))
        # Commit the changes to the database.
        conn.commit()
        # Close the database connection.
        conn.close()

        # Return a JSON response indicating successful user update.
        return jsonify({"message": "User updated successfully"}), 200
    except Exception as e:
        # Return a JSON response indicating any errors that occurred during user update.
        return jsonify({"error": str(e)}), 400



```

6. **Delete Data From DetaBase Using Api**:
   - If you want to add data in your database then you have to follow this steps
  ```python

# Define a route for deleting a user via a DELETE request.
@app.route('/deleteUser/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    try:
        # Connect to the SQLite database.
        conn = sqlite3.connect("my_user.db")
        # Create a cursor object to execute SQL queries.
        cursor = conn.cursor()
        # Execute an SQL query to delete a user with the specified user_id.
        cursor.execute("DELETE FROM User WHERE Id=?", (user_id,))
        # Commit the changes to the database.
        conn.commit()
        # Close the database connection.
        conn.close()

        # Return a JSON response indicating successful user deletion.
        return jsonify({"message": "User deleted successfully"}), 200
    except Exception as e:
        # Return a JSON response indicating any errors that occurred during user deletion.
        return jsonify({"error": str(e)}), 400

```
7. **Run This Py File**:
   - This will run your peoject
```python

  # Start the Flask application.
if __name__ == "__main__":
    # Call the createTables function to ensure the User table exists in the database.
    createTables()
    # Run the Flask application in debug mode.
    app.run(debug=True)

```
