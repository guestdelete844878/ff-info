from flask import Flask, render_template, request, redirect, url_for, session
import os

app = Flask(__name__)
app.secret_key = os.urandom(24) # A strong secret key for session management

# In a real application, you'd fetch this from a database
# For demonstration purposes, we'll use a simple dictionary
USERS = {
    "admin": "password123",
    "user1": "pass456"
}

@app.route('/')
def index():
    if 'username' in session:
        return redirect(url_for('success'))
    return redirect(url_for('login'))

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']

        if username in USERS and USERS[username] == password:
            session['username'] = username
            return redirect(url_for('success'))
        else:
            return render_template('login.html', error="Invalid credentials. Please try again.")
    return render_template('login.html')

@app.route('/success')
def success():
    if 'username' in session:
        return render_template('success.html', username=session['username'])
    return redirect(url_for('login'))

@app.route('/logout')
def logout():
    session.pop('username', None)
    return redirect(url_for('login'))

if __name__ == '__main__':
    app.run(debug=True) # debug=True allows automatic reloading and provides a debugger
