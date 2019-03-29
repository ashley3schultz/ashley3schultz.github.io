---
layout: post
title:      "Three Ways To Fetch API Data With Ruby"
date:       2019-03-29 21:23:05 +0000
permalink:  three_ways_to_fetch_api_data_with_ruby
---


This week I came across two different technical challenges and both of them consisted of fetching data from an API. In one challenge it featured Net::HTTP and I used the Faraday gem for the other one. There are several more gems to fetch data but I have chosen to stick to the three most popular one which are the two above and HTTParty.
## 1. Faraday

**Class**
```
require 'faraday'
require 'json'

class Far

    def self.fetch
        url = "https://api.github.com/repos/ashley3schultz/ashley3schultz.github.io/commits"
        response = Faraday.get(url)
        JSON.parse(response.body)
    end

end
```

**Test**
```
describe Far do
    context 'Fetches Data with faraday' do
        it "Get commits from GitHub API" do
            items = Far.fetch
            expect(items.size).to eq 30
        end
    end
end
```

## 2. HTTParty

**Class**
```
require 'httparty'
require 'json'

class Par

    def self.fetch
        url = "https://api.github.com/repos/ashley3schultz/ashley3schultz.github.io/commits"
        response = HTTParty.get(url)
        JSON.parse(response.body)
    end

end
```

**Test**
```
describe Par do
    context 'Fetches Data with HTTParty' do
        it "Get commits from GitHub API" do
            items = Par.fetch
            expect(items.size).to eq 30
        end
    end
end
```

## 3. Net::HTTP
#### Simple Version

**Class**
```
require "net/http"
require "uri"
require "json"

class Net

    def self.fetch
        uri = URI("https://api.github.com/repos/ashley3schultz/ashley3schultz.github.io/commits")
        response =  Net::HTTP.get(uri)
        JSON.parse(response)
    end

end
```

**Test**
```
describe Net do
    context 'Fetches Data with faraday' do
        it "Get commits from GitHub API" do
            items = Net.fetch
            expect(items.size).to eq 30
        end
    end
end
```

#### Complex Version

**Class**
```
require "net/http"
require "uri"
require "json"

class Net

    def self.fetch(query, att)
        params = "q=#{query}+in:#{att}"
        uri = URI.parse("https://api.github.com/search/repositories")
        http =  Net::HTTP.new(uri.host, uri.port)
        http.use_ssl = (uri.scheme == "https")
        request=Net::HTTP::Get.new("#{uri}?#{params}")
        response = http.request(request)
        JSON.parse(response.body)
    end

end
```

**Test**
```
describe Net do
    context 'Fetches Data with net/http' do
        it "Get commits from GitHub API" do
            items = Net.fetch('rails','name')
            expect(items['items'].size).to eq 30
        end
    end
end
```

The main reason to use the more complex version is because it's more robust. If you want to designate things along the way it's possible. Plus there are a ton of methods that can be called on each instant along the way. 

*Note: There's nothing stopping us from making the simple version dynamic, as we could simply interpolate the url with the params.*

## Summary
If I can use a simple version, I would go with Faraday or HTTParty because they are cleaner. There's no need to parse the url and I think it feels more intuitive. They are pretty much identical when it comes to using them so I guess it comes down to which one you like typing more. If I were to choose it would be Faraday simply because I tend to stumble a bit with the Half caps & single "P" in HTTParty.

The advantage of Net::HTTP is that it's already built into Ruby, therefore more stable and we have more complex features without adding extra dependencies.

## References 
* [Faraday Docs](https://www.rubydoc.info/gems/faraday/0.7.6)
* [HTTParty Docs](https://www.rubydoc.info/gems/httparty/0.16.4)
* [Net/HTTP Docs](https://ruby-doc.org/stdlib-2.6.2/libdoc/net/http/rdoc/Net/HTTP.html)
