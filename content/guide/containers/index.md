---
title: "Containers"
date: 2022-04-21T12:25:57+02:00
draft: false
weight: 105
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


if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

We'll not care too much about the Python contents, just know that running
this Python script will start a webserver on port `5000` which will answer
`"Hello World!"` on the root path.

So, now we'll have to write what's called a `Dockerfile`[^2] which describes 
how to build one of these containers.

Create the following file and call it "Dockerfile"
```Dockerfile
FROM python:3.8

WORKDIR /app

COPY app.py /app/

RUN pip install flask

EXPOSE 5000

ENTRYPOINT ["python"]

CMD ["app.py"]
```

[^1]: If you want a quick introduction to what containers actually are, 
have a look a look at 
[this tech talk by Liz Rice](https://www.youtube.com/watch?v=8fi7uSYlOdc) 
[^2]: https://docs.docker.com/engine/reference/builder/
