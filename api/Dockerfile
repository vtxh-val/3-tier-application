# 1) Base image for ASP.NET runtime
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
WORKDIR /app

# 2) We'll build in a separate stage
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src

# Copy the .NET solution and projects
COPY Basic3TierAPI.sln .
COPY Basic3Tier.Core/Basic3Tier.Core.csproj Basic3Tier.Core/
COPY Basic3Tier.Infrastructure/Basic3Tier.Infrastructure.csproj Basic3Tier.Infrastructure/
COPY Basic3TierAPI/Basic3TierAPI.csproj Basic3TierAPI/
# (If you have tests, you can copy those too, but for building the API alone, this suffices)

RUN dotnet restore Basic3TierAPI/Basic3TierAPI.csproj

COPY . .
WORKDIR /src/Basic3TierAPI
RUN dotnet build Basic3TierAPI.csproj -c Release -o /app/build

FROM build AS publish
RUN dotnet publish Basic3TierAPI.csproj -c Release -o /app/publish /p:UseAppHost=false

# 3) Final stage - build minimal runtime image
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
# Expose port 80 (and optionally 443, if you wish)
EXPOSE 80
ENTRYPOINT ["dotnet", "Basic3TierAPI.dll"]
