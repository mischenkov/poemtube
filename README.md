poemtube
========

An example of a web site with a REST API.

Getting started
---------------

    # Dependencies:
    sudo apt-get install python-webpy python-couchdb python-requests

    # Start the server (with an in-memory database)
    cd server/src
    ./poemtube-dev-memory.py

    # In another terminal, run the client programs
    cd client/src
    ./list-poems.py           # list the available poems
    ./get-poem.py a-question  # print a single poem

Running the tests
-----------------

    # Dependencies
    sudo apt-get install python-nose python-paste

    # Run unit tests
    cd server/src
    nosetests

    # Run system tests (requires a local running couch db)
    cd server/system-tests
    make test

Deploy to Heroku
----------------

    # Create new Heroku app
    heroku create
    
    # Push repository to Heroku
    git push heroku master
    
    # Scale to one dyno (optionally)
    heroku ps:scale web=1

    # Test run of your app
    heroku open

In the next section described process of local testing your app. For testing
Heroku app just change "localhost:8080" with "your-app-name"

API usage examples
------------------

Start the server (+ sample data) with:

    $ cd server/src
    $ ./poemtube-dev-memory.py

List poems:

    $ curl http://localhost:8080/api/v1/poems
    ["a-question", "this-is-a-photograph-of-me"]

Search:

    $ curl http://localhost:8080/api/v1/poems?search=Robert%20Frost
    ["a-question"]

Get a poem:

    $ curl http://localhost:8080/api/v1/poems/a-question
    {"text": "A voice said, Look me in the stars\nAnd tell me truly, men of earth,\nIf all the soul-and-body scars\nWere not too much to pay for birth.\n", "title": "A Question", "id": "a-question", "author": "Robert Frost"}

Add a poem:

    $ curl --data '{"title":"Foo","author":"Bar","text":"Baz"}' http://localhost:8080/api/v1/poems -u user1:pass1
    foo
    $ curl http://localhost:8080/api/v1/poems/foo
    {"text": "Baz", "title": "Foo", "id": "foo", "author": "Bar"}

Replace a poem:

    $ curl --request PUT --data '{"title":"Foo2","author":"Bar2","text":"Baz2"}' http://localhost:8080/api/v1/poems/a-question -u user1:pass1
    $ curl http://localhost:8080/api/v1/poems/a-question
    {"text": "Baz2", "title": "Foo2", "id": "foo", "author": "Bar2"}

Delete a poem:

    $ curl --request DELETE http://localhost:8080/api/v1/poems/a-question -u user1:pass1
    ""
    $ curl -i http://localhost:8080/api/v1/poems/a-question
    HTTP/1.1 404 Not Found
    Content-Type: application/json
    Transfer-Encoding: chunked
    Date: Thu, 31 Oct 2013 15:22:39 GMT
    Server: localhost

    {"error": "\"a-question\" is not the ID of an existing poem."}

Amend a poem:

    $ curl --request PATCH --data '{"text":"Cheer up"}' http://localhost:8080/api/v1/poems/a-question -u user1:pass1
    ""
    $ curl http://localhost:8080/api/v1/poems/a-question
    {"text": "Cheer up", "title": "A Question", "id": "a-question", "author": "Robert Frost"}

Get a login session:

    $ curl -c mycookies.txt http://localhost:8080/api/v1/login -u user1:pass1

This stores an authentication token in a cookie in mycookies.txt.  Now we can act as this user by including this cookie in our requests, with no username and password.

    $ curl -b mycookies.txt --data '{"title":"Foo","author":"Bar","text":"Baz"}' http://localhost:8080/api/v1/poems

Launching in different modes
----------------------------

To run against a real running CouchDB instance (on localhost):

    sudo service couchdb start
    cd server/src
    ./poemtube-dev-couch.py

To run as a FastCGI process against a real CouchDB (on localhost):

    sudo service couchdb start
    cd server/src
    ./poemtube-prod.py

To run with no dependency on CouchDB (using an in-memory database), with some sample data:

    cd server/src
    ./poemtube-dev-memory.py

The other launcher, "poemtube-systest.py" is used by the system tests to launch against a real CouchDB, but with a prefix for all table names to avoid overwriting real data.


