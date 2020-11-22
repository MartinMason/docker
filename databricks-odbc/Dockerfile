# escape=`

ARG DOTNET_VERSION=5.0.0
ARG REPO=mcr.microsoft.com/dotnet/runtime
ARG ASPNET_VERSION=5.0.0

# Installer image
FROM mcr.microsoft.com/windows/servercore:1909 AS installer
ARG DOTNET_VERSION
ARG ASPNET_VERSION

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Retrieve .NET Runtime
RUN Invoke-WebRequest -OutFile dotnet.zip https://dotnetcli.azureedge.net/dotnet/Runtime/$Env:DOTNET_VERSION/dotnet-runtime-$Env:DOTNET_VERSION-win-x64.zip; `
    $dotnet_sha512 = '968f2aff18aabc8f9ef0e1a6a3c0c3704d7a010ed453f0d56ded804513ccff913032461f1dbe0ce89522e85fbe5593d444f4310e8fdf7de7c7b72d01912086f1'; `
    if ((Get-FileHash dotnet.zip -Algorithm sha512).Hash -ne $dotnet_sha512) { `
        Write-Host 'CHECKSUM VERIFICATION FAILED!'; `
        exit 1; `
    }; `
    `
    mkdir dotnet; `
    tar -C dotnet -oxzf dotnet.zip; `
    Remove-Item -Force dotnet.zip

# Install ASP.NET Core Runtime
RUN Invoke-WebRequest -OutFile aspnetcore.zip https://dotnetcli.azureedge.net/dotnet/aspnetcore/Runtime/$Env:ASPNET_VERSION/aspnetcore-runtime-$Env:ASPNET_VERSION-win-x64.zip; `
    $aspnetcore_sha512 = 'ead6b1e12361261124bb89de1e77dba9469ce00483d963b237ce1e2a17500e420c2bd078c2732e7ce269e23ee3922b880f88ade9c1426c7170045fec1818ece4'; `
    if ((Get-FileHash aspnetcore.zip -Algorithm sha512).Hash -ne $aspnetcore_sha512) { `
        Write-Host 'CHECKSUM VERIFICATION FAILED!'; `
        exit 1; `
    }; `
    `
    mkdir dotnet/shared/Microsoft.AspNetCore.App; `
    tar -C dotnet -oxzf aspnetcore.zip ./shared/Microsoft.AspNetCore.App; `
    Remove-Item -Force aspnetcore.zip

# Runtime image
FROM mcr.microsoft.com/windows/servercore:1909
ARG DOTNET_VERSION
ARG ASPNET_VERSION

COPY . .
WORKDIR /drivers 
RUN msiexec /i "Simba Spark 2.6 64-bit.msi" /qn /norestart IACCEPTMSODBCSQLLICENSETERMS=YES 

ENV `
    # Configure web servers to bind to port 80 when present
    ASPNETCORE_URLS=http://+:80 `
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true `
    DOTNET_VERSION=$DOTNET_VERSION

# In order to set system PATH, ContainerAdministrator must be used
USER ContainerAdministrator
RUN setx /M PATH "%PATH%;C:\Program Files\dotnet"
USER ContainerUser

COPY --from=installer ["/dotnet", "/Program Files/dotnet"]
