## Thuở sơ khai
Giả sử bạn có 1 REST API và bạn muốn giới hạn truy cập chỉ cho một nhóm người dùng có thẩm quyền. Bằng một cách ngây thơ nhất, API sẽ yêu cầu username và password, sau đó sẽ tìm trong cơ sở dữ liệu xem cặp username và password đó có tồn tại hay không. Ở bước này ta kiểm tra authenticity. Cuối cùng, ta sẽ kiểm tra liệu rằng người dùng đã được xác thực đó có được uỷ quyền để thực hiện request đó hay không. Nếu cả 2 lần kiểm tra đều thoả, API sẽ được thực hiện.

## Vấn đề của state
Giao thức HTTP là stateless, có nghĩa là một request mới sẽ không biết bất kì điều gì về request trước nó, cho nên chúng ta cần phải xác thực lại cho mỗi một request mới.
Cách truyền thống để giải quyết vấn đề này là sử dụng Server Side Sessions (SSS). Ở ngữ cảnh này, đầu tiên ta kiểm tra username và password, nếu bước này thành công, server sẽ lưu một session id ở vùng nhớ chính và trả nó về cho client. Kể từ thời điểm này, client chỉ cần gửi session id của nó là server có thể nhận ra được. Tuy nhiên cách này sẽ nảy sinh về vấn đề về Scale.

## Vấn đề Scale
Trong kỉ nguyên API, endpoints của chúng ta có thể đối mặt với 1 lượng request lớn, cho nên hệ thống cần phải được scale. Có 2 loại scale:
+ Vertical scale: thêm tài nguyên cho server -> cách này khá đắt đỏ kèm với sự hạn chế về khả năng scale lâu dài.
+ Horizontal scale: đơn giản hơn và tiết kiệm chi phí hơn, ta chỉ cần thêm một server mới sau một cái load balancer.
-> Sử dụng horizontal scale với load balancer sẽ rất khó cho việc áp dụng Server Side Session do session id chỉ được lưu ở 1 server khi có 1 request mới đến. Tới đây nếu muốn áp dụng tiếp Server Side Session, chúng ta có thể sử dụng 2 phương án:
+ Đồng bộ hoá session giữa các server -> dễ sinh ra lỗi.
+ Sử dụng một cơ sở dữ liệu ngoài với in memory -> cần phải thêm thành phần khác vào hệ thống (cơ sở dữ liệu).
=> Cần tìm kiếm 1 giải pháp tối ưu hơn.

## JSON WEB TOKEN (JWT)
JSON Web Token (JWT) là một chuẩn mở (RFC 7519) định nghĩa một phương thức để truyền tải thông tin - như là xác thực và uỷ quyền giữa 2 phía: một issuer và một audience. Giao tiếp là an toàn bởi vì mỗi token được phát hành đều được kí điện tử, cho nên là người sử dụng có thể kiểm tra token có mang tính xác thực không hay là giả mạo.
Mỗi token là độc lập với nhau, điều này có nghĩa là nó chứa tất cả thông tin cần thiết để cho phép hoặc từ chối bất kì request nào tới API. 

JWT
Một JSON web token là một chuỗi text dài được encode. Chuỗi này bao gồm 3 phần, ngăn cách nhau bởi dấu chấm. Những phần này là:
+ Header
+ Payload
+ Signature
Header.payload.signature

### Header
Header bao gồm các thông tin về token:
{
  "kid": "ywdoAL4WL...rV4InvRo=",
  "alg": "RS256"
}
alg là giải thuật được sử dụng để tạo token (sign token) và key (kid) được sử dụng để kiểm tra nó. Chuỗi JSON này sẽ được encode bởi base64URL.

### Payload hoặc Body
Đây là phần quan trọng nhất của một JWT. Nó chứa các thông tin về client:
```
{
  [...]
  "iss": "https://cognito-idp.eu-west-1.amazonaws.com/XXX",
  "name": "Mariano Calandra",
  "admin": false
}
```
“Iss” là một registered claim, nó biểu thị nhà cung cấp danh tính mà cấp token, ta có thể thêm nhiều claim vào tuỳ thuộc vào nhu cầu của chúng ta.
Sau đó payload được encode bằng base64URL.

### Signature
Phần 3 của token là một chuỗi hash được tính toán qua những bước sau:
+ Nối chuỗi header và payload (được encode) bởi dấu “.”.
+ Hash chuỗi kết quả trên sử dụng giải thuật mã hoá được định trong thuộc tính “alg” trong header với 1 private key.
+ Encode chuỗi hash trên bởi base64URL.

Cuối cùng là kết nối các chuỗi kết quả của 3 phần header, payload và signature lại bằng dấu “.”.
Chú ý rằng chuỗi JWT trông có vẻ như được mã hoá, nhưng không phải vậy, Base64URL không phải là một giải thuật mã hoá, cho nên hãy để tâm đến payload của bạn.

### Xác thực JWT
Bởi vì token là độc lập nhau, chúng ta sở hữu tất cả thông tin để xác thực nó. Ví dụ, chúng ta biết rằng token được kí bằng giải thuật RS256 (thuộc tính ang trong header) và private key. Bây giờ chúng ta cần phải biết làm sao để lấy public key đúng để thực hiện xác thực.
Note: trong mã hoá bất đối xứng, ta sử dụng public key để mã hoá và private key để giải mã. Trong một giải thuật kí số, quá trình này ngược lại, ở đó Data sẽ được kí bằng private key và public key sẽ được dùng để kiểm tra rằng chữ kí có hợp lệ không.
Public key sẽ được tìm thấy dựa trên endpoint của nhà phát hành (trong thuộc tính “iss”, là issuer), từ đó có thể xác thực được chữ kí có hợp lệ hay không, nếu hợp lệ, ta có thể chắc chắn rằng thông tin được chứa trong token là đáng tin cậy.

### Trong tình huống thực tế
Đầu tiên, client sẽ gửi username,password đến cho Authentication server, server này sẽ kiểm tra tính xác thực và cấp về cho client chuỗi JWT. Client request API đến API server, server này sẽ xác thực token và nếu thành công sẽ trả về kết quả API cho client.
Ngoài ra, ta có thể thêm vào payload các custom claim để tăng tính xác thực (kiểm tra 1 người dùng có phải admin không bằng cách gắn claim admin: true vào payload)