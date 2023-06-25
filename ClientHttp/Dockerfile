# Base ASP.NET Core runtime
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

# SDK for building the project
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /src

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y git

RUN git clone -b main https://github.com/yordanlp/DistributedFileSystem-Client.git

WORKDIR "/src/DistributedFileSystem-Client/HttpClient"
RUN dotnet restore

RUN dotnet build "./HttpClient/HttpClient.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "./HttpClient/HttpClient.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "HttpClient.dll"]