# http_requests

http_requests is a lib that provides easy methods to make http and https requests.

## Install

Add to your Gemfile:

```ruby
gem http_requests
```

Then execute the command `bundle install` on terminal when inside your folder with the Gemfile.

Or install yourself:

`gem install http_requests`

## Usage

First, import the gem to you project using

```ruby
require http_requests
```

Then, follow the bellow examples to make the most simple http post and get requests possible:

Simplle HTTP request.

```ruby
conn = HTTPConn.new("http://www.someexample.com")
res = conn.get # Or conn.post

puts res.message
puts res.code
puts res.body
```

Setting a header and a query string for a request to an specific end point:

```ruby
conn = HTTPConn.new("http://www.someexample.com")
res = conn.get(end_point: "/someaction"
               header: { "Accept" => "application/json" },
               query_str: { "token" => "abc123" })
# Will generate the uri "http://www.someexample.com/someaction?token=abc123"
```

Setting up an HTTPS (without certificate validation) connection with a preset header (so every requets will use it):

```ruby
settings = {
  header: { "Accept" => "application/json" },
  ssl: true
}

conn = HTTPConn.new("https://www.someexample.com", settings)

res = conn.post(end_point: "/someaction",
                body: %q{"{ "token": "abc123" }"})

```

If you want to use the HTTPS certificate validation, set the certificate path when declaring the settings:

```ruby
settings = {
  header: { "Accept" => "application/json" },
  ssl: true,
  cert: "certs/certificate.pem"
}
```

In order to issue multiple requests to the same connection, use conn.start passing a block. It is useful because everytime a
request is issued, a connection is opened and automatically closed, then, when a next request is called, another connection
is opened.

```ruby
conn.start do
  conn.get
  conn.get
  conn.post
end
```

### SingleConn

The SingleConn is a module that allows the same connection instance to be used throughout the entire application.
It works as a Singleton Class.

File 1:
```ruby
settings = {
  # ...
}
SingleConn.init("http://www.someexample.com", settings)
res = SingleConn.get
```

Once initialized, you can just use it in other files:

File 2:
```ruby
res = SingleConn.post(end_point: "/anyaction/youwant")
```

As seen, SingleConn works exactly like a normal Connection, but its "instance" can be shared.