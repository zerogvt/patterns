# node.js patterns

## Implement a polling function
```
function poll(search_function, search_for, expected_result,
              timeout, interval,
              callback)
{
    if (expected_result) {
        console.log("Timing appearance of: " + search_for)
    } else {
        console.log("Timing removal of: " + search_for)
    }
    let startTime = Number(new Date())
    let endTime = Number(new Date()) + (timeout);
    let checkCondition = function() {
        // If the condition is met, we're done! 
        search_function(search_for, result => {
            if(result == expected_result) {
                callback(true, (Number(new Date()) - startTime))
            }
            // the condition isn't met but the timeout hasn't elapsed so go again
            else if (Number(new Date()) < endTime) {
                setTimeout(checkCondition, interval);
            }
            // Didn't match and time is out
            else {
                callback(false, Number.MAX_SAFE_INTEGER)
            }
        })
    };
    setTimeout(checkCondition, interval);
}

function query(target, callback) {
    // perform a query
    query(..., response => {
        query_result = analyze_result(response)
        callback(query_result)
    })
}
```

## HTTP query
```
const http_req = require('request')
function post(auth_token, url, payload, callback) {
    let options = {
        method: "POST",
        url: url,
        headers: {
            'Accept': 'application/json',
            'Authorization': 'bearer ' + auth_token
        },
        json: payload
    }
    http_req(options, (err, response) => {
        handleHttpResult(err, response, callback)
    })
}

const util = require('util')
function handleHttpResult(err, response, callback) {
    if (err) {
        util.inspect(err)
    }
    // handle auth errors separatelly 
    if (response.statusCode >= 400 && response.statusCode <= 499){
        console.log("ERROR: " + response.statusCode)
        console.log(JSON.stringify(response))
        return
    } else {
        if (response.statusCode >= 500 && response.statusCode <= 600) {
            console.log("Error " + response.statusCode)
            return
        }
    }
    if (callback) {
        callback(response.body)
    }
}
```