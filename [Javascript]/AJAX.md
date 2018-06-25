# Javascript AJAX

## jQuery 3.*

* http://api.jquery.com/jquery.ajax/


```javascript
$ajax({
    method: 'GET', // "GET", "POST", "PUT"
    dataType: 'json',
    url: 'https://example.com/',
    headers: {},
    cache: false
}).done(function(data){
    console.log(data);
}).fail(function(jqXHR,status,errorThrown){
    if(jqXHR.status===500 && jqXHR.responseText){
        console.err(jqXHR.responseText);
    }
}).always(function(data){
    console.log(data);
});
```


## XMLHttpRequest (ES2015)

```javascript
var request = new XMLHttpRequest();
request.open('GET', 'https://example.com/', true);

request.onload = function() {
    if (this.status >= 200 && this.status < 400) {
        // Success!
        var data = JSON.parse(this.response);
    } else {
        // We reached our target server, but it returned an error

    }
};

request.onerror = function() {
  // There was a connection error of some sort
};

request.send();

```


## Fetch (ES6)

* https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch
* https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch

```javascript
fetch(
    'https://example.com/',
    {
        method: 'get', // GET, POST
        headers: {
            'content-type': 'application/json'
        },
        cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
        mode: 'cors', // no-cors, cors, *same-origin
    }
).then(function(response){
    return response.json();
}).then(function(myJson){
    console.log(myJson);
});
```