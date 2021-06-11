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

## What is a Mapping?
![image](https://user-images.githubusercontent.com/60980933/121580875-7953d700-c9ea-11eb-8a4c-015ea238540e.png)

## Review from Previous Workshops
![image](https://user-images.githubusercontent.com/60980933/121580744-55909100-c9ea-11eb-98fe-8c8491b0a7a1.png)

### Indexing a Document
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

![image](https://user-images.githubusercontent.com/60980933/121590398-83c79e00-c9f5-11eb-95f8-e32654447359.png)

With `dynamic mapping`, Elasticsearch looks at each field and tries to infer the data type from the field content. Then, it assigns a type to each field and creates a list of field names and types known as mapping.  

Depending on the assigned field type, each field is indexed and primed for different types of search. This is why mapping plays an important role in how Elasticsearch stores and searches for data. 

### View the Mapping 
Syntax:
```
GET Enter_name_of_the_index_here/_mapping
```
Example:
```
GET temp_index/_mapping
```
Expected response from Elasticsearch:

Elasticsearch will return the mapping of the temp_index. It lists all the fields of the document in an alphabetical order and lists the type of each field(text, keyword, long, float, date, boolean and etc). 

![image](https://user-images.githubusercontent.com/60980933/121591969-5c71d080-c9f7-11eb-95dc-70f04276929a.png)
![image](https://user-images.githubusercontent.com/60980933/121592051-76131800-c9f7-11eb-8820-d3d2b39e1e4f.png)
![image](https://user-images.githubusercontent.com/60980933/121592106-83c89d80-c9f7-11eb-97fc-56d23b0242ae.png)

For the list of all field types, click [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)!

### Indexing Strings 
There are two kinds of string data types:
1. Text
2. Keyword

By default, every string gets mapped twice as a text field and as a keyword multi-field. Each data type is primed for different types of searches. 

`Text` field type is designed for full-text searches. 

`Keyword`field type is designed for exact searches, aggregations, and sorting.

You can customize your mapping by assigning the filed type as either text or keyword or both! 

#### Text Field Type
##### Text Analysis
Ever notice that when you search in Elasticsearch, it is not case sensitive or punctuation does not seem to matter? This is because `text analysis` occurs when your fields are indexed. 

By default, strings are analyzed when it is indexed. The string is broken up into individual words also known as tokens. The analyzer further lowercases each token and removes punctuations. 

![image](https://user-images.githubusercontent.com/60980933/120847933-672cf100-c531-11eb-9b9c-522c354b0e10.png)

**Inverted Index**
![image](https://user-images.githubusercontent.com/60980933/121099236-b33b9800-c7b4-11eb-837b-a914ed8e3725.png)
Once the string is analyzed, the individual tokens are stored in a sorted list known as the `inverted index`. Each unique token is stored in the index with its relevant ID. 

The same process occurs every time you index a new document. 

![image](https://user-images.githubusercontent.com/60980933/120848060-8c216400-c531-11eb-9fed-6cfd5b50d7c8.png)
![image](https://user-images.githubusercontent.com/60980933/120851449-14096d00-c536-11eb-88d6-08add98441db.png)
![image](https://user-images.githubusercontent.com/60980933/121601915-cdb78080-ca03-11eb-81ad-9265cffc8581.png)
![image](https://user-images.githubusercontent.com/60980933/121113584-89439f00-c7cf-11eb-80a8-22b39f230ef9.png)

#### Keyword Field Type
`Keyword` field type is used for aggregations, sorting, and exact searches. These actions look up the document ID to find the values it has in its fields. 

`Keyword` field is suited to perform these actions because it uses a data structure called `doc values` to store data. 

For each document, the document id along with the field value(original string) are added to a table. This data structure(`doc values`) is designed for actions that require looking up the document ID to find the values it has in its fields.

![image](https://user-images.githubusercontent.com/60980933/121603436-fccef180-ca05-11eb-817e-cb77b46ae969.png)

### Mapping exercise

**Project**: Build an app for a client who manages a produce warehouse 

**This app must enable users to:** 
1. Search for produce name, country of origin and description

2. Identify top countries of origin where the client buys most produce from

3. Sort produce by produce type(Fruit or Vegetable)

4. Get the summary of monthly expense

**Sample data**
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
**Plan of Action**
![image](https://user-images.githubusercontent.com/60980933/121710560-f89ee480-ca96-11eb-98c5-ba9a535e4360.png)
![image](https://user-images.githubusercontent.com/60980933/121604166-4704a280-ca07-11eb-9fc6-eb494ec92699.png)
![image](https://user-images.githubusercontent.com/60980933/121604184-4c61ed00-ca07-11eb-84f8-208c3e927a08.png)
![image](https://user-images.githubusercontent.com/60980933/121713123-ba56f480-ca99-11eb-9da9-72ce8f3995ac.png)
![image](https://user-images.githubusercontent.com/60980933/121713301-effbdd80-ca99-11eb-9c1c-bce14b41a9ac.png)

### Defining your own mapping
**Rules**
1. If you do not define a mapping ahead of time, Elastcisearch dynamically creates the mapping if it doesn't exist. 
2. If you do decide to define your own mapping, you can do so at index creation.
3. ONE mapping is defined per index. Once the index has been created, we can only add *new* fields to a mapping. We CANNOT change the mapping of an *existing* field. 
4. If you must change the type of an existing field, you must create a new index with the desired mapping, then reindex all documents into the new index. 

**Step 1: Index a sample document into a test index.**

The sample document must contain the fields that you want to define. These fields must also contain values that map closely to the field types you want. 

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
Expected response from Elasticsearch:

The test_index has been successfully created. 
![image](https://user-images.githubusercontent.com/60980933/121616770-c81c6380-ca20-11eb-9f3a-593e61eff319.png)

**Step 2: View the dynamic mapping** 

Syntax:
```
GET Name-of-test-index/_mapping
```

Example:
```
GET test_index/_mapping
```
Expected response from Elasticsearch:

Elasticsearch will display the mapping it has created. It lists the fields in alphabetical order. This document is identical to the one we indexed into temp_index. To save space, the screenshots of the mapping has not been included here. 

**Step 3: Edit the mapping**

Copy and paste the mapping from step 2 into the Kibana console. From the pasted results, remove the test index along with its opening and closing brackets. Edit the mapping to fit your use case. 

![image](https://user-images.githubusercontent.com/60980933/121617475-3b72a500-ca22-11eb-885b-4fab72db17b5.png)

Your edited mapping should look like the following: 
```
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
![image](https://user-images.githubusercontent.com/60980933/121721478-87b0fa00-caa1-11eb-81c2-aac579020b38.png)

**Step 4: Create a new index using your customized mappings from step 3.** 

Syntax:
```
PUT Name-of-your-final-index
{
  copy and paste your edited mapping here
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
Expected response from Elasticsearch:

Elasticsearch creates a produce_index with the customized mapping we defined above! 

![image](https://user-images.githubusercontent.com/60980933/121618138-89d47380-ca23-11eb-96e3-68204da782dc.png)

**Step 5: Check the mapping of the new index to make sure the all the fields have been mapped correctly**

Syntax:
```
GET Name-of-test-index/_mapping
```

Example:
```
GET produce_index/_mapping
```
Expected response from Elasticsearch:

Compared to the dynamic mapping, our customized mappign looks more simple and concise!  The current mapping satisfies the requirements that are marked with green check marks. 

![image](https://user-images.githubusercontent.com/60980933/121619679-3879b380-ca26-11eb-9ac9-ed19a52c05c2.png)
![image](https://user-images.githubusercontent.com/60980933/121619466-e5076580-ca25-11eb-8c59-ec2caf3ddb50.png)
![image](https://user-images.githubusercontent.com/60980933/121619506-f5b7db80-ca25-11eb-9916-fea11e8fd79d.png)


**Step 6: Index your dataset into the new index**
For simplicity's sake, we will index two documents. 

*Document 1*
Syntax:
```
POST produce_index/_doc
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
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/121621403-5563b600-ca29-11eb-8ee9-83686a937fd2.png)

*Document 2*
The second document has almost identical fields as the first document except that it has an extra field called organic set to true!
```
POST produce_index/_doc
{
  "name": "Mango",
  "botanical_name": "Harum Manis",
  "produce_type": "Fruit",
  "country_of_origin": "Indonesia",
  "organic": true,
  "date_purchased": "2020-05-02T07:15:35",
  "quantity": 500,
  "unit_price": 1.5,
  "description": "Mango Arumanis or Harum Manis is originated from East Java. Arumanis means harum dan manis or fragrant and sweet just like its taste. The ripe Mango Arumanis has dark green skin coated with thin grayish natural wax. The flesh is deep yellow, thick, and soft with little to no fiber. Mango Arumanis is best eaten when ripe.",
  "vendor_details": {
    "vendor": "Ayra Shezan Trading",
    "main_contact": "Suharto",
    "vendor_location": "Binjai, Indonesia",
    "preferred_vendor": true
  }
}
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/121621463-73c9b180-ca29-11eb-9849-955b8d7872fb.png)

Let's see what happens to the mapping by sending this request below. 
Example: 
```
GET produce_index/_mapping
```
Expected response from Elasticsearch:
The new field(organic) and its field type(boolean) have been added to the mapping. This is in line with the rules of mapping we discussed earlier. You can add new fields to the mapping. We just cannot change the mapping of an existing field! 

![image](https://user-images.githubusercontent.com/60980933/121694928-d2257d00-ca87-11eb-9141-77143d59081a.png)
![image](https://user-images.githubusercontent.com/60980933/121694969-db164e80-ca87-11eb-9ddf-479af3077c46.png)

#### What if you do need to make changes to the field type? 
Let's say your client changed his mind. He wants to run a full text search on the field botanical name we disabled earlier(no aggregation, sorting, or exact searches needed). 

Remember, you CANNOT change the mapping of an existing field. If you do need to make changes to the existing field, type, you must create a new index with the desired mapping, then reindex all documents into the new index. 

**STEP 1: Create a new index(produce_v2) with the latest desired mapping.**

The mapping below 
Example:
```
PUT produce_v2
{
  "mappings": {
    "properties": {
      "botanical_name": {
        "type": "text"
      },
      "country_of_origin": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
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
      "organic": {
        "type": "boolean"
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
        "type": "object",
        "enabled": false
      }
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch creates a new index(produce_v2) with the latest mapping. 
![image](https://user-images.githubusercontent.com/60980933/121725821-fb093a80-caa6-11eb-8e76-aa84704fb951.png)

If you check the mapping, you will see that the botanical_name field has been typed as text. 
![image](https://user-images.githubusercontent.com/60980933/121725730-e036c600-caa6-11eb-8b9d-5a9ca0a72a3c.png)
![image](https://user-images.githubusercontent.com/60980933/121725765-e88f0100-caa6-11eb-9610-97cbf980e6c2.png)

**STEP 2: Reindex the data from original index(produce_index) to the one you just created(produce_v2).**
```
POST _reindex
{
  "source": {
    "index": "produce_index"
  },
  "dest": {
    "index": "produce_v2"
  }
}
```
Expected response form Elasticsearch:

This request moves data from the produce_index to the produce_v2 index. Now we can use the produce_v2 index to run requests that clients asked for.

![image](https://user-images.githubusercontent.com/60980933/121726550-ee391680-caa7-11eb-89a9-be1d4416e0e3.png)

#### Runtime field
![image](https://user-images.githubusercontent.com/60980933/121726752-348e7580-caa8-11eb-82ff-c248364438f6.png)

![image](https://user-images.githubusercontent.com/60980933/121622651-9b217e00-ca2b-11eb-8fa2-fed0d1129841.png)

We have one last feature to work on! 

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
```
Expected response from Elasticsearch: 

Elasticsearch successfully adds runtime field to the mapping. 
![image](https://user-images.githubusercontent.com/60980933/121705927-609efc00-ca92-11eb-95fe-4cc2d700cdd7.png)

Check the mapping:
```
GET produce_index/_mapping
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/121706959-5b8e7c80-ca93-11eb-8887-4524f80fc58b.png)
![image](https://user-images.githubusercontent.com/60980933/121707004-63e6b780-ca93-11eb-9b97-b03e90663856.png)

```
GET produce_index/_search
{
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
![image](https://user-images.githubusercontent.com/60980933/121706279-b673a400-ca92-11eb-9a5a-4d85699d661b.png)
![image](https://user-images.githubusercontent.com/60980933/121706366-cc816480-ca92-11eb-9a84-e9d20c3b588d.png)
![image](https://user-images.githubusercontent.com/60980933/121706402-d4410900-ca92-11eb-8241-ae10cd68f7d2.png)


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

These two documents are successfully indexed according to the mapping we defined for the produce index. 

Send this request to Elasticsearch and see what I mean.

Syntax:
```
GET enter-name-of-the-index/_search
```
Example:
```
GET produce_index/_search
```
Expected response from Elasticsearch: 

![image](https://user-images.githubusercontent.com/60980933/121622158-bcce3580-ca2a-11eb-96f1-e6c238fe03d9.png)
![image](https://user-images.githubusercontent.com/60980933/121622172-c6579d80-ca2a-11eb-9775-4ae390db4710.png)

Elasticsearch retreives documents we indexed in the produce index. Remember how we disabled the field botanical_name and object vendor_details in our customized mapping? 

Disabling a field only prevents inverted index and doc values from being created. It does not modify the original JSON object sent to Elasticsearch. 

While these fields cannot be used in queries or aggregation, these fields are still stored in the `_source` so you can still see it in the hits of a query! 

Notice that the field botanical_name and vendor_details object are displayed in the `_source` field. 
