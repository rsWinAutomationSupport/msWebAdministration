msWebAdministration
===================
<pre>
USAGE NOTES - Dependencies must be used throughout the config to prevent failure. Your config should run in the following order.

1. Install IIS and .NET
2. Create Website folder (even if deploying code from Git or TeamCity)
3. Create application pool (with dependency for IIS)
4. Create website (with dependency for app pool and folder)


Example:

<b>WindowsFeature IIS</b>
{
Ensure = "Present"
Name = "Web-Server"
}

<b>File rackspacedevops_com_folder</b>
{
  DestinationPath = "C:\inetpub\wwwroot\www.rackspacedevops.com"
  Type = "Directory"
  Ensure = "Present"
}

<b>xWebAppPool rackspacedevops_com_pool</b>
{
  Name = "www.rackspacedevops.com"
  Ensure = "Present"
  State = "Started"
  DependsOn = @("[WindowsFeature]IIS")
}

<b>xWebSite rackspace_devops_com_site</b>
{
  Name = "www.rackspacedevops.com"
  ApplicationPool = "www.rackspacedevops.com"
  Ensure = "Present"
  State = "Started"
  PhysicalPath = "C:\inetpub\wwwroot\www.rackspacedevops.com"
  BindingInfo = @(
    <b>MSFT_xWebBindingInformation</b>
    {
      IPAddress = "*"
      Port = 80
      Protocol = "HTTP"
      HostName = "www.rackspacedevops.com"
    }
    <b>MSFT_xWebBindingInformation</b>
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
