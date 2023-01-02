```c#
//Adding Login Page And Logout

Add Empty Razor Page
Pages/Login.cshtml

//Add Below Class To Login
  public class LoginViewModel
    {
        [Required(ErrorMessage = "Email is invalid")]
        [EmailAddress]
        public string Email { get; set; }
        [Required(ErrorMessage = "Password is required")]
        public string Password { get; set; }
        public bool RememberMe { get; set; }

        public string? ReturnUrl { get; set; }
    }

//Add Below Code To Login
[BindProperty]
        public LoginViewModel UserForLogin { get; set; } = new LoginViewModel();
private readonly SignInManager<ApplicationUser> _signInManager;
        private readonly UserManager<ApplicationUser> _userManager;
        public LoginModel(SignInManager<ApplicationUser> signInManager, UserManager<ApplicationUser> userManager)
        {
            _signInManager = signInManager;
            _userManager = userManager;
        }

//Change Get Method
 public IActionResult OnGet(string returnUrl = null)
        {
            if (User.Identity.IsAuthenticated) return Redirect("/");
            returnUrl = string.IsNullOrEmpty(returnUrl) ? "/" : returnUrl;
            UserForLogin.ReturnUrl = returnUrl;
            return Page();
        }

//Add Below Codes 
	public async Task<IActionResult> OnPostAsync()
		{
			if (ModelState.IsValid)
			{

				var dbUser = await _userManager.Users.Where(u => u.Email.ToLower() == UserForLogin.Email.ToLower()).FirstOrDefaultAsync();

				if (dbUser == null || !await _userManager.CheckPasswordAsync(dbUser, UserForLogin.Password))
				{
					ModelState.AddModelError(string.Empty, "Invalid Email or Password");
					return Page();
				}
				else if (dbUser == null || !await _userManager.IsEmailConfirmedAsync(dbUser))
				{
					ModelState.AddModelError(string.Empty, "Please Confirm Your Email");
					return Page();
				}
				var dbUserRoles = await _userManager.GetRolesAsync(dbUser);
				await LoginInCookie(dbUser, dbUserRoles, UserForLogin.RememberMe);
				 return LocalRedirect(UserForLogin.ReturnUrl);

			}
			return Page();

		}
		private async Task LoginInCookie(ApplicationUser user, IList<string?> userRoles, bool rememberMe)
		{
			var identity = new ClaimsIdentity(CookieAuthenticationDefaults.AuthenticationScheme, ClaimTypes.Name, ClaimTypes.Role);
			identity.AddClaim(new Claim(ClaimTypes.Name, user.UserName));
			identity.AddClaim(new Claim(ClaimTypes.Email, user.Email));
			identity.AddClaim(new Claim(ClaimTypes.Role, userRoles[0]));

			var principal = new ClaimsPrincipal(identity);
			await HttpContext.SignInAsync(
				CookieAuthenticationDefaults.AuthenticationScheme,
				principal,
				new AuthenticationProperties
				{
					IsPersistent = rememberMe,
					AllowRefresh = true,
					ExpiresUtc = DateTime.UtcNow.AddDays(1)
				});
		}


//https://github.com/nauvalazhar/bootstrap-4-login-page

  <div asp-validation-summary="All" class="text-danger"></div>
                                                         <input  type="hidden" asp-for="UserForLogin.ReturnUrl"/>

Pages/Logout

 public async Task<IActionResult> OnGet()
        {
            await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
            return Redirect("/login");
        }
```
