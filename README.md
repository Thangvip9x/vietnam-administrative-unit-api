📘 API chuyển đổi đơn vị hành chính Việt Nam bởi Nguyễn Hữu Thắng, admin tuoitreit.vn
-------------------------------------------
**Demo:** https://diachi.tuoitreit.vn (Có thể chuyển địa chỉ đơn lẻ hoặc hàng loạt, ẩn hiện mã phường xã mới và nghị quyết sắp xếp đơn vị hành chính)

Chuyển đổi chính xác đơn vị hành chính Việt Nam dù phường, xã chỉ sáp nhập 1 phần

**Nguồn:** https://tuoitreit.vn/threads/api-chuyen-doi-don-vi-hanh-chinh-viet-nam-boi-tuoitreit-vn.49632

**Base URL:** `https://diachi.tuoitreit.vn/api.php`

**Authentication:** Gửi API key qua header `X-API-Key` hoặc query parameter `api_key`.

* * *

### I. Lưu ý chung

*   Tất cả các endpoint trả về dữ liệu dạng **JSON UTF-8**.
*   API hỗ trợ **CORS** (Access-Control-Allow-Origin: \*), có thể gọi trực tiếp từ JavaScript client.
*   Các tham số `province_id`, `district_id`, `ward_id` là bắt buộc (trừ endpoint `info` và `provinces`).
*   Nếu API key không hợp lệ → trả về `401 Unauthorized`.
*   Mỗi phản hồi có thể bao gồm `api_usage` thể hiện thông tin quota còn lại trong ngày.

* * *

### 1️⃣ Endpoint mặc định (thông tin API)

Gọi `GET /api.php` không kèm `action` sẽ trả về thông tin mô tả API.

**Ví dụ:** `GET /api.php?api_key=YOUR_API_KEY`

**Phản hồi mẫu:**

    {
        "api_name": "Vietnamese Administrative Unit Converter API",
        "version": "1.0",
        "documentation": "https:\/\/diachi.tuoitreit.vn",
        "available_endpoints": {
            "convert": {
                "method": "GET",
                "params": [
                    "province_id",
                    "district_id",
                    "ward_id",
                    "subward (optional)"
                ],
                "description": "Convert old administrative unit to new one"
            },
            "provinces": {
                "method": "GET",
                "params": [],
                "description": "Get list of provinces"
            },
            "districts": {
                "method": "GET",
                "params": [
                    "province_id"
                ],
                "description": "Get list of districts in a province"
            },
            "wards": {
                "method": "GET",
                "params": [
                    "province_id",
                    "district_id"
                ],
                "description": "Get list of wards in a district"
            },
            "info": {
                "method": "GET",
                "params": [],
                "description": "Get API key usage information"
            }
        },
        "authentication": {
            "method_1": "Header: X-API-Key",
            "method_2": "Query parameter: api_key"
        },
        "rate_limiting": {
            "window": "daily",
            "your_limit": 10000,
            "used_today": 1344,
            "remaining": 8656
        }
    }

* * *

### 2️⃣ Lấy danh sách tỉnh/thành phố

    GET /api.php?action=provinces&api_key=YOUR_API_KEY

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
        {
          "id": "92",
          "name": "Thành phố Cần Thơ"
        },
        {
          "id": "01",
          "name": "Thành phố Hà Nội"
        },
        {
          "id": "31",
          "name": "Thành phố Hải Phòng"
        },
        {
          "id": "79",
          "name": "Thành phố Hồ Chí Minh"
        },
      ...
      ],
      "count": 63
    }

* * *

### 3️⃣ Lấy danh sách quận/huyện

    GET /api.php?action=districts&province_id=<PROVINCE_ID>&api_key=YOUR_API_KEY

**Ví dụ:** `/api.php?action=districts&province_id=01&api_key=YOUR_API_KEY`

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
        {
          "id": "271",
          "name": "Huyện Ba Vì"
        },
        {
          "id": "277",
          "name": "Huyện Chương Mỹ"
        },
        {
          "id": "018",
          "name": "Huyện Gia Lâm"
        },
        {
          "id": "274",
          "name": "Huyện Hoài Đức"
        },
       ...
      ],
    
      "count": 30
    
    }

* * *

### 4️⃣ Lấy danh sách phường/xã

    GET /api.php?action=wards&province_id=<PROVINCE_ID>&district_id=<DISTRICT_ID>&api_key=YOUR_API_KEY

**Ví dụ:** `/api.php?action=wards&province_id=01&district_id=007&api_key=YOUR_API_KEY`

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
        {
          "id": "00277",
          "name": "Phường Bách Khoa"
        },
        {
          "id": "00292",
          "name": "Phường Bạch Mai"
        },
        {
          "id": "00244",
          "name": "Phường Bạch Đằng"
        },
       ...
      ],
      "count": 15
    }

* * *

### 5️⃣ Chuyển đổi đơn vị hành chính

    GET /api.php?action=convert&province_id=<PROVINCE_ID>&district_id=<DISTRICT_ID>&ward_id=<WARD_ID>&api_key=YOUR_API_KEY

