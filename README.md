# NoSQL to SQL Schema
[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=A8YE92K9QM7NA) ![Coverage](https://img.shields.io/badge/Auto-20%-green.svg)

Convert schema from NoSQL(MongoDB) to SQL.
Now based on common command (create and insert).

## Usage

Revise some variable on source code and then run source by NodeJS

```javascript
var databaseName = 'DATABASE_NAME';                                // <----------- apply here
var collectionName = 'COLLECTION_NAME';                            // <----------- apply here

var schemaObject = {
	DATA_OR_COLUMN_NAME: DATA_TYPE,                                // <----------- apply here
	...
};

var fs = require('fs');
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/' + databaseName);           // <----------- apply here

var Collections = mongoose.model(collectionName, schemaObject);

var tableObject = {
	main:[]
};


var sql_createTable = function(tbName){
	var sql = 'CREATE TABLE \'' + tbName + '\' ( COLUMNS );';      // <----------- apply here
	
	.
	.
	.
```

## Donation
If this project help you reduce time to develop, you can give me a cup of coffee :) 

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=A8YE92K9QM7NA)
