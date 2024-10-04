## Thông tin

Thư viện hổ trợ tích cổng thanh toán VNPay phát triển trên nền tảng [Omnipay League](https://github.com/thephpleague/omnipay).

Để nắm sơ lược về khái niệm và cách sử dụng các **Omnipay** gateways bạn hãy truy cập vào [đây](https://omnipay.thephpleague.com/) 
để kham khảo.

## Cài đặt

Cài đặt Omnipay VNPay thông qua [Composer](https://getcomposer.org):

```bash
composer require phuongnamsoft/omnipay-vnpay
```
## Cách sử dụng

hoặc nếu bạn muốn sử dụng không dựa trên framework thì tiếp tục xem tiếp.

### Khởi tạo gateway:

```php
use Omnipay\Omnipay;

$gateway = Omnipay::create('VNPay');
$gateway->initialize([
    'vnp_TmnCode' => 'Do VNPay cấp',
    'vnp_HashSecret' => 'Do VNPay cấp',
]);
```

Gateway khởi tạo ở trên dùng để tạo các yêu cầu xử lý đến VNPay hoặc dùng để nhận yêu cầu do VNPay gửi đến.

### Tạo yêu cầu thanh toán:

```php
$response = $gateway->purchase([
    'vnp_TxnRef' => time(),
    'vnp_OrderType' => 100000,
    'vnp_OrderInfo' => time(),
    'vnp_IpAddr' => '127.0.0.1',
    'vnp_Amount' => 1000000,
    'vnp_ReturnUrl' => 'https://github.com/phuongnamsoft',
])->send();

if ($response->isRedirect()) {
    $redirectUrl = $response->getRedirectUrl();
    
    // TODO: chuyển khách sang trang VNPay để thanh toán
}
```

Kham khảo thêm các tham trị khi tạo yêu cầu và VNPay trả về tại [đây](https://sandbox.vnpayment.vn/apis/docs/huong-dan-tich-hop/#t%E1%BA%A1o-url-thanh-to%C3%A1n).


### Kiểm tra thông tin `vnp_ReturnUrl` khi khách được VNPay redirect về:

```php
$response = $gateway->completePurchase()->send();

if ($response->isSuccessful()) {
    // TODO: xử lý kết quả và hiển thị.
    print $response->vnp_Amount;
    print $response->vnp_TxnRef;
    
    var_dump($response->getData()); // toàn bộ data do VNPay gửi sang.
    
} else {

    print $response->getMessage();
}
```

Kham khảo thêm các tham trị khi VNPay trả về tại [đây](https://sandbox.vnpayment.vn/apis/docs/huong-dan-tich-hop/#code-returnurl).

### Kiểm tra thông tin `IPN` do VNPay gửi sang:

```php
$response = $gateway->notification()->send();

if ($response->isSuccessful()) {
    // TODO: xử lý kết quả.
    print $response->vnp_Amount;
    print $response->vnp_TxnRef;
    
    var_dump($response->getData()); // toàn bộ data do VNPay gửi sang.
    
} else {

    print $response->getMessage();
}
```

Kham khảo thêm các tham trị khi VNPay gửi sang tại [đây](https://sandbox.vnpayment.vn/apis/docs/huong-dan-tich-hop/#code-ipn-url).

### Kiểm tra trạng thái giao dịch:

```php
$response = $gateway->queryTransaction([
    'vnp_TransDate' => 20190705151126,
    'vnp_TxnRef' => 1562314234,
    'vnp_OrderInfo' => time(),
    'vnp_IpAddr' => '127.0.0.1',
    'vnp_TransactionNo' => 496558,
])->send();

if ($response->isSuccessful()) {
    // TODO: xử lý kết quả và hiển thị.
    print $response->getTransactionId();
    print $response->getTransactionReference();
    
    var_dump($response->getData()); // toàn bộ data do VNPay gửi về.
    
} else {

    print $response->getMessage();
}
```

Kham khảo thêm các tham trị khi tạo yêu cầu và VNPay trả về tại [đây](https://goo.gl/FHdM5B).

### Yêu cầu hoàn tiền:

```php
$response = $gateway->refund([
    'vnp_Amount' => 10000,
    'vnp_TransactionType' => '03',
    'vnp_TransDate' => 20190705151126,
    'vnp_TxnRef' => 32321,
    'vnp_OrderInfo' => time(),
    'vnp_IpAddr' => '127.0.0.1',
    'vnp_TransactionNo' => 496558,
])->send();

if ($response->isSuccessful()) {
    // TODO: xử lý kết quả và hiển thị.
    print $response->getTransactionId();
    print $response->getTransactionReference();
    
    var_dump($response->getData()); // toàn bộ data do VNPay gửi về.
    
} else {

    print $response->getMessage();
}
```

Kham khảo thêm các tham trị khi tạo yêu cầu và VNPay trả về tại [đây](https://goo.gl/FHdM5B).

## Dành cho nhà phát triển

