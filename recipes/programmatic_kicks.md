{
  "title": "How to send kicks to WDT.io programmatically",
  "shortTitle": "programmatic kicks",
  "date": "2015-12-27"
}
---
Or *how to make HTTP HEAD requests in various programming languages*.

You have a program that is supposed to do something on a regular schedule, and you want to monitor that scheduled execution with WDT.io. 

1. [Sign up](https://wdt.io/signup) on WDT.io if you haven't already.
2. [Create](inbound_timer.html) a new inbound timer.
3. Copy the URL of this new timer.
4. In the part of your program that is supposed to run regularly, send a kick to the URL copied from step 3.

Now every time that part of your program runs, it'll send a kick to WDT.io. This regular kick prevents WDT.io from sending an alert to you. If, for whatever reason the part fails or doesn't get invoked, WDT.io won't get the kick and will send you an alert.


### Go

using the [http package](https://golang.org/pkg/net/http/#Client.Head)

```
import "net/http"

client := http.Client{Timeout: time.Duration(30 * time.Second)}    
client.Head("http://<the URL from step 3>")
```


### Java

using [HttpURLConnection](https://docs.oracle.com/javase/7/docs/api/java/net/HttpURLConnection.html)

```
import java.net.URL;
import java.net.HttpURLConnection;
import java.io.IOException;

HttpURLConnection connection = (HttpURLConnection) new URL("http://<the URL from step 3>").openConnection();
connection.setRequestMethod("HEAD");
connection.setConnectTimeout(30000);
try {
  connection.getResponseCode();
} catch (IOException e) {}
```


### Node.js

using [Request](https://github.com/request/request)

```
var request = require('request');

request.head('http://<the URL from step 3>', {timeout: 30000});
```

or using [Restler](https://github.com/danwrong/restler)

```
var rest = require('restler');

rest.head('http://<the URL from step 3>', {timeout: 30000});
```


### Perl

using [HTTP::Request](http://search.cpan.org/~ether/HTTP-Message-6.11/lib/HTTP/Request/Common.pm)

```
use HTTP::Request::Common;
use LWP::UserAgent;

my $ua = LWP::UserAgent->new;
$ua->timeout(30);
$ua->request(HEAD 'http://<the URL from step 3>');
```

or using [WWW::Curl::Easy](http://search.cpan.org/~crisb/WWW-Curl-3.02/Easy.pm.in)

```
use WWW::Curl::Easy;

my $curl = WWW::Curl::Easy->new;
$curl->setopt(CURLOPT_URL, 'http://<the URL from step 3>');
$curl->setopt(CURLOPT_NOBODY, 1);
$curl->setopt(CURLOPT_TIMEOUT, 30);
$curl->perform;
```


### PHP

using [cURL](http://php.net/manual/en/ref.curl.php)

```
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'http://<the URL from step 3>');
curl_setopt($ch, CURLOPT_NOBODY, true);
curl_setopt($ch, CURLOPT_TIMEOUT, 30);
curl_exec($ch);
curl_close($ch);
```


### Python

using [requests](http://docs.python-requests.org/en/latest/user/quickstart/#make-a-request)

```
import requests

try:
  requests.head("http://<the URL from step 3>", timeout=30)
except:
  pass
```


### Ruby

using [net/http](http://ruby-doc.org/stdlib/libdoc/net/http/rdoc/Net/HTTP.html)

```
require 'net/http'

Net::HTTP.start('k.wdt.io', :open_timeout => 30) {|http| http.head('<the path of the URL from step 3>')} rescue nil
```

or using [curl](http://www.ruby-doc.org/core/Kernel.html#method-i-60)

```
`curl -Ism 30 http://<the URL from step 3>`
```
