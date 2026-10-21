# BÁO CÁO THỰC HÀNH KIỂM THỬ TỰ ĐỘNG VỚI SELENIUM WEBDRIVER
> **Môn học / Chuyên đề:** Kiểm thử phần mềm & Đảm bảo chất lượng (Software Testing & QA)  
> **Người thực hiện:** Hwaranz  
> **GitHub Repository:** [https://github.com/Hwaranz/selenium](https://github.com/Hwaranz/selenium)  
> **Website mục tiêu kiểm thử:** [SauceDemo (Swag Labs)](https://www.saucedemo.com/)  
> **Công nghệ sử dụng:** Python 3.14, Selenium WebDriver 4.51+, Pytest, Pytest-HTML, GitHub Actions  

---

## MỤC LỤC
1. [Giới thiệu & Mục tiêu đề tài](#1-giới-thiệu--mục-tiêu-đề-tài)
2. [Phần 1: Nghiên cứu lý thuyết Selenium WebDriver](#2-phần-1-nghiên-cứu-lý-thuyết-selenium-webdriver)
   - 2.1. [Tổng quan & Kiến trúc Selenium WebDriver](#21-tổng-quan--kiến-trúc-selenium-webdriver)
   - 2.2. [So sánh Selenium với Cypress và Playwright](#22-so-sánh-selenium-với-cypress-và-playwright)
   - 2.3. [Chiến lược định vị phần tử (Locators)](#23-chiến-lược-định-vị-phần-tử-locators)
   - 2.4. [Cơ chế đồng bộ hóa (Synchronization & Waits)](#24-cơ-chế-đồng-bộ-hóa-synchronization--waits)
   - 2.5. [Mô hình thiết kế Page Object Model (POM)](#25-mô-hình-thiết-kế-page-object-model-pom)
   - 2.6. [Tài liệu & Nguồn học tập tham khảo](#26-tài-liệu--nguồn-học-tập-tham-khảo)
3. [Phần 2: Thiết kế kịch bản kiểm thử (Test Cases)](#3-phần-2-thiết-kế-kịch-bản-kiểm-thử-test-cases)
   - [Bảng tổng hợp kịch bản kiểm thử](#bảng-tổng-hợp-kịch-bản-kiểm-thử)
   - [Chi tiết từng Test Case](#chi-tiết-từng-test-case)
4. [Phần 3: Kiến trúc & Cấu trúc mã nguồn](#4-phần-3-kiến-trúc--cấu-trúc-mã-nguồn)
5. [Phần 4: Hướng dẫn cài đặt và thực thi](#5-phần-4-hướng-dẫn-cài-đặt-và-thực-thi)
6. [Phần 5: Báo cáo kết quả kiểm thử](#6-phần-5-báo-cáo-kết-quả-kiểm-thử)
7. [Phần 6: Đánh giá & Kết luận](#7-phần-6-đánh-giá--kết-luận)

---

## 1. Giới thiệu & Mục tiêu đề tài

Trong quy trình phát triển phần mềm hiện đại (Agile / DevOps), **Kiểm thử tự động (Automation Testing)** đóng vai trò then chốt trong việc rút ngắn chu kỳ phát hành, phát hiện sớm các lỗi hồi quy (Regression Bugs), và đảm bảo chất lượng hệ thống một cách nhất quán.

Dự án này được xây dựng nhằm mục đích:
1. Nghiên cứu sâu về công cụ kiểm thử giao diện người dùng **Selenium WebDriver**.
2. Vận dụng kiến thức thực tế để xây dựng bộ kịch bản kiểm thử tự động hoàn chỉnh (tối thiểu 05 test cases, thực tế xây dựng **08 test cases**) cho ứng dụng thương mại điện tử **SauceDemo (Swag Labs)**.
3. Áp dụng mô hình chuẩn công nghiệp **Page Object Model (POM)** kết hợp cùng framework kiểm thử **Pytest**, hỗ trợ xuất báo cáo HTML trực quan và tự động hóa CI/CD với **GitHub Actions**.

---

## 2. Phần 1: Nghiên cứu lý thuyết Selenium WebDriver

### 2.1. Tổng quan & Kiến trúc Selenium WebDriver

Selenium là một bộ công cụ mã nguồn mở hàng đầu thế giới dành cho việc tự động hóa trình duyệt web. Kể từ phiên bản **Selenium 4**, Selenium tuân thủ hoàn toàn tiêu chuẩn **W3C WebDriver Recommendation**, cho phép tương tác trực tiếp với trình duyệt mà không cần chuyển đổi mã qua giao thức JSON Wire Protocol trung gian như các phiên bản cũ.

Kiến trúc hoạt động của Selenium WebDriver gồm 4 thành phần chính:
1. **Selenium Client Libraries / Language Bindings:** Thư viện ngôn ngữ lập trình (Python, Java, C#, JavaScript, Ruby) cung cấp các API để lập trình viên viết kịch bản kiểm thử.
2. **W3C WebDriver Protocol:** Giao thức chuẩn hóa quốc tế của W3C định nghĩa định dạng các lệnh giao tiếp HTTP/REST giữa mã nguồn và trình điều khiển trình duyệt.
3. **Browser Drivers:** Các phần mềm driver chuyên biệt do nhà sản xuất trình duyệt cung cấp (ví dụ: `msedgedriver` cho Microsoft Edge, `chromedriver` cho Google Chrome, `geckodriver` cho Firefox). Kể từ Selenium 4.6+, tính năng **Selenium Manager** được tích hợp sẵn giúp tự động tải và quản lý driver phù hợp với phiên bản trình duyệt của máy mà không cần cấu hình thủ công.
4. **Real Browsers:** Trình duyệt thực tế nhận lệnh từ driver và thực thi thao tác (click, nhập văn bản, chuyển trang, chụp ảnh màn hình).

```
+------------------------------------+
|  Mã kiểm thử (Python / Pytest)     |
+------------------------------------+
                 | (API Calls)
                 v
+------------------------------------+
|   Selenium Client Library (W3C)    |
+------------------------------------+
                 | (HTTP REST / W3C Protocol)
                 v
+------------------------------------+
|  Browser Driver (Edge / Chrome)    |
+------------------------------------+
                 | (DevTools / Native Browser Control)
                 v
+------------------------------------+
|      Web Browser (Edge / Chrome)   |
+------------------------------------+
```

---

### 2.2. So sánh Selenium với Cypress và Playwright

| Tiêu chí so sánh | **Selenium WebDriver** | **Playwright** | **Cypress** |
| :--- | :--- | :--- | :--- |
| **Độ phủ ngôn ngữ** | Đa dạng (Python, Java, C#, JS, Ruby) | Python, TypeScript, JS, Java, C# | Chỉ hỗ trợ JavaScript / TypeScript |
| **Kiến trúc** | Giao thức W3C chuẩn quốc tế, Out-of-process | WebSocket / CDP (Chrome DevTools Protocol) | Chạy trực tiếp trong Run-loop của trình duyệt |
| **Hỗ trợ đa trình duyệt** | Chrome, Edge, Firefox, Safari, IE | Chromium, Firefox, WebKit | Chrome, Edge, Firefox (giới hạn Safari) |
| **Đa tab & Đa cửa sổ** | Hỗ trợ đầy đủ và mạnh mẽ | Hỗ trợ tốt qua Multi-context | Không hỗ trợ nhiều tab/cửa sổ |
| **Tốc độ thực thi** | Trung bình - Khá | Nhanh | Nhanh |
| **Hệ sinh thái & Cộng đồng** | Lâu đời nhất, tài liệu đồ sộ, chuẩn mực doanh nghiệp | Đang phát triển rất mạnh | Phổ biến trong nhóm Frontend |

---

### 2.3. Chiến lược định vị phần tử (Locators)

Selenium cung cấp nhiều phương thức để tìm kiếm phần tử HTML trên cây DOM:

1. **By.ID (Khuyến nghị số 1):** Định vị theo thuộc tính `id`. Đây là phương pháp nhanh nhất và ổn định nhất vì `id` là duy nhất trên trang web hợp lệ.
   ```python
   driver.find_element(By.ID, "user-name")
   ```
2. **By.NAME:** Định vị theo thuộc tính `name` (thường dùng trong form input).
   ```python
   driver.find_element(By.NAME, "postalCode")
   ```
3. **By.CSS_SELECTOR (Khuyến nghị số 2):** Định vị theo biểu thức CSS. Tốc độ rất nhanh, cú pháp ngắn gọn, dễ bảo trì.
   ```python
   driver.find_element(By.CSS_SELECTOR, "button[data-test='checkout']")
   ```
4. **By.XPATH:** Định vị dựa trên đường dẫn XML. Rất mạnh mẽ khi cần duyệt ngược lên thẻ cha (`parent::`), tìm kiếm theo quan hệ tương đối hoặc tìm theo nội dung text (`contains(text(), ...)`).
   ```python
   driver.find_element(By.XPATH, "//div[@class='inventory_item'][contains(., 'Backpack')]")
   ```
5. **By.CLASS_NAME / By.TAG_NAME:** Thích hợp khi muốn lấy một danh sách nhiều phần tử có cùng cấu trúc hoặc class.

---

### 2.4. Cơ chế đồng bộ hóa (Synchronization & Waits)

Trong ứng dụng Web hiện đại (Single Page Application - SPA, AJAX, Fetch API), các phần tử thường được render bất đồng bộ. Nếu kiểm thử thực hiện thao tác ngay khi phần tử chưa xuất hiện, lỗi `NoSuchElementException` hoặc `ElementNotInteractableException` sẽ phát sinh.

Selenium cung cấp 3 cơ chế chờ:
- **Implicit Wait (Chờ ngầm định):** Cài đặt thời gian chờ tối đa cho toàn bộ phiên làm việc của driver. Nếu phần tử chưa có mặt, driver sẽ liên tục thăm dò DOM cho đến khi hết thời gian.
- **Explicit Wait (Chờ tường minh - Best Practice):** Sử dụng `WebDriverWait` kết hợp với các điều kiện mong đợi `expected_conditions (EC)` (như `element_to_be_clickable`, `visibility_of_element_located`, `url_contains`). Cơ chế này chỉ chờ chính xác điều kiện của phần tử cần tương tác và tiếp tục ngay khi điều kiện thỏa mãn mà không lãng phí thời gian.
- **Tại sao KHÔNG dùng `time.sleep()`:** `time.sleep()` là cơ chế Hard Sleep làm dừng toàn bộ luồng thực thi trong khoảng thời gian cố định dù trang web đã tải xong từ trước. Việc này khiến test suite chạy rất chậm, không phản ánh đúng hiệu năng thực tế và dễ gây lỗi "flaky test".

---

### 2.5. Mô hình thiết kế Page Object Model (POM)

**Page Object Model (POM)** là một mẫu thiết kế kiến trúc chuẩn mực trong kiểm thử tự động giao diện (UI Automation).

**Nguyên lý cốt lõi:**
- Mỗi trang web (hoặc một thành phần giao diện lớn) được đại diện bởi một Lớp (Class) tương ứng.
- Các thuộc tính của Class là các **Locators** (vị trí phần tử).
- Các phương thức của Class là các **Hành động nghiệp vụ** (Actions) có thể thực hiện trên trang đó (ví dụ: `login()`, `sort_products()`, `add_to_cart()`).
- File kiểm thử (Test Scripts) chỉ gọi các phương thức nghiệp vụ và thực hiện lệnh `assert`, hoàn toàn không chứa mã định vị HTML thô.

**Lợi ích của POM:**
1. **Phân tách trách nhiệm (Separation of Concerns):** Tách biệt tầng kiểm thử logic và tầng giao diện người dùng.
2. **Khả năng tái sử dụng mã cao:** Một hành động đăng nhập có thể dùng lại ở hàng chục kịch bản kiểm thử khác nhau.
3. **Dễ dàng bảo trì (High Maintainability):** Khi nhà phát triển thay đổi giao diện (đổi ID nút, đổi class), kỹ sư QA chỉ cần cập nhật tại một nơi duy nhất trong file Page tương ứng mà không phải sửa hàng chục file test.

---

### 2.6. Tài liệu & Nguồn học tập tham khảo

- **Selenium Official Documentation:** [https://www.selenium.dev/documentation/](https://www.selenium.dev/documentation/)
- **Selenium with Python Documentation:** [https://selenium-python.readthedocs.io/](https://selenium-python.readthedocs.io/)
- **Test Automation University (TAU):** Khóa học *Web Element Locator Strategies* và *Selenium WebDriver with Python*.
- **W3C WebDriver Specification:** [https://www.w3.org/TR/webdriver2/](https://www.w3.org/TR/webdriver2/)
- **Khóa học trực tuyến YouTube:** *Selenium Python Tutorial* của giảng viên Raghav Pal (Automation Step by Step).

---

## 3. Phần 2: Thiết kế kịch bản kiểm thử (Test Cases)

Dự án lựa chọn website **SauceDemo (Swag Labs)** (`https://www.saucedemo.com/`) làm ứng dụng mục tiêu. Đây là hệ thống thương mại điện tử mẫu chuẩn quốc tế được phát triển bởi Sauce Labs nhằm phục vụ nghiên cứu và thực hành kiểm thử phần mềm.

### Bảng tổng hợp kịch bản kiểm thử

| Mã TC | Tên kịch bản kiểm thử | Mô tả nghiệp vụ | Trạng thái thực tế |
| :---: | :--- | :--- | :---: |
| **TC01** | `test_tc01_valid_login` | Đăng nhập thành công với thông tin tài khoản hợp lệ | **PASSED** |
| **TC02** | `test_tc02_locked_out_user` | Kiểm tra xử lý lỗi khi đăng nhập bằng tài khoản bị khóa | **PASSED** |
| **TC03** | `test_tc03_invalid_password` | Kiểm tra xử lý lỗi khi nhập sai mật khẩu | **PASSED** |
| **TC04** | `test_tc04_sort_price_low_to_high` | Sắp xếp danh mục sản phẩm theo giá tăng dần (Low to High) | **PASSED** |
| **TC05** | `test_tc05_sort_price_high_to_low` | Sắp xếp danh mục sản phẩm theo giá giảm dần (High to Low) | **PASSED** |
| **TC06** | `test_tc06_add_and_remove_products_cart` | Thêm nhiều sản phẩm vào giỏ, kiểm tra badge và xóa sản phẩm | **PASSED** |
| **TC07** | `test_tc07_complete_checkout_e2e` | Toàn bộ luồng mua hàng và thanh toán End-to-End (E2E) | **PASSED** |
| **TC08** | `test_tc08_logout_and_session_invalidation` | Đăng xuất và xác thực cơ chế bảo mật phiên truy cập | **PASSED** |

---

### Chi tiết từng Test Case

#### TC01: Đăng nhập thành công với tài khoản hợp lệ
- **Mục tiêu:** Kiểm tra chức năng xác thực người dùng hoạt động chính xác khi cung cấp đúng thông tin.
- **Tiền điều kiện:** Trình duyệt đang ở trang đăng nhập `https://www.saucedemo.com/`.
- **Dữ liệu kiểm thử:** Username: `standard_user` | Password: `secret_sauce`.
- **Các bước thực hiện:**
  1. Mở trang chủ SauceDemo.
  2. Nhập `standard_user` vào trường Username.
  3. Nhập `secret_sauce` vào trường Password.
  4. Nhấn nút "Login".
- **Kết quả mong đợi:** Người dùng được chuyển hướng đến `https://www.saucedemo.com/inventory.html`, trang hiển thị tiêu đề "Products".
- **Kết quả thực tế:** Chuyển hướng thành công, tiêu đề trang là "Products".
- **Đánh giá:** **PASSED**.

#### TC02: Đăng nhập thất bại với tài khoản bị khóa
- **Mục tiêu:** Kiểm tra hệ thống nhận diện và từ chối tài khoản có trạng thái khóa (Locked Out).
- **Tiền điều kiện:** Trình duyệt đang ở trang đăng nhập.
- **Dữ liệu kiểm thử:** Username: `locked_out_user` | Password: `secret_sauce`.
- **Các bước thực hiện:**
  1. Mở trang chủ SauceDemo.
  2. Nhập `locked_out_user` vào trường Username.
  3. Nhập `secret_sauce` vào trường Password.
  4. Nhấn nút "Login".
- **Kết quả mong đợi:** Hệ thống ở lại trang đăng nhập và hiển thị thông báo lỗi: *"Epic sadface: Sorry, this user has been locked out."*.
- **Kết quả thực tế:** Thông báo lỗi hiển thị chính xác như kỳ vọng.
- **Đánh giá:** **PASSED**.

#### TC03: Đăng nhập thất bại với sai mật khẩu
- **Mục tiêu:** Kiểm tra hệ thống xử lý khi người dùng nhập sai mật khẩu.
- **Tiền điều kiện:** Trình duyệt đang ở trang đăng nhập.
- **Dữ liệu kiểm thử:** Username: `standard_user` | Password: `wrong_password_123`.
- **Các bước thực hiện:**
  1. Mở trang đăng nhập.
  2. Nhập `standard_user` và mật khẩu không đúng.
  3. Nhấn nút "Login".
- **Kết quả mong đợi:** Hiển thị thông báo lỗi: *"Epic sadface: Username and password do not match any user in this service"*.
- **Kết quả thực tế:** Thông báo lỗi khớp 100%.
- **Đánh giá:** **PASSED**.

#### TC04: Sắp xếp sản phẩm theo giá tăng dần
- **Mục tiêu:** Kiểm tra bộ lọc giá hiển thị danh sách từ sản phẩm rẻ nhất đến đắt nhất.
- **Tiền điều kiện:** Người dùng đã đăng nhập thành công vào trang sản phẩm.
- **Dữ liệu kiểm thử:** Tùy chọn sắp xếp `lohi` (Price: low to high).
- **Các bước thực hiện:**
  1. Mở dropdown sắp xếp sản phẩm (`.product_sort_container`).
  2. Chọn tùy chọn "Price (low to high)".
  3. Lấy danh sách toàn bộ giá bán của các sản phẩm đang hiển thị.
- **Kết quả mong đợi:** Mảng giá thu được trùng khớp hoàn toàn với mảng giá đã được hàm `sorted()` sắp xếp tăng dần: `prices == sorted(prices)`.
- **Kết quả thực tế:** Dãy giá sắp xếp chính xác: `[$7.99, $9.99, $15.99, $15.99, $29.99, $49.99]`.
- **Đánh giá:** **PASSED**.

#### TC05: Sắp xếp sản phẩm theo giá giảm dần
- **Mục tiêu:** Kiểm tra bộ lọc giá hiển thị danh sách từ đắt nhất đến rẻ nhất.
- **Tiền điều kiện:** Người dùng đã đăng nhập thành công.
- **Dữ liệu kiểm thử:** Tùy chọn sắp xếp `hilo` (Price: high to low).
- **Các bước thực hiện:**
  1. Chọn tùy chọn "Price (high to low)".
  2. Lấy danh sách giá các sản phẩm trên màn hình.
- **Kết quả mong đợi:** `prices == sorted(prices, reverse=True)`.
- **Kết quả thực tế:** Dãy giá: `[$49.99, $29.99, $15.99, $15.99, $9.99, $7.99]`.
- **Đánh giá:** **PASSED**.

#### TC06: Quản lý giỏ hàng (Thêm & Xóa sản phẩm)
- **Mục tiêu:** Kiểm tra tính chính xác của số lượng sản phẩm trên icon badge và danh mục hàng trong giỏ.
- **Tiền điều kiện:** Đã đăng nhập vào trang sản phẩm.
- **Các bước thực hiện:**
  1. Nhấn "Add to cart" cho sản phẩm "Sauce Labs Backpack".
  2. Nhấn "Add to cart" cho sản phẩm "Sauce Labs Bike Light".
  3. Xác thực badge giỏ hàng hiển thị số `2`.
  4. Nhấp vào biểu tượng giỏ hàng để sang trang `/cart.html`.
  5. Xác nhận cả 2 sản phẩm đều có mặt trong giỏ hàng.
  6. Nhấn nút "Remove" cho sản phẩm "Sauce Labs Backpack".
  7. Kiểm tra lại badge và danh sách còn lại.
- **Kết quả mong đợi:** Badge giảm còn `1`, sản phẩm "Backpack" bị xóa khỏi danh sách, chỉ còn lại "Bike Light".
- **Kết quả thực tế:** Dữ liệu giỏ hàng được cập nhật tức thì và chính xác.
- **Đánh giá:** **PASSED**.

#### TC07: Quy trình mua hàng & thanh toán hoàn tất (End-to-End Checkout)
- **Mục tiêu:** Kiểm tra toàn bộ luồng nghiệp vụ mua sắm từ đầu đến khi thanh toán thành công.
- **Tiền điều kiện:** Đã đăng nhập và thêm ít nhất 01 sản phẩm vào giỏ hàng.
- **Dữ liệu kiểm thử:** First Name: `Nguyen` | Last Name: `Van A` | Postal Code: `70000`.
- **Các bước thực hiện:**
  1. Từ trang giỏ hàng, nhấn nút "Checkout".
  2. Tại trang Step 1, điền đầy đủ Tên, Họ, Mã bưu chính và nhấn "Continue".
  3. Tại trang Step 2 (Overview), kiểm tra thông tin hiển thị tổng tiền có chứa `Total: $`.
  4. Nhấn nút "Finish".
- **Kết quả mong đợi:** Chuyển sang trang `checkout-complete.html`, xuất hiện tiêu đề lớn: *"Thank you for your order!"* cùng nút "Back Home".
- **Kết quả thực tế:** Đơn hàng được tạo thành công, giao diện cảm ơn hiển thị đầy đủ.
- **Đánh giá:** **PASSED**.

#### TC08: Đăng xuất an toàn & Kiểm tra bảo mật phiên
- **Mục tiêu:** Kiểm tra khả năng hủy phiên đăng nhập và ngăn chặn truy cập trái phép khi chưa xác thực.
- **Tiền điều kiện:** Đang trong phiên làm việc hợp lệ tại trang `inventory.html`.
- **Các bước thực hiện:**
  1. Nhấn biểu tượng menu bánh hamburger bên trái màn hình.
  2. Nhấn vào liên kết "Logout".
  3. Xác thực trình duyệt được điều hướng về trang chủ đăng nhập.
  4. Cố tình sử dụng lệnh `driver.get("https://www.saucedemo.com/inventory.html")` để truy cập lại.
- **Kết quả mong đợi:** Hệ thống lập tức chặn truy cập, giữ người dùng ở trang đăng nhập và hiển thị thông báo lỗi bảo mật: *"Epic sadface: You can only access '/inventory.html' when you are logged in."*.
- **Kết quả thực tế:** Chặn truy cập trái phép chính xác, thông báo lỗi xuất hiện chuẩn xác.
- **Đánh giá:** **PASSED**.

---

## 4. Phần 3: Kiến trúc & Cấu trúc mã nguồn

Dự án được tổ chức theo chuẩn **Page Object Model (POM)**:

```
selenium/
├── .github/
│   └── workflows/
│       └── tests.yml            # CI/CD pipeline tự động chạy kiểm thử trên GitHub Actions
├── pages/                       # Tầng Page Object Model (chứa Locators và Actions)
│   ├── __init__.py
│   ├── base_page.py             # Lớp cơ sở chứa Explicit Waits và phương thức dùng chung
│   ├── login_page.py            # Quản lý giao diện và nghiệp vụ trang Đăng nhập
│   ├── inventory_page.py        # Quản lý trang Danh mục sản phẩm, sắp xếp và giỏ hàng
│   ├── cart_page.py             # Quản lý trang Giỏ hàng và xóa sản phẩm
│   └── checkout_page.py         # Quản lý quy trình Checkout 3 bước
├── tests/                       # Tầng kịch bản kiểm thử (Test Cases)
│   ├── __init__.py
│   ├── test_01_login.py         # TC01, TC02, TC03 (Xác thực đăng nhập & bảo mật)
│   ├── test_02_inventory_sorting.py # TC04, TC05 (Bộ lọc sắp xếp giá sản phẩm)
│   ├── test_03_cart_management.py   # TC06 (Quản lý giỏ hàng & badge)
│   ├── test_04_checkout_flow.py     # TC07 (E2E quy trình mua sắm hoàn chỉnh)
│   └── test_05_logout.py        # TC08 (Đăng xuất & kiểm tra phân quyền phiên)
├── reports/                     # Thư mục chứa báo cáo và ảnh chụp màn hình khi lỗi
│   ├── report.html              # Báo cáo kết quả kiểm thử định dạng HTML
│   └── screenshots/             # Tự động chụp và lưu ảnh khi có test case thất bại
├── .gitignore                   # Loại trừ các file rác, bộ nhớ đệm khỏi Git
├── conftest.py                  # Pytest fixtures cấu hình WebDriver đa trình duyệt
├── pytest.ini                   # Cấu hình mặc định cho Pytest Runner
├── requirements.txt             # Danh sách thư viện phụ thuộc của dự án
└── README.md                    # Tài liệu báo cáo dự án hoàn chỉnh
```

---

## 5. Phần 4: Hướng dẫn cài đặt và thực thi

### 5.1. Yêu cầu môi trường
- **Hệ điều hành:** Windows 10/11, macOS, hoặc Linux.
- **Python:** Phiên bản 3.10 trở lên (khuyên dùng Python 3.11+).
- **Trình duyệt:** Microsoft Edge hoặc Google Chrome.

### 5.2. Cài đặt các bước

1. **Clone mã nguồn từ GitHub:**
   ```bash
   git clone https://github.com/Hwaranz/selenium.git
   cd selenium
   ```

2. **Tạo và kích hoạt môi trường ảo (Khuyến nghị):**
   ```bash
   # Trên Windows:
   python -m venv venv
   .\venv\Scripts\activate

   # Trên Linux/macOS:
   python3 -m venv venv
   source venv/bin/activate
   ```

3. **Cài đặt các thư viện cần thiết:**
   ```bash
   pip install -r requirements.txt
   ```

---

### 5.3. Các lệnh thực thi kiểm thử

- **Chạy toàn bộ kiểm thử ở chế độ ngầm định (Headless Mode):**
  ```bash
  pytest
  ```
  *(Mặc định hệ thống sử dụng Microsoft Edge headless, tự động xuất báo cáo tại `reports/report.html`)*

- **Chạy kiểm thử có hiển thị giao diện trình duyệt trực quan (Headed Mode):**
  ```bash
  pytest --headed
  ```

- **Chạy kiểm thử với trình duyệt Google Chrome:**
  ```bash
  pytest --browser=chrome
  ```

- **Chạy riêng một nhóm kiểm thử cụ thể (ví dụ chỉ kiểm thử Đăng nhập):**
  ```bash
  pytest tests/test_01_login.py -v
  ```

- **Xem báo cáo HTML trực quan:**  
  Sau khi chạy kiểm thử, mở tệp `reports/report.html` bằng bất kỳ trình duyệt nào để xem bảng tổng kết, thời gian thực thi của từng test case.

---

## 6. Phần 5: Báo cáo kết quả kiểm thử

Toàn bộ 08/08 test cases đã được thực thi kiểm thử tự động với kết quả **100% PASSED**:

```text
============================= test session starts =============================
platform win32 -- Python 3.14.7, pytest-9.1.1, pluggy-1.7.0
rootdir: D:\vibecode\selenium
configfile: pytest.ini
testpaths: tests
plugins: platformdirs-4.12.3, html-4.2.0, metadata-3.1.1
collected 8 items

tests/test_01_login.py::TestLogin::test_tc01_valid_login PASSED
tests/test_01_login.py::TestLogin::test_tc02_locked_out_user PASSED
tests/test_01_login.py::TestLogin::test_tc03_invalid_password PASSED
tests/test_02_inventory_sorting.py::TestInventorySorting::test_tc04_sort_price_low_to_high PASSED
tests/test_02_inventory_sorting.py::TestInventorySorting::test_tc05_sort_price_high_to_low PASSED
tests/test_03_cart_management.py::TestCartManagement::test_tc06_add_and_remove_products_cart PASSED
tests/test_04_checkout_flow.py::TestCheckoutFlow::test_tc07_complete_checkout_e2e PASSED
tests/test_05_logout.py::TestLogout::test_tc08_logout_and_session_invalidation PASSED

--- Generated html report: file:///D:/vibecode/selenium/reports/report.html ---
============================= 8 passed in 41.14s ==============================
```

---

## 7. Phần 6: Đánh giá & Kết luận

### 7.1. Kết quả đạt được
1. Nắm vững lý thuyết nền tảng về kiểm thử tự động, kiến trúc giao tiếp W3C của Selenium WebDriver.
2. Xây dựng thành công bộ kịch bản kiểm thử bao phủ toàn bộ các luồng nghiệp vụ quan trọng của một ứng dụng thương mại điện tử: Xác thực, Danh mục sản phẩm, Lọc/Sắp xếp, Giỏ hàng, Đặt hàng (E2E), và Đăng xuất bảo mật.
3. Ứng dụng thành công mẫu thiết kế **Page Object Model (POM)** giúp cấu trúc dự án sáng sủa, độc lập giữa mã tương tác giao diện và mã kiểm thử.
4. Tự động hóa hoàn toàn việc tạo báo cáo HTML và tích hợp kiểm thử liên tục (CI) qua GitHub Actions.

### 7.2. Hướng mở rộng trong tương lai
- **Data-Driven Testing (DDT):** Kết hợp đọc dữ liệu kiểm thử từ file bên ngoài (`JSON`, `CSV` hoặc `Excel`) để kiểm thử hàng loạt bộ dữ liệu khác nhau mà không cần sửa code.
- **Allure Reporting:** Nâng cấp báo cáo kiểm thử bằng Allure Framework với biểu đồ xu hướng, timeline và video quay lại thao tác trình duyệt.
- **Selenium Grid / Docker:** Phân tán việc thực thi kiểm thử song song trên nhiều môi trường container khác nhau nhằm tối ưu thời gian chạy.
