## ES查询实例demo



```

查询es节点信息
curl --location 'IP:9200/_cat/nodes?v'

curl  -XGET 'IP:9200/_cat/health?v'

查询指定的索引并排序
curl --location 'IP:9200/_cat/indices/yx_supervise_lo_nor_*?v'| sort -r


curl --location --request GET -u {USER}:{PASSWORD}  'IP:9200/_cat/indices/yx_supervise_lo_nor_*?v'| sort -r

查询索引mapping:
IP:9200/yx_supervise_lo_nor_202404/_mapping?pretty=true




线上查询：
curl --location --request GET -u {USER}:{PASSWORD}  'IP:9200/yx_supervise_lo_nor_202311/_search?pretty=true' \
--header 'Content-Type: application/json' \
--data '{
    "query": {
        "bool": {
            "must": [
                {
                    "terms": {
                        "account_id": [
                            "8888277502188541"
                        ]
                    }
                },
                {
                    "term": {
                        "course_id": 10034273
                    }
                },
				  {
                    "term": {
                        "tag_code": m040227
                    }
                },
                {
                    "terms": {
                        "user_id": [
                            "6868277502380492"
                        ]
                    }
                },
                {
                    "terms": {
                        "module_code": [
                            "TEST_MODULE",
                            "STUDY_MODULE",
                            "RESTUDY_MODULE",
                            "TEST_CHECK_MODULE",
                            "STUDY_CHECK_MODULE",
                            "SURFACE_TEST_MODULE",
                            "SURFACE_STUDY_MODULE",
                            "SURFACE_RESTUDY_MODULE"
                        ]
                    }
                },
                {
                    "terms": {
                        "master_status": [
                            "PASSED",
                            "FAILED",
                            "DEALED",
                            "INPROCESS"
                        ]
                    }
                },
                {
                    "bool": {
                        "should": [
                            {
                                "term": {
                                    "module_type": "STUDY"
                                }
                            },
                            {
                                "bool": {
                                    "must": [
                                        {
                                            "term": {
                                                "module_type": "TEST"
                                            }
                                        }
                                    ]
                                }
                            }
                        ]
                    }
                },
                {
                    "range": {
                        "created": {
                            "gte": "2023-11-11 00:00:00",
                            "lte": "2023-11-11 23:59:59"
                        }
                    }
                }
            ]
        }
    },
    "sort": [
        {
            "created": {
                "order": "desc"
            }
        }
    ],
    "from": 0,
    "size": 500
}'





查询：
curl --location --request GET 'IP:9200/INDEX_NAME/_search?pretty=true' \
--header 'Content-Type: application/json' \
--data '{
    "query": {
        "bool": {
            "must": [
                {
                    "terms": {
                        "account_id": [
                            "8885623814107645"
                        ]
                    }
                },
				{
                    "term": {
                        "tag_code": "m240106"
                        
                    }
                },
                {
                    "term": {
                        "course_id": 10002608
                    }
                },
                {
                    "terms": {
                        "user_id": [
                            "6867177758915260"
                        ]
                    }
                },
                {
                    "terms": {
                        "module_code": [
                            "TEST_MODULE",
                            "STUDY_MODULE",
                            "RESTUDY_MODULE",
                            "TEST_CHECK_MODULE",
                            "STUDY_CHECK_MODULE",
                            "SURFACE_TEST_MODULE",
                            "SURFACE_STUDY_MODULE",
                            "SURFACE_RESTUDY_MODULE"
                        ]
                    }
                },
                {
                    "terms": {
                        "master_status": [
                            "PASSED",
                            "FAILED",
                            "DEALED",
                            "INPROCESS"
                        ]
                    }
                },
                {
                    "bool": {
                        "should": [
                            {
                                "term": {
                                    "module_type": "STUDY"
                                }
                            },
                            {
                                "bool": {
                                    "must": [
                                        {
                                            "term": {
                                                "module_type": "TEST"
                                            }
                                        }
                                    ]
                                }
                            }
                        ]
                    }
                },
                {
                    "range": {
                        "created": {
                            "gte": "2024-04-03 00:00:00",
                            "lte": "2024-04-03 23:59:59"
                        }
                    }
                }
            ]
        }
    },
    "sort": [
        {
            "created": {
                "order": "desc"
            }
        }
    ],
    "from": 0,
    "size": 500
}'


```

