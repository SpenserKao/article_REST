# Pet Project: Articles Retrieval in RESTful API
## _Abstract_
This is the document of pet project *articles retrieval in RESTful API*, outlining its requirements, design, development environment, setup/installation, execution and test. Maintenance wise, some configurable attributes, eventhough less frequently changed, have been moved to configuration. Last but not the least, Wishlist outlines what can be improved, time permitting.<br/> <br/>
**Each original requirement is assigned a unique id starting with capital 'R'**, which helps requirements management, especially when no management system is in place that is critical for team-based software development.
Such unique requirement identification is also vital in designing test plan, particularly test case-to-requirement matrix. In addition, these requirement ids have been cited in codebase for pertinent implementation.<br/><br/>
Other than three endpoins specified in the original requirements, I have further added one (**_R4_**) to list attributes of all articles.
## _Requirements_
### Endpoints
Originally, it's required to create a simple API with three endpoints. I've further added one to list attributes of all articles. 
- **_R1_: POST/articles** - Should hanlde the receipt of some article data in JSON format, and store within the service.
- **_R2_: GET/articles/{id}** - Should return the JSON representation of the article.
- **_R3_: GET/tags/{tagName}/{date}** - Should return the list of article(s) that have that tag name on the given date and some summary data about that tag for that day. 
- **_R4_: GET/articles/all** - Not part of original requirements, but proposed to list all articles' attributes. 
### Content Structure of an Article
**_R5_**: An article has the following attributes _id_, _title_, _date_, _body_, and _list of tags_. for example:
```json
{
  "id": "1",
  "title": "latest science shows that potato chips are better for you than sugar",
  "date" : "2016-09-22",
  "body" : "some text, potentially containing simple markup about how potato chips are great",
  "tags" : ["health", "fitness", "science"]
}
```
### Content Structure of **GET/tags/{tagName}/{date}** result in JSON
**_R6_**: The GET /tags/{tagName}/{date} endpoint should produce the following JSON. Note that the actual url would look like _/tags/health/20160922_.
```json
{
  "tag" : "health",
  "count" : 17,
  "articles" :
      [
        "1",
        "7"
      ],
  "related_tags" :
      [
        "science",
        "fitness"
      ]
}
```
**_R7_**: The **_related_tags_** field contains a list of tags that **_are on the articles that the current tag is on for the same day_**. It should not contain duplicates.<br/>
**_R8_**: The **_count_** field **_shows the number of tags_** for the tag for that day.<br/>
**_R9_**: The **_articles_** field contains a list of ids for the **_last 10 articles entered for that day_**.

## _Design_
![Dataflow Diagram of ArticleAPI Server](image/ArticleAPI_Server.png "Dataflow Diagram of ArticleAPI Server")<br/>
**_Gin_** web framework is utilised to build the application.<br/>
**_Gin_** is a HTTP web framework written in Go (Golang). It features a Martini-like API with up to 40 times faster performance. 

### List of Assumptions
1. For requirement #8 (R8), those counted tags are non-duplicate, including inquiring tag name.
1. The date of each article proposed in the original requirements is assumed to be publishing date, hence I have to add a date-related field also contain time, of the article's entry, in order to be sortable to include the most recently **_ten_** articles **_entered_** for the date. The added field shall contain UTC time and be named **_EntryTime_**.
### Source of Articles Data
For the sake of maintenance, the source of articles data shall be contained in a file named **_articles.json_**. For possible further population of the input data, just do it at the file without having to go to modify the **_Go_** code, followed by rebuilding of the code.

### Choice of Languages and Package
| Language/Package | Version | Reference | Reason |
|:-----------------|:-------:|-----------|:-------|
| Golang | go1.17.1 windows/amd64 | | Mandatory choice of language |
| github.com/gin-contrib/sse | v0.1.0 | go.mod & go.sum | Better performance of resultant web app |
| github.com/gin-gonic/gin | v1.7.7 |||
| time | | | To parse UTC-based EntryTime of each article |
| sort | | | To sort in last-entered-first-out order multiple pieces of UTC-based EntryTime info before cutting off ten most recent entries |

