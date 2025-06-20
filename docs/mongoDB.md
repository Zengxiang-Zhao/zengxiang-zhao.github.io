---
layout: default
title: MongoDB
date:   2024-01-11 20:28:05 -0400
categories: MongoDB
nav_order: 12

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Aggregation pipeline

Check the official document for [pipeline stages](https://www.mongodb.com/docs/manual/reference/operator/aggregation-pipeline/)

You can use aggregation pipeline to get the documents based on complex conditions.

```python
def searchFusions():
    tumorType = request.form.get('tumorType',None)
    geneName1 = request.form.get('geneName1',None)
    geneName2 = request.form.get('geneName2',None)
    tier = request.form.get('tier',None)
    
    
    listAnd = []
    if geneName1:
        listAnd.append({"$regexMatch":{ "input": "$$fusion.gene" , "regex": f"{geneName1}", "options": "i" }})
    # because gene is the second level of dict, so use double $: $$
    # Do Not use f"/{geneName1}/" as the CLI format
    if geneName2:
        listAnd.append({"$regexMatch":{ "input": "$$fusion.gene" , "regex": f"{geneName2}", "options": "i" }})
    if tier:
        listAnd.append({"$eq":["$$fusion.tier",tier]})

    stageMatchTumorType = {}
    if tumorType:
        stageMatchTumorType = {
            '$match':{'dictCaseInfo.tumorType':{'$regex':tumorType,'$options':'i'}}
        }

    stageProjectFilter = {
        "$project":{
            "listFusion":{
                "$filter":{
                    "input":"$listFusion",
                    "as":"fusion",
                    "cond": {"$and":listAnd},
                },
            },
            'dictCaseInfo':True
            
        
        }
    }

    stageProjectFileds = {
        "$project":{
            '_id':True,
            'listFusion':True,
            'dictCaseInfo':True,
            "numberOfFusion":{"$size":"$listFusion"},

        }
    }

    stageMatchRemoveEmptyResult = {
        "$match":{
        "numberOfFusion":{"$gt":0}
        }
    }

    # narrow down the documents and arrnge the results
    pipeline = [stageMatchTumorType,stageProjectFilter,stageProjectFileds,stageMatchRemoveEmptyResult]
    pipeline = [element for element in pipeline if len(element) > 0]

    listRecord = collectionReport.aggregate(pipeline)
    
    listRecord = list(listRecord)

    return jsonify(listFormatRecord)
```

## update array using filter

if you'd like to update an element in an array 

```python
query = {'_id':ObjectId(ID)}
update = {"$set":{f'listArray.$[element]':updateStuff}}
arrayFilter = [{f"element.ID2":ID2}]
result = collectionImportant.update_one(filter=query,update=update,array_filters=arrayFilter)
```

## restart MongoDB

When the server crashed down, and you failed to connect to the mongoDB server after typing `mongosh`. Then you need to restart the mongoDB server as shown below.

```bash
sudo service mongod restart
```

## projection in pyMongo

```python
query = {'status':'active', 'testOrdered':testOrdered,'panel':panel,'specimen':specimen}
list_tag = list(collection_rule_extraction.find(query, projection={'_id':False,'tag':True}))
list_tag = [rule['tag'] for rule in list_tag]
```

You need to use `True` or `False` to specify which filed needed to be returned. 

## [Copy one collection from one database to another](https://stackoverflow.com/questions/11554762/how-to-copy-a-collection-from-one-database-to-another-in-mongodb)

1. Install mongosh
2. In the shell using the command line to backup: mongodump -d some_database -c some_collection (e.g. mongodump -d comment -c comment_ST)
3. restore the collection to another database: mongorestore -d some_other_db -c some_or_other_collection dump/some_collection.bson (e.g. mongorestore -d report -c comment_ST dump/comment/comment_ST.bson)
4. Done!

## [Remove one filed in collection](https://stackoverflow.com/questions/6851933/how-to-remove-a-field-completely-from-a-mongodb-document)

```bash
db.example.update({}, {$unset: {words:1}} , {multi: true});
```

Add one filed in collection:
```bash
db.example.update({}, {$set: {'status':'active'}} , {multi: true});
```

## [Rename the filed in collection](https://stackoverflow.com/questions/9254351/how-can-i-rename-a-field-for-all-documents-in-mongodb)

go into the mongosh command line interface

```bash
db.collectionName.update({}, {
    $rename: {
        "old": "new"
    }
}, false, true);


//e.g.
db.foo.update({}, {
    $rename: {
        "name.additional": "name.last"
    }
}, false, true);

```

The false, true in the method above are: { upsert:false, multi:true }. You need the multi:true to update all your records.

## [append an item to an array in mongoDB](https://stackoverflow.com/questions/33189258/append-item-to-mongodb-document-array-in-pymongo-without-re-insertion)

```python
coll.update({'ref': ref}, {'$push': {'tags': new_tag}})
```
