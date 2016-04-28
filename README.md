# NoSQL to SQL Schema
[![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=A8YE92K9QM7NA) ![Coverage](https://img.shields.io/badge/Auto-80%-green.svg)

Convert schema from NoSQL(MongoDB) to SQL

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
	console.log(sql);

	fs.appendFile('./create_table.sql', (sql + '\n'), function (err) {
		if(!err) {
			console.log("create_table.sql saved.");
		} else {
			console.log("create_table.sql save error : ", err);
		}
	});
};

var sql_insert2Table = function(dataObj, tbName){
	var insertStr = '';

	for(var i=0;i<dataObj[tbName].length;i++){
		if(i>0) insertStr += ',';
		insertStr += 'INSERT INTO `' + tbName + '` VALUES (' + dataObj[tbName][i] + ')\n';
	}

	fs.appendFile('./inser2table.sql', (insertStr + ';\n'), function (err) {
		if(!err) {
			console.log("inser2table.sql saved.");
		} else {
			console.log("inser2table.sql save error : ", err);
		}
	});
		
	console.log(insertStr);
	console.log('------------------------------');
};

var commaHandler = function(str){
	var sqlStr = str;
	console.log('Check comma at first index...');

	// Remove comma at first index
	if(sqlStr.substring(0, 1)==',') {
		console.log('|_ Found comma');
		console.log('|_ Removed');
		sqlStr = sqlStr.substring(2, (sqlStr.length));
	}

	return sqlStr;
};

var visit = function(collection, lv, inObject, primaryKey){
	if(typeof collection === 'undefined' || collection == null){
		// Do nothing
	} else {
		var mainTbStr = '';
		var sqlStr = '';
		var keys = Object.keys(collection);
		var priKey = null;

		if(primaryKey!=null) priKey = primaryKey;

		for(var key in Object.keys(keys)){
			var space = '';
			for(var spaceIndex=0;spaceIndex<lv;spaceIndex++) space += '  ';
			console.log(space + keys[key] + ' : ' + collection[keys[key]]);

			if(keys[key] == '_id') priKey = collection[keys[key]];

			if(collection[keys[key]] === 'undefined' || collection[keys[key]] == null){
				sqlStr += ", \'null\'";
			} else if(typeof collection[keys[key]] === 'object'){
				if(lv>0){
					var value = "";
					value += ", \'" + keys[key] + "\'";
					value += visit(collection[keys[key]], (lv+1), keys[key], priKey);

					sqlStr += value;

					// Check sub-data
					if(tableObject[inObject]==='undefined'||tableObject[inObject]==null){
						// Create the new one 
						tableObject[inObject] = [];
					}
						
					value = "\'" + priKey + "\', " + commaHandler(value);
					tableObject[inObject].push(value);
				} else {
					sqlStr += visit(collection[keys[key]], (lv+1), keys[key], priKey);
				}
			} else {
				sqlStr += ", \'" + collection[keys[key]] + "\'";

				if(lv==0){
					mainTbStr += ", \'" + collection[keys[key]] + "\'";
				}
			}
		}

		if(mainTbStr!='') {
			mainTbStr = commaHandler(mainTbStr);
			tableObject['main'].push(mainTbStr);
		}

		return sqlStr;
	}
};

var travel = function(collections){
	if(collections === undefined || collections == null) {
		//return false;
	} else {
		var sqlStr = '';
		for(var i=0;i<collections.length;i++){
			//console.log(collections[i]);
			sqlStr = visit(collections[i], 0, null, null);
			sqlStr = commaHandler(sqlStr);
			console.log('|_ Result : ' + sqlStr);
			console.log('-------------------------------------\n');

			sqlStr = null;
		}
	}
};

Collections.find({}, function (err, collections) {
  if (err) {
    console.log(err);
  } else {
  	var jsonObj = JSON.stringify(collections);
  	//console.log(jsonObj);
  	travel(JSON.parse(jsonObj));

	for(var tb in tableObject){
		sql_createTable(tb);
		sql_insert2Table(tableObject, tb);
	}
  }
});
```

## Donation
If this project help you reduce time to develop, you can give me a cup of coffee :) 

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=A8YE92K9QM7NA)
