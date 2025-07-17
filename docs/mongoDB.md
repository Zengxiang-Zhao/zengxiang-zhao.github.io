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

## [Performing regex queries with PyMongo](https://stackoverflow.com/questions/3483318/performing-regex-queries-with-pymongo)


```bash
        listTumorType = tumorType.split('/')

        query = {} # build multiple fields query

        tumorType_str = '|'.join(listTumorType) 
        patternTumorType = re.compile(rf"\b{tumorType_str}\b") 
        query['tumorType'] = patternTumorType

        patternGeneName = re.compile(rf"\b{geneName}\b",re.IGNORECASE)
        query['geneName'] = patternGeneName

        patternScope = re.compile(rf"\b{scope}\b",re.IGNORECASE)
        query['scope'] = patternScope

        result =list(collectionComment.find(query,{'listComment':True,'listReference':True,'_id':False}))
```

Note:
1. `r""`: Defines a raw string, which prevents backslashes from being interpreted as escape sequences.
2. `\b`: Matches a word boundary.E.g.  `r"\b(apple|banana)\b"` This ensures that only whole words "apple" or "banana" are matched, not parts of other words (e.g., "pineapple").
3. `|`: Acts as an "OR" operator, matching either the pattern before or after it.

## How to create a volume and launch a mongoDB container using that volume

1. Create a volume that mount to your local path
    ```bash
    docker volume create --driver local \
      --opt type=none \
      --opt device=/path/to/your/local \ # change it to your data path
      --opt o=bind \
      volume_name # change it to your volume name
    
    ```

2. Check the volumne and inspect
   ```bash
   docker volume ls

   docker volume inspect volume_name # using this command you can find the mount details
  
   ```
3. create the mongodb container using the volume
   ```bash
   docker run -it --rm \ # change ` -it --rm` to `-d --restart always ` to make the container run backend and restart always
     --name container-mongodb \ # change to your name
     --hostname my-mongodb \ # change to your name
     -v volume_name:/data/db \
     -p 27017:27017 \ # the port may need to change if you have already used 27017. E.g. `-p 27018:27017`
     --network share_network_with_other_containers \ # change to your network
     mongo:latest

   ```
5. Check the container
   ```bash
   docker container ls  | grep mongodb
   ```
7. Test the mongodb
   ```bash
   mongosh --port 27017 # change the port number to the number you used above
   ```
9. 

## Transfer database from one server to another server

1. Copy the database: `mongodump` : `mongodump --db db_name --archive=./mongodbBackup/db_name.dump --gzip`
2. Scp transfer the dump file: `scp db_name.yml  server_name@ip_address:path_to_save`
3. login another server
4. Restore the database: `mongorestore --gzip --archive=db_name.dump`

It will restore the database to the default mongodb save path. You can find the save path at `/etc/mongod.conf`

## [Install mongodb in Centos7](https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-centos-7)

1. Create a file : `sudo vi /etc/yum.repos.d/mongodb-org.repo`
2. Enter the content at /etc/yum.repos.d/mongodb-org.repo:
   ```bash
    [mongodb-org-6.0]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/7/mongodb-org/6.0/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-6.0.asc
   ```
3. Install the package: `sudo yum install mongodb-org`
4. Start : `sudo systemctl start mongod`
5. Check Status: `sudo systemctl status mongod`
6. If you'd like to change the /etc/mongo.conf, such as change the dbPath, then you should use the command to restart the Mongodb service: `sudo service mongod restart`
7. If you change the dbPath to a location that the mongodb have no permission to modify, then the status of mongodb is failed. you need to change it back or change the folder permission.


Uninstall the Mongodb :
1. Stop : `sudo service mongod stop`
2. Remove the installed package: `sudo yum erase $(rpm -qa | grep mongodb-org)`
3. Remove data files:
   ```bash
   sudo rm -r /var/log/mongodb
   sudo rm -r /var/lib/mongo

   ```


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
