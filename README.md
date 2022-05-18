# Beginner's Crash Course to Elastic Stack Series
## Part 5: Understanding Mapping with Elasticsearch and Kibana
Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 5: Understanding Mapping with Elasticsearch and Kibana.

Have you ever encountered the error “Field type is not supported for [whatever you are trying to do with Elasticsearch]”?

The most likely culprit of this error is the mapping of your index!

Mapping is the process of defining how a document and its fields are indexed and stored. It defines the type and format of the fields in the documents. As a result, mapping can significantly affect how Elasticsearch searches and stores data.

Understanding how mapping works will help you define mapping that best serves your use case.

By the end of this workshop, you will be able to define what a mapping is and define your own mapping to make indexing and searching more efficient. 

## Resources

[Table of Contents: Beginner's Crash Course to Elastic Stack](https://github.com/LisaHJung/Beginners-Crash-Course-to-the-Elastic-Stack-Series): 

This workshop is a part of the Beginner's Crash Course to Elastic Stack series. Check out this table contents to access all the workshops in the series thus far. This table will continue to get updated as more workshops in the series are released! 

[Free Elastic Cloud Trial](https://www.elastic.co/cloud/cloud-trial-overview/30-days?ultron=community-beginners-crash-course-May2022+&hulk=30d)

[Instructions](https://dev.to/lisahjung/beginner-s-guide-to-setting-up-elasticsearch-and-kibana-with-elastic-cloud-1joh) on how to access Elasticsearch and Kibana on Elastic Cloud

[Instructions](https://dev.to/elastic/downloading-elasticsearch-and-kibana-macos-linux-and-windows-1mmo) for downloading Elasticsearch and Kibana

[Video recording of the workshop](https://www.youtube.com/watch?v=FQAHDrVwfok&amp;t=37s)

[Mini Beginner's Crash Course to Elasticsearch & Kibana playlist](https://ela.st/mini-beginners-crash-course)

Do you prefer learning by watching shorter videos? Check out this playlist to watch short clips of beginner's crash course full length workshops. Part 5 workshop is broken down into episodes 19-22. Season 2 clips will be uploaded here in the future!

[Presentation Slides](https://github.com/LisaHJung/Part-5-Understanding-Mapping-with-Elasticsearch-and-Kibana/blob/main/Part%205_%20Understanding%20Mapping%20with%20Elasticsearch%20and%20Kibana.pdf)

[What's next?](https://github.com/LisaHJung/Part-6-Troubleshooting-Beginner-Level-Elasticsearch-Errors) Eager to continue your learning after mastering the concept from this workshop? Move on to Part 6: Troubleshooting Beginner Level Elasticsearch Errors [here](https://github.com/LisaHJung/Part-6-Troubleshooting-Beginner-Level-Elasticsearch-Errors)!

## What is a Mapping?
![image](https://user-images.githubusercontent.com/60980933/121580875-7953d700-c9ea-11eb-8a4c-015ea238540e.png)

## Review from Previous Workshops
![image](https://user-images.githubusercontent.com/60980933/121580744-55909100-c9ea-11eb-98fe-8c8491b0a7a1.png)

### Indexing a Document
The following request will index the following document.  

Syntax: 
```
POST Enter-name-of-the-index/_doc
{
  "field": "value"
}
```
Example: 
```
POST temp_index/_doc
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

Elasticsearch will confirm that this document has been successfully indexed into the temp_index. 
![image](https://user-images.githubusercontent.com/60980933/120387213-d5ca3e80-c2e6-11eb-8ca8-731222174724.png)

## Mapping Explained
Mapping determines how a document and its fields are indexed and stored by defining the type of each field.  

![image](https://user-images.githubusercontent.com/60980933/121219417-e7f53100-c840-11eb-9848-7acb3df84227.png)

It contains a list of the names and types of fields in an index. Depending on its type, each field is indexed and stored differently in Elasticsearch.  

### Dynamic Mapping
When a user does not define mapping in advance, Elasticsearch creates or updates the mapping as needed by default. This is known as `dynamic mapping`. 

![image](https://user-images.githubusercontent.com/60980933/121590398-83c79e00-c9f5-11eb-95f8-e32654447359.png)

With `dynamic mapping`, Elasticsearch looks at each field and tries to infer the data type from the field content. Then, it assigns a type to each field and creates a list of field names and types known as mapping.  

Depending on the assigned field type, each field is indexed and primed for different types of requests(full text search, aggregations, sorting). This is why mapping plays an important role in how Elasticsearch stores and searches for data. 

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

Elasticsearch returns the mapping of the temp_index. It lists all the fields of the document in an alphabetical order and lists the type of each field(text, keyword, long, float, date, boolean and etc). 

![image](https://user-images.githubusercontent.com/60980933/121591969-5c71d080-c9f7-11eb-95dc-70f04276929a.png)
![image](https://user-images.githubusercontent.com/60980933/121592051-76131800-c9f7-11eb-8820-d3d2b39e1e4f.png)
![image](https://user-images.githubusercontent.com/60980933/121592106-83c89d80-c9f7-11eb-97fc-56d23b0242ae.png)

For the list of all field types, click [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)!

### Indexing Strings 
There are two kinds of string field types:
1. Text
2. Keyword

By default, every string gets mapped twice as a text field and as a keyword multi-field. Each field type is primed for different types of requests. 

`Text` field type is designed for full-text searches. 

`Keyword`field type is designed for exact searches, aggregations, and sorting.

You can customize your mapping by assigning the field type as either text or keyword or both! 

#### Text Field Type
##### Text Analysis
Ever notice that when you search in Elasticsearch, it is not case sensitive or punctuation does not seem to matter? This is because `text analysis` occurs when your fields are indexed. 

By default, strings are analyzed when it is indexed. The string is broken up into individual words also known as tokens. The analyzer further lowercases each token and removes punctuations. 

![image](https://user-images.githubusercontent.com/60980933/120847933-672cf100-c531-11eb-9b9c-522c354b0e10.png)

**Inverted Index**
![image](https://user-images.githubusercontent.com/60980933/121099236-b33b9800-c7b4-11eb-837b-a914ed8e3725.png)
Once the string is analyzed, the individual tokens are stored in a sorted list known as the `inverted index`. Each unique token is stored in the `inverted index` with its associated ID. 

The same process occurs every time you index a new document. 

![image](https://user-images.githubusercontent.com/60980933/122119940-0d58e080-cde7-11eb-903b-54a628b8de60.png)
![image](https://user-images.githubusercontent.com/60980933/122119962-147fee80-cde7-11eb-915e-3531c405a315.png)
![image](https://user-images.githubusercontent.com/60980933/122119979-19dd3900-cde7-11eb-86e4-274b5e44fec2.png)
![image](https://user-images.githubusercontent.com/60980933/122120075-324d5380-cde7-11eb-9b4e-744dfa6d527d.png)

#### Keyword Field Type
`Keyword` field type is used for aggregations, sorting, and exact searches. These actions look up the document ID to find the values it has in its fields. 

`Keyword` field is suited to perform these actions because it uses a data structure called `doc values` to store data. 

For each document, the document id along with the field value(original string) are added to the table. This data structure(`doc values`) is designed for actions that require looking up the document ID to find the values it has in its fields.

![image](https://user-images.githubusercontent.com/60980933/121603436-fccef180-ca05-11eb-817e-cb77b46ae969.png)

When Elasticsearch dynamically creates a mapping for you, it does not know what you want to use a string for so it maps all strings to both field types. 

In cases where you do not need both field types, the default setting is wasteful. Since both field types require creating either an inverted index or doc values, creating both field types for unnecessary fields will slow down indexing and take up more disk space.

This is why we define our own mapping as it helps us store and search data more efficiently.

### Mapping Exercise

**Project**: Build an app for a client who manages a produce warehouse 

**This app must enable users to:** 
1. search for produce name, country of origin and description

2. identify top countries of origin with the most frequent purchase history

3. sort produce by produce type(Fruit or Vegetable)

4. get the summary of monthly expense

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
![image](https://user-images.githubusercontent.com/60980933/122120548-cc150080-cde7-11eb-8613-a4ec5a5c115e.png)
![image](https://user-images.githubusercontent.com/60980933/121604184-4c61ed00-ca07-11eb-84f8-208c3e927a08.png)
![image](https://user-images.githubusercontent.com/60980933/122100555-8ac52680-cdd0-11eb-843f-556fa5313e15.png)
![image](https://user-images.githubusercontent.com/60980933/121749036-3b78b080-cac7-11eb-8706-561a1bb61315.png)
![image](https://user-images.githubusercontent.com/60980933/121749523-09b41980-cac8-11eb-8214-e986760e5d96.png)
![image](https://user-images.githubusercontent.com/60980933/122100826-d972c080-cdd0-11eb-995c-048e6709ef57.png)

### Defining your own mapping
**Rules**
1. If you do not define a mapping ahead of time, Elasticsearch dynamically creates the mapping for you.
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

The test_index is successfully created. 
![image](https://user-images.githubusercontent.com/60980933/121616770-c81c6380-ca20-11eb-9f3a-593e61eff319.png)

**Step 2: View the dynamic mapping** 

Syntax:
```
GET Name-the-index-whose-mapping-you-want-to-view/_mapping
```

Example:
```
GET test_index/_mapping
```
Expected response from Elasticsearch:

Elasticsearch will display the mapping it has created. It lists the fields in an alphabetical order. This document is identical to the one we indexed into the temp_index. To save space, the screenshots of the mapping has not been included here. 

**Step 3: Edit the mapping**

Copy and paste the mapping from step 2 into the Kibana console. From the pasted results, remove the "test_index" along with its opening and closing brackets. Then, edit the mapping to satisfy the requirements outlined in the figure below.

![image](https://user-images.githubusercontent.com/60980933/122103275-ad0c7380-cdd3-11eb-9a74-babe7442e5b3.png)

The optimized mapping should look like the following: 
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
![image](https://user-images.githubusercontent.com/60980933/122103954-68350c80-cdd4-11eb-82c4-83b47bc364dc.png)

**Step 4: Create a new index with the optimized mapping from step 3.** 

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

Compared to the `dynamic mapping`, our optimized mapping looks more simple and concise!  The current mapping satisfies the requirements that are marked with green check marks. 

![image](https://user-images.githubusercontent.com/60980933/121619679-3879b380-ca26-11eb-9ac9-ed19a52c05c2.png)
![image](https://user-images.githubusercontent.com/60980933/121619466-e5076580-ca25-11eb-8c59-ec2caf3ddb50.png)
![image](https://user-images.githubusercontent.com/60980933/121619506-f5b7db80-ca25-11eb-9916-fea11e8fd79d.png)


**Step 6: Index your dataset into the new index**

For simplicity's sake, we will index two documents. 

*Index the first document*

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

Elasticsearch successfully indexes the first document. 
![image](https://user-images.githubusercontent.com/60980933/121621403-5563b600-ca29-11eb-8ee9-83686a937fd2.png)

*Index the second document*

The second document has almost identical fields as the first document except that it has an extra field called "organic" set to true!
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

Elasticsearch successfully indexes the second document. 
![image](https://user-images.githubusercontent.com/60980933/121621463-73c9b180-ca29-11eb-9849-955b8d7872fb.png)

Let's see what happens to the mapping by sending this request below: 
```
GET produce_index/_mapping
```
Expected response from Elasticsearch:

The new field("organic") and its field type(boolean) have been added to the mapping. This is in line with the rules of mapping we discussed earlier since you can add *new* fields to the mapping. We just cannot change the mapping of an *existing* field! 

![image](https://user-images.githubusercontent.com/60980933/121694928-d2257d00-ca87-11eb-9141-77143d59081a.png)
![image](https://user-images.githubusercontent.com/60980933/121694969-db164e80-ca87-11eb-9ddf-479af3077c46.png)

#### What if you do need to make changes to the mapping of an existing field? 
Let's say your client changed his mind. He wants to run only full text search on the field "botanical_name" we disabled earlier. 

Remember, you CANNOT change the mapping of an *existing* field. If you do need to make changes to an existing field, you must create a new index with the desired mapping, then reindex all documents into the new index. 

**STEP 1: Create a new index(produce_v2) with the latest mapping.**

We removed the "enabled" parameter from the field "botanical_name" and changed its type to "text". 

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

If you check the mapping, you will see that the filed "botanical_name" has been typed as text. 

**View the mapping of produce_v2:**
```
GET produce_v2/_mapping
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/121725730-e036c600-caa6-11eb-8b9d-5a9ca0a72a3c.png)
![image](https://user-images.githubusercontent.com/60980933/121725765-e88f0100-caa6-11eb-9610-97cbf980e6c2.png)

**STEP 2: Reindex the data from the original index(produce_index) to the one you just created(produce_v2).**
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
Expected response from Elasticsearch:

This request reindexes data from the produce_index to the produce_v2 index. The produce_v2 index can now be used to run the requests that the client has specified.

![image](https://user-images.githubusercontent.com/60980933/121726550-ee391680-caa7-11eb-89a9-be1d4416e0e3.png)

#### Runtime Field
![image](https://user-images.githubusercontent.com/60980933/122100555-8ac52680-cdd0-11eb-843f-556fa5313e15.png)
![image](https://user-images.githubusercontent.com/60980933/121749036-3b78b080-cac7-11eb-8706-561a1bb61315.png)
![image](https://user-images.githubusercontent.com/60980933/121749523-09b41980-cac8-11eb-8214-e986760e5d96.png)

**Step 1: Create a `runtime field` and add it to the mapping of the existing index.** 

Syntax:
```
PUT Enter-name-of-index/_mapping
{
  "runtime": {
    "Name-your-runtime-field-here": {
      "type": "Specify-field-type-here",
      "script": {
        "source": "Specify the formula you want executed"
      }
    }
  }
}
```
Example:
```
PUT produce_v2/_mapping
{
  "runtime": {
    "total": {
      "type": "double",
      "script": {
        "source": "emit(doc['unit_price'].value* doc['quantity'].value)"
      }
    }
  }
}
```
Expected response from Elasticsearch: 

Elasticsearch successfully adds the `runtime field` to the mapping. 
![image](https://user-images.githubusercontent.com/60980933/121744031-8393d500-cabf-11eb-850c-2e13cf79a92a.png)

**Step 2: Check the mapping:**
```
GET produce_v2/_mapping
```
Expected response from Elasticsearch:

Elasticsearch adds a `runtime field` to the mapping(red box).

![image](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yi1u4cpx7gl65udiuvr3.png)

Note that the `runtime field` is not listed under "properties" object which includes the fields in our documents. This is because the `runtime field` "total" is not indexed!

**Step 3: Run a request on the `runtime field` to see it perform its magic!** 

Please note that the following request does not aggregate the monthly expense here. We are running a simple aggregation request to demonstrate how `runtime field` works!  

The following request runs a sum aggregation against the `runtime field` total of all documents in our index. 

Syntax:
```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "Specify the aggregation type here": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```

Example:
```
GET produce_v2/_search
{
  "size": 0,
  "aggs": {
    "total_expense": {
      "sum": {
        "field": "total"
      }
    }
  }
}
```
Expected response from Elasticsearch:

When this request is sent, a `runtime field` called "total" is created and calculated for documents within the scope of our request(entire index). Then, the sum aggregation is ran on the field "total" over all documents in our index.

![image](https://user-images.githubusercontent.com/60980933/121815555-50268700-cc34-11eb-8fb4-8112cb8e0806.png)

The `runtime field` is only created and calculated when a request made on the `runtime field` is being executed. `Runtime fields` are not indexed so these do not take up disk space.  

We also did not have to reindex in order to add a new field to existing documents. For more information on runtime fields, check out this [blog](https://www.elastic.co/blog/introducing-elasticsearch-runtime-fields)! 

### Questions from the workshop
**Q:  If possible please explain the _meta in mapping which was part of previous video.**

A: Of course! _meta in mapping this question is referring to [workshop part 4](https://github.com/LisaHJung/Part-4-Running-Aggregations-with-Elasticsearch-and-Kibana).

![image](https://user-images.githubusercontent.com/60980933/122953873-3247d900-d33c-11eb-8c77-d3f344ddbf43.png)

I should have just removed the _meta part before I published this repo. Thank you for submitting a pull request on GitHub @radhakrishnaakamat!

So the _meta field was automatically created by the ml file data visualizer. This is a field where you can store any information regarding the index or the app for developers who are managing it. Think of this field as a place where you can include information regarding the app so developers have info necessary to debug.

The _meta field is optional and deleting the _meta field will not affect the mapping in any way whatsoever. I am going to delete this field in my part 4 workshop repo as it has been causing a lot of confusion! 

**Q:  After you create a new mapping, how do you configure your ingest to use the new mapping?**


A: I should have asked for clarification as this question can be interpreted in many different ways. 

If I didn't interpret it correctly, please let me know via Twitter @LisaHJung and I will add the answer to this repo! 

If you were referring to a situation where you have an old index with outdated mapping that needed to be changed:

Remember, we cannot change the mapping of an existing field. Even if you add a new field to a mapping, it only adds the new field to the list of field names and types. It does not add the new field to documents that have been indexed prior to adding a new field to the mapping.

You must create a new index with the desired mapping, then reindex documents from the old index to the new one, and direct requests to the new index! 


