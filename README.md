Import/Export Mongo Schema
===================
Import and export mongodb schemas without copying all the data. It will extract the following meta data about a mongo database:

* Collections
* Indexes
* Cap sizes
* Schema validators

This data will be stored in a `json` file for a database that looks something like this:

```json
{
    "databases": {
        "test": {
            "location": {
                "indexes": [
                    {
                        "name": "_id_",
                        "keys": [
                            [
                                "_id",
                                1
                            ]
                        ]
                    },
                    {
                        "name": "pos_2dsphere",
                        "2dsphereIndexVersion": 3,
                        "keys": [
                            [
                                "pos",
                                "2dsphere"
                            ]
                        ]
                    },
                    {
                        "name": "device_1_timestamp_1",
                        "keys": [
                            [
                                "device",
                                1
                            ],
                            [
                                "timestamp",
                                1
                            ]
                        ]
                    }
                ],
                "options": {}
            },
            "users": {
                "indexes": [
                    {
                        "name": "_id_",
                        "keys": [
                            [
                                "_id",
                                1
                            ]
                        ]
                    },
                    {
                        "unique": true,
                        "name": "username_idx",
                        "keys": [
                            [
                                "username",
                                1
                            ]
                        ]
                    }
                ],
                "options": {
                    "validator": {
                        "$jsonSchema": {
                            "bsonType": "object",
                            "required": [
                                "username",
                                "password",
                                "level"
                            ],
                            "properties": {
                                "username": {
                                    "bsonType": "string",
                                    "description": "must be a string and is required"
                                },
                                "level": {
                                    "bsonType": "string",
                                    "enum": [
                                        "user",
                                        "admin",
                                        "moderator"
                                    ],
                                    "description": "must be a string"
                                },
                                "password": {
                                    "bsonType": "string",
                                    "description": "must be a bcrypt password",
                                    "pattern": "^\\$2b\\$\\d{1,2}\\$[A-Za-z0-9\\.\\/]{53}$"
                                }
                            }
                        }
                    },
                    "validationLevel": "strict",
                    "validationAction": "error"
                }
            },
            "capped": {
                "indexes": [
                    {
                        "name": "_id_",
                        "keys": [
                            [
                                "_id",
                                1
                            ]
                        ]
                    },
                    {
                        "unique": true,
                        "name": "key_1",
                        "keys": [
                            [
                                "key",
                                1
                            ]
                        ]
                    }
                ],
                "options": {
                    "capped": true,
                    "size": 64000,
                    "max": 5000,
                    "validator": {
                        "$jsonSchema": {
                            "bsonType": "object",
                            "description": "Simple key value store",
                            "required": [
                                "key",
                                "value"
                            ],
                            "properties": {
                                "key": {
                                    "bsonType": "string",
                                    "maxLength": 64.0,
                                    "description": "the key value"
                                },
                                "value": {
                                    "bsonType": "string",
                                    "description": "the associated value"
                                }
                            }
                        }
                    },
                    "validationLevel": "strict",
                    "validationAction": "error"
                }
            }
        }
    },
    "exported": "2018-07-18T17:12:43.460992"
}
```

Installation
------------

```bash
pip install MongoSchemaImportExport
```

Usage
-----
Make sure the user you are using to import/export has the appropriate privileges, they'll probably need to have the `root` role or `dbOwner` on the source and destination.
To export your data run:

```bash
mongo-schema-export --uri mongodb://user:password@database.host1.com:27017/admin --databases db_1,db_2
```

To import your schema run:

```bash
mongo-schema-import --uri mongodb://user:password@database.host2.com:27017/admin --databases db_1,db_2
```

If you get permission errors, make sure your user has the right roles to read and write databases and collections.