# Beginner's Crash Course to Elastic Stack Series
## Part 5: Understanding Mapping with Elasticsearch and Kibana
Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 5: Understanding Mapping with Elasticsearch and Kibana.

Have you ever encountered an error “Field type is not supported for [whatever you are trying to do with Elasticsearch]”?

The most likely culprit of this error is the mapping of your index!

Mapping is the process of defining how a document and its fields are indexed and stored. It defines the type and format of the fields in the documents. As a result, mapping can significantly affect how Elasticsearch searches and stores data.

Understanding how mapping works will help you define mapping that best serves your use case.

By the end of this workshop, you will be able to define what a mapping is and define your own mapping to make indexing and searching more efficient. 

## Resources

[Table of Contents: Beginner's Crash Course to Elastic Stack](https://github.com/LisaHJung/Beginners-Crash-Course-to-the-Elastic-Stack-Series): 

This workshop is a part of the Beginner's Crash Course to Elastic Stack series. Check out this table contents to access all the workshops in the series thus far. This table will continue to get updated as more workshops in the series are released! 

[Free Elastic Cloud Trial](https://ela.st/elastic-beginners)

[Instructions](https://dev.to/lisahjung/beginner-s-guide-to-setting-up-elasticsearch-and-kibana-with-elastic-cloud-1joh) on how to access Elasticsearch and Kibana on Elastic Cloud

[Instructions](https://dev.to/elastic/downloading-elasticsearch-and-kibana-macos-linux-and-windows-1mmo) for downloading Elasticsearch and Kibana

[Presentation slides]()

[Elastic America Virtual Chapter](https://community.elastic.co/amer-virtual/): 

Want to attend live workshops? Join the Elastic Americal Virtual Chapter to get the deets!

## Review from previous workshops
![image](https://user-images.githubusercontent.com/60980933/120677555-6e320180-c454-11eb-890b-b2a7c1d61618.png)

![image](https://user-images.githubusercontent.com/60980933/121091155-ccd5e300-c7a6-11eb-8afb-766c6723fa0d.png)

### Indexing a document
The following request will index the following document.  

Syntax: 
```
PUT Enter-name-of-the-index/_doc/id-you-want-to-assign-to-this-document
{
  "field": "value"
}
```
Example: 
```
POST temp_index/_doc
{
  "name": "Pineapple",
  "country_of_origin": "New Zealand",
  "date_received": "2020-06-02T12:15:35",
  "quantity": 200,
  "unit_price": 3.11,
  "description": "a large juicy tropical fruit consisting of aromatic edible yellow flesh surrounded by a tough segmented skin and topped with a tuft of stiff leaves.These pineapples are sourced from New Zealand.",
  "vendor_details": {
    "vendor": "Tropical Fruit Growers of New Zealand",
    "main_contact": "Hugh Rose",
    "vendor_location": "Whangarei, New Zealand",
    "preferred_vendor": true
  }
}
```
Expected response from Elasticsearch:

Elasticsearch will confirm that this document has been successfully indexed in the temp_index. 
![image](https://user-images.githubusercontent.com/60980933/120387213-d5ca3e80-c2e6-11eb-8ca8-731222174724.png)

## Mapping
Mapping defines how a document and its fields are indexed and stored by defining the type of each field.  

![image](https://user-images.githubusercontent.com/60980933/121219417-e7f53100-c840-11eb-9848-7acb3df84227.png)

It contains a list of names and field types of an index. Depending on its type, the fields are indexed and stored differently in Elasticsearch.  

### Dynamic Mapping
When a user does not define mapping in advance, Elasticsearch creates or updates the mapping as needed by default. This is known as `dynamic mapping`. 

![image](https://user-images.githubusercontent.com/60980933/121225461-c5661680-c846-11eb-9e3c-e780e4689a1f.png)

With `dynamic mapping`, Elasticsearch looks at each field and tries to infer the data type from the field content. Then, it assigns a type to each field. 

Depending on the assigned field type, each field is indexed and primed for different types of search. This is why mapping plays an important role in how Elasticsearch stores and searches for data. 

### View the mapping 
Syntax:
```
GET Enter_name_of_the_index_here/_mapping
```
Example:
```
GET temp_index/_mapping
```
Expected response from Elasticsearch:

Elasticsearch will return the mapping of the temp_index. It lists all the fields of the document in an alphabetical order and lists the data type of each field(text, keyword, long, float, date, boolean and etc). 

![image](https://user-images.githubusercontent.com/60980933/121097045-98ffbb00-c7b0-11eb-824d-20d01f7ade75.png)
![image](https://user-images.githubusercontent.com/60980933/121097085-a9b03100-c7b0-11eb-9ffb-f5106bbcd0fb.png)
![image](https://user-images.githubusercontent.com/60980933/121097110-b3399900-c7b0-11eb-8e8e-4ffe9a1b3551.png)

For the list of all field types, click [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)!

### Indexing Strings 
There are two kinds of string data types:
1. Text
2. Keyword

By default, every string gets mapped twice as a text field and as a keyword multi-field. Each data type is primed for different types of searches. 

`Text` field type is designed for full-text searches. 

`Keyword`field type is designed for exact searches, aggregations, and sorting.

You can customize your mapping by choosing either text or keyword only or both! 

#### Text field type
##### Text Analysis
Ever notice that when you search in Elasticsearch, it is not case sensitive or punctuation does not seem to matter? This is because `text analysis` occurs when your fields are indexed. 

By default, strings are analyzed when it is indexed. The string is broken up into individual words also known as tokens. The analyzer further lowercases each token and removes punctuations. 

![image](https://user-images.githubusercontent.com/60980933/120847933-672cf100-c531-11eb-9b9c-522c354b0e10.png)

**Inverted index**
![image](https://user-images.githubusercontent.com/60980933/121099236-b33b9800-c7b4-11eb-837b-a914ed8e3725.png)
Once the string is analyzed, the individual tokens are stored in a sorted list known as the `inverted index`. Each unique token is stored in the index with its relevant ID. 

The same process occurs every time you index a new document. 

![image](https://user-images.githubusercontent.com/60980933/120848060-8c216400-c531-11eb-9fed-6cfd5b50d7c8.png)
![image](https://user-images.githubusercontent.com/60980933/120851449-14096d00-c536-11eb-88d6-08add98441db.png)
![image](https://user-images.githubusercontent.com/60980933/121221919-4a4f3100-c843-11eb-9901-db131f9508c4.png)
![image](https://user-images.githubusercontent.com/60980933/121113584-89439f00-c7cf-11eb-80a8-22b39f230ef9.png)

#### Keyword data type
`Keyword` data type is used for aggregations, sorting, and exact searches. These actions look up the document ID to find the values it has in its fields. 

`Keyword` field is suited to perform these actions because it uses a data structure called `doc values` to store data. 

For each document, the document id along with the field value(original string) are added to a table. This data structure(`Doc values`) is designed for actions that require looking up the document ID to find the values it has in its fields. 
![image](https://user-images.githubusercontent.com/60980933/121113465-526d8900-c7cf-11eb-8e31-cdbf952a1d34.png)

### Mapping Exercise

**Project**: Build an app for a client who manages a produce warehouse. 

**This app must enable users to:** 
1.Search for produce name and description 
2. Pull up a list of produce received within a date range 
3. Identify top countries of origin where the client buys most produce from
4. Compare unit prices of produce item across countries

**Sample Data**
```{
  "name": "Pineapple",
  "country_of_origin": "New Zealand",
  "date_received": "2020-06-02T12:15:35",
  "quantity": 200,
  "unit_price": 3.11,
  "description": "a large juicy tropical fruit consisting of aromatic edible yellow flesh surrounded by a tough segmented skin and topped with a tuft of stiff leaves.These pineapples are sourced from New Zealand.",
  "vendor_details": {
    "vendor": "Tropical Fruit Growers of New Zealand",
    "main_contact": "Hugh Rose",
    "vendor_location": "Whangarei, New Zealand",
    "preferred_vendor": true
  }
}
```
** Field Analysis **

### Defining your own mapping
Rules
1. If you do not define a mapping ahead of time, Elastcisearch dynamically creates the mapping if it doesn't exist. 
2. If you do decide to define your own mapping, you can do so at index creation.
3. ONLY ONE mapping is defined per index. Once the index has been created, we CANNOT change the mapping of a field. 
4. If you must change it, you will have to reindex the existing index to another index and define mapping for the new index. 

Step 1: Index a sample document into a test index. 
The sample document must contain the fields that you want to define. These fields must also contain values that map closely to the field types that you want. 

Syntax:
```
POST Name-of-test-index/_doc
{
  "field": "value"
}
```

Example:
```
POST test_index/_doc
{
  "name": "Pineapple",
  "country_of_origin": "New Zealand",
  "date_received": "2020-06-02T12:15:35",
  "quantity": 200,
  "unit_price": 3.11,
  "description": "a large juicy tropical fruit consisting of aromatic edible yellow flesh surrounded by a tough segmented skin and topped with a tuft of stiff leaves.These pineapples are sourced from New Zealand.",
  "vendor_details": {
    "vendor": "Tropical Fruit Growers of New Zealand",
    "main_contact": "Hugh Rose",
    "vendor_location": "Whangarei, New Zealand",
    "preferred_vendor": true
  }
}
```
Step 2: View the dynamic mapping 

Syntax:
```
GET Name-of-test-index/_mapping
```

Example:
```
GET test_index/_mapping
```
Step 3: Edit the mapping
Copy and paste the mapping from step 2 into the Kibana console. Edit the mapping to fit your use case. 

Step 4: Create a new index using your customized mappings from step 3. 
Syntax:
```
PUT Name-of-your-final-index
{
  copy and paste your editied mapping here
}
```
Example: 
```
PUT produce_index
{
  "mappings": {
    "properties": {
      "country_of_origin": {
        "type": "keyword"
      },
      "date_received": {
        "type": "date"
      },
      "description": {
        "type": "text"
      },
      "name": {
        "type": "text"
      },
      "quantity": {
        "type": "long"
      },
      "unit_price": {
        "type": "float"
      },
      "vendor_details": {
        "enabled": false
      }
    }
  }
}
```
Step 5: Check the mapping of the new index to make sure the mapping has been defined correctly

Syntax:
```
GET Name-of-test-index/_mapping
```

Example:
```
GET produce_index/_mapping
```
#### Dynamic indexing

#### What if you do need to make changes to the field type? 
