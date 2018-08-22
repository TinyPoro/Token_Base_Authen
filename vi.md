
[Source](https://scotch.io/tutorials/the-ins-and-outs-of-token-based-authentication "Permalink to The Ins and Outs of Token Based Authentication ― Scotch")

# The Ins and Outs of Token Based Authentication ― Scotch

## Giới thiệu

TBA là luôn là 1 thứ đáng chú ý trên các trang web ngày nay. Với hầu hết mỗi công ty web sử dụng 1 API, token sẽ là cách tốt nhất để giải quyết vấn đề xác thực cho nhiều người dùng.

Có vài yếu tố cực kỳ quan trọng khi phải quyết định  TBA cho ứng dụng của bạn. Những lý do chính cho các token là:

Các server không trạng thái và dễ mở rộng

Tính sẵn dùng cho ứng dụng điện thoại 

Truyền xác thực đến các ứng dụng khác

Tăng cường bảo vệ 

## Ai sẽ sử dụng Token Based Authentication?

Phần lớn API hoặc ứng dụng web nào bạn biết đến dường như đều sử dụng các tokens . Những ứng dụng như Facebook, Twitter, Google+, GitHub và rất nhiều cái khác sử dụng tokens

Hãy xem cách thức hoạt dộng của chúng.

## Tại sao phải chấp nhận ToKen

Trước khi chúng ta muốn xem cách hoạt động của token based authentication và ưu điểm của nó, chúng ta cần xem cách thức hoạt động authentication mà bạn đã làm trước đây. 

### Xác thực dựa trên máy chủ ( Phương thức truyền thống)

> Vì giao thức HTTP là _stateless_, nghĩa là nếu chúng ta xác thực một tài khoản với tên tài khoản và mật khẩu, thì ở yêu cầu tiếp theo, ứng dụng sẽ không nhận biết được chúng ta là ai. Chúng ta sẽ phải xác thực lại.

Phương pháp truyền thống để ứng dụng có thể ghi nhớ được chúng ta là ai là **lưu trữ thông tin người dùng đã đăng nhập trên máy chủ** .Có vài cách thực hiện khác nhau trong session, thường được lưu trữ trên đĩa hoặc trong bộ nhớ

Dưới dây là một biểu đồ quy trình làm việc của máy chủ  based authentication sẽ như thế nào:

Vì trang web, ứng dụng, và sự phát triển của ứng dụng di động  ngày 1 phát triển, phương pháp xác thực authentication này đã cho thấy những vấn đề, đặc biệt là khả năng mở rộng.

## Những vấn đề với máy chủ Based Authentication

Một vài vấn đề chính phát sinh với phương thức xác thực này.

**Sessions**: Mỗi khi người dùng xác thực, máy chủ sẽ tạo ra một bản ghi ở đâu đó trên máy chủ của chúng tôi

**Scalability**: Vì sessions được lưu trữ trong bộ nhớ, nó nảy sinh vấn đề với khả năng mở rộng. Vì những nhà cung cấp các dịch vụ đám mây bắt đầu tạo các bản sao máy chủ để xử lí việc tải ứng dụng, sẽ có những thông tin quan trọng trong bộ nhớ session sẽ giới hạn khả năng mở rộng của chúng ta.

**CORS**: Khi chúng ta muốn mở rộng ứng dụng để cho phép dữ liệu có thể chạy trên nhiều thiết bị điện thoại, chúng tôi cảm thấy lo lắng về việc chia sẻ tài nguyên gốc(CORS). Khi sử dụng các lời gọi AJAX để lấy tài nguyên từ một domain khác(từ điện thoại đến máy chủ API), chúng ta có thể gặp vấn đề với những requests bị cấm.

**CSRF**: Chúng tôi cũng sẽ bảo vệ chống lại [cross-site request forgery][1] (CSRF). Người dùng sẽ đễ bị CSRF tấn công bởi vì họ có thể đã xác thực với một trang web của ngân hàng và điều này có thể được tận dụng khi chúng ta truy cập các trang khác.

Với những vấn đề này, khả năng mở rộng là vấn đề chính, điều này tùy thuộc vào những cố gắng khác nhau.

## Các hoạt động dựa trên Token 

Token based authentication là **stateless**. Chúng tôi không lưu trữ bất kì thông tin nào về người dùng trên máy chủ hoặc trong session.

Khái niệm này quan tâm đến nhiều vấn đề mà phải lưu trữ trên máy chủ.

>Không có  thông tin session này có nghĩa là ứng dụng của  bạn có thể mở rộng và cài thêm nhiều máy khi cần thiết mà không phải lo lắng người dùng sẽ đăng nhập ở đâu.

Mặc dù việc triển khai có thể thay đổi, nhưng sẽ có những ý chính như sau:

1. Truy cập yêu cầu người dùng với tài khoản đăng nhập/ mật khẩu
2. Ứng dụng xác thực thông tin đăng nhập
3. Ứng dụng cung cấp token đã đăng kí cho khách hàng 
4. Khách hàng lưu lại token và gửi cùng với mọi request
5. Máy chủ sẽ xác minh token và gửi lại phản hồi

**Mỗi request sẽ yêu cầu một token**. Mỗi token sẽ được gửi trong header HTTP  để chúng ta duy trì ý tưởng stateless HTTP requests. Chúng ta cũng cần đặt máy chủ của chúng ta chấp nhận các request từ tất cả các domain sử dụng  `Access-Control-Allow-Origin: *` Điều tuyệt vời của việc  sử dụng `*` trong ACAO header là nó không cho phép các reuquest để cung cấp các thông tin xác thực như xác thực HTTP, SSL certificates bên người dùng, hay cookie


Dưới đây là 1 minh họa để giải thích quá trình trên

Ngay khi chúng ta đã được xác thực  với những thông tin của chúng ta và chúng ta có token của mình, chúng ta sẽ có thể làm rất nhiều thứ với token này.

Chúng ta thậm chí có thể tạo 1 token dựa trên quyền và truyền nó  theo 1 ứng dụng bên thứ 3 (thông báo 1 ứng dụng mobile mới bạn muốn sử dụng), và  chúng sẽ có quyền truy cập đến dữ liệu của chúng ta -- **nhưng chỉ những thông tin chúng ta cho phép với những token cụ thể**.

## Những lợi ích của Tokens

### Stateless và Scalable


Các token được lưu trữ trên phía người dùng. Hoàn toàn stateless, và sẵn sàng để mở rộng. Cân bằng tải có thể được truyền 1 người dùng đến bất kỳ máy chủ nào của chúng ta vì không có thông tin và trạng thái hay session

Nếu chúng ta giữ thông tin session trên 1 user đăng nhập, điều này sẽ yêu cầu chúng ta liên tục gửi user đó đến cúng 1 máy chủ họ đăng nhập (điều này gọi là  tương đồng session)

Điều này dẫn tới những vấn đề là, 1 vài người dùng sẽ buộc tới 1 vài server và có thể dẫn tới 1 chỗ có lưu lượng rất lớn

Đừng quá lo lắng! Những vấn đề này sẽ được giải quyết với token vì tự token giữ dữ liệu cho người dùng

### Security

Token, không phải cookie, được gửi trên mỗi request và vì thế không có cookie nào được gửi, điều này giúp phòng chống tấn công CSRF. Ngay cả khi  các  triển khai cụ thể của bạn lưu token với cookie trên phía client, cookie chỉ đơn thuần là 1 kĩ thuật lưu chữ thay vì 1 công cụ xác thực. Không có thông tin dựa trên session để tận dụng vì chúng ta không có session nào cả!

Token cũng sẽ hết hạn sau 1 khoảng thời gian, vì vậy 1 người dùng sẽ được yêu cầu  đăng nhập 1 lần nữa. Điều này giúp chúng ta duy trì sự an toàn. Có 1 vài khái niệm  về [thu hồi token][2] cho phép chúng ta vô hiệu 1 token cụ thể và thậm chí 1 nhóm token dựa theo cũng quyền xác thực.

### Extensibility (Friend of A Friend and Permissions)


token sẽ cho phép chúng ta xây dựng những ứng dụng chia sẻ quyền với người khác. Ví dụ, chúng ta liên kết 1 vài tài khoản xã hội tới những tài khoản chỉnh của chúng ta như Facebook hay Twitter.

Khi chúng ta đăng nhập Twitter qua 1 dịch vụ ( như Buffer), chúng ta đang cho phép Buffer đăng lên luồng Twitter của chúng ta.

Bằng cách sử dụng token, đây là cách chúng ta ** cung cấp những quyền được lựa chọn đến ứng dụng bên thứ ba**. Chúng ta thậm chí có thể xây dựng  API của riêng chúng ta và trao những token quyền đặc biệt nếu người dùng của chúng ta muốn trao quyền đến dữ liệu của họ đến ứng dụng khác.
### Multiple Platforms and Domains

Chúng ta đã nói 1 chút về CORS ở bên trên. Khi ứng dụng và dịch vụ của chúng ta phát triển, chúng ta sẽ cần chúng cấp quyền truy cập đến tất cả các loại thiết bị và ứng dụng(vì ứng dụng của  chúng ta sẽ trở nên phổ biến!)

Sử dụng API để phục vụ dữ liệu, chúng ta cũng có thể  có những lựa chọn thiết kế để phục vụ asets từ 1 CDN.  Điều này sẽ loại bỏ vấn đề CORS mang lại sau khi chúng ta thiết lập cấu hình header nhanh cho ứng dụng của chúng ta.
    
    
    Access-Control-Allow-Origin: *
    

từ bây giờ Dữ liệu và tài nguyên của chúng ta sẽ khả dụng cho bất cứ request nào từ bất kỳ domain nếu người dùng có token hợp lệ. 
### Standards Based

KHi tạo token, bạn có vài tùy chọn. Chúng ta  sẽ bàn nhiều hơn chủ đề này khi chúng ta bảo mật 1 API trong bài viết tiếp theo, nhưng chuẩn nên được sử dụng là [JSON WEB TOKENS][3]


Biểu đồ thư viện và trình gỡ lỗi tiện dụng này hiển thị hỗ trợ cho JSON Web tokens Bạn có thể thấy rằng nó hỗ trợ trên rất nhiều ngôn ngữ. Điều này có nghĩa là bạn có thể thực sự chuyển đổi cơ chế xác thực của mình nếu bạn chọn làm như vậy trong tương lai!

## Tổng kết

Đây chỉ là cài nhìn tổng quan về cách thức và lý do của token based authentication. Như 1 lẽ thường tình trong thế giới an ninh, có cực nhiều(quá nhiều?) thứ về mỗi chủ đề và nó thay đổi theo từng trường hợp sử dụng. Thậm chí, chúng tôi còn nghiên cứu một số chủ đề về khả năng mở rộng cũng đáng để nói

Đây là một bài tổng quan cực nhanh, vì vậy xin vui lòng chỉ ra bất cứ điều gì đã bị bỏ lỡ hoặc bất kỳ câu hỏi nào bạn có về vấn đề này.

Trong bài viết tiếp theo của chúng tôi, chúng tôi sẽ xem xét cấu trúc của JSON Web Tokens. Để biết các code ví dụ đầy đủ về cách xác thực API Node bằng cách sử dụng JSON Web Tokens, hãy xem cuốn sách `MEAN Machine` của chúng tôi.

[1]: https://en.wikipedia.org/wiki/Cross-site_request_forgery
[2]: https://tools.ietf.org/html/rfc7009
[3]: https://scotch.io/tutorials/the-anatomy-of-a-json-web-token
[4]: https://leanpub.com/mean-machine
[5]: https://twitter.com/estark37







ád
  
