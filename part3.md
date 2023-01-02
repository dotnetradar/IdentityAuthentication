```c#
//Creating Database Context And Configuration Using Fluent API

Models/EFContext.cs: IdentityDbContext<ApplicationUser, ApplicationRole, string, IdentityUserClaim<string>
    , ApplicationUserRole, IdentityUserLogin<string>, IdentityRoleClaim<string>, IdentityUserToken<string>>

public EFContext(DbContextOptions<EFContext> options) : base(options)
        {
        }

//Add override OnModelCreating

builder.Entity<ApplicationUser>()
        .HasMany(e => e.UserRoles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

            builder.Entity<ApplicationUserRole>()
                .HasOne(e => e.User)
                .WithMany(e => e.UserRoles)
                .HasForeignKey(e => e.UserId);

            builder.Entity<ApplicationUserRole>()
                .HasOne(e => e.Role)
                .WithMany(e => e.UserRoles)
                .HasForeignKey(e => e.RoleId);
```
