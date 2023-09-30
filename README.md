Steps to host Asp.net Core application on EC2 Linux Instance
1.Create a EC2 instance of Amazon Linux flavor. (Make sure EC2 instance is Publicly available)
2.Connect to EC2 instance.
3.Install Centos Linux using below command
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
4.Install dotnet SDK using below command
sudo yum install dotnet-sdk-7.0
5. Install the Apache web server on CentOS with a single yum command
sudo yum -y install httpd mod_ssl
6. change directory to the below path
cd /etc/httpd/conf.d/
7.Create a .conf file using below command
Sudo vi <filename.conf>
8.Insert the below code to above create file.
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}s
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
</VirtualHost>
9.To test the configuration use below command
sudo apachectl configtest
expected output:Syntax [OK]
if error please check any space or extra line added in the above .conf file to retest configuration.


10.Restart Apache.
sudo systemctl restart httpd
sudo systemctl enable httpd
11.Install Git
sudo yum install git
12.create a folder of your choice and do git clone.
git clone https://github.com/thejasv/Login-App.git
13.Create MS SQL Server RDS (Make sure you enable public access and username and password noted).

Establish a connection between the newly created EC2 Instance and RDS

1.Go to the newly created RDS and Look for connect to compute resource.
2.Click on Actions in connect to compute resource and select the setup EC2 connection.
3.Select the newly created EC2 instance and click on continue and then create.

Creation of Connection string to establish a connection between application and RDS.
1.Go to RDS and copy the end point available in Connectivity & security section.
2.Paste the endpoint, username and password to the below connection string.
"conn": "server=tcp:<endpoint>,1433;database=ProductDb;User ID=<username>;Password=<password>;Connection Timeout=30;TrustServerCertificate=True;Encrypt=False"
3.Go to the newly created folder on ec2 instance and do cd Login-App
4. vi appsettings.json
5.remove the existing conn in the file and paste the new connection string.
6.Connect to Db Server using SSMS 
7.If SSMS not available please download it using the below link
SSMS Download
8.After connecting to Database by providing End point, Username and Password on SSMS
Execute the below commands 
Create database ProductDb
Use ProductDb
CREATE TABLE [dbo].[User](
	[Email] [nvarchar](450) NOT NULL,
	[FirstName] [nvarchar](max) NOT NULL,
	[LastName] [nvarchar](max) NOT NULL,
	[Age] [int] NOT NULL,
	[Password] [nvarchar](max) NOT NULL,
 CONSTRAINT [PK_User] PRIMARY KEY CLUSTERED 
(
	[Email] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
GO

9.Once all Db command execution is done. Go back to EC2 Instance Login-App folder and execute
Dotnet Login-App.dll
Copy the public IP of the server and paste it on new chrome tab.
Application will be up and running.
