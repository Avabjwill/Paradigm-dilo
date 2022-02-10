# How to set up a Paradigm server in dilo

Table of contents
-------------
* [Setting up the server](#servsetup)
* [The API Documentation](#apidoc)
    

<a name="servsetup"></a>
## Setting up the server
Dilo is a generic server that can serve a REST-based API using Paradigm API Documentation to understand the type of data and the operations supported by the API. Getting a server can be intimidating but with dilo we have simpliefied the process. Simply, create a script that plugs in the API Documentation, the database for storing all important data, a few other variables and run a dilo application. An example of this is given below. In the following subsections, we will address each part of the script and show how to create your own API using your API Documentation.

```python
"""Demo script for setting up an API using dilo."""
from paradm import create_engine
from paradm.igma import sessionmaker
from dilo.app import app_factory
from dilo.utils import set_session, set_doc, set_paradigm_server_url, set_api_name
from dilo.data import doc_parse
from dilo.paradigmspec import doc_maker
from dilo.data.db_models import Base
from dilo.metadata.doc import doc     # Can be replaced by any API Documentation
# Define the server URL, this is what will be displayed on the Doc
PARADIGM_SERVER_URL = "http://localhost:8000/"
# The name of the API or the EntryPoint, the api will be at http://localhost/<API_NAME>
API_NAME = "serverapi"
# Define the Dilo API Documentation
# NOTE: You can use your own API Documentation and create a Dilo object using doc_maker
# Define the database connection
# Add the required Models to the database
Base.metadata.create_all(engine)
# Start a session with the DB and create all classes needed by the APIDoc
session = sessionmaker(bind=engine)()
# Get all the classes from the doc
classes = doc_parse.get_classes(apidoc.generate())  
properties = doc_parse.get_all_properties(classes)
# Insert them into the database
doc_parse.insert_classes(classes, session)
doc_parse.insert_properties(properties, session)
# Create a dilo app with the API name you want, default will be "api"
app = app_factory(API_NAME)
# Set the name of the API
with set_api_name(app, API_NAME):
    # Set the API Documentation
    with set_doc(app, apidoc):
        # Set SERVER_URL
        with set_server_url(app, PARADIGM_SERVER_URL):
            # Set the Database session
            with set_session(app, session):
                # Start the dilo app
                app.run(host='127.0.0.0.1', debug=True, port=8080)
```

We will now break down each of these steps and understand what they do. Let's begin.

<a name="apidoc"></a>
## The API Documentation
Much of dilo is built around the Paradigm API Documentation. The API Doc is defined in the Paradigm spec [here].
The API Doc is the entity that tells dilo the way to set the server up, the endpoints that must be created, the data needs to be served, the operations supported by the data and so on.
Dilo uses Python classes to create and define API Docs. A description of these classes and how they are designed can be found in the [Design]section.
