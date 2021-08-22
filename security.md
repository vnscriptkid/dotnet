## Checklist
* Install `NWebsec.AspNetCore.Middleware`
* Config headers in `Startup.cs`
```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseXContentTypeOptions();
    app.UseReferrerPolicy(opt => opt.NoReferrer());
    app.UseXXssProtection(opt => opt.EnabledWithBlockMode());
    app.UseXfo(opt => opt.Deny());
    // UseCspReportOnly to see report
    app.UseCsp(opt => opt
        .BlockAllMixedContent()
        .StyleSources(s => s.Self().CustomSources("https://fonts.googleapis.com"))
        .FontSources(s => s.Self().CustomSources("https://fonts.gstatic.com", "data:"))
        .FormActions(s => s.Self())
        .FrameAncestors(s => s.Self())
        .ImageSources(s => s.Self().CustomSources("https://res.cloudinary.com"))
        .ScriptSources(s => s.Self().CustomSources("sha256-zUFjt0EEs9COUnBrzMHnTS0AKG6iNDJbI1udluNR+3s="))
    );
    // ...
    
    if (env.IsDevelopment())
    {
        // ...
    }
    else // prod 
    { 
        app.Use(async (context, next) =>
        {
            context.Response.Headers.Add("Strict-Transport-Security", "max-age=31536000");
            await next.Invoke();
        });
    }
}    
```

## Rate your site's security
* https://securityheaders.com/