## _Development Environment_
### OS (Operating System)
| OS | Version | OS Build |
|----|---------|----------|
| Windows 10 Home | 21H1 | 19043.1466 |

### IDE (Integrated Development Environment)
To code, execute the application at the server side. Or even execute curl command to communicate with the application at server side from client side.
| Tool | Version | 
|---------------|---------|
| Visual Studio Code | v1.63.2 |

### Web Browser
To open URL to access the article API. Following three major brands of browser work as expected.
| Tool | Version | 
|---------------|---------|
| Firefox Developer Edition | v97.0b4 (64-bit) |
| Google Chrome | v97.0.4692.71 (Official Build) (64-bit) |
| Microsoft Edge | v97.0.1072.02 (Official Build) (64-bit) |

### Command Line Tool
To transfer data to and from a server that hosts the article API.
| Tool | Version | Remark |
|---------------|---------|-------|
| cURL | v7.65.3 (x86_64-w64-mingw32) | aka Client URL |

### Postman
Other than using cURL for command line-based test, Google's Postman is a popular GUI-based tool for testing HTTP application.
| Tool | Version |
|---------------|---------|
| Postman | v9.9.3| 

### GitBash
When not issued from VS Code, we need an shell emulation layer for executing cURL command, or issuing Git command.<br/>
| Tool | Version | Remark |
|---------------|---------|-------|
| GitBash | v2.23.0.windows.1 |  |

## _Setup/Installation_
### Enter GitBash and Preparation
Open a GitBash and change into a working directory.<br/>
Execute following command: <br/>
<code>
git init  
</code>

### Cloning
Left-mouse click on green "Code" ![Code](image/codeButton.JPG "Code") button at middle right of the screen, then click on ![Copy](image/copyButton.JPG "Copy") button, as illustrated by the screenshot below. <br/>
![Find clone path](image/getClonePath.JPG "Finding clone path")<br/>
Execute command in following syntax: <br/>
<code>
git clone &lt;clone path&gt;  
</code><br/>
Where &lt;clone path&gt; is the clone gained through aforementioned process.<br/>
Then you will see the cloning process as shown below.<br/>
![Cloning Project](image/cloningProject.JPG "Cloning Project")<br/>

## _Execution_
Under a GitBash or VS Code, issue following command to execute the app.<br/>
<code>
go run .
</code><br/>
Either under VS Code or GitBash, once seeing following message, prepare to go to a browser for further testing from frontend.<br/>
![Backend Execution Message](image/backendExecution.JPG "Backend Execution Message")<br/>

## _Test_
### Test Plan
There are eight test cases as explained below through a matric mapping between test cases and requirements.<br/>
| Test Case | URL or Tool    | REQ Id | Description | Expected Result |
|:---------:|:---------------|:-------|:------------|:----------------|
| 1 | localhost:8080/articles/all | R4| Lists all articles |![op_articles.json](output/op_articles.json "op_articles.json") |
| 2 | localhost:8080/articles/1 | R2 | Lists article with Id=1 | ![op_article1.json](output/op_article1.json "op_article1.json") |
| 3 | localhost:8080/articles/2 | R2 | Lists article with Id=2 | ![op_article2.json](output/op_article2.json "op_article2.json") |
| 2 | localhost:8080/articles/15 | R2 | Lists article with Id=15 | ![op_article15.json](output/op_article15.json "op_article15.json") |
| 3 | localhost:8080/articles/16 | R2 | Lists article with Id=16 | ![op_article16.json](output/op_article16.json "op_article16.json") |
| 6 | localhost:8080/articles/17 | R2 | Lists article with non-existing Id | Expected to see  ![error](output/op_articleNotFound.json "error")  |
| 7 | http://localhost:8080/tags/climate%20change/2013-01-21 | R3 | Lists articles whose dates are all "2013-01-21" and one of tag names is "Climate Change"| ![op_climate_change_2013-01-21](output/op_climate_change_2013-01-21.json "op_climate_change_2013-01-21.json") |
| 8 | cURL, explained more below | R1 | HTTP POST act | explained more below |

