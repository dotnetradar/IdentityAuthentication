```c#
//Creating Database Models

Models/ApplicationUser.cs : IdentityUser

Models/ApplicationRole.cs : IdentityRole<string>
 public ApplicationRole() { }

        public ApplicationRole(string roleName)
            : base(roleName)
        {
        }

Models/ApplicationUserRole.cs : IdentityUserRole<string>
   public virtual ApplicationUser User { get; set; }
   public virtual ApplicationRole Role { get; set; }

//Add Below Line To ApplicationUser
        public virtual ICollection<ApplicationUserRole> UserRoles { get; } = new List<ApplicationUserRole>();

//Add Below Line To ApplicationRole
                public virtual ICollection<ApplicationUserRole> UserRoles { get; } = new List<ApplicationUserRole>();


```
