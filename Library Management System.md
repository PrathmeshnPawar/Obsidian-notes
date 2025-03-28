

## Backend: .NET Web API

### 1. Create a New .NET Web API Project

```sh
dotnet new webapi -n LibraryManagementAPI
cd LibraryManagementAPI
```

### 2. Install Dependencies

```sh
dotnet add package Microsoft.EntityFrameworkCore
```

```sh
dotnet add package Microsoft.EntityFrameworkCore.Design
```

```sh
dotnet add package Microsoft.EntityFrameworkCore.Npgsql
```

```sh
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

```sh
dotnet add package Swashbuckle.AspNetCore
```

### 3. Configure `appsettings.json`

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=librarydb;Username=postgres;Password=yourpassword"
  },
  "Jwt": {
    "Key": "YourSecretKeyHere",
    "Issuer": "LibraryAPI",
    "Audience": "LibraryFrontend"
  }
}
```

### 4. Create Models

#### User.cs

```csharp
public class User {
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Email { get; set; } = string.Empty;
    public string PasswordHash { get; set; } = string.Empty;
    public bool IsAdmin { get; set; } = false;
}
```

#### Book.cs

```csharp
public class Book {
    public int Id { get; set; }
    public string Title { get; set; } = string.Empty;
    public string Author { get; set; } = string.Empty;
    public string ISBN { get; set; } = string.Empty;
    public int AvailableCopies { get; set; } = 1;
}
```

#### BorrowedBook.cs

```csharp
public class BorrowedBook {
    public int Id { get; set; }
    public int UserId { get; set; }
    public int BookId { get; set; }
    public DateTime BorrowedDate { get; set; } = DateTime.UtcNow;
    public DateTime? ReturnedDate { get; set; }
}
```

### 5. Set Up Database (Entity Framework Core)

#### Create `LibraryDbContext.cs`

```csharp
public class LibraryDbContext : DbContext {
    public LibraryDbContext(DbContextOptions<LibraryDbContext> options) : base(options) { }

    public DbSet<User> Users { get; set; }
    public DbSet<Book> Books { get; set; }
    public DbSet<BorrowedBook> BorrowedBooks { get; set; }
}
```

#### Register in `Program.cs`

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDbContext<LibraryDbContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("DefaultConnection")));
```

#### Run Migrations

```sh
dotnet ef migrations add InitialCreate
```

```sh
dotnet ef database update
```

### 6. Implement Controllers

#### UserController.cs

```csharp
[HttpPost("/login")]
public async Task<IActionResult> Login([FromBody] UserLoginDto loginDto)
{
    var user = await _context.Users.FirstOrDefaultAsync(u => u.Email == loginDto.Email);
    if (user == null || user.PasswordHash != loginDto.Password)
    {
        return Unauthorized(new { message = "Invalid credentials" });
    }
    return Ok(new { token = "fake-jwt-token" });
}
```

#### BookController.cs

```csharp
[HttpPost("/add")]
public async Task<IActionResult> AddBook([FromBody] Book book)
{
    _context.Books.Add(book);
    await _context.SaveChangesAsync();
    return Ok(new { message = "Book added successfully" });
}
```

#### BorrowedBookController.cs (Return Book Functionality)

```csharp
[HttpPost("/return")]
public async Task<IActionResult> ReturnBook([FromBody] int borrowedBookId)
{
    var borrowedBook = await _context.BorrowedBooks.FindAsync(borrowedBookId);
    if (borrowedBook == null || borrowedBook.ReturnedDate != null)
    {
        return BadRequest(new { message = "Invalid return request" });
    }
    borrowedBook.ReturnedDate = DateTime.UtcNow;
    var book = await _context.Books.FindAsync(borrowedBook.BookId);
    if (book != null)
    {
        book.AvailableCopies++;
    }
    await _context.SaveChangesAsync();
    return Ok(new { message = "Book returned successfully" });
}
```

## Frontend: React (Vite + Tailwind)

### 1. Create React App

```sh
npm create vite@latest library-management --template react
cd library-management
npm install
```

### 2. Install Dependencies

```sh
npm install axios react-router-dom tailwindcss
```

```sh
npx tailwindcss init -p
```

### 3. Configure Tailwind

Modify `tailwind.config.js`:

```js
module.exports = {
  content: ["./index.html", "./src/**/*.{js,jsx,ts,tsx}"],
  theme: { extend: {} },
  plugins: [],
};
```

### 4. Create Login Page (`Login.jsx`)

```jsx
import { useState } from "react";
import axios from "axios";

const Login = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleLogin = async () => {
    try {
      const res = await axios.post("http://localhost:5000/api/users/login", { email, password });
      localStorage.setItem("token", res.data.token);
      alert("Login Successful!");
    } catch (error) {
      alert("Invalid credentials");
    }
  };

  return (
    <div>
      <input type="email" placeholder="Email" onChange={(e) => setEmail(e.target.value)} />
      <input type="password" placeholder="Password" onChange={(e) => setPassword(e.target.value)} />
      <button onClick={handleLogin}>Login</button>
    </div>
  );
};

export default Login;
```

## Next Steps

- Secure API with JWT Authentication.
    
- Create Dashboard for Users & Admins.
    

Would you like additional features added? 🚀