**Note on the design of test case#7**: 
- Deliberately designed to be 12 artices, whose Ids are 2, 5, and 7 ~ 16, commonly containing **_date 2013-01-21_** and one **_tag name 'climate change'_**.
- The total number of articles is **_12_**, shall force to select only the **_ten most recent_** entries among them.

### Via cURL
#### Resemble to test case#1
Find a GitBash, issue following command<br/>
<code>
curl -X GET http://localhost:8080/articles/all
</code><br/>or shell script<br/>
<code>
./curlGetArticles.sh
</code><br/>
On screen, should see output message resemble to that of test case#1 stated above.

#### Resemble to test case#2
Find a GitBash, execute following command<br/>
<code>
curl -X GET http://localhost:8080/articles/1
</code><br/>or shell script<br/>
<code>
./curlGetArticle1.sh
</code><br/>
On screen, should see output message resemble to that of test case#2 stated above.

#### Resemble to test case#6
Find a GitBash, execute following command<br/>
<code>
curl -X GET http://localhost:8080/articles/17
</code><br/>or shell script<br/>
<code>
./curlGetArticleNonExisting.sh
</code><br/>
On screen, should see output message resemble to that of test case#6 stated above.

#### Test Case#8
Requirement Coverage: **_R1_**<br/>
Find a GitBash, execute following command<br/>
<pre>
curl -i \
    -H "Content-Type: application/json" \
    -X POST \
    -d '{"Id":"19","Title":"Test Article 19 psuedo title","Date":"2022-01-17","Body":"Psuedo body","Tags": ["tag1", "tag2", "tag3"]}' \
    http://localhost:8080/articles
</pre><br/>or shell script<br/>
<code>
./curlPost.sh
</code><br/>

#### Verification
Either use a web browser to open following URL:<br/>
<code>localhost:8080/articles/19</code><br/>
or shell script<br/>
<code>
./curlGetArticle19.sh
</code><br/>

### Via Postman
Same above mentioned tests can also be conduct through Postman. Be sure to utlisie those aforementioned URLs in Postman. Screenshot shown below tells the corresponding test scripts.<br/>
![Testing via Postman](image/TestViaPostman.JPG "Testing via Postman")<br/>

Paricularly for test case#7, those info following "-d", or "--data", option of curl command can be fed into Postman as shown in screenshot below.<br/>
![Speicying Data via Postman](image/SpeciyingDataViaPostman.JPG "Speicying Data via Postman")<br/>

## _Configuration_
Eventhough it's kind of low frequency of adjustment, I make following three constants configurable at the beginning of codebase **_main.go_**.
| Constant | Value |
|---------------|:---------|
| DevHostURL | localhost:8080
| ARTICLES_FILE | articles.json |
| MAX_ARTICLES_OF_TAGNAME_DATE_QUERY | 10 |<br/>

Changes from adjusting these adjustables all indicated the configuration is reponsive as expected.

## _Wishlist_
- Move articles data from file to database, such as NoSQL MongoDB.
- Integrate Selenium + Ginkgo + Gomock for automated web application test.
- Upon closure of the API application, the executioner should be asked whether to save the recent addition of article(s), very likely through "POST" act, back to data file **_articles.json_**.
- For requirement#1 (R1) re POST request act, do consider checking whether there exists a redundant article; if so, should reject the request. Of course, the criteria of redundancy is to be discussed, for instance, from database joint keys perspective, attributes _Id_ and _Title_? Or more?
