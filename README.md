# Simultaneuos HTTP requests in PHP with cURL

The curl_extension is one of the most important extensions of the PHP especially when we have to interface with external APIs and we must communicate with other applications.

Well, as we know, many novels and many volumes have been written on this topic :))) :
 
During a project that I was carrying out, where I needed to make multiple and simultaneous asynchronous requests, I came across a problem with the curl_multi, which, after some vain but useful research, some analysis and many tests I developed a solution through an insight.

The problem is when you use the cURL multi with arrays to add the handles because it can cause the loss of some requests.
 
Well, sometime the solutions are complex and sometime are simple.

In this case, the cURL multi, adding a simple solution during the execution of the first loop to add the handles, became stable.   

The problem has been in how the array with the CURL handles was executed, 
in fact if the loop of the array is executed normally all together, from the key number zero to the key number (n)keys, the cURL multi, sometime, could returns errors or loses some hits.

The solution, I found, is to detach the first key adding the first handle to it without a loop, than executing the first loop, to add the handles, for the subsequent keys. 

All the requests will still remain anyway simultaneous because they are performed by the second loop with the curl_multi_exec.

In this way is very stable, light and fast. I have stressed very much this class with several tests, and it never lost a beat.

Since I had read somewhere, I do not remember where, that curl_multi gave some problems when the number of requests were equal or greater than 10, I did also a test with 13 simultaneous requests and it is ok, no problems.
 
I have also simulated an error inserting a wrong url inside the array and it go on to execute all the rest with no stop and no errors.    

As default options inside the main class there are:

```php
CURL_setopt($x, CURLOPT_URL, $y); 
CURL_setopt($x, CURLOPT_HEADER, 0); 
CURL_setopt($x, CURLOPT_FOLLOWLOCATION, 1); 
CURL_setopt($x, CURLOPT_RETURNTRANSFER, 1); 
CURL_setopt($x, CURLOPT_TCP_FASTOPEN, 1); 
CURL_setopt($x, CURLOPT_ENCODING, "gzip,deflate"); 
CURL_setopt($x, CURLOPT_SSL_VERIFYPEER, 0); 
CURL_setopt($x, CURLOPT_SSL_VERIFYHOST, 0);
```

obviously you can change this options, according to your needs and according to your libCURL version (view: https://CURL.haxx.se/libCURL/c/symbols-in-versions.html) 

editing the main class file:
```php
./lib/curl_multi_array.php 
private function set_option($x, $y)
```


###Usage:

**multiple request:**
```php
include_once("./lib/curl_multi_array.php"); 
$ref = new MultipleCurlArray;

$linkarray = array(
    'http://php.net/manual/it/function.CURL-multi-add-handle.php', 
    'http://php.net/manual/en/function.CURL-multi-init.php', 
    'http://php.net/manual/en/function.CURL-multi-setopt.php'
);

$urls = $ref->run_multi_curl($linkarray);

echo $urls[0],$urls[1],$urls[2];

//or

foreach ($urls as $value){
    echo $value; 
}
```


**single request:**
```php
include_once("./lib/curl_multi_array.php"); 
$ref = new MultipleCurlArray;

$urls = $ref->run_multi_curl(
    array(
        'http://php.net/manual/it/function.CURL-multi-add-handle.php#122964'
    )
);

echo $urls[0]; 

//or 

foreach ($urls as $value){
    echo $value; 
}
```
