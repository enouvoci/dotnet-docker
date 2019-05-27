# escape=`

# FROM microsoft/nanoserver:sac2016
FROM mcr.microsoft.com/windows/servercore:1607

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Install .NET Core SDK
ENV DOTNET_SDK_VERSION 2.1.700

RUN Invoke-WebRequest -OutFile dotnet.zip https://dotnetcli.blob.core.windows.net/dotnet/Sdk/$Env:DOTNET_SDK_VERSION/dotnet-sdk-$Env:DOTNET_SDK_VERSION-win-x86.zip; `
    $dotnet_sha512 = 'b388f7f73606c3d498723442f8cc7435fee0af700d9336448848344be467ec2b1f2e7e52c409369bc85415af5215d7205125ae37e49743504ece12270707f9e0'; `
    if ((Get-FileHash dotnet.zip -Algorithm sha512).Hash -ne $dotnet_sha512) { `
        Write-Host 'CHECKSUM VERIFICATION FAILED!'; `
        exit 1; `
    }; `
    `
    Expand-Archive dotnet.zip -DestinationPath "${env:ProgramFiles(x86)}\dotnet"; `
    Remove-Item -Force dotnet.zip

RUN setx /M PATH $($Env:PATH + ';' + "${Env:ProgramFiles(x86)}" + '\dotnet')

# Configure Kestrel web server to bind to port 80 when present
ENV ASPNETCORE_URLS=http://+:80 `
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true `
    # Enable correct mode for dotnet watch (only mode supported in a container)
    DOTNET_USE_POLLING_FILE_WATCHER=true `
    # Skip extraction of XML docs - generally not useful within an image/container - helps perfomance
    NUGET_XMLDOC_MODE=skip

# RUN dir "${Env:ProgramFiles(x86)}\dotnet"

# RUN "${Env:ProgramFiles(x86)}\dotnet\dotnet.exe --help"
# RUN Write-Host "$Env:PATH"
# Trigger first run experience by running arbitrary cmd to populate local package cache
# RUN & "${Env:ProgramFiles(x86)}\dotnet\dotnet.exe" help
RUN dotnet help
