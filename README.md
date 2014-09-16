msWebAdministration
===================
<pre>
USAGE NOTES - Dependencies must be used throughout the config to prevent failure. Your config should run in the following order.

1. Install IIS and .NET
2. Create Website folder (even if deploying code from Git or TeamCity)
3. Create application pool (with dependency for IIS)
4. Create website (with dependency for app pool and folder)


Example:

WindowsFeature IIS
{
Ensure = "Present"
Name = "Web-Server"
}

File rackspacedevops_com_folder
{
  DestinationPath = "C:\inetpub\wwwroot\www.rackspacedevops.com"
  Type = "Directory"
  Ensure = "Present"
}

xWebAppPool rackspacedevops_com_pool
{
  Name = "www.rackspacedevops.com"
  Ensure = "Present"
  State = "Started"
  DependsOn = @("[WindowsFeature]IIS")
}

xWebSite rackspace_devops_com_site
{
  Name = "www.rackspacedevops.com"
  ApplicationPool = "www.rackspacedevops.com"
  Ensure = "Present"
  State = "Started"
  PhysicalPath = "C:\inetpub\wwwroot\www.rackspacedevops.com"
  BindingInfo = @(
    MSFT_xWebBindingInformation
    {
      IPAddress = "*"
      Port = 80
      Protocol = "HTTP"
      HostName = "www.rackspacedevops.com"
    }
    MSFT_xWebBindingInformation
    {
      IPAddress = "*"
      Port = 443
      Protocol = "HTTPS"
      HostName = "www.rackspacedevops.com"
      CertificateThumbprint = "B00000000954CDC740C933406571469EEE53C71"
      CertificateStoreName = "WebHosting"
    }
    )
  DependsOn = @("[File]rackspacedevops_com_folder","[xWebAppPool]rackspacedevops_com_pool")
}
</pre>
