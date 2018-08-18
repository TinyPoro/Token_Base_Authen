
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

Phần lớn API hoặc ứng dụng web nào bạn biết đến dường như đều sử dụng các tokens . Một số ứng dụng như Fa và rất nhiều cebook, Twitter, Google+, GitHub, và nhiều ứng dụng khác sử dụng tokens

Hãy xem cách thức hoạt dộng của chúng.

## Tại sao phải chấp nhận ToKen

Trước khi chúng ta muốn xem cách hoạt động của token based authentication và ưu điểm của nó, chúng ta cần xem cách thức hoạt động authentication mà bạn đã làm trước đây. 

### Server Based Authentication (The Traditional Method)
### Máy chủ Based Authentication ( Máy chủ truyền thống)

> Vì giao thức HTTP là _stateless_, nghĩa là nếu chúng ta authenticate một tài khoản với tên tài khoản và mật khẩu, tiếp theo là những yêu cầu, ứng dụng sẽ không nhận biết được chúng ta là ai. Chúng ta sẽ xác thực lại authenticate.

Phương pháp truyền thống để ứng dụng có thể ghi nhớ được chúng ta là ai **lưu trữ thông tin người dùng đã đăng nhập trên máy chủ** sẽ có một vài cách hiện khác nhau trong session, thường được lưu trữ trên đĩa hoặc trong bộ nhớ
Dưới dây là một biểu đồ quy trình làm việc của máy chủ  based authentication sẽ như thế nào:
Như trang web, ứng dụng, và sự phát triển của ứng dụng di động đã tới, phương pháp xác thực authentication đã cho thấy những vấn đề, đặc biệt là khả năng mở rộng.

## Những vấn đề với máy chủ Based Authentication

Một vài vấn đề chính phát sinh với phương thức xác thực này.

**Sessions**: Mỗi khi người dùng xác thực, máy chủ sẽ tạo ra một bản ghi ở đâu đó trên mmáy chủ của chúng tôi

**Scalability**: Vì sessions được lưu trữ trong bộ nhớ, nó cung cấp vấn đề với khả năng mở rộng. Vì những nhà cung cấp các dịch vụ đám mây bắt đầu tạo các bản sao máy chủ để xử lí việc tải ứng dụng, sẽ có những thông tin quan trọng trong bộ nhớ session sẽ giới hạn khả năng mở rộng của chúng ta.

**CORS**: Khi chúng ta muốn mở rộng ứng dụng để cho phép dữ liệu có thể chạy trên nhiều thiết bị điện thoại, chúng tôi cảm thấy lo lắng về việc chia sẻ tài nguyên gốc(CORS). Khi sử dụng các cuộc gọi AJAX để lấy tài nguyên từ một domain khác(từ điện thoại đến máy chủ API), chúng ta có thể gặp vấn đề với những requests bị cấm.

**CSRF**: Chúng tôi cũng sẽ bảo vệ chống lại [cross-site request forgery][1] (CSRF). Người dùng sẽ đễ bị CSRF tấn công bởi vì họ có thể đã xác thực với một trang web của ngân hàng và điều này có thể được tận dụng khi chúng ta truy cập các trang khác.

Với những vấn đề này, khả năng mở rộng là vấn đề chính, điều này tùy thuộc vào những cố gắng khác nhau.

## Các hoạt động dựa trên Token 

Token based authentication là **stateless**. Chúng tôi không lưu trữ bất kì thông tin nào về người dùng trên máy chủ hoặc trong session.

Khái niệm này quan tâm đến nhiều vấn đề mà phải lưu trữ trên máy chủ.

>Không có  thông tin session này có nghĩa là ứng dụng của  bạn có thể sử dụng và cài thêm nhiều máy khi cần thiết mà không phải lo lắng người dùng sẽ đăng nhập ở đâu.

Mặc dù việc triển khai có thể thay đổi, nhưng sẽ có những ý chính như sau:

1. Quyền truy cập yêu cầu đối với người dùng là tài khoản đăng nhập/ mật khẩu
2. Ứng dụng xác thực thông tin đăng nhập
3. Ứng dụng cung cấp token cho khách hàng đã đăng kí
4. Khách hàng lưu lại token và gửi cùng với mọi request
5. Máy chủ sẽ xác minh token và gửi lại phản hồi

**Every single request will require the token**. This token should be sent in the HTTP header so that we keep with the idea of stateless HTTP requests. We will also need to set our server to accept requests from all domains using `Access-Control-Allow-Origin: *`. What's interesting about designating `*` in the ACAO header is that it does not allow requests to supply credentials like HTTP authentication, client-side SSL certificates, or cookies.
**Mỗi request sẽ có một token**. Mỗi token sẽ được gửi dưới dạng tiêu đề HTTP 

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

  
