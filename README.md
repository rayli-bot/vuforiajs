# vuforiajs
 Node.js client for the Vuforia Web Services API (VWS API) and Cloud Recognition API (CloudReco API).
 
## usage
      
```javascript
// load module
var vuforia = require('vuforiajs');

// init client with valid credentials
var client = vuforia.client({

    // Server access key (used for Vuforia Web Services API)
    'accessKey': 'your server access key',

    // Server secret key (used for Vuforia Web Services API)
    'secretKey': 'your server secret key'

    // Client access key (used for Vuforia Cloud Recognition API)
    'clientAccessKey': 'your client access key',

    // Client secret key (used for Vuforia Cloud Recognition API)
    'clientSecretKey': 'your client secret key',
});

// util for base64 encoding and decoding
var util = vuforia.util();
```

## create a new target
    
```javascript
var target = {

    // name of the target, unique within a database
    'name': 'my target',
    // width of the target in scene unit
    'width': 32.0,
    // the base64 encoded binary recognition image data
    'image': util.encodeFileBase64(__dirname + '/someImage.png'),
    // indicates whether or not the target is active for query
    'active_flag': true,
    // the base64 encoded application metadata associated with the target
    'application_metadata': util.encodeBase64('some metadata about your image')
};
```

## add target to cloud database
   
```javascript
client.addTarget(target, function (error, result) {

    if (error) { // e.g. [Error: AuthenticationFailure]

        console.error(result);
        /* result would look like
            {
            result_code: 'AuthenticationFailure',
            transaction_id: '58b51ddc7a2c4ac58d405027acf5f99a'
            }
            */

    } else {

        console.log(result);
        /* result will look like
            {
            target_id: '93fd6681f1r74b76bg80tf736a11b6a9',
            result_code: 'TargetCreated',
            transaction_id: 'xf157g63179641c4920728f1650d1626'
            }
            */
    }
});
```

## list all targets within cloud database
    
```javascript
client.listTargets(function (error, result) {

    console.log(result);

    /*
    {
        “result_code”:”Success”,
        “transaction_id”:”550e8400e29440000b41d4a716446655”,
        “results”:[
            ”00550e84e29b41d4a71644665555678”,
            ”578fe7fd60055a5a84c2d215066b7a9d”
        ]
    }
    */

});             
```
        
## retrieve a target from cloud database
    
```javascript
client.retrieveTarget('00550e84e29b41d4a71644665555678', function (error, result) {

    /*
        {
        “result_code”:”Success”,
        “transaction_id”:”e29b41550e8400d4a716446655440000”,
        “target_record”:{
            “target_id”:”550b41d4a7164466554e8400e2949364”,
            “active_flag”:true,
            “name”:”tarmac”,
            “width”:100.0,
            “tracking_rating”:4,
            “reco_rating”:””
        },
        “status”:”Success”
        }
        */
});
```

## update a target
  
```javascript
var update = {

    'active_flag' : false
};

client.updateTarget('00550e84e29b41d4a71644665555678', update, function (error, result) {

    /*
        {
        “name”:”tarmac”,
        “width:32.0,
        “image”:”0912ba39x….”,
        “active_flag”:false,
        “application_metadata”:“496fbb6532b3863460a984de1d980bed5ebcd507”
        }
        */
    // util.decodeBase64(application_metadata);

});
```

## delete a target
     
```javascript
client.deleteTarget('00550e84e29b41d4a71644665555678', function (error, result) {

    /*
        {
        “result_code”:”Success”,
        “transaction_id”:”550e8400e29b41d4a716446655482752”
        }
        */
});
```
           
## search cloud database for images that can be considered duplicates

```javascript
client.checkForDuplicateTargets('00550e84e29b41d4a71644665555678', function (error, result) {

    // images that are the same as the reference one identified by a given target id
    /*
        {
        “similar_targets”:
        [
            ”550e8400e29b41d4a716446655447300”,
            ”578fe7fd60055cbc84c2d215066b7a9d”
        ]
        }
        */
});
```

## perform Cloud Recognition query

```javascript
var filename = __dirname + '/test.jpg'; // image file that will be send to Cloud Recognition
var max_num_results = 5; // return only 5 matches

client.cloudRecoQuery(fs.readFileSync(filename, 'binary'), max_num_results, function (error, result) {

    // result from cloud recognition API
    /*
    { 
        result_code: 'Success',
        transaction_id: 'b66bef31ba394c86a7ea2ab7e35c93d1',
        results: 
        [ 
            'eacbb1487c584dbb8741fb5a28b4228f' 
        ] 
    }
    */
});
```
