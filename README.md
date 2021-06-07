# Beginner's Crash Course to Elastic Stack Series
## Part 5: Understanding Mapping with Elasticsearch and Kibana
Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 5: Understanding Mapping with Elasticsearch and Kibana.

Have you ever encountered an error “Field type is not supported for [whatever you are trying to do with Elasticsearch]”?

The most likely culprit of this error is the mapping of your index!

Mapping is the process of defining how a document and its fields are indexed and stored. It defines the data type and format of the fields in the documents. As a result, mapping can significantly affect how Elasticsearch searches and stores data.

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

![image](https://user-images.githubusercontent.com/60980933/120678079-f912fc00-c454-11eb-80ef-147329337628.png)

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
  "quantity": 200,
  "unit_price": 3.11,
  "description": "a large juicy tropical fruit consisting of aromatic edible yellow flesh surrounded by a tough segmented skin and topped with a tuft of stiff leaves.These pineapples are sourced from New Zealand.",
  "vendor_details": {
    "vendor": "Tropical Fruit Growers of New Zealand",
    "main_contact": "Hugh Rose",
    "phone_number": "011-506-2479-2000",
    "country": "New Zealand",
    "date_received": "2020-06-02T12:15:35",
    "preferred vendor": true
  }
}
```
Expected response from Elasticsearch:

Elasticsearch will confirm that this document has been successfully indexed in the temp_index. 
![image](https://user-images.githubusercontent.com/60980933/120387213-d5ca3e80-c2e6-11eb-8ca8-731222174724.png)

## Mapping
Mapping defines how a document and its fields are indexed and stored. 

![image](https://user-images.githubusercontent.com/60980933/120681399-915eb000-c458-11eb-9a32-2d121c93b3e8.png)

It contains a list of names and data types of the fields of an index. It also includes information about how the fields should be indexed and stored by Lucene! 

![image](https://user-images.githubusercontent.com/60980933/120688263-1e593780-c460-11eb-9963-87ac8689e2fc.png)

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

![image](https://user-images.githubusercontent.com/60980933/118303597-da979180-b4a2-11eb-83ae-bb7d10514fe2.png)
![image](https://user-images.githubusercontent.com/60980933/118303631-e7b48080-b4a2-11eb-98d7-ec3d724ea9dc.png)
![image](https://user-images.githubusercontent.com/60980933/118303655-eedb8e80-b4a2-11eb-8998-68b32bdbe4a6.png)

For list of all data types, click [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)!

### Indexing Strings 
There are two kinds of string data types:
1. Text
2. Keyword

By default, every string gets mapped twice as a text field and as a keyword multi-field. Each data type is primed for different types of searches. 

`Text` is designed for full-text searches. 

`Keyword` is designed for exact searches, aggregations, and sorting.

You can customize your mapping by choosing either text or keyword only or both! 

#### Text Data type
**Text Analysis**
Ever notice that when you search in Elasticsearch, it is not case sensitive or punctuation does not seem to matter? This is because `text analysis` occurs when your fields are indexed. 

By default, strings are analyzed when it is indexed. The string is broken up into individual words also known as tokens. The analyzer further lowercases each token and removes punctuations. 

![image](https://user-images.githubusercontent.com/60980933/120847933-672cf100-c531-11eb-9b9c-522c354b0e10.png)

**Inverted index**
![image](https://user-images.githubusercontent.com/60980933/120848012-7ad85780-c531-11eb-86c4-e972cfb3abb2.png)
Once the string is analyzed, the individual tokens are stored in a sorted list known as the `inverted index`. Each unique token is stored in the index with its relevant ID. 

The same process occurs every time you index a new document. 

![image](https://user-images.githubusercontent.com/60980933/120848060-8c216400-c531-11eb-9fed-6cfd5b50d7c8.png)
![image](https://user-images.githubusercontent.com/60980933/120851449-14096d00-c536-11eb-88d6-08add98441db.png)

![image](https://user-images.githubusercontent.com/60980933/120851817-985bf000-c536-11eb-8e5e-3c54b4304258.png)

#### Keyword data type
Keyword data type is used for aggregations, sorting, and exact searches. These types ofo searches require original strings therefore the field typed Keyword is not analyzed. 

![image](https://user-images.githubusercontent.com/60980933/120907913-d7676f80-c622-11eb-8351-8c79894b9eae.png)

Keyword data type is used for aggregations, sorting, and exact searches. These types of searches require original strings therefore the field typed Keyword is not analyzed. 

### Defining your own mapping
Step 1: Index a sample of documents into a temporary index. 
This document should closely resemble the fields and values of the dataset you want to index. 

Syntax:
```
POST Name-of-temporary-index/_doc
{
  "field": "value"
}
```

Example:
```
```
Step 2: View the dynamic mapping 

Syntax:
```
GET Name-of-temporary-index/_mapping
```

Example:
```
GET produce_temp/_mapping
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
