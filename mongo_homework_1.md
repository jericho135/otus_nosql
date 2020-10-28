Экземпляр mongodb установлен на виртуальной машине
```bash
[root@localhost tmp]# free -m
              total        used        free      shared  buff/cache   available
Mem:           1837         331        1011          30         494        1333
Swap:          2047           0        2047
[root@localhost tmp]# cat /proc/cpuinfo | grep processor | wc -l
3

[root@localhost tmp]# less /etc/*release*
CentOS Linux release 7.7.1908 (Core)

[root@localhost tmp]# sudo dmidecode | grep -i -e manufacturer -e product -e vendor
        Vendor: innotek GmbH
        Manufacturer: innotek GmbH
        Product Name: VirtualBox
        Manufacturer: Oracle Corporation
        Product Name: VirtualBox
        Manufacturer: Oracle Corporation

```
В качестве тренировочного даатсета был выбран датасет companies. 

```bash
[root@localhost tmp]# wget https://raw.githubusercontent.com/ozlerhakan/mongodb-json-files/master/datasets/companies.json
--2020-10-28 16:43:03--  https://raw.githubusercontent.com/ozlerhakan/mongodb-json-files/master/datasets/companies.json
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.128.133, 151.101.192.133, 151.101.0.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.128.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 78231204 (75M) [text/plain]
Saving to: ‘companies.json’
```
Датасет скачан, ниже создается база данных и новая коллекция
```bash
[root@localhost tmp]# mongo
MongoDB shell version v4.2.9
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
	> show dbs;
	admin   0.000GB
	config  0.000GB
	local   0.000GB
	> use otus;
	switched to db otus
	> db.createCollection("companies");
	{ "ok" : 1 }
```
С помощью mongoimport датасет закачивается в созданную коллекцию
```bash
[root@localhost tmp]# mongoimport --db otus --collection companies  --file /tmp/companies.json
2020-10-28T16:50:56.989+0300    connected to: mongodb://localhost/
2020-10-28T16:51:00.181+0300    [#######################.] otus.companies       74.2MB/74.6MB (99.5%)
2020-10-28T16:51:00.219+0300    [########################] otus.companies       74.6MB/74.6MB (100.0%)
2020-10-28T16:51:00.219+0300    18801 document(s) imported successfully. 0 document(s) failed to import.
```
Проверка, что данные загрузились в коллекцию 
```bash
[root@localhost tmp]# mongo
	> show dbs;
	admin   0.000GB
	config  0.000GB
	local   0.000GB
	otus    0.023GB
	> use otus;
	switched to db otus
	> show collections;
	companies
	> db.companies.find().limit(3);
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d8b"), "name" : "AdventNet", "permalink" : "abc3", "crunchbase_url" : "http://www.crunchbase.com/company/adventnet", "homepage_url" : "http://adventnet.com", "blog_url" : "", "blog_feed_url" : "", "twitter_username" : "manageengine", "category_code" : "enterprise", "number_of_employees" : 600, "founded_year" : 1996, "deadpooled_year" : 2, "tag_list" : "", "alias_list" : "Zoho ManageEngine ", "email_address" : "pr@adventnet.com", "phone_number" : "925-924-9500", "description" : "Server Management Software", "created_at" : ISODate("2007-05-25T19:24:22Z"), "updated_at" : "Wed Oct 31 18:26:09 UTC 2012", "overview" : "<p>AdventNet is now <a href=\"/company/zoho-manageengine\" title=\"Zoho ManageEngine\" rel=\"nofollow\">Zoho ManageEngine</a>.</p>\n\n<p>Founded in 1996, AdventNet has served a diverse range of enterprise IT, networking and telecom customers.</p>\n\n<p>AdventNet supplies server and network management software.</p>", "image" : { "available_sizes" : [ [ [ 150, 55 ], "assets/images/resized/0001/9732/19732v1-max-150x150.png" ], [ [ 150, 55 ], "assets/images/resized/0001/9732/19732v1-max-250x250.png" ], [ [ 150, 55 ], "assets/images/resized/0001/9732/19732v1-max-450x450.png" ] ] }, "products" : [ ], "relationships" : [ { "is_past" : true, "title" : "CEO and Co-Founder", "person" : { "first_name" : "Sridhar", "last_name" : "Vembu", "permalink" : "sridhar-vembu" } }, { "is_past" : true, "title" : "VP of Business Dev", "person" : { "first_name" : "Neil", "last_name" : "Butani", "permalink" : "neil-butan...".... и т.д.
	```	
	
	Вывести первые 20 полей name без сортировки, по умолчанию выводится и поле _id, если его явно не убрать 
	```bash
	> db.companies.find({}, {name:1}).limit(20)	
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d8b"), "name" : "AdventNet" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d8a"), "name" : "Wetpaint" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d8c"), "name" : "Zoho" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d8d"), "name" : "Digg" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d90"), "name" : "Postini" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d91"), "name" : "Geni" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d8f"), "name" : "Omnidrive" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d92"), "name" : "Flektor" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d93"), "name" : "Fox Interactive Media" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d95"), "name" : "StumbleUpon" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d96"), "name" : "Gizmoz" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d97"), "name" : "Scribd" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d98"), "name" : "Slacker" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d99"), "name" : "Lala" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d9a"), "name" : "Helio" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d8e"), "name" : "Facebook" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d9c"), "name" : "MeetMoi" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d9d"), "name" : "Joost" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d9e"), "name" : "CBS" }
	{ "_id" : ObjectId("52cdef7c4bab8bd675297d94"), "name" : "Twitter" }
	```
	Убрали _id, теперь выводятся только name 
	```bash
	> db.companies.find({}, {name:1, _id: 0}).limit(20)	
	{ "name" : "AdventNet" }
	{ "name" : "Wetpaint" }
	{ "name" : "Zoho" }
	{ "name" : "Digg" }
	{ "name" : "Postini" }
	{ "name" : "Geni" }
	{ "name" : "Omnidrive" }
	{ "name" : "Flektor" }
	{ "name" : "Fox Interactive Media" }
	{ "name" : "StumbleUpon" }
	{ "name" : "Gizmoz" }
	{ "name" : "Scribd" }
	{ "name" : "Slacker" }
	{ "name" : "Lala" }
	{ "name" : "Helio" }
	{ "name" : "Facebook" }
	{ "name" : "MeetMoi" }
	{ "name" : "Joost" }
	{ "name" : "CBS" }
	{ "name" : "Twitter" }
	```
	Выводятся первые 20 полей name по возрастающей
	```bash
	> db.companies.find({}, {name:1, _id: 0}).sort({name:1}).limit(20) 
	{ "name" : "(fluff)Friends" }
	{ "name" : "*faircompanies" }
	{ "name" : "/community" }
	{ "name" : "1 800 vending" }
	{ "name" : "1 to 101" }
	{ "name" : "1 to 101" }
	{ "name" : "1-800-905-GEEK" }
	{ "name" : "1000 Markets" }
	{ "name" : "1000MIKES" }
	{ "name" : "100for100 Web Hosting" }
	{ "name" : "101 Holidays" }
	{ "name" : "10East" }
	{ "name" : "10to1" }
	{ "name" : "111pix" }
	{ "name" : "12 Inch Design" }
	{ "name" : "123i" }
	{ "name" : "123people" }
	{ "name" : "12seconds" }
	{ "name" : "12snap Mobile Advertising and Entertainment" }
	{ "name" : "1366 Technologies" }
	```
	Выводятся первые 20 полей name по убывающей
	```bash
	> db.companies.find({}, {name:1, _id: 0}).sort({name:-1}).limit(20) 
	{ "name" : "zyntroPICS" }
	{ "name" : "zweitgeist" }
	{ "name" : "zip2" }
	{ "name" : "zip realty" }
	{ "name" : "zintin" }
	{ "name" : "zimtkorn" }
	{ "name" : "zembly" }
	{ "name" : "zebraspot design" }
	{ "name" : "zeaLOG" }
	{ "name" : "zappn" }
	{ "name" : "zanox" }
	{ "name" : "zahdoo" }
	{ "name" : "zSoup" }
	{ "name" : "zSlide" }
	{ "name" : "youserbase" }
	{ "name" : "youcalc" }
	{ "name" : "youcalc" }
	{ "name" : "youCharades" }
	{ "name" : "yoonew" }
	{ "name" : "yiid" }
	```
	Вывести весь документ, где name = "1000MIKES" <=> SELECT * FROM companies WHERE name = '1000MIKES'
	```bash
	> db.companies.find({name: "1000MIKES"})	
	{ "_id" : ObjectId("52cdef7e4bab8bd67529a6f8"), "name" : "1000MIKES", "permalink" : "1000mikes", "crunchbase_url" : "http://www.crunchbase.com/company/1000mikes", "homepage_url" : "http://www.1000mikes.com", "blog_url" : "http://blog.1000mikes.com", "blog_feed_url" : "http://blog.1000mikes.com/de/index.rdf", "twitter_username" : null, "category_code" : "web", "number_of_employees" : 5, "founded_year" : 2007, "founded_month" : 11, "founded_day" : 1, "deadpooled_year" : null, "deadpooled_month" : null, "deadpooled_day" : null, "deadpooled_url" : null, "tag_list" : "audio-radio-live", "alias_list" : null, "email_address" : "info@1000mikes.com", "phone_number" : "+49 40 8797691-0", "description" : null, "created_at" : "Sun Dec 07 21:26:02 UTC 2008", "updated_at" : "Sun Dec 14 18:25:49 UTC 2008", "overview" : "<p>1000MIKES is Radio 2.0. Its users run their own live and interactive radio channels. They broadcast directly via their phone, or via the Internet using the 1000MIKES web phone that allows to stream with high audio quality. Past broadcasts can be put under a Creative Commons license and are available via RSS as a podcast or in the 1000MIKES archive. </p>\n\n<p>1000MIKES started April 2008 with its German version. It is very popular in Germany for live Sport coverage where fans broadcast themselves from soccer, ice hockey and basketball games. Also very popular are talk shows, where guests call in to the show, or event radios. </p>\n\n<p>Since autumn 2008, an English version is available. </p>", "image" : { "available_sizes" : [ [ [ 149, 150 ], "assets/images/resized/0003/1233/31233v2-max-150x150.jpg" ], [ [ 169, 170 ], "assets/images/resized/0003/1233/31233v2-max-250x250.jpg" ], [ [ 169, 170 ], "assets/images/resized/0003/1233/31233v2-max-450x450.jpg" ] ], "attribution" : null }, "products" : [ ], "relationships" : [ { "is_past" : false, "title" : "CEO", "person" : { "first_name" : "Frank Felix", "last_name" : "Debatin", "permalink" : "frank-felix-debatin" } }, { "is_past" : false, "title" : "CFO", "person" : { "first_name" : "Peter", "last_name" : "Schweyer", "permalink" : "peter-schweyer" } } ], "competitions" : [ { "competitor" : { "name" : "Blog Talk Radio", "permalink" : "blog-talk-radio" } }, { "competitor" : { "name" : "TalkShoe", "permalink" : "talkshoe" } } ], "providerships" : [ ], "total_money_raised" : "$0", "funding_rounds" : [ ], "investments" : [ ], "acquisition" : null, "acquisitions" : [ ], "offices" : [ { "description" : "Headquarters", "address1" : "BernstorffstraÃŸe 99", "address2" : "", "zip_code" : "22765", "city" : "Hamburg", "state_code" : null, "country_code" : "DEU", "latitude" : 53.5571845, "longitude" : 9.9584473 } ], "milestones" : [ ], "ipo" : null, "video_embeds" : [ ], "screenshots" : [ { "available_sizes" : [ [ [ 150, 112 ], "assets/images/resized/0003/1232/31232v2-max-150x150.jpg" ], [ [ 250, 187 ], "assets/images/resized/0003/1232/31232v2-max-250x250.jpg" ], [ [ 450, 337 ], "assets/images/resized/0003/1232/31232v2-max-450x450.jpg" ] ], "attribution" : null } ], "external_links" : [ ], "partners" : [ ] }
	```
	 Вывести только поле crunchbase_url из документа, где name = "1000MIKES" и  <=> SELECT crunchbase_url FROM companies WHERE name = '1000MIKES'
	 ```bash
	> db.companies.find({name: "1000MIKES"}, {crunchbase_url: 1}) 
	{ "_id" : ObjectId("52cdef7e4bab8bd67529a6f8"), "crunchbase_url" : "http://www.crunchbase.com/company/1000mikes" }
	```
	 Обновить один документ, подставить вместо "http://www.crunchbase.com/company/1000mikes" строку "test_test_test", где name = "1000MIKES"
	 ```bash
	> db.companies.updateOne(       { "name" : "1000MIKES" },       { $set: { "crunchbase_url" : "test_test_test" } }    );
	{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
	```
	Проверка, что обновился
	```bash
	> db.companies.find({name: "1000MIKES"}, {crunchbase_url: 1})
	{ "_id" : ObjectId("52cdef7e4bab8bd67529a6f8"), "crunchbase_url" : "test_test_test" }
	```
	Найти все документы, где поле name = "pacani-pacan4iki"
	```bash
	> db.companies.find({name: "pacani-pacan4iki"})
	>
	```
	Таких нет, еще посчитаем явно, чтобы убедиться
	```bash
	> db.companies.find({name: "pacani-pacan4iki"}).count()
	0
	```
	Вставим в коллекцию новый документ и проверим, что он вставился
	```bash
	> db.companies.insert({name: "pacani-pacan4iki", "crunchbase_url": "http://pacani_pacan4iki.com", qty: 15})
	WriteResult({ "nInserted" : 1 })
	>  db.companies.find({name: "pacani-pacan4iki"}).count()
	1
	```
	Проверим планы выполнения аналога запроса SELECT * FROM companies WHERE name = 'pacani-pacan4iki'
	```bash
	> db.companies.find({name: "pacani-pacan4iki"}).explain("verbose")
	{
			"queryPlanner" : {
					"plannerVersion" : 1,
					"namespace" : "otus.companies",
					"indexFilterSet" : false,
					"parsedQuery" : {
							"name" : {
									"$eq" : "pacani-pacan4iki"
							}
					},
					"queryHash" : "01AEE5EC",
					"planCacheKey" : "01AEE5EC",
					"winningPlan" : {
							"stage" : "COLLSCAN",
							"filter" : {
									"name" : {
											"$eq" : "pacani-pacan4iki"
									}
							},
							"direction" : "forward"
					},
					"rejectedPlans" : [ ]
			},
			"serverInfo" : {
					"host" : "localhost.localdomain",
					"port" : 27017,
					"version" : "4.2.9",
					"gitVersion" : "06402114114ffc5146fd4b55402c96f1dc9ec4b5"
			},
			"ok" : 1
	}
	> db.companies.find({name: "pacani-pacan4iki"}).explain("allPlansExecution")
	{
			"queryPlanner" : {
					"plannerVersion" : 1,
					"namespace" : "otus.companies",
					"indexFilterSet" : false,
					"parsedQuery" : {
							"name" : {
									"$eq" : "pacani-pacan4iki"
							}
					},
					"winningPlan" : {
							"stage" : "COLLSCAN",
							"filter" : {
									"name" : {
											"$eq" : "pacani-pacan4iki"
									}
							},
							"direction" : "forward"
					},
					"rejectedPlans" : [ ]
			},
			"executionStats" : {
					"executionSuccess" : true,
					"nReturned" : 1,
					"executionTimeMillis" : 9,
					"totalKeysExamined" : 0,
					"totalDocsExamined" : 18802,
					"executionStages" : {
							"stage" : "COLLSCAN",
							"filter" : {
									"name" : {
											"$eq" : "pacani-pacan4iki"
									}
							},
							"nReturned" : 1,
							"executionTimeMillisEstimate" : 0,
							"works" : 18804,
							"advanced" : 1,
							"needTime" : 18802,
							"needYield" : 0,
							"saveState" : 146,
							"restoreState" : 146,
							"isEOF" : 1,
							"direction" : "forward",
							"docsExamined" : 18802
					},
					"allPlansExecution" : [ ]
			},
			"serverInfo" : {
					"host" : "localhost.localdomain",
					"port" : 27017,
					"version" : "4.2.9",
					"gitVersion" : "06402114114ffc5146fd4b55402c96f1dc9ec4b5"
			},
			"ok" : 1
	}
```
	Как видно, оптимизатор выбрал сканирование COLLSCAN, выполнил запрос за 9мс и просканировал 18802 документа
	```bash
	"winningPlan" : {
							"stage" : "COLLSCAN",
							"filter" : {
									"name" : {
											"$eq" : "pacani-pacan4iki"
									}
							},
							"direction" : "forward"
	"executionTimeMillis" : 9,
	"totalKeysExamined" : 0,
	"totalDocsExamined" : 18802
	```
	Создадим индекс по полю name по возрастающей и посмотрим план
	```bash
	> db.companies.createIndex( { name: 1 } )
	{
			"createdCollectionAutomatically" : false,
			"numIndexesBefore" : 1,
			"numIndexesAfter" : 2,
			"ok" : 1
	}

	> db.companies.find({name: "pacani-pacan4iki"}).explain("allPlansExecution")
	{
        "queryPlanner" : {
                "plannerVersion" : 1,
                "namespace" : "otus.companies",
                "indexFilterSet" : false,
                "parsedQuery" : {
                        "name" : {
                                "$eq" : "pacani-pacan4iki"
                        }
                },
                "winningPlan" : {
                        "stage" : "FETCH",
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "keyPattern" : {
                                        "name" : 1
                                },
                                "indexName" : "name_1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "name" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "forward",
                                "indexBounds" : {
                                        "name" : [
                                                "[\"pacani-pacan4iki\", \"pacani-pacan4iki\"]"
                                        ]
                                }
                        }
                },
                "rejectedPlans" : [ ]
        },
        "executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 1,
                "executionTimeMillis" : 0,
                "totalKeysExamined" : 1,
                "totalDocsExamined" : 1,
                "executionStages" : {
                        "stage" : "FETCH",
                        "nReturned" : 1,
                        "executionTimeMillisEstimate" : 0,
                        "works" : 2,
                        "advanced" : 1,
                        "needTime" : 0,
                        "needYield" : 0,
                        "saveState" : 0,
                        "restoreState" : 0,
                        "isEOF" : 1,
                        "docsExamined" : 1,
                        "alreadyHasObj" : 0,
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "nReturned" : 1,
                                "executionTimeMillisEstimate" : 0,
                                "works" : 2,
                                "advanced" : 1,
                                "needTime" : 0,
                                "needYield" : 0,
                                "saveState" : 0,
                                "restoreState" : 0,
                                "isEOF" : 1,
                                "keyPattern" : {
                                        "name" : 1
                                },
                                "indexName" : "name_1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "name" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "forward",
                                "indexBounds" : {
                                        "name" : [
                                                "[\"pacani-pacan4iki\", \"pacani-pacan4iki\"]"
                                        ]
                                },
                                "keysExamined" : 1,
                                "seeks" : 1,
                                "dupsTested" : 0,
                                "dupsDropped" : 0
                        }
                },
                "allPlansExecution" : [ ]
        },
        "serverInfo" : {
                "host" : "localhost.localdomain",
                "port" : 27017,
                "version" : "4.2.9",
                "gitVersion" : "06402114114ffc5146fd4b55402c96f1dc9ec4b5"
        },
        "ok" : 1
	}
```
Как видно из плана, оптимизатор выбрал сканирование индекса IXSCAN, выполнил операцию за 0мс и просканировал 1 документ вместо 18802
```bash
	"winningPlan" : {
                        "stage" : "FETCH",
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "keyPattern" : {
                                        "name" : 1
                                },
                                "indexName" : "name_1"
	"executionTimeMillis" : 0,
                "totalKeysExamined" : 1,
                "totalDocsExamined" : 1,
```
Посмотрим, как меняется план запроса при сортировке по возрастанию (индекс был тоже так создан)
```bash
> db.companies.find({},{name: 1}).sort({name: 1}).explain("allPlansExecution")
{
        "queryPlanner" : {
                "plannerVersion" : 1,
                "namespace" : "otus.companies",
                "indexFilterSet" : false,
                "parsedQuery" : {

                },
                "winningPlan" : {
                        "stage" : "PROJECTION_SIMPLE",
                        "transformBy" : {
                                "name" : 1
                        },
                        "inputStage" : {
                                "stage" : "FETCH",
                                "inputStage" : {
                                        "stage" : "IXSCAN",   
                                        "keyPattern" : {
                                                "name" : 1
                                        },
                                        "indexName" : "name_1",  
                                        "isMultiKey" : false,
                                        "multiKeyPaths" : {
                                                "name" : [ ]
                                        },
                                        "isUnique" : false,
                                        "isSparse" : false,
                                        "isPartial" : false,
                                        "indexVersion" : 2,
                                        "direction" : "forward", 
                                        "indexBounds" : {
                                                "name" : [
                                                        "[MinKey, MaxKey]"
                                                ]
                                        }
                                }
                        }
                },
                "rejectedPlans" : [ ]
        },
        "executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 18802,
                "executionTimeMillis" : 50,
                "totalKeysExamined" : 18802,
                "totalDocsExamined" : 18802,
                "executionStages" : {
                        "stage" : "PROJECTION_SIMPLE",
                        "nReturned" : 18802,
                        "executionTimeMillisEstimate" : 0,
                        "works" : 18803,
                        "advanced" : 18802,
                        "needTime" : 0,
                        "needYield" : 0,
                        "saveState" : 146,
                        "restoreState" : 146,
                        "isEOF" : 1,
                        "transformBy" : {
                                "name" : 1
                        },
                        "inputStage" : {
                                "stage" : "FETCH",
                                "nReturned" : 18802,
                                "executionTimeMillisEstimate" : 0,
                                "works" : 18803,
                                "advanced" : 18802,
                                "needTime" : 0,
                                "needYield" : 0,
                                "saveState" : 146,
                                "restoreState" : 146,
                                "isEOF" : 1,
                                "docsExamined" : 18802,
                                "alreadyHasObj" : 0,
                                "inputStage" : {
                                        "stage" : "IXSCAN",
                                        "nReturned" : 18802,
                                        "executionTimeMillisEstimate" : 0,
                                        "works" : 18803,
                                        "advanced" : 18802,
                                        "needTime" : 0,
                                        "needYield" : 0,
                                        "saveState" : 146,
                                        "restoreState" : 146,
                                        "isEOF" : 1,
                                        "keyPattern" : {
                                                "name" : 1
                                        },
                                        "indexName" : "name_1",
                                        "isMultiKey" : false,
                                        "multiKeyPaths" : {
                                                "name" : [ ]
                                        },
                                        "isUnique" : false,
                                        "isSparse" : false,
                                        "isPartial" : false,
                                        "indexVersion" : 2,
                                        "direction" : "forward",
                                        "indexBounds" : {
                                                "name" : [
                                                        "[MinKey, MaxKey]"
                                                ]
                                        },
                                        "keysExamined" : 18802,
                                        "seeks" : 1,
                                        "dupsTested" : 0,
                                        "dupsDropped" : 0
                                }
                        }
                },
                "allPlansExecution" : [ ]
        },
        "serverInfo" : {
                "host" : "localhost.localdomain",
                "port" : 27017,
                "version" : "4.2.9",
                "gitVersion" : "06402114114ffc5146fd4b55402c96f1dc9ec4b5"
        },
        "ok" : 1
}
```
Оптимизатор увидел индекс "indexName" : "name_1",  "direction" : "forward", проверим, изменится ли план при обратной сортировке
```bash
> db.companies.find({},{name: 1}).sort({name: -1}).explain("allPlansExecution")
{
        "queryPlanner" : {
                "plannerVersion" : 1,
                "namespace" : "otus.companies",
                "indexFilterSet" : false,
                "parsedQuery" : {

                },
                "winningPlan" : {
                        "stage" : "PROJECTION_SIMPLE",
                        "transformBy" : {
                                "name" : 1
                        },
                        "inputStage" : {
                                "stage" : "FETCH",
                                "inputStage" : {
                                        "stage" : "IXSCAN",
                                        "keyPattern" : {
                                                "name" : 1
                                        },
                                        "indexName" : "name_1",
                                        "isMultiKey" : false,
                                        "multiKeyPaths" : {
                                                "name" : [ ]
                                        },
                                        "isUnique" : false,
                                        "isSparse" : false,
                                        "isPartial" : false,
                                        "indexVersion" : 2,
                                        "direction" : "backward", <--------------------
                                        "indexBounds" : {
                                                "name" : [
                                                        "[MaxKey, MinKey]"
                                                ]
                                        }
                                }
                        }
                },
                "rejectedPlans" : [ ]
        },
        "executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 18802,
                "executionTimeMillis" : 41,
                "totalKeysExamined" : 18802,
                "totalDocsExamined" : 18802,
                "executionStages" : {
                        "stage" : "PROJECTION_SIMPLE",
                        "nReturned" : 18802,
                        "executionTimeMillisEstimate" : 0,
                        "works" : 18803,
                        "advanced" : 18802,
                        "needTime" : 0,
                        "needYield" : 0,
                        "saveState" : 146,
                        "restoreState" : 146,
                        "isEOF" : 1,
                        "transformBy" : {
                                "name" : 1
                        },
                        "inputStage" : {
                                "stage" : "FETCH",
                                "nReturned" : 18802,
                                "executionTimeMillisEstimate" : 0,
                                "works" : 18803,
                                "advanced" : 18802,
                                "needTime" : 0,
                                "needYield" : 0,
                                "saveState" : 146,
                                "restoreState" : 146,
                                "isEOF" : 1,
                                "docsExamined" : 18802,
                                "alreadyHasObj" : 0,
                                "inputStage" : {
                                        "stage" : "IXSCAN",
                                        "nReturned" : 18802,
                                        "executionTimeMillisEstimate" : 0,
                                        "works" : 18803,
                                        "advanced" : 18802,
                                        "needTime" : 0,
                                        "needYield" : 0,
                                        "saveState" : 146,
                                        "restoreState" : 146,
                                        "isEOF" : 1,
                                        "keyPattern" : {
                                                "name" : 1
                                        },
                                        "indexName" : "name_1",
                                        "isMultiKey" : false,
                                        "multiKeyPaths" : {
                                                "name" : [ ]
                                        },
                                        "isUnique" : false,
                                        "isSparse" : false,
                                        "isPartial" : false,
                                        "indexVersion" : 2,
                                        "direction" : "backward",
                                        "indexBounds" : {
                                                "name" : [
                                                        "[MaxKey, MinKey]"
                                                ]
                                        },
                                        "keysExamined" : 18802,
                                        "seeks" : 1,
                                        "dupsTested" : 0,
                                        "dupsDropped" : 0
                                }
                        }
                },
                "allPlansExecution" : [ ]
        },
        "serverInfo" : {
                "host" : "localhost.localdomain",
                "port" : 27017,
                "version" : "4.2.9",
                "gitVersion" : "06402114114ffc5146fd4b55402c96f1dc9ec4b5"
        },
        "ok" : 1
}
```
Оптимизатор увидел индекс "indexName" : "name_1", но на этот начал сканироване в другую сторону "direction" : "baskward"
