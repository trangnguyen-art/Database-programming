# MyComesticShop

> **Một giải pháp cửa hàng mỹ phẩm (ASP.NET Core + EF Core) — backend API, quản lý sản phẩm, giỏ hàng, đơn hàng và quản trị.**

## Mô tả ngắn

Dự án MyComesticShop là một ứng dụng thương mại điện tử mẫu dành cho cửa hàng mỹ phẩm, được tổ chức dưới dạng một giải pháp .NET (`*.sln`). Mục tiêu của dự án là cung cấp nền tảng backend RESTful API (ASP.NET Core) kết hợp với lớp truy xuất dữ liệu (Entity Framework Core) để quản lý sản phẩm, danh mục, người dùng, giỏ hàng và xử lý đơn hàng. README này mô tả cấu trúc, công nghệ, cách cài đặt, chạy local và một số ví dụ API giúp nhanh chóng triển khai và phát triển mở rộng.

---

## Tính năng chính

* Quản lý sản phẩm (CRUD), phân loại, hình ảnh và thuộc tính.
* Quản lý người dùng & phân quyền (Admin / Customer).
* Giỏ hàng, giỏ tạm, và quy trình đặt hàng cơ bản.
* Xử lý đơn hàng, trạng thái (Pending, Confirmed, Shipped, Delivered, Cancelled).
* Tích hợp EF Core với migrations để quản lý schema.
* Cấu hình bằng biến môi trường và hỗ trợ Docker.

## Kiến trúc & Công nghệ (gợi ý)

* Backend: ASP.NET Core Web API (.NET 7+)
* ORM: Entity Framework Core
* CSDL: SQL Server / PostgreSQL (tuỳ chọn)
* Frontend: React hoặc Razor Pages (không kèm trong repo .sln nếu là API)
* Docker & docker-compose cho môi trường phát triển/triển khai

## Yêu cầu trước khi chạy

* .NET SDK 7.0 hoặc mới hơn
* SQL Server / PostgreSQL hoặc Docker
* (Tuỳ chọn) Node.js nếu sử dụng frontend React

## Cài đặt nhanh (local)

1. Clone repo:

```bash
git clone <repo-url>
cd MyComesticShop
```

2. Thiết lập biến môi trường (ví dụ `.env`):

```
ConnectionStrings__DefaultConnection="Server=localhost;Database=MyCosmeticDb;User Id=sa;Password=Your_password123;"
ASPNETCORE_ENVIRONMENT=Development
JWT__Key="your-very-strong-secret"
```

3. Tạo/migrate database:

```bash
dotnet ef database update --project src/MyComesticShop.Infrastructure
```

4. Chạy ứng dụng:

```bash
dotnet run --project src/MyComesticShop.Api
```

## Docker (ví dụ docker-compose)

```yaml
version: '3.8'
services:
  db:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      SA_PASSWORD: "Your_password123"
      ACCEPT_EULA: "Y"
    ports:
      - "1433:1433"
  api:
    build: ./src/MyComesticShop.Api
    depends_on:
      - db
    environment:
      - ConnectionStrings__DefaultConnection=Server=db,1433;Database=MyCosmeticDb;User Id=sa;Password=Your_password123;
    ports:
      - "5000:80"
```

## Ví dụ endpoint

* `POST /api/auth/login` — đăng nhập
* `GET /api/products` — lấy danh sách sản phẩm (paging, filter)
* `GET /api/products/{id}` — chi tiết sản phẩm
* `POST /api/cart` — thêm sản phẩm vào giỏ
* `POST /api/orders` — tạo đơn hàng

### Ví dụ gọi nhanh (curl)

```bash
curl -X GET "http://localhost:5000/api/products?page=1&pageSize=12"
```

## Mẫu `Program.cs` (ASP.NET Core minimal setup)

```csharp
var builder = WebApplication.CreateBuilder(args);
// Configuration, DbContext, Identity, JWT, Services
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

## Kiểm thử

* Sử dụng xUnit / NUnit cho unit tests
* Tách service/business logic để dễ test

## Cách đóng góp

1. Fork repository
2. Tạo branch feature: `feature/x-my-feature`
3. Mở pull request với mô tả chi tiết

## License

Mặc định cho MIT — điều chỉnh nếu cần.

---

> Nếu bạn muốn mình dựng README bằng tiếng Anh, thêm ví dụ frontend React, hoặc tách ra README cho từng project con trong solution (`src/*`), cho mình biết — mình sẽ chỉnh ngay.
