---
title: "Containers"
date: 2022-04-21T12:25:57+02:00
draft: false
weight: 7
---

A container is an "isolated process", it's easiest to just think of it 
as a VM.[^1] The benefits of containers is that you have a self-contained 
run-time environment that describes how to run your application.

For example, let's say I had written a Java server application, and I hand 
it off to you and say "run it". What are you supposed to do with it? First,
you need to figure out which version of Java it needs, then you need to know 
if it requires a configuration file, is it supposed to run inside TomCat or 
as a stand-alone server?

It can be even more challenging to be told "this is our production server, 
figure out how it works". Then you are stuck with nothing and have to rely
on advanced bash or batch scripting and diagnose the machine from scratch.

Containers get around this by providing a structured and unified way of 
describing "how to run my application".

Docker is not the only solution for this, but they were the pioneers of 
containerization, and it has quickly become the golden standard of how 
we operate our applications across the industry.

## Your first Docker application
To keep it simple we will stick to a very simple Python application. You 
don't have to install Python for this example.

First create a folder somewhere for this project. Then put the following 
file in that folder calling it `app.py`:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():
    return "Hello World!"


def terminate(signal, frame):
    sys.exit(0)


if __name__ == "__main__":
    # Workaround for Flask not always respecting sigterm
    signal.signal(signal.SIGTERM, terminate)
    app.run(host='0.0.0.0')
```

We'll not care too much about the Python contents, just know that running
this Python script will start a webserver on port `5000` which will answer
`"Hello World!"` on the root path.

So, now we'll have to write what's called a `Dockerfile`[^2] which describes 
how to build one of these containers.

Create the following file and call it "Dockerfile"
```Dockerfile
# This first line lets you build on top of an existing Docker image
# You can build on top of any image you'd like, the image you create 
# here can be used to be built on top of as well
FROM python:3.8

# The second line here sets the "working directory" inside the Docker 
# image.
WORKDIR /app

# You can copy specific files or even whole directories into the Docker
# image. If you want to copy all of the current directory into the 
# Docker image, you can write:
# COPY . /app/
# You can also just copy the files you need:
COPY app.py /app/

# With the RUN keyword you can run commands inside the image
# This is typically just regular bash commands 
RUN pip install flask

# The EXPOSE doesn't actually do anything, it just adds information 
# about which port this Docker image uses
EXPOSE 5000

# Set the entrypoint in the Docker image, in Docker containers the 
# command that is run split into "entrypoint" and "command" 
# Where "entrypoint" is the program to run and "command" is the 
# arguments for the program. 
ENTRYPOINT ["python"]

# This will ask Python to run the app.py file
CMD ["app.py"]
```

Now you can build this file using:
```bash
docker build -t pythonapp -f Dockerfile .
```

This will build the image called "pythonapp" using the 
instructions in `Dockerfile` and executing those instructions inside 
the current directory (`.`).

Now that the image has been built you can run:
```bash
docker run --rm -p 5000:5000 pythonapp
```

This will run the image "pythonapp" and directing the port `5000` on 
your machine to the port `5000` inside the container. Try going to 
[localhost:5000](http://localhost:5000) and see if you can access the 
Python application.

You could also run:
```bash
docker run --rm -p 5005:5000 pythonapp
```

And now you should be able to find the Python application on 
[localhost:5005](http://localhost:5005) instead.

[^1]: If you want a quick introduction to what containers actually are, 
have a look a look at 
[this tech talk by Liz Rice](https://www.youtube.com/watch?v=8fi7uSYlOdc) 
[^2]: https://docs.docker.com/engine/reference/builder/
