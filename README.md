# JSdecoratorHack
A small python hack allowing you to use JS decorators through Flask

_**Note**_: I'm making this into a repo and not a gist because at some point I'll refacto this code into something actually usable and covered by tests

```python
from flask import Flask, Response

app = Flask(__name__)

@app.route('/static/<path:path>')
def send_static(path):
    if path.endswith('.js'):
        #Note 1: if you use the code as it is, I'm pretty sure you get an arbitrary file read vuln
	#Note 2: Nesting is bad, I'm posting this like that for the sake of simplicity but 
	#        you should really google "guard clauses" if you're not familiar with the concept)
	with open(f'front/{path}', 'r') as f:
	    data = f.read()
	data = re.sub(
	            r'^@(?P<dec>.*)\n(?P<func>(?:export)[ ]*(?:async)?[ ]*function (?P<funcname>[^(]*).*$(?:.*\n)*?(?:^}$))',
                    'var \g<funcname> = \g<func>\n\g<funcname>=\g<dec>(\g<funcname>);\nexport var \g<funcname>;',
                     data,
                     flags=re.X|re.M)

        return Response(data, mimetype='application/javascript')

app.run(port=18791, host='127.0.0.1', debug=True)
```



# /!\ Important note

I made this hacky bit of code, I'll turn it into a pip package with tests and all. but since it's doing its job already,
I figured why not publish it. Use it if you know what you're doing

## TODO

* [ ] Write unit tests for various scenarios
* [ ] Make the tests run
* [ ] Make a pip package
* [ ] Make that it doesn't screw exec stack print

## Why, dude ? Why ?
A small edit to answer to a concern I could have raised, should that be made by not me: `Why the fuck is this for ? That's not how we do JS anymore, don't you know transpiling you bloody moron (I would say that with a british accent)`

In a ideal world, that's a valid concern, that's a real good rationnal for not coding that.

The reason it exists is that as coders (especially freelance) we work with legacy code, often made by people that genuinly believe `test is doubting` (I only write code using TDD when it's my own codebase).

It is common in this type of context to have JS served as static files by Flask (exactly the case for the contract I'm in right now). I use decorator and Typescript is my everyday life, I was very bother to not be able to. So I'm going to make a proper lib, for all my fellow devs that have to work on horrible codebases and need a quick hack. It's not much, it won't ever be, but I like my quick and dirty hacks to be written using TDD.


oh also,

# IF THIS IS YOUR OWN CODEBASE YOU WANT USE THIS ON, YOU'RE A BAD CODER
