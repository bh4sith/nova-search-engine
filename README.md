# nova-search-engine

API - a simple web search engine.
The goal is to index an infinite list of URLs (web pages), and then be able to quickly search relevant URLs against a query. This engine uses the ElasticSearch database.

### Indexing
The indexing operation of a new URL first crawls URL, then extracts the title and main text content from the page.
Then, a new document representing the URL's data is saved in ElasticSearch, and goes for indexing.

### Searching
When searching for relevant URLs, the engine will compare the query with the data of each document (web page), and retrieve a list of URLs matching the query, sorted by relevance.

### Note
This API works for a finite list of languages, see here for the complete list :   https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html.

## INSTALL AND RUN

### REQUIREMENTS
This tool requires *Python3+* and *ElasticSearch5+*.

### WITH PIP
```
git clone https://github.com/bh4sith/nova-search-engine.git
cd nova-search-engine
pip install -r requirements.txt
```

Then, run the tool :
```
FLASK_APP=index.py HOST=<ip> PORT=<port> USERNAME=<username> PASSWORD=<password> flask run
```
Where :
* `ip` + `port` : route to ElasticSearch
* `username` + `password` : credentials to access

To run in debug mode, prepend `FLASK_DEBUG=1` to the command :
```
FLASK_DEBUG=1 ... flask run
```

### WITH DOCKER

build yourself a Docker image :
```
git clone https://github.com/bh4sith/nova-search-engine.git
cd nova-search-engine
docker build -t nova-search-engine .
```
```
docker run -p 5000:5000 \
-e "HOST=<ip>" \
-e "PORT=<port>" \
-e "USERNAME=<username>" \
-e "PASSWORD=<password>" \
bh4sith/nova-search-engine
```
Where :
* `ip` + `port` : route to ElasticSearch
* `username` + `password` : credentials to access ElasticSearch


## USAGE AND EXAMPLES
To list all services of API, type this endpoint in your web browser : http://localhost:5000/

### INDEXING
Index a web page through its URL.

* **URL**

  /index

* **Method**

  `POST`

* **Form Data Params**

  **Required:**

  `url=[string]`, the url to index

* **Success Response**

  * **Code:** 200 <br />
    **Content:** `Success`


* **Error Response**

  * **Code:** 400 INVALID USAGE <br />


* **Sample Call (with cURL)**

  ```
  curl http://localhost:5000/index --data "language=en&url=https://www.freelancer.com/"
  ```

### SEARCHING
Query engine to find a list of relevant URLs.
Return the sublist of matching URLs sorted by relevance, and the total of matching URLs, in JSON.

* **URL**

  /search

* **Method**

  `POST`

* **Form Data Params**

  **Required:**

  `query=[string]`, the search query  

  **Not required:**

  `start=[integer]`, the start of hits (0 by default)

  `hits=[integer]`, the number of hits returned by query (10 by default)

  `highlight=[integer]`, return highlight parts for each URL (0 or 1, 0 by default)

* **Success Response**

  * **Code:** 200 <br />
    **Content:**
    ```
    {
      "total": 1,
      "results": [
        {
          "title": "Hire Freelancers & Find Freelance Jobs Online | Freelancer",
          "description": "Freelancer.com has by far the largest pool of quality freelancers globally- over 50 million to choose from. Track progress . Keep up-to-date and on-the-go with our time tracker, and mobile app. Always know what freelancers are up to. Make it Real with Freelancer.",
          "url": "https://www.freelancer.com/"
        }
      ]
    }
    ```

* **Error Response**

  * **Code:** 400 INVALID USAGE <br />


* **Sample Call (with cURL)**

  ```
  curl http://localhost:5000/search --data "query=freelance"
  ```

## FUTURE FEATURES
* index more page features like keywords,...
* better scoring function
* filter bad results
* create a docker compose
* traduct tools in several languages
* connect to pixel tool
* better description of results
* redis to index a single url

## LICENCE
MIT
