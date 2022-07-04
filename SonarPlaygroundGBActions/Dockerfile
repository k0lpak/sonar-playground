FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

ARG SONAR_TOKEN

RUN apt-get update && apt-get dist-upgrade -y && apt-get install -y openjdk-11-jre

## Install sonarscanner
RUN dotnet tool install --global dotnet-sonarscanner --version 5.3.1

## Install report generator
RUN dotnet tool install --global dotnet-reportgenerator-globaltool --version 4.8.12

## Set the dotnet tools folder in the PATH env variable
ENV PATH="${PATH}:/root/.dotnet/tools"

## Start scanner
RUN dotnet sonarscanner begin \
	/o:"k0lpak" \
	/k:"k0lpak_sonar-playground" \
	/d:sonar.host.url="https://sonarcloud.io" \
	/d:sonar.login="$SONAR_TOKEN" \
	/d:sonar.coverageReportPaths="coverage/SonarQube.xml"

COPY ["SonarPlaygroundGBActions/SonarPlaygroundGBActions.csproj", "SonarPlaygroundGBActions/"]
RUN dotnet restore "SonarPlaygroundGBActions/SonarPlaygroundGBActions.csproj"
COPY . .
WORKDIR "/src/SonarPlaygroundGBActions"
RUN dotnet build "SonarPlaygroundGBActions.csproj" -c Release -o /app/build

RUN  dotnet sonarscanner end /d:sonar.login="$SONAR_TOKEN"

FROM build AS publish
RUN dotnet publish "SonarPlaygroundGBActions.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "SonarPlaygroundGBActions.dll"]
