# Asp.Net-Core-Authentication
Login registration  using built in  Asp.Net Core Identity  Authentication &amp; Authorization

<br>

### 1. Create a model class inside the Models folder.
```csharp

using Microsoft.AspNetCore.Identity;

namespace Asp.NetCore_Identity_Auth.Models
{
    public class Users : IdentityUser
    {
       public string Fullname { get; set; }
    }
}

```

Note: The Fullname is not part of the identity properties; it is only added if you want to include additional data in your Register Account Form or Database

<br>
<br>

### 2. Create a new folder in your project and name it whatever you prefer, but in our case, we named it 'Database'. This is the folder where we will place the DbContext Class.
![Step 1](Database.png)

```csharp
using Asp.NetCore_Identity_Auth.Models;
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore;
namespace Asp.NetCore_Identity_Auth.Database
{
    public class _DbContext : IdentityDbContext<Users>
    {
        public _DbContext(DbContextOptions options) : base(options) { }
    }
}

```


<br>
<br>


### 3. Register the following Identity Auth configurations, DbContext configuration and middleware needed inside the Program.cs file.
Here is the code for the DbContext configuration.

```csharp
builder.Services.AddDbContext<_DbContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("database")));
```

<br>

Here is the code for the Identity Auth configuration.

```csharp
builder.Services.AddIdentity<Users, IdentityRole>(option =>
{
    option.Password.RequireDigit = false;
    option.Password.RequireLowercase = false;
    option.Password.RequireUppercase = false;
    option.Password.RequireNonAlphanumeric = false;
    option.Password.RequiredLength = 3;

    option.User.RequireUniqueEmail = true; 
    option.SignIn.RequireConfirmedEmail = false;

})
    .AddEntityFrameworkStores<_DbContext>()
    .AddDefaultTokenProviders();  
```

<br>

Here is the code for Identity Auth Middleware.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

<br>

### Overall Program.cs file set up

```csharp
using Asp.NetCore_Identity_Auth.Database;
using Asp.NetCore_Identity_Auth.Models;
using Microsoft.AspNetCore.Identity;
using Microsoft.EntityFrameworkCore;



var builder = WebApplication.CreateBuilder(args);

//for MVC registration
builder.Services.AddControllersWithViews();

//for DbContext Configuration
builder.Services.AddDbContext<_DbContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("database")));

//Setup for Identity Auth configuration
builder.Services.AddIdentity<Users, IdentityRole>(option =>
{
    option.Password.RequireDigit = false;
    option.Password.RequireLowercase = false;
    option.Password.RequireUppercase = false;
    option.Password.RequireNonAlphanumeric = false;
    option.Password.RequiredLength = 3;

    option.User.RequireUniqueEmail = true; 
    option.SignIn.RequireConfirmedEmail = false;

})
    .AddEntityFrameworkStores<_DbContext>()
    .AddDefaultTokenProviders();    


var app = builder.Build();


if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();
app.UseAuthentication();
app.UseAuthorization();


app.MapControllerRoute(
    name:"default",
    pattern: "{controller=Account}/{action=login}/{id?}"
);


app.Run();

```

<br>
<br>

### 4. Create a new folder in your project and name it ViewModels, although you can name it anything you prefer. In this case, we’re following the proper naming convention used in the industry. Inside this folder, we will place the model class that will be used for Login, Register, Change Password, and so on.
![Step 1](ViewModels.png)

Here is the code for the LoginViewModel.cs.

```csharp
using System.ComponentModel.DataAnnotations;

namespace Asp.NetCore_Identity_Auth.ViewModels
{
    public class LoginViewModel
    {
        [Required(ErrorMessage ="Email is required.")]
        public string Email { get; set; }


        [Required(ErrorMessage = "Password is required.")]
        [DataType(DataType.Password)]
        public string Password { get; set; }


        [Display(Name = "Remember me?")]
        public bool RememberMe { get; set; } = false;   

    } 
}

```

<br>

Here is the code for the RegisterViewModel.cs.

```csharp
using System.ComponentModel.DataAnnotations;

namespace Asp.NetCore_Identity_Auth.ViewModels
{
    public class RegisterViewModel
    {
        [Required(ErrorMessage = "Name is required")]
        public string Name { get; set; }



        [Required(ErrorMessage = "Email is required")]
        [EmailAddress]
        public string Email { get; set; }



        [Required(ErrorMessage = "Password is required")]
        [StringLength(40, MinimumLength = 3, ErrorMessage = "The {0} must be at {2} and at max {1} characters" )]
        [DataType(DataType.Password)]//this code is for masking textbox for password
        [Compare("ConfirmPassword", ErrorMessage = "Password does not match.")]
        public string Password { get; set; }



        [Required(ErrorMessage = "Confirm Password is required")]
        [DataType(DataType.Password)]//this code is for masking textbox for password
        public string ConfirmPassword { get; set; }

    }
}


```











