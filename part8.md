```c#
//Add Register Page

Pages/Register.cshtml

//Add Below Class To Register

 public class RegisterViewModel
    {
      
        [Required(ErrorMessage = "Email is required.")]
        [EmailAddress]
        public string Email { get; set; }

        [Required(ErrorMessage = "Password is required.")]
        [DataType(DataType.Password)]
        public string Password { get; set; }

   		//[DataType(DataType.Password)]
		//[Compare("Password", ErrorMessage = "The password and confirmation password do not match.")]
		//public string ConfirmPassword { get; set; }

    }

//Add Below Codes To Register

 [BindProperty]
        public RegisterViewModel userForRegister { get; set; } = new RegisterViewModel();

        private readonly SignInManager<ApplicationUser> _signInManager;
        private readonly UserManager<ApplicationUser> _userManager;

        public RegisterModel(SignInManager<ApplicationUser> signInManager, UserManager<ApplicationUser> userManager)
        {
            _signInManager = signInManager;
            _userManager = userManager;
        }

//Add OnPost Method
        public async Task<IActionResult> OnPostAsync()
        {

            if (ModelState.IsValid)
            {
               

                  var user = new ApplicationUser
                {
                    UserName = UserForRegister.Email,
                    Email = UserForRegister.Email,
                    EmailConfirmed = true,
                };


                var result = await _userManager.CreateAsync(user, UserForRegister.Password);
                if (!result.Succeeded)
                {
                    foreach (var error in result.Errors)
                    {
                        ModelState.TryAddModelError(error.Code, error.Description);
                    }
                    return Page();
                }
                await _userManager.AddToRoleAsync(user, Constants.NORMALUSERROLE);
                var token = HttpUtility.UrlEncode(await _userManager.GenerateEmailConfirmationTokenAsync(user));

                //Todo Sending Email

                //var confirmationLink = Url.Action("ConfirmEmail", "Email", new { token, email = user.Email }, Request.Scheme);

                return RedirectToPage("/Login");
            }
            return Page();

        }

//https://github.com/nauvalazhar/bootstrap-4-login-page

```
