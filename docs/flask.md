---
layout: default
title: Flask
date:   2021-09-11 20:28:05 -0400
categories: flask
nav_order: 101

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# [How to get the current user in Flask API backend using flask_jwt_extended and the frontend is React](https://flask-jwt-extended.readthedocs.io/en/stable/basic_usage.html)

You need to use `cookies` to store and transfer the json token.

On the backend side:

```python
def create_app():
    app = Flask(__name__)
    app.config["JWT_TOKEN_LOCATION"] = ["cookies"]


    app.config["JWT_SECRET_KEY"] = "super-secret" 
    jwt = JWTManager(app)
```

The code snippet `app.config["JWT_TOKEN_LOCATION"] = ["cookies"]` configures the Flask-JWT-Extended extension to look for JSON Web Tokens (JWTs) exclusively within HTTP cookies instead of the default Authorization header. The extension will now automatically extract JWTs from incoming request cookies when an endpoint decorated with `@jwt_required()` or`verify_jwt_in_request()` is accessed.

In web development, a cookie is a small piece of data that a server sends to a user's web browser, which the browser then stores and sends back to the same server with subsequent requests

So that you do not need to add the headers everytime to make request to use jwt: `Authorization: Bearer <access_token>`


Just as the link of official web said. when you create the login function create the access_token as shown below:
```python
from flask_jwt_extended import create_access_token
from flask_jwt_extended import get_jwt_identity, verify_jwt_in_request, set_access_cookies


# Create a route to authenticate your users and return JWTs. The
# create_access_token() function is used to actually generate the JWT.
@app.route("/login", methods=["POST"])
def login():
    username = request.json.get("username", None)
    password = request.json.get("password", None)
    if username != "test" or password != "test":
        return jsonify({"msg": "Bad username or password"}), 401

    # here the identiy is the info that you can get when using get_jwt_identity()
    access_token = create_access_token(identity=username)
    response = jsonify({'data':'hello'})
    set_access_cookies(response, access_token) # save the access_token to cookies
    return response
```

When you need to get the current user info, you can add `@jwt_required()` before the route. Or you can use `verify_jwt_in_request()` before the `currentUser = get_jwt_identity()`. And then you will get the currentUser. And it's the username that you specified in `create_access_token`.

# [How to use Flask to create a large website](https://www.digitalocean.com/community/tutorials/how-to-structure-a-large-flask-application-with-flask-blueprints-and-flask-sqlalchemy)

1. Use `Blueprint` to contain different modules
2. use `pathlib.Path(__file__).resolve().parent.parent` to redirect the package path


