# Base image for running the application
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8081  # Only expose the port you will use

# Image for building the application
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy project files and restore dependencies
COPY ["dotnet-jenkins/dotnet-jenkins.csproj", "dotnet-jenkins/"]
RUN dotnet restore "dotnet-jenkins/dotnet-jenkins.csproj"

# Copy the rest of the files and build the project
COPY . .
WORKDIR "/src/dotnet-jenkins"
RUN dotnet build "dotnet-jenkins.csproj" -c $BUILD_CONFIGURATION -o /app/build

# Publish the project
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "dotnet-jenkins.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# Final stage to build the runtime image
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "dotnet-jenkins.dll"]