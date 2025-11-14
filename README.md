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
      "documentation": "https://diachi.tuoitreit.vn",
      "available_endpoints": {
        "convert": { "method": "GET", "params": ["province_id", "district_id", "ward_id", "subward (optional)"] },
        "provinces": { "method": "GET" },
        "districts": { "method": "GET", "params": ["province_id"] },
        "wards": { "method": "GET", "params": ["province_id", "district_id"] },
        "info": { "method": "GET" }
      },
      "authentication": {
        "method_1": "Header: X-API-Key",
        "method_2": "Query parameter: api_key"
      },
      "rate_limiting": {
        "window": "daily",
        "your_limit": 1000,
        "used_today": 5,
        "remaining": 995
      }
    }

* * *

### 2️⃣ Lấy danh sách tỉnh/thành phố

    GET /api.php?action=provinces&api_key=YOUR_API_KEY

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
        { "id": 1, "name": "Hà Nội" },
        { "id": 79, "name": "Hồ Chí Minh" },
        { "id": 49, "name": "Quảng Nam" }
      ],
      "count": 3
    }

* * *

### 3️⃣ Lấy danh sách quận/huyện

    GET /api.php?action=districts&province_id=<PROVINCE_ID>&api_key=YOUR_API_KEY

**Ví dụ:** `/api.php?action=districts&province_id=49&api_key=YOUR_API_KEY`

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
        { "id": 502, "name": "Thành phố Tam Kỳ" },
        { "id": 503, "name": "Thị xã Điện Bàn" }
      ],
      "count": 2
    }

* * *

### 4️⃣ Lấy danh sách phường/xã

    GET /api.php?action=wards&province_id=<PROVINCE_ID>&district_id=<DISTRICT_ID>&api_key=YOUR_API_KEY

**Ví dụ:** `/api.php?action=wards&province_id=49&district_id=502&api_key=YOUR_API_KEY`

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
        { "id": 20335, "name": "Bàn Thạch" },
        { "id": 20336, "name": "An Phú" }
      ],
      "count": 2
    }

* * *

### 5️⃣ Chuyển đổi đơn vị hành chính

    GET /api.php?action=convert&province_id=<PROVINCE_ID>&district_id=<DISTRICT_ID>&ward_id=<WARD_ID>&api_key=YOUR_API_KEY

**Ví dụ:** `/api.php?action=convert&province_id=49&district_id=502&ward_id=20335&api_key=YOUR_API_KEY`

**Phản hồi mẫu:**

    {
      "success": true,
      "data": [
      {
        "ward_name": "Bàn Thạch",
        "ward_code": "20335",
        "district_name": "Thành phố Tam Kỳ",
        "province_name": "Thành phố Đà Nẵng",
        "citation": {
          "header": "1659/NQ-UBTVQH15: Sắp xếp các đơn vị hành chính cấp xã của thành phố Đà Nẵng",
          "line": "16. Sắp xếp toàn bộ diện tích tự nhiên, quy mô dân số của phường Tân Thạnh, phường Hòa Thuận và xã Tam Thăng thành phường mới có tên gọi là phường Bàn Thạch."
        }
      }
      ],
      "api_usage": {
        "requests_today": 5,
        "rate_limit": 1000,
        "remaining": 995
      }
    }

* * *

### 6️⃣ Kiểm tra thông tin API Key

    GET /api.php?action=info&api_key=YOUR_API_KEY

**Phản hồi mẫu:**

    {
      "success": true,
      "data": {
        "api_key_name": "Tên người dùng",
        "rate_limit": 1000,
        "requests_today": 5,
        "remaining": 995,
        "last_request": "2025-11-13 10:00:00"
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
