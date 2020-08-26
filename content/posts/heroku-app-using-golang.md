---
title: "Heroku app using Golang"
date: 2018-07-08T03:17:48+09:00
draft: true
---

This article is written as a reminder for myself so that I don’t spend hours setting things up in the future again, and for any others who have no idea how to start a heroku project from an existing webapp written in golang. Remember that I am in no way an expert, and you shouldn't trust blindly what’s written here. These steps worked for me, but there’s no guarantee that it’ll work for you. With that being said, let’s begin!

To deploy an existing (non-heroku) golang webapp to heroku, these steps need to be followed. 
This assumes you have a working webapp and you did all the git init stuff. Also note that heroku assigns the port dynamically from my limited understanding so your webapp needs to look something like this.

```go
//...
port := os.Getenv("PORT")

    if port == ":" {
        log.Fatal("$PORT must be set")
    }

    log.Fatal(http.ListenAndServe(":" + port, somehandler)
```
Notice that you don't hardcode the port variable.

# Steps
1. Create a heroku project

    ```bash
    $heroku create [project name]
    #If you forget to add a name for the heroku project, type:
    $heroku apps:rename [project name]
    ```
2. Manually set the buildpack

    ```bash
    $heroku config:set BUILDPACK_URL=https://github.com/heroku/heroku-buildpack-go.git
    ```

3. Create a Procfile. 

    To initialize a golang project, you need a [procfile](https://devcenter.heroku.com/articles/procfile) containing the following code.

    ```
    $echo “web: [project name]" < Procfile
    ```

    **Important**
    The file needs to be in **utf-8** encoding (unless you want to see some very unhelpful error messages when building your app). From experience, it seems like windows powersehll creates the file in utf-16 encoding (I've spent *way* too long trying to figure this one out). In most editors, you should be able to change the encoding manually. Although, perhaps, just creating the file manually instead of the above command may be more efficient.
4. Integrate a dependency Manager
    
    For a golang project to be deployed to heroku, you need a dependency manager. There are many options available: godep, dep etc. This example will use dep, as I believe it is the simplest. All you need to do to initialize dep is run the following command.
    ```
    $dep init
    ```
    Add the following code snippet at the end of the Gopkg.toml file. The file is automatically created from running dep. My guess is that this process would be different if you’re using a dependency manager other than dep.
    ```toml
    [metadata.heroku]
    root-package = "github.com/user/repository"
    ```
5. Git stuff

    Now do all the git add, commit stuff (I'm assuming you know how to do this) to deploy your app to heroku.
    ```
    $git push heroku master
    ```
    Open the app using the following command `$heroku open` or just type the url (*yourprojectname*.herokuapp.com) on your browser. That’s it!