**Ví dụ:** `/api.php?action=convert&province_id=01&district_id=007&ward_id=00247&api_key=YOUR_API_KEY`

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
        {
          "ward_name": "Phường Cửa Nam",
          "ward_code": "00082",
          "province_name": "Thành phố Hà Nội",
          "full_address": "Phường Cửa Nam, Thành phố Hà Nội",
          "citation": {
            "header": "1656/NQ-UBTVQH15: Sắp xếp các đơn vị hành chính cấp xã của thành phố Hà Nội",
            "line": "2. Sắp xếp toàn bộ diện tích tự nhiên, quy mô dân số của các phường Hàng Bài, Phan Chu Trinh, Trần Hưng Đạo, một phần diện tích tự nhiên, quy mô dân số của các phường Cửa Nam, Nguyễn Du, Phạm Đình Hổ và phần còn lại của các phường Hàng Bông, Hàng Trống, Tràng Tiền sau khi sắp xếp theo quy định tại khoản 1 Điều này thành phường mới có tên gọi là phường Cửa Nam."
          }
        },
        {
          "ward_name": "Phường Hai Bà Trưng",
          "ward_code": "00256",
          "province_name": "Thành phố Hà Nội",
          "full_address": "Phường Hai Bà Trưng, Thành phố Hà Nội",
          "citation": {
            "header": "1656/NQ-UBTVQH15: Sắp xếp các đơn vị hành chính cấp xã của thành phố Hà Nội",
            "line": "6. Sắp xếp toàn bộ diện tích tự nhiên, quy mô dân số của phường Đồng Nhân, phường Phố Huế, một phần diện tích tự nhiên, quy mô dân số của các phường Bạch Đằng, Lê Đại Hành, Nguyễn Du, Thanh Nhàn và phần còn lại của phường Phạm Đình Hổ sau khi sắp xếp theo quy định tại khoản 2 Điều này thành phường mới có tên gọi là phường Hai Bà Trưng."
          }
        }
      ],
      "api_usage": {
        "requests_today": 1342,
        "rate_limit": 10000,
        "remaining": 8658
      }
    }

* * *

### 6️⃣ Kiểm tra thông tin API Key

    GET /api.php?action=info&api_key=YOUR_API_KEY

**Phản hồi mẫu:**

    {
      "success": true,
      "data": {
        "api_key_name": "tuoitreit",
        "rate_limit": 10000,
        "requests_today": 1343,
        "remaining": 8657,
        "last_request": "2025-11-14 16:28:31"
      }
    }

* * *

### ⚠️ Phản hồi lỗi mẫu

Dưới đây là các loại lỗi phổ biến mà API có thể trả về:

    400 Bad Request
    {
      "error": "Bad Request",
      "message": "Missing province_id parameter"
    }
    
    401 Unauthorized
    {
      "error": "Unauthorized",
      "message": "Invalid or inactive API key. Please contact admin to get API key.",
      "documentation": "https://diachi.tuoitreit.vn"
    }
    
    404 Not Found
    {
      "error": "Not Found",
      "message": "Administrative unit not found with provided IDs"
    }
    
    429 Too Many Requests
    {
      "error": "Rate limit exceeded. Maximum 1000 requests per day."
    }

* * *

### 🔧 Ví dụ sử dụng JavaScript

    fetch('https://diachi.tuoitreit.vn/api.php?action=provinces&api_key=YOUR_API_KEY')
      .then(res => res.json())
      .then(data => {
        if (data.success && data.data.length > 0) {
          console.log('Danh sách tỉnh:', data.data);
          const firstProvinceId = data.data[0].id;
          fetch(`https://diachi.tuoitreit.vn/api.php?action=districts&province_id=${firstProvinceId}&api_key=YOUR_API_KEY`)
            .then(res => res.json())
            .then(districtData => console.log('Danh sách huyện:', districtData));
        }
      });

* * *

### 🔧 Ví dụ sử dụng PHP

    <?php
    $apiKey = 'YOUR_API_KEY';
    $baseUrl = 'https://diachi.tuoitreit.vn/api.php';
    
    function callApi($url, $apiKey) {
        $headers = ['X-API-Key: ' . $apiKey, 'Content-Type: application/json'];
        $context = stream_context_create([
            'http' => ['method' => 'GET', 'header' => implode("\r\n", $headers), 'timeout' => 30]
        ]);
        $result = file_get_contents($url, false, $context);
        if ($result === false) throw new Exception('Lỗi khi gọi API');
        return json_decode($result, true);
    }
    
    try {
        $url = "$baseUrl?action=provinces&api_key=$apiKey";
        $response = callApi($url, $apiKey);
        print_r($response);
    } catch (Exception $e) {
        echo "Lỗi: " . $e->getMessage();
    }
    ?>

* * *

**Liên hệ để nhận API key:** Nguyễn Hữu Thắng – ☎️ 0981 869 555 – 📧 [admin@tuoitreit.vn](mailto:admin@tuoitreit.vn)
