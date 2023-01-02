```c#
//Adding Connection String And Seeding Some Data

   protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            //Install Microsoft.EntityFrameworkCore.SqlServer If You Want Use SQL Server
            //optionsBuilder.UseSqlServer(@"Server=.;Database=dbName;User Id=user;Password=password;");

            optionsBuilder.UseSqlite("DataSource=app.db;Cache=Shared");
        }



Constants.cs
#region Roles
        public const string SUPERADMINROLE = "SuperAdmin";
        public const string ADMINROLE = "Admin";
        public const string NORMALUSERROLE = "NormalUser";
        #endregion

//Seeding SuperAdmin User And Roles

 private void SeedUsers(ModelBuilder builder)
        {
            ApplicationUser user = new ApplicationUser()
            {
                Id = "b74ddd14-6340-4840-95c2-db12554843e5",
                FirstName = "First Name",
                LastName = "Last Name",
                UserName = "SuperAdmin",
                Email = "SuperAdmin@gmail.com",
                LockoutEnabled = false,
                EmailConfirmed = true,
                NormalizedEmail = "SuperAdmin@gmail.com".ToUpper(),
                NormalizedUserName = "SuperAdmin".ToUpper(),
            };

            PasswordHasher<ApplicationUser> passwordHasher = new PasswordHasher<ApplicationUser>();
            user.PasswordHash = passwordHasher.HashPassword(user, "fnV@l^Z5EXH*5Mi$7kMf");
            builder.Entity<ApplicationUser>().HasData(user);


        }

        private void SeedRoles(ModelBuilder builder)
        {
            builder.Entity<ApplicationRole>().HasData(
                new ApplicationRole() { Id = "fab4fac1-c546-41de-aebc-a14da6895711", Name = Constants.SUPERADMINROLE, ConcurrencyStamp = "1", NormalizedName = Constants.SUPERADMINROLE.ToUpper() },
                new ApplicationRole() { Id = "bvb4fac1-c546-41de-aebc-a14da6895712", Name = Constants.ADMINROLE, ConcurrencyStamp = "2", NormalizedName = Constants.ADMINROLE.ToUpper() },
                new ApplicationRole() { Id = "c7b013f0-5201-4317-abd8-c211f91b7330", Name = Constants.NORMALUSERROLE, ConcurrencyStamp = "2", NormalizedName = Constants.NORMALUSERROLE.ToUpper() }
                //new IdentityRole() { Id = "b9b013f0-5201-4318-abd8-c271f91b7323", Name = "Engineer", ConcurrencyStamp = "2", NormalizedName = "Engineer" },
                );
        }

        private void SeedUserRoles(ModelBuilder builder)
        {
            builder.Entity<ApplicationUserRole>().HasData(
                new ApplicationUserRole() { RoleId = "fab4fac1-c546-41de-aebc-a14da6895711", UserId = "b74ddd14-6340-4840-95c2-db12554843e5" }
                );
        }

//Add Below Lines To OnModelCreating
    SeedUsers(builder);
            SeedRoles(builder);
            SeedUserRoles(builder);
```
