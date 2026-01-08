Below is a **complete, minimal, working ASP.NET MVC 5 CRUD project** using **Entity Framework (Code First)**.
You can **copy–paste these files directly** into a new MVC project in Visual Studio and run it.

---

# 1️⃣ Create the Project

In **Visual Studio**:

* File → New → Project
* **ASP.NET Web Application (.NET Framework)**
* Name: `MvcCrudApp`
* Template: **MVC**
* Authentication: **No Authentication**

---

# 2️⃣ Project Structure

```
MvcCrudApp
│
├── Controllers
│   └── ProductsController.cs
│
├── Models
│   ├── Product.cs
│   └── ApplicationDbContext.cs
│
├── Views
│   ├── Products
│   │   ├── Index.cshtml
│   │   ├── Create.cshtml
│   │   ├── Edit.cshtml
│   │   ├── Delete.cshtml
│   │   └── Details.cshtml
│
├── Web.config
├── Global.asax
└── README.md
```

---

# 3️⃣ Model Code

## 📄 Models/Product.cs

```csharp
using System.ComponentModel.DataAnnotations;

namespace MvcCrudApp.Models
{
    public class Product
    {
        public int Id { get; set; }

        [Required]
        [StringLength(100)]
        public string Name { get; set; }

        [Required]
        public decimal Price { get; set; }

        [StringLength(500)]
        public string Description { get; set; }
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
        public ApplicationDbContext() : base("DefaultConnection")
        {
        }

        public DbSet<Product> Products { get; set; }
    }
}
```

---

# 4️⃣ Controller Code

## 📄 Controllers/ProductsController.cs

```csharp
using System.Linq;
using System.Net;
using System.Web.Mvc;
using MvcCrudApp.Models;
using System.Data.Entity;

namespace MvcCrudApp.Controllers
{
    public class ProductsController : Controller
    {
        private ApplicationDbContext db = new ApplicationDbContext();

        // GET: Products
        public ActionResult Index()
        {
            return View(db.Products.ToList());
        }

        // GET: Products/Details/5
        public ActionResult Details(int? id)
        {
            if (id == null)
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);

            Product product = db.Products.Find(id);
            if (product == null)
                return HttpNotFound();

            return View(product);
        }

        // GET: Products/Create
        public ActionResult Create()
        {
            return View();
        }

        // POST: Products/Create
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Product product)
        {
            if (ModelState.IsValid)
            {
                db.Products.Add(product);
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(product);
        }

        // GET: Products/Edit/5
        public ActionResult Edit(int? id)
        {
            if (id == null)
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);

            Product product = db.Products.Find(id);
            if (product == null)
                return HttpNotFound();

            return View(product);
        }

        // POST: Products/Edit/5
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(Product product)
        {
            if (ModelState.IsValid)
            {
                db.Entry(product).State = EntityState.Modified;
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(product);
        }

        // GET: Products/Delete/5
        public ActionResult Delete(int? id)
        {
            if (id == null)
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);

            Product product = db.Products.Find(id);
            if (product == null)
                return HttpNotFound();

            return View(product);
        }

        // POST: Products/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Product product = db.Products.Find(id);
            db.Products.Remove(product);
            db.SaveChanges();
            return RedirectToAction("Index");
        }
    }
}
```

---

# 5️⃣ Views (Razor Pages)

## 📄 Views/Products/Index.cshtml

```html
@model IEnumerable<MvcCrudApp.Models.Product>

<h2>Products</h2>

<p>
    @Html.ActionLink("Create New", "Create")
</p>

<table border="1" cellpadding="5">
    <tr>
        <th>Name</th>
        <th>Price</th>
        <th>Description</th>
        <th>Actions</th>
    </tr>

@foreach (var item in Model)
{
    <tr>
        <td>@item.Name</td>
        <td>@item.Price</td>
        <td>@item.Description</td>
        <td>
            @Html.ActionLink("Edit", "Edit", new { id = item.Id }) |
            @Html.ActionLink("Details", "Details", new { id = item.Id }) |
            @Html.ActionLink("Delete", "Delete", new { id = item.Id })
        </td>
    </tr>
}
</table>
```

---

## 📄 Views/Products/Create.cshtml

```html
@model MvcCrudApp.Models.Product

<h2>Create Product</h2>

@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()

    <p>Name: @Html.TextBoxFor(m => m.Name)</p>
    <p>Price: @Html.TextBoxFor(m => m.Price)</p>
    <p>Description: @Html.TextBoxFor(m => m.Description)</p>

    <input type="submit" value="Save" />
}
```

---

## 📄 Views/Products/Edit.cshtml

```html
@model MvcCrudApp.Models.Product

<h2>Edit Product</h2>

@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()
    @Html.HiddenFor(m => m.Id)

    <p>Name: @Html.TextBoxFor(m => m.Name)</p>
    <p>Price: @Html.TextBoxFor(m => m.Price)</p>
    <p>Description: @Html.TextBoxFor(m => m.Description)</p>

    <input type="submit" value="Update" />
}
```

---

## 📄 Views/Products/Details.cshtml

```html
@model MvcCrudApp.Models.Product

<h2>Product Details</h2>

<p><strong>Name:</strong> @Model.Name</p>
<p><strong>Price:</strong> @Model.Price</p>
<p><strong>Description:</strong> @Model.Description</p>

<p>@Html.ActionLink("Back to List", "Index")</p>
```

---

## 📄 Views/Products/Delete.cshtml

```html
@model MvcCrudApp.Models.Product

<h2>Delete Product</h2>

<p>Are you sure you want to delete this?</p>

<p><strong>@Model.Name</strong></p>

@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()
    <input type="submit" value="Delete" />
}
```

---

# 6️⃣ Database Configuration

## 📄 Web.config (connection string)

```xml
<connectionStrings>
  <add name="DefaultConnection"
       connectionString="Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=MvcCrudDb;Integrated Security=True"
       providerName="System.Data.SqlClient" />
</connectionStrings>
```

---

# 7️⃣ Create Database (IMPORTANT)

Open **Package Manager Console** and run:

```powershell
Enable-Migrations
Add-Migration InitialCreate
Update-Database
```

---

# 8️⃣ Run the App 🚀

* Press **F5**
* Open browser:

```
https://localhost:{port}/Products
```

---

# ✅ What You Have Now

✔ Full MVC project
✔ Entity Framework CRUD
✔ Database
✔ Ready for GitHub

If you want:

* ASP.NET Core version
* API + MVC
* Authentication
* Bootstrap UI

Just tell me 👍
