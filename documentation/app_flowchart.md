flowchart TD
    Start[Start]  
    Start --> SignInPage[Open Sign In Page]  
    SignInPage --> Credentials[Enter Credentials]  
    Credentials --> AuthService[Authenticate via Better Auth]  
    AuthService --> AuthCheck{Credentials Valid}  
    AuthCheck -->|No| SignInError[Show Error Message]  
    SignInError --> SignInPage  
    AuthCheck -->|Yes| CreateSession[Create User Session]  
    CreateSession --> DashboardRedirect[Redirect to Dashboard]  
    DashboardRedirect --> RoleCheck{Select Dashboard by Role}  
    RoleCheck -->|Admin| AdminDashboard[Admin Dashboard]  
    RoleCheck -->|User| UserDashboard[User Dashboard]  
    AdminDashboard --> ManageTemplates[Manage Wedding Templates]  
    ManageTemplates --> TemplateTable[View Template Table]  
    TemplateTable --> NewTemplate[Create Template]  
    TemplateTable --> EditTemplate[Edit Template]  
    TemplateTable --> DeleteTemplate[Delete Template]  
    UserDashboard --> EditOwnWedding[Edit Own Wedding Template]  
    NewTemplate --> API[Call Nextjs API Route]  
    EditTemplate --> API  
    DeleteTemplate --> API  
    EditOwnWedding --> API  
    API --> ORM[Drizzle ORM with PostgreSQL]  
    ORM --> Database[PostgreSQL Database]  
    AdminDashboard --> Logout[Logout]  
    UserDashboard --> Logout  
    Logout --> Start