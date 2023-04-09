---
title: "Creating a User Interface in Webapp"
date: 2022-09-30T17:22:24+05:30
draft: flase
cover: 
    image: download.png 
tags: ["Web Development","golang","PostgreSQL"]
categories: ["Coding"]
---
**[Click here for Full Code](https://github.com/ShubhamSupekar/User_Interface)**
***
In this Blog, We are creating a simple Web application which can do login, logout, signup like basic operations.
So let's get started.
***
## Creating Database:
First start the PostgreSQL. 
 I'm using fedora so if you are using different linux then some command might be different.

To start psql run the command 
```
sudo -i -u postgres
```
Then connect to the database or create a new one. Here I'm connecting to my existing database `app`
```
psql -d app
```
to create database
```
CREATE DATABASE app;
```

We can create table using command
```
CREATE TABLE users (
    username TEXT PRIMARY KEY   NOT NULL,
    fname TEXT NOT NULL,
    lname TEXT NOT NULL,
    pass TEXT NOT NULL
);
```
After this create a user 
```
CREATE USER shubham WITH PASSWORD '3232';
```
and give `user shubham` permission of `app` database.
```
GRANT ALL PRIVILEGES ON DATABASE app to james;
```
Create another table to store sessions
```
CREATE TABLE session(
    uid TEXT NOT NULL,
    uname TEXT references users(username)
)
```
***
## Creating Signup page: 
first create a directory name  `webapp` 
inside `webapp` directory create `main.go` file
and a `templates` directory 
```
mkdir templates
touch main.go templates/signup.html
```
final structure of our project is like
```
webapp
    main.go
    - templates
        signup.html
```
create a file name `signup.html` in `templates` directory
and open `main.go` file in editor and write following code
```golang
package main

import (
	"database/sql"
	"net/http"
	"text/template"
	_ "github.com/lib/pq"
)
var db *sql.DB
var tpl *template.Template

type User struct {
	Username  string
	Firstname string
	Lastname  string
	Password  string
}

func init() {
	var err error
	db, err = sql.Open("postgres", "postgres://shubham:3232@localhost/app?sslmode=disable")
	if err != nil {
		panic(err)
	}

	if err = db.Ping(); err != nil {
		panic(err)
	}
	tpl = template.Must(template.ParseGlob("templates/*.html"))
}
```
Here we initialize some variables like db is pointer to DB struct from sql package and tpl is pointer to Template struct from template package. 

After accepting values from form we will pass them into `User struct`.
In `func init()` `sql.Open` opens a database specified by its database driver name and a
driver-specific data source name, usually consisting of at least a
database name and connection information. We are passing our user `shubham` and database name `app`.

`db.Ping` verifies a connection to the database is still alive and
establishing a connection if necessary.

`ParseGlob` adding all the templates present in the `templates` directory having `.html` extension.   

Now we add some routes.
```
func main() {
	http.HandleFunc("/", index)
	http.HandleFunc("/signup", signup)
	http.HandleFunc("/signupAuth", signupAuth)
	http.ListenAndServe(":8080", nil)
}
```
For now we are using default multiplexer and 
we want our website at port:80. hence we pass `8080` for port and `nil` for default mux in `http.ListenAndServe`