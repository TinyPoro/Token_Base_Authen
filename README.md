
[Source](https://scotch.io/tutorials/the-ins-and-outs-of-token-based-authentication "Permalink to The Ins and Outs of Token Based Authentication ― Scotch")

# The Ins and Outs of Token Based Authentication ― Scotch

## Giới thiệu

TBA là luôn là 1 thứ đáng chú ý trên các trang web ngày nay. Với hầu hết mỗi công ty web sử dụng 1 API, token sẽ là cách tốt nhất để giải quyết vấn đề xác thực cho nhiều người dùng.

Có vài yếu tố cực kỳ quan trọng khi phải quyết định  TBA cho ứng dụng của bạn. Những lý do chính cho các token là:

Các server không trại thái và dễ mở rộng

Tính sẵn dùng cho ứng dụng điện thoại 

Truyền xác thực đến các ứng dụng khác

Tăng cường bảo vệ 

## Ai sẽ sử dụng Token Based Authentication?

Any major API or web application that you've come across has most likely used tokens. Applications like Facebook, Twitter, Google+, GitHub, and so many more use tokens.

Let's take a look at exactly how it works. 

## Why Tokens Came Around

Before we can see how token based authentication works and its benefits, we have to look at the way authentication has been done in the past.

### Server Based Authentication (The Traditional Method)

> Since the HTTP protocol is _stateless_, this means that if we authenticate a user with a username and password, then on the next request, our application won't know who we are. We would have to authenticate again.

The traditional way of having our applications remember who we are is to **store the user logged in information on the server**. This can be done in a few different ways on the session, usually in memory or stored on the disk.

Here is a graph of how a server based authentication workflow would look:

As the web, applications, and the rise of the mobile application have come about, this method of authentication has shown problems, especially in scalability.

## The Problems with Server Based Authentication

A few major problems arose with this method of authentication.

**Sessions**: Every time a user is authenticated, the server will need to create a record somewhere on our server. This is usually done in memory and when there are many users authenticating, the overhead on your server increases.

**Scalability**: Since sessions are stored in memory, this provides problems with scalability. As our cloud providers start replicating servers to handle application load, having vital information in session memory will limit our ability to scale.

**CORS**: As we want to expand our application to let our data be used across multiple mobile devices, we have to worry about cross-origin resource sharing (CORS). When using AJAX calls to grab resources from another domain (mobile to our API server), we could run into problems with forbidden requests.

**CSRF**: We will also have protection against [cross-site request forgery][1] (CSRF). Users are susceptible to CSRF attacks since they can already be authenticated with say a banking site and this could be taken advantage of when visiting other sites.

With these problems, scalability being the main one, it made sense to try a different approach. 

## How Token Based Works

Token based authentication is **stateless**. We are not storing any information about our user on the server or in a session.

This concept alone takes care of many of the problems with having to store information on the server.

> No session information means your application can scale and add more machines as necessary without worrying about where a user is logged in.

Although this implementation can vary, the gist of it is as follows:

1. User Requests Access with Username / Password
2. Application validates credentials
3. Application provides a signed token to the client
4. Client stores that token and sends it along with every request
5. Server verifies token and responds with data

**Every single request will require the token**. This token should be sent in the HTTP header so that we keep with the idea of stateless HTTP requests. We will also need to set our server to accept requests from all domains using `Access-Control-Allow-Origin: *`. What's interesting about designating `*` in the ACAO header is that it does not allow requests to supply credentials like HTTP authentication, client-side SSL certificates, or cookies.

Here's an infographic to explain the process:

Once we have authenticated with our information and we have our token, we are able to do many things with this token.

We could even create a permission based token and pass this along to a third-party application (say a new mobile app we want to use), and they will be able to have access to our data -- **but only the information that we allowed with that specific token**.

## The Benefits of Tokens

### Stateless and Scalable

Tokens stored on client side. Completely stateless, and ready to be scaled. Our load balancers are able to pass a user along to any of our servers since there is no state or session information anywhere.

If we were to keep session information on a user that was logged in, this would require us to keep sending that user to the _same server that they logged in at_ (called session affinity).

This brings problems since, some users would be forced to the same server and this could bring about a spot of heavy traffic.

Not to worry though! Those problems are gone with tokens since the token itself holds the data for that user.

### Security

The token, not a cookie, is sent on every request and since there is no cookie being sent, this helps to prevent CSRF attacks. Even if your specific implementation stores the token within a cookie on the client side, the cookie is merely a storage mechanism instead of an authentication one. There is no session based information to manipulate since we don't have a session!

The token also expires after a set amount of time, so a user will be required to login once again. This helps us stay secure. There is also the concept of [token revocation][2] that allows us to invalidate a specific token and even a group of tokens based on the same authorization grant.

### Extensibility (Friend of A Friend and Permissions)

Tokens will allow us to build applications that share permissions with another. For example, we have linked random social accounts to our major ones like Facebook or Twitter.

When we login to Twitter through a service (let's say Buffer), we are allowing Buffer to post to our Twitter stream.

By using tokens, this is how we **provide selective permissions to third-party applications**. We could even build our own API and hand out special permission tokens if our users wanted to give access to their data to another application.

### Multiple Platforms and Domains

We talked a bit about CORS earlier. When our application and service expands, we will need to be providing access to all sorts of devices and applications (since our app will most definitely become popular!).

Having our API just serve data, we can also make the design choice to serve assets from a CDN. This eliminates the issues that CORS brings up after we set a quick header configuration for our application.
    
    
    Access-Control-Allow-Origin: *
    

Our data and resources are available to requests from any domain now **as long as a user has a valid token**.

### Standards Based

When creating the token, you have a few options. We'll be diving more into this topic when we secure an API in a follow-up article, but the standard to use would be [JSON Web Tokens][3].

This handy debugger and library chart shows the support for JSON Web Tokens. You can see that it has a great amount of support across a variety of languages. This means you could actually switch out your authentication mechanism if you choose to do so in the future!

## Conclusion

This was just a look at the how and why of token based authentication. As is always the case in the world of security, there is much, much, much, much (too many?) more to each topic and it varies per use case. We even dove into some topics on scalability which deserves its own conversation as well.

This was a high level quick overview, so please feel free to point out anything that was missed or any questions you have on the matter.

In our next article, we'll be looking at the [anatomy of JSON Web Tokens][3]. For full code examples on how to authenticate a Node API using JSON Web Tokens, check out our book [MEAN Machine][4].

**Edit #1** Adding ACAO header info and CSRF clarifications (thanks to [Emily Stark][5] for the article info) 

[1]: https://en.wikipedia.org/wiki/Cross-site_request_forgery
[2]: https://tools.ietf.org/html/rfc7009
[3]: https://scotch.io/tutorials/the-anatomy-of-a-json-web-token
[4]: https://leanpub.com/mean-machine
[5]: https://twitter.com/estark37

  
