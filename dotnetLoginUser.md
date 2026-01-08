Below is a **COMPLETE, SIMPLE, WORKING Login + Register + Profile Update system** for **ASP.NET MVC 5 with Entity Framework (Code-First)**.
This is **custom authentication** (not Identity) so it’s **easy to understand for students/interviews**.

You can **copy-paste everything** and run it.

---

# 🔐 FEATURES

✅ Register
✅ Login
✅ Logout
✅ Profile page
✅ Update user info
✅ Session-based authentication

---

# 1️⃣ DATABASE MODEL

## 📄 Models/User.cs

```csharp
using System.ComponentModel.DataAnnotations;

namespace MvcCrudApp.Models
{
    public class User
    {
        public int Id { get; set; }

        [Required]
        public string FullName { get; set; }

        [Required]
        [EmailAddress]
        public string Email { get; set; }

        [Required]
        public string Password { get; set; }
    }
}
```

---

## 📄 Models/ApplicationDbContext.cs

```csharp
using System.Data.Entity;

namespace MvcCrudApp.Models
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext() : base("DefaultConnection") { }

        public DbSet<User> Users { get; set; }
    }
}
```

---

# 2️⃣ ACCOUNT CONTROLLER (LOGIN / REGISTER / PROFILE)

## 📄 Controllers/AccountController.cs

```csharp
using MvcCrudApp.Models;
using System.Linq;
using System.Web.Mvc;

namespace MvcCrudApp.Controllers
{
    public class AccountController : Controller
    {
        ApplicationDbContext db = new ApplicationDbContext();

        // REGISTER
        public ActionResult Register()
        {
            return View();
        }

        [HttpPost]
        public ActionResult Register(User user)
        {
            if (db.Users.Any(x => x.Email == user.Email))
            {
                ViewBag.Error = "Email already exists";
                return View();
            }

            db.Users.Add(user);
            db.SaveChanges();

            return RedirectToAction("Login");
        }

        // LOGIN
        public ActionResult Login()
        {
            return View();
        }

        [HttpPost]
        public ActionResult Login(string email, string password)
        {
            var user = db.Users
                         .Where(x => x.Email == email && x.Password == password)
                         .FirstOrDefault();

            if (user != null)
            {
                Session["UserId"] = user.Id;
                Session["UserName"] = user.FullName;
                return RedirectToAction("Profile");
            }

            ViewBag.Error = "Invalid email or password";
            return View();
        }

        // PROFILE
        public ActionResult Profile()
        {
            if (Session["UserId"] == null)
                return RedirectToAction("Login");

            int id = (int)Session["UserId"];
            var user = db.Users.Find(id);
            return View(user);
        }

        [HttpPost]
        public ActionResult Profile(User updatedUser)
        {
            var user = db.Users.Find(updatedUser.Id);
            user.FullName = updatedUser.FullName;
            user.Email = updatedUser.Email;
            user.Password = updatedUser.Password;

            db.SaveChanges();
            ViewBag.Success = "Profile updated successfully";

            return View(user);
        }

        // LOGOUT
        public ActionResult Logout()
        {
            Session.Clear();
            return RedirectToAction("Login");
        }
    }
}
```

---

# 3️⃣ VIEWS

Create folder:

```
Views/Account
```

---

## 📄 Views/Account/Register.cshtml

```html
@model MvcCrudApp.Models.User

<h2>Register</h2>

@using (Html.BeginForm())
{
    <p>Full Name</p>
    @Html.TextBoxFor(m => m.FullName)

    <p>Email</p>
    @Html.TextBoxFor(m => m.Email)

    <p>Password</p>
    @Html.PasswordFor(m => m.Password)

    <br /><br />
    <input type="submit" value="Register" />
}

<p style="color:red">@ViewBag.Error</p>

<p>@Html.ActionLink("Login", "Login")</p>
```

---

## 📄 Views/Account/Login.cshtml

```html
<h2>Login</h2>

@using (Html.BeginForm())
{
    <p>Email</p>
    <input type="text" name="email" />

    <p>Password</p>
    <input type="password" name="password" />

    <br /><br />
    <input type="submit" value="Login" />
}

<p style="color:red">@ViewBag.Error</p>

<p>@Html.ActionLink("Register", "Register")</p>
```

---

## 📄 Views/Account/Profile.cshtml

```html
@model MvcCrudApp.Models.User

<h2>My Profile</h2>

<p>Welcome, <b>@Session["UserName"]</b></p>

@using (Html.BeginForm())
{
    @Html.HiddenFor(m => m.Id)

    <p>Full Name</p>
    @Html.TextBoxFor(m => m.FullName)

    <p>Email</p>
    @Html.TextBoxFor(m => m.Email)

    <p>Password</p>
    @Html.PasswordFor(m => m.Password)

    <br /><br />
    <input type="submit" value="Update Profile" />
}

<p style="color:green">@ViewBag.Success</p>

<p>@Html.ActionLink("Logout", "Logout")</p>
```

---

# 4️⃣ DATABASE CONNECTION

## 📄 Web.config

```xml
<connectionStrings>
  <add name="DefaultConnection"
       connectionString="Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=UserAuthDb;Integrated Security=True"
       providerName="System.Data.SqlClient" />
</connectionStrings>
```

---

# 5️⃣ CREATE DATABASE

Open **Package Manager Console**:

```powershell
Enable-Migrations
Add-Migration CreateUserTable
Update-Database
```

---

# 6️⃣ RUN APPLICATION 🚀

URLs:

```
/Account/Register
/Account/Login
/Account/Profile
```

---

# 🧠 INTERVIEW EXPLANATION

> “This is a custom MVC authentication system using Entity Framework, session-based login, and profile update functionality.”

---

# 🔥 NEXT UPGRADES (tell me if you want)

✔ Password hashing
✔ ASP.NET Identity
✔ Bootstrap UI
✔ Role-based login (Admin/User)
✔ Profile picture upload

Just tell me 👍
