# How to run SonarQube Locally
Its possible to run SonarQube locally on a git repository using Docker. 

For docker to work, Virtualization on your Win10 machine must be turned on. To enable virtualization: 

`dism.exe /Online /Enable-Feauture:Microsoft-Hyper-V /All`
`bcdedit /set hypervisorlaunchtype auto`

Then reboot your machine.

See [this page](https://learn.microsoft.com/en-us/troubleshoot/windows-client/application-management/virtualization-apps-not-work-with-hyper-v) for more information. 

## Install Docker
Go to docker hub then download and install Docker Desktop for Windows. 

You can test the install of docker by running the following commands in a cmd.exe

`docker version`

`docker run hello-world`

Through settings you can configure Docker to start on demand (by default it starts on logon). 

## Run SonarQube in Docker
With Docker installed, you can run SonarQube locally. In a cmd.exe enter the following command: 

`docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube`

Wait maybe 20s for the container to start. Once the SonarQube container is running, logon to SonarQube by browsing to localhost:9000. When prompted for a username/password enter  admin/admin. From the main page you need to create a project (e.g. your git repository) and a token. The token may look like this: 

253fbd94adea49ecd3db401fa347890a448ec9c8

## Command Line Tool (for msbuild)
Follow the instructions on this page to download and install the command line tool for msbuild.

- There is a link for the .NET 4.6+ scanner tool. Selecting the link starts the download.
- Unzip it to a suitable location (e.g. C:\Work_Tools\sonar-scanner-msbuild-5.0.4.24009-net46).
- Add this path to you %PATH%.
- Follow the instructions to run the tool

Three separate commands are required, roughly speaking: 

- begin the analysis
- build the repository using msbuild
- end the analysis

Basically in a cmd.exe navigate to your git repository and run 3 commands. My 3 commands looked like this: 

`SonarScanner.MSBuild.exe begin /k:"hockeyhero" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="253fbd94adea49ecd3db401fa347890a448ec9c8"`
`MsBuild.exe /t:Rebuild /p:Configuration=Release;Platform="Any CPU"`
`SonarScanner.MSBuild.exe end /d:sonar.login="253fbd94adea49ecd3db401fa347890a448ec9c8"`

You can see the commands are 'begin', 'run' and 'end'. Also note - there are instructions on the webpage to uncomment a block of xml in SonarQube.Analysis.xml. I found i had to leave it commented out: 

<!--
  <Property Name="sonar.host.url">http://localhost:9000</Property>
 
  <Property Name="sonar.login">admin</Property>
  <Property Name="sonar.password">phoenix01</Property>
-->

On completion browse back to your project (e.g. http://localhost:9000/dashboard?id=log-extractor) and view the results. 

Fix all warnings and errors. Rinse and repeat until all warnings and errors are gone. 
