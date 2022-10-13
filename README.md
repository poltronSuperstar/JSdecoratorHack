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

app.run(port=18791, host='127.0.0.1', debug=True)```



# /!\ Important note

I made this hacky bit of code, I'll turn it into a pip package with tests and all. but since it's doing its job already,
I figured why not publish it. Use it if you know what you're doing

## TODO

* [ ] Write unit tests for various scenarios
* [ ] Make the tests run
* [ ] Make a pip package
