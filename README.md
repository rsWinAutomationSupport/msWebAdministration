msWebAdministration
===================

USAGE NOTES - Dependencies must be used throughout the config to prevent failure. Your config should run in the following order.

1. Install IIS and .NET
2. Create Website folder (even if deploying code from Git or TeamCity)
3. Create application pool (with dependency for IIS)
4. Create website (with dependency for app pool and folder)
5. Create sub-applications and virtual directories (also creating their folder and making them dependent on the website)


Example:
```PoSh
WindowsFeature IIS
{
  Ensure = "Present"
  Name = "Web-Server"
}

WindowsFeature AspNet45
{
  Ensure = "Present"
  Name = "Web-Asp-Net45"
}


##################################################
# Website - www.rackspacedevops.com
##################################################
File rackspacedevops_com_folder
{
  DestinationPath = "C:\inetpub\wwwroot\www.rackspacedevops.com"
  Type = "Directory"
  Ensure = "Present"
}

msWebAppPool rackspacedevops_com_pool
{
  Name = "www.rackspacedevops.com"
  Ensure = "Present"
  State = "Started"
  DependsOn = @("[WindowsFeature]IIS")
}

msWebsite rackspace_devops_com_site
{
  Name = "www.rackspacedevops.com"
  ApplicationPool = "www.rackspacedevops.com"
  Ensure = "Present"
  State = "Started"
  PhysicalPath = "C:\inetpub\wwwroot\www.rackspacedevops.com"
  BindingInfo = @(
    MSFT_msWebBindingInformation
    {
      IPAddress = "*"
      Port = 80
      Protocol = "HTTP"
      HostName = "www.rackspacedevops.com"
    }
    MSFT_msWebBindingInformation
    {
      IPAddress = "*"
      Port = 443
      Protocol = "HTTPS"
      HostName = "www.rackspacedevops.com"
      CertificateThumbprint = "B00000000954CDC740C933406571469EEE53C71"
      CertificateStoreName = "WebHosting"
    }
    )
  DependsOn = @("[File]rackspacedevops_com_folder","[msWebAppPool]rackspacedevops_com_pool")
}

File rackspacedevops_com_blog_folder
{
  DestinationPath = "C:\inetpub\wwwroot\www.rackspacedevops.com"
  Type = "Directory"
  Ensure = "Present"
}

msWebApplication rackspacedevops_com_blog
{
  Ensure = "Present"
  Website = "www.rackspacedevops.com"
  Name = "blog"
  WebAppPool = "www.rackspacedevops.com"
  PhysicalPath = "C:\blog"
  DependsOn = @("[File]rackspacedevops_com_blog_folder","[msWebsite]rackspace_devops_com_site")
}
##################################################
# End website configuration
##################################################


```
