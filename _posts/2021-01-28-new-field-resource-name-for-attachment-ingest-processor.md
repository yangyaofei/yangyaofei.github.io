---
layout	: post
title	: "New resource_name field for plugin ingest-attachment of Elasticsearch"
subtitle: " "
date	: 2020-01-28
author	: "yangyaofei"
tags	:
    - resource_name
    - ingest
    - ingest-attachment
---

In one of my project, I need a server to store files and read text from them.
Those files contain word, ppt, pdf, text etc. In that case , I found there is a
plugin in Elasticsearch called ingest-attachment. It uses tika to extract text
from file which is a Apache project.

But I found there is a flaw in this plugin. Tika can read file name to get file
more type properly, but this field not be used in Elasticsearch. In most situation,
It's not a problem, but when you sent a text file with non-UTF8 encode and contains
non-ascii characters this plugin cannot read this file properly. So, I decide to 
add this `resource_name` aka filename to this plugin.

# How to use

This field is easily to use. I will show it with python:

```
	import cbor2
	import requests
	import json

	# create a pipeline and set attachment's field

	pipeline = {
        "description": "Store file's meta data and text to ES",
        "processors": [
            {
                "attachment": {
                    "field": "data",
                    # "resource_name": "file_name" you can change the field name to want you want
                }
            }
		]
	}
	requests.put(
		url='http://localhost:9200/_ingest/pipeline/cbor-attachment',
		data=json.dumps(pipeline)
	)

	#  assume there is a file nameed somename.txt and we read it from binary
	file_data = read_from_file
	file_name = "somename.txt"

	# process and put it to elasticsearch
	data = {
		"data" = file_data,
		"resource_name" = file_name
	}
	headers = {'content-type': 'application/cbor'}
	requests.put(
    	'http://localhost:9200/index-000001/_doc/id?pipeline=cbor-attachment',
    	data=cbor2.dumps(data),
    	headers=headers
  	)
```

After this , you will see the Elasticsearch read this file and get a right encode,
and you can test if you don't sent the file's name with a non-ascii character in non-utf8
encode.


