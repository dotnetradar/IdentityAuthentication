```c#
//Adding AuthenticationConfig

Auth/AuthenticationConfig.cs
//Make AuthenticationConfig Class Static

//Add Below Codes In It

 public static void AddAuthenticationConfig(this IServiceCollection services)
        {
           
            services.AddAuthentication(opt =>
            {
                opt.DefaultAuthenticateScheme = "JWT_OR_COOKIE";
                opt.DefaultSignInScheme = "JWT_OR_COOKIE";
                opt.DefaultChallengeScheme = "JWT_OR_COOKIE";
            })
                .AddCookie("Cookies", options =>
                {
                    options.LoginPath = "/login";
                    options.LogoutPath = "/logout";
                    options.ExpireTimeSpan = TimeSpan.FromDays(1);
                })
                .AddJwtBearer("Bearer", options =>
                {
                    options.TokenValidationParameters = new TokenValidationParameters
                    {

                        ValidateIssuer = false,
                        ValidateAudience = false,
                        ValidateLifetime = true,
                        ValidateIssuerSigningKey = true,
                        ValidIssuer = "https://localhost:7011",
                        ValidAudience = "https://localhost:4200",
                        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("superSecretKey@653"))
                    };
                })
                  .AddPolicyScheme("JWT_OR_COOKIE", "JWT_OR_COOKIE", options =>
                  {
                      // runs on each request
                      options.ForwardDefaultSelector = context =>
                      {
                          // filter by auth type
                          string authorization = context.Request.Headers[HeaderNames.Authorization];
                          if (!string.IsNullOrEmpty(authorization) && authorization.StartsWith("Bearer "))
                              return "Bearer";

                          // otherwise always check for cookie auth
                          return "Cookies";
                      };
                  });
        }

        public static void AddIdentityConfig(this IServiceCollection services)
        {
            services.AddIdentity<ApplicationUser, ApplicationRole>(options =>
            {
                options.SignIn.RequireConfirmedAccount = false;
                options.User.RequireUniqueEmail = false;
                options.Password.RequiredLength = 6;
                options.Password.RequireDigit = true;
                options.Password.RequireUppercase = true;
            })
                .AddEntityFrameworkStores<EFContext>().AddDefaultTokenProviders();
            services.Configure<DataProtectionTokenProviderOptions>(opt =>
             opt.TokenLifespan = TimeSpan.FromHours(2));
        }


//Add Below Lines Before "builder.Services.AddDbContext<EFContext>();" In //Program Class
builder.Services.AddIdentityConfig();
builder.Services.AddAuthenticationConfig();


//Add Line Below After "app.UseHttpsRedirection();" In Program Class
app.UseAuthentication();

```
