# Flask API Documentation

![Untitled design (1)](https://github.com/GeniusApk/Api_With_Flask/assets/101592615/1ce0706f-b980-4524-a640-31046b7e671b)


## Introduction

API stands for Application Programming Interface. In the context of web development, an API is a set of rules and protocols that allows different software applications to communicate with each other. APIs define how requests and responses should be formatted, allowing developers to build modular and interoperable systems.

In this Flask API project, we're using HTTP methods like GET, POST, PUT, and DELETE to interact with the server and perform CRUD (Create, Read, Update, Delete) operations on a SQLite3 database. Each endpoint in the API represents a specific action or resource, and clients can make requests to these endpoints to manipulate data.

APIs are commonly used in web development to enable communication between different parts of a web application, as well as to integrate with third-party services and tools. They provide a standardized way for applications to exchange data and functionality, making it easier to build complex systems and integrate with external services.
[Learn more ](https://github.com/GeniusApk/Api_With_Flask/blob/main/More_About_Api.md)


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


# How to Create Flask API with SQLite3
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
   - Define a route for adding a new user via a POST request.
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
   - Define a route for fetching and displaying all users via a GET request.
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
   - Define a route for updating user information via a PUT request.
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
   - Define a route for deleting a user via a DELETE request.
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
7. **Run the Flask App**:
   - Start the Flask application.
```python

  # Start the Flask application.
if __name__ == "__main__":
    # Call the createTables function to ensure the User table exists in the database.
    createTables()
    # Run the Flask application in debug mode.
    app.run(debug=True)

```

## Recommended VS Code Extensions

To enhance your development experience with this Flask API project in Visual Studio Code, we recommend the following extensions:

- **Thunder Client**: A lightweight REST client extension for VS Code, Thunder Client allows you to easily send HTTP requests and test your API endpoints directly within the editor.

- **SQLite Viewer**: This extension provides a convenient way to view and interact with SQLite databases directly from within VS Code. You can explore tables, run SQL queries, and manage your database schema.

- **Code Runner**: Code Runner allows you to run your Python scripts directly within VS Code with a single click. It supports various programming languages and provides a quick and easy way to execute code snippets or entire files.

These extensions can streamline your development workflow and make it easier to build and test your Flask API project directly within Visual Studio Code.





## Contributing

Contributions are welcome! Here's how you can contribute to the project:

1. Fork the repository.
2. Create a new branch (`git checkout -b feature`).
3. Make changes and commit them (`git commit -am 'Add new feature'`).
4. Push to the branch (`git push origin feature`).
5. Create a new Pull Request.


## Developer

- **Name:** MOHD AAKIB
- **GitHub:** https://github.com/GeniusApk


## 🔗 Links

[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mohd-aakib-0546ab272/)

[![linkedin](https://img.shields.io/badge/instagram-bc2a8d?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/_aakib__21/)

