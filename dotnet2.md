Here’s a **ready-to-paste `README.md`** version of your ASP.NET MVC Data Passing Guide, formatted cleanly for GitHub:

```markdown
# ASP.NET MVC Data Passing Guide  
*(ViewData, ViewBag, TempData, Strongly Typed Views)*

This guide is for beginners who only know basic routing in ASP.NET MVC and want to understand how data moves from Controller to View.

---

## MVC Flow (Big Picture)

```
Request → Controller → View → Response
```

The controller prepares data, and the view displays it.

---

## 4 Main Ways to Pass Data from Controller to View

| Method              | Lifetime       | Type Safety | Common Use            |
|---------------------|----------------|-------------|-----------------------|
| **ViewData**        | Same request   | No          | Small data            |
| **ViewBag**         | Same request   | No          | Small data (clean syntax) |
| **TempData**        | Next request   | No          | Redirect messages     |
| **Strongly Typed**  | Same request   | Yes         | Real applications     |

---

## ViewData

**What it is:**
- A dictionary (key → value)
- Used to pass data from controller to view
- Keys are strings

**Controller:**
```csharp
public IActionResult Index()
{
    ViewData["Message"] = "Hello from ViewData";
    return View();
}
```

**View (Index.cshtml):**
```html
<h2>@ViewData["Message"]</h2>
```

**Notes:**
- No compile-time checking
- Typo in key causes runtime error
- Not recommended for large applications

---

## ViewBag

**What it is:**
- A dynamic wrapper around ViewData
- Cleaner and easier syntax

**Controller:**
```csharp
public IActionResult Index()
{
    ViewBag.Message = "Hello from ViewBag";
    ViewBag.Age = 18;
    return View();
}
```

**View:**
```html
<h2>@ViewBag.Message</h2>
<p>Age: @ViewBag.Age</p>
```

**Notes:**
- Same lifetime as ViewData
- No type safety
- Easier to read and write
- ViewBag is basically ViewData with better syntax

---

## TempData

**Why TempData exists:**
- ViewData and ViewBag do **NOT** survive redirects

**Example:**
```csharp
return RedirectToAction("Index");
```
All data is lost after redirect.  
TempData solves this problem.

**Controller:**
```csharp
public IActionResult Save()
{
    TempData["Success"] = "Data saved successfully!";
    return RedirectToAction("Index");
}
```

**View (Index.cshtml):**
```csharp
@if (TempData["Success"] != null)
{
    <p style="color:green">@TempData["Success"]</p>
}
```

**Notes:**
- Data lives for one request only
- Stored in session
- Automatically removed after it is read
- Best for success or error messages

---

## Strongly Typed Views (Recommended)

This is the professional and correct way to pass data in MVC.

**Step 1: Create a Model**

File: `Models/Student.cs`
```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

**Step 2: Send Model from Controller**
```csharp
public IActionResult Details()
{
    Student s = new Student
    {
        Id = 1,
        Name = "Alex",
        Age = 20
    };

    return View(s);
}
```

**Step 3: Strongly Type the View**

At the top of `Details.cshtml`:
```csharp
@model Student
```

Use the model in the view:
```html
<h2>Student Details</h2>
<p>Id: @Model.Id</p>
<p>Name: @Model.Name</p>
<p>Age: @Model.Age</p>
```

---

## Why Strongly Typed Views Are Best
- Compile-time checking  
- IntelliSense support  
- Refactor-safe  
- Clean and maintainable  
- Slightly more setup required  

---

## Comparison

| Feature             | ViewBag | TempData | Strongly Typed |
|---------------------|---------|----------|----------------|
| **Type safety**     | No      | No       | Yes            |
| **Survives redirect** | No    | Yes      | No             |
| **Recommended for apps** | No | Sometimes | Yes            |

---

## When to Use What
- **ViewBag / ViewData** → Small UI values  
- **TempData** → Messages after redirect  
- **Strongly Typed Views** → Main application data  

---

## Easy Way to Remember
- **ViewBag → Sticky note**  
- **TempData → Passing note**  
- **Strongly Typed View → Contract**  

---

## Suggested Next Topics
- Model Binding (Form → Controller)  
- Passing Lists (`List<Student>`)  
- Layouts and `_ViewStart.cshtml`  
- MVC Project Structure  
- MVC vs Razor Pages  

---
```

This is fully GitHub‑ready — paste it into your repo’s `README.md` and it will render with tables, code blocks, and headings nicely.  

Would you like me to also add a **diagram image (ASCII or Mermaid)** showing the MVC flow visually so your README looks even more engaging?
