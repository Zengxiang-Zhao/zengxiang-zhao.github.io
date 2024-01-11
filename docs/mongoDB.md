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
## [Copy one collection from one database to another](https://stackoverflow.com/questions/11554762/how-to-copy-a-collection-from-one-database-to-another-in-mongodb)

1. Install mongosh
2. In the shell using the command line to backup: mongodump -d some_database -c some_collection (e.g. mongodump -d comment -c comment_ST)
3. restore the collection to another database: mongorestore -d some_other_db -c some_or_other_collection dump/some_collection.bson (e.g. mongorestore -d report -c comment_ST dump/comment/comment_ST.bson)
4. Done!
