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

## What is a mapping?
![image](https://user-images.githubusercontent.com/60980933/121580875-7953d700-c9ea-11eb-8a4c-015ea238540e.png)

## Review from previous workshops
![image](https://user-images.githubusercontent.com/60980933/121580744-55909100-c9ea-11eb-98fe-8c8491b0a7a1.png)

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
Mapping determines how a document and its fields are indexed and stored by defining the type of each field.  

![image](https://user-images.githubusercontent.com/60980933/121219417-e7f53100-c840-11eb-9848-7acb3df84227.png)

It contains a list of the names and types of fields in an index. Depending on its type, each field is indexed and stored differently in Elasticsearch.  

### Dynamic Mapping
When a user does not define mapping in advance, Elasticsearch creates or updates the mapping as needed by default. This is known as `dynamic mapping`. 

![image](https://user-images.githubusercontent.com/60980933/121588751-875a2580-c9f3-11eb-8971-85c6168989fe.png)

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
1. Search for produce name and description

2. Identify top countries of origin where the client buys most produce from

3. Sort produce by produce type(Fruit or Vegetable)

4. Get the summary of monthly total purchase

**Sample Data**
```
{
  "name": "Pineapple",
  "botanical_name": "Ananas comosus",
  "produce_type": "Fruit",
  "country_of_origin": "New Zealand",
  "date_purchased": "2020-06-02T12:15:35",
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
Add slides here. 

### Defining your own mapping
Rules
1. If you do not define a mapping ahead of time, Elastcisearch dynamically creates the mapping if it doesn't exist. 
2. If you do decide to define your own mapping, you can do so at index creation.
3. ONE mapping is defined per index. Once the index has been created, we can only add *new* fields to a mapping. We CANNOT change the mapping of an *existing* field. 
4. If you must change the type of an existing field, you must create a new index with the desired mapping, then reindex all documents into the new index. 

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
  "botanical_name": "Ananas comosus",
  "produce_type": "Fruit",
  "country_of_origin": "New Zealand",
  "date_purchased": "2020-06-02T12:15:35",
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
      "botanical_name": {
        "enabled": false
      },
      "country_of_origin": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword"
          }
        }
      },
      "date_purchased": {
        "type": "date"
      },
      "description": {
        "type": "text"
      },
      "name": {
        "type": "text"
      },
      "produce_type": {
        "type": "keyword"
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
Step 5: Check the mapping of the new index to make sure the all the fields have been mapped correctly

Syntax:
```
GET Name-of-test-index/_mapping
```

Example:
```
GET produce_index/_mapping
```
#### Adding a new field to the mapping of an existing index
```
PUT produce_index/_doc/2
{
  "organic": true
}
```
#### runtime field
```

PUT produce_index/_mapping
{
  "runtime": {
    "total_expense": {
      "type": "double",
      "script": {
        "source": "emit(doc['unit_price'].value* doc['quantity'].value)"
      }
    }
  }
}

GET produce_index/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "exists": {
            "field": "unit_price"
          }
        },
        {
          "exists": {
            "field": "quantity"
          }
        }
      ]
    }
  },
  "aggregations": {
    "total": {
      "sum": {
        "field": "total_expense"
      }
    }
  }
}


```
Expected response from Elasticsearch:

#### What if you do need to make changes to the field type? 
You must reindex the whole thing. 
STEP 1: Create a new index(produce_v2) with the following mapping.
Example:
```
PUT produce_v2
{
    "mappings" : {
      "properties" : {
        "country_of_origin" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "date_received" : {
          "type" : "date"
        },
        "description" : {
          "type" : "text"
        },
        "name" : {
          "type" : "text"
        },
        "organic" : {
          "type" : "boolean"
        },
        "quantity" : {
          "type" : "long"
        },
        "unit_price" : {
          "type" : "float"
        },
        "vendor_details" : {
          "type" : "object",
          "enabled" : false
        }
      }
    }
  }
}
```
STEP 2: Reindex the data from original index(produce) to the one you just created(produce_v2).
```
POST _reindex
{
  "source": {
    "index": "produce"
  },
  "dest": {
    "index": "produce_v2"
  }
}
```
##### Dynamic template
```
PUT Enter-name-of-index-here
{
  "mappings": {
    "dynamic_templates": [
      {
        "Name-your-template-here": {
          "match_mapping_type": "string",
          "mapping": {
            "type": "keyword"
          }
        }
      }
    ]
  }
}
```
```
PUT test_v2
{
  "mappings": {
    "dynamic_templates": [
      {
        "string_fields": {
          "match_mapping_type": "string",
          "mapping": {
            "type": "keyword"
          }
        }
      }
    ]
  }
}
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/121422110-1bf95080-c92c-11eb-866e-6e3d50fb06cb.png)

check to see if it worked
```
PUT test_v2/_doc/1
{
  "category": "news",
  "city": "Denver"
}
```
```
GET test_v2/_mapping
```

Expected response from Elasticsearch:
The mapping shows the dynamic teamplates we have specified earlier when we defined the mapping. Under properties key, you will see that both fields category and city have been typed as keyword only. 
