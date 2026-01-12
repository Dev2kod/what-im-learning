Great choice 👍 — building a **simple Login/Register CRUD app** is the *best* way to learn ASP.NET Core MVC 8.

Below is a **complete, minimal, DB-First ASP.NET Core MVC 8 app** using:

* ASP.NET Core MVC 8
* Entity Framework Core (DB First)
* SQL Server
* **NO Identity**, **NO extra NuGet packages**
* Simple session-based authentication
* CRUD: Register, Login, Edit Profile, List Users, Delete User

---

# 1️⃣ Database (Already Done ✅)

You said DB First is already implemented and table exists:

```sql
UsersDevesh
-----------
Id        INT (PK, Identity)
Username  NVARCHAR(50)
Password  NVARCHAR(50)
FName     NVARCHAR(50)
LName     NVARCHAR(50)
Age       INT
```

EF Core generated:

* `UsersDevesh.cs`
* `AppDbContext.cs`

---

# 2️⃣ Model (DB First Generated)

### `Models/UsersDevesh.cs`

```csharp
public partial class UsersDevesh
{
    public int Id { get; set; }
    public string Username { get; set; } = null!;
    public string Password { get; set; } = null!;
    public string Fname { get; set; } = null!;
    public string Lname { get; set; } = null!;
    public int Age { get; set; }
}
```

---

# 3️⃣ DbContext

### `Models/AppDbContext.cs`

```csharp
using Microsoft.EntityFrameworkCore;

public partial class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options)
        : base(options)
    {
    }

    public virtual DbSet<UsersDevesh> UsersDeveshs { get; set; }
}
```

---

# 4️⃣ Program.cs (IMPORTANT)

### `Program.cs`

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddSession();

var app = builder.Build();

app.UseStaticFiles();
app.UseRouting();
app.UseSession();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Account}/{action=Login}/{id?}");

app.Run();
```

---

# 5️⃣ Account Controller (Login / Register / Logout)

### `Controllers/AccountController.cs`

```csharp
using Microsoft.AspNetCore.Mvc;

public class AccountController : Controller
{
    private readonly AppDbContext _context;

    public AccountController(AppDbContext context)
    {
        _context = context;
    }

    // LOGIN
    public IActionResult Login()
    {
        return View();
    }

    [HttpPost]
    public IActionResult Login(string username, string password)
    {
        var user = _context.UsersDeveshs
            .FirstOrDefault(u => u.Username == username && u.Password == password);

        if (user == null)
        {
            ViewBag.Error = "Invalid username or password";
            return View();
        }

        HttpContext.Session.SetInt32("UserId", user.Id);
        HttpContext.Session.SetString("Username", user.Username);

        return RedirectToAction("Index", "Users");
    }

    // REGISTER
    public IActionResult Register()
    {
        return View();
    }

    [HttpPost]
    public IActionResult Register(UsersDevesh user)
    {
        _context.UsersDeveshs.Add(user);
        _context.SaveChanges();

        return RedirectToAction("Login");
    }

    // LOGOUT
    public IActionResult Logout()
    {
        HttpContext.Session.Clear();
        return RedirectToAction("Login");
    }
}
```

---

# 6️⃣ Users Controller (CRUD)

### `Controllers/UsersController.cs`

```csharp
using Microsoft.AspNetCore.Mvc;

public class UsersController : Controller
{
    private readonly AppDbContext _context;

    public UsersController(AppDbContext context)
    {
        _context = context;
    }

    private bool IsLoggedIn()
    {
        return HttpContext.Session.GetInt32("UserId") != null;
    }

    public IActionResult Index()
    {
        if (!IsLoggedIn())
            return RedirectToAction("Login", "Account");

        return View(_context.UsersDeveshs.ToList());
    }

    public IActionResult Edit(int id)
    {
        var user = _context.UsersDeveshs.Find(id);
        return View(user);
    }

    [HttpPost]
    public IActionResult Edit(UsersDevesh user)
    {
        _context.UsersDeveshs.Update(user);
        _context.SaveChanges();
        return RedirectToAction("Index");
    }

    public IActionResult Delete(int id)
    {
        var user = _context.UsersDeveshs.Find(id);
        _context.UsersDeveshs.Remove(user);
        _context.SaveChanges();
        return RedirectToAction("Index");
    }
}
```

---

# 7️⃣ Views

## 🔹 Login View

### `Views/Account/Login.cshtml`

```html
<h2>Login</h2>

<form method="post">
    <input name="username" placeholder="Username" />
    <br />
    <input name="password" type="password" placeholder="Password" />
    <br />
    <button type="submit">Login</button>
</form>

<p style="color:red">@ViewBag.Error</p>

<a href="/Account/Register">Register</a>
```

---

## 🔹 Register View

### `Views/Account/Register.cshtml`

```html
<h2>Register</h2>

<form method="post">
    <input name="Username" placeholder="Username" /><br />
    <input name="Password" type="password" placeholder="Password" /><br />
    <input name="Fname" placeholder="First Name" /><br />
    <input name="Lname" placeholder="Last Name" /><br />
    <input name="Age" type="number" placeholder="Age" /><br />
    <button type="submit">Register</button>
</form>
```

---

## 🔹 Users List (CRUD)

### `Views/Users/Index.cshtml`

```html
<h2>Users</h2>

<a href="/Account/Logout">Logout</a>

<table border="1">
    <tr>
        <th>Username</th>
        <th>Name</th>
        <th>Age</th>
        <th>Actions</th>
    </tr>

@foreach (var u in Model)
{
    <tr>
        <td>@u.Username</td>
        <td>@u.Fname @u.Lname</td>
        <td>@u.Age</td>
        <td>
            <a href="/Users/Edit/@u.Id">Edit</a> |
            <a href="/Users/Delete/@u.Id">Delete</a>
        </td>
    </tr>
}
</table>
```

---

## 🔹 Edit User

### `Views/Users/Edit.cshtml`

```html
<h2>Edit User</h2>

<form method="post">
    <input type="hidden" name="Id" value="@Model.Id" />

    Username: <input name="Username" value="@Model.Username" /><br />
    Password: <input name="Password" value="@Model.Password" /><br />
    First Name: <input name="Fname" value="@Model.Fname" /><br />
    Last Name: <input name="Lname" value="@Model.Lname" /><br />
    Age: <input name="Age" value="@Model.Age" /><br />

    <button type="submit">Save</button>
</form>
```

---

# 8️⃣ What You Have Learned 🎯

✔ MVC Pattern
✔ DB First EF Core
✔ Session-based login
✔ CRUD operations
✔ Routing & Controllers
✔ Razor Views

---

# 9️⃣ Next Improvements (Highly Recommended)

* 🔐 Hash passwords (SHA256 / BCrypt)
* 🛡 Authorization filters
* 🎨 Bootstrap UI
* 📦 Repository pattern
* 🔑 ASP.NET Identity (later)

---

If you want, I can:

* Convert this to **ASP.NET Identity**
* Add **password hashing**
* Add **role-based login**
* Explain **each file line-by-line**

Just tell me 👌
