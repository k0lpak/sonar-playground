FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["SonarPlaygroundGBActions/SonarPlaygroundGBActions.csproj", "SonarPlaygroundGBActions/"]
RUN dotnet restore "SonarPlaygroundGBActions/SonarPlaygroundGBActions.csproj"
COPY . .
WORKDIR "/src/SonarPlaygroundGBActions"
RUN dotnet build "SonarPlaygroundGBActions.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "SonarPlaygroundGBActions.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "SonarPlaygroundGBActions.dll"]
