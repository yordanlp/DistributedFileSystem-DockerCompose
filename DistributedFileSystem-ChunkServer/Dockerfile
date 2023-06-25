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

RUN git clone -b main https://github.com/yordanlp/DistributedFileSystem-ChunkServer.git

WORKDIR "/src/DistributedFileSystem-ChunkServer/ChunkServer"
RUN dotnet restore

RUN dotnet build "./ChunkServer/ChunkServer.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "./ChunkServer/ChunkServer.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
RUN mkdir Chunks
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ChunkServer.dll"]