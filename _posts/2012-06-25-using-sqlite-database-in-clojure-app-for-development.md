---
layout: post
title: "Using SQLite database in clojure app for development"
description: ""
category:[clojure, database] 
tags: [how-to, clojure]
---
{% include JB/setup %}

SQLite is light weight embedded relational database that you can easily
use in your clojure/compojure projects during development. Once you have
tested your application in local enviorment you can choose to move to any 
other SQL compliant databases (ie MySQL, PostgreSQL)

Assuming that you have generated your project with Lein (which is
awesome build tool for clojure developers) there would be
project.clj file in root directory of your app. Add reference to SQLite
and JDBC driver in dependencies. Your project file would look something
like this with SQLite drivers added:

```clojure
(defproject myapp "0.0.1"
  :description "my app"
  :dependencies [[org.clojure/clojure "1.3.0"]
                 [org.clojure/java.clojurejdbc "0.0.6"]
                 [org.xerial/sqlite-jdbc "3.6.13"] 
                 [ring/ring-jetty-adapter "1.0.1"]
                 [compojure "0.6.4"]
                 [hiccup "0.3.6"]])
```

After this you would want to create a migration so that you can create
database file and a tables in database. SQLite databases are stored on
your file system. Here are setting you would use for SQLite in your
migration.clj file:

```clojure
(def db {
     :classname "org.sqlite.JDBC"
     :subprotocol "sqlite"
     :subname "db/database.db"})
```
Your migration file should look something like this:

```clojure
(ns shouter.models.migration
    (:require [clojure.java.jdbc :as sql]))

(def db {
      :classname "org.sqlite.JDBC"
      :subprotocol "sqlite"
      :subname "db/database.db"})

(defn create-posts [] 
      (try (sql/with-connection db
         (sql/create-table :posts
          [:id :serial "PRIMARY KEY"]
          [:title :varchar "NOT NULL"]
          [:body :varchar "NOT NULL"]
          [:created_at :timestamp "NOT NULL" "DEFAULT CURRENT_TIMESTAMP"]))
      (catch Exception e (println e)))
)

(defn -main []
      (print "Migrating database ... ") (flush)
      (create-posts)
      (println "Done ...")
)

```
Thats it! 
