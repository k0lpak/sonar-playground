FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

ARG SONAR_TOKEN
ARG BRANCH_NAME

RUN apt-get update && apt-get dist-upgrade -y && apt-get install -y openjdk-11-jre

## Install sonarscanner
RUN dotnet tool install --global dotnet-sonarscanner --version 5.7.1

## Install report generator
RUN dotnet tool install --global dotnet-reportgenerator-globaltool --version 5.1.9

## Set the dotnet tools folder in the PATH env variable
ENV PATH="${PATH}:/root/.dotnet/tools"

COPY . .

## Start scanner
RUN dotnet sonarscanner begin \
	/o:"k0lpak" \
	/k:"k0lpak_sonar-playground" \
	/d:sonar.host.url="https://sonarcloud.io" \
    /d:sonar.pullrequest.provider="github" \
    /d:sonar.branch.name="$BRANCH_NAME" \
    /d:sonar.login="$SONAR_TOKEN" && \
    #/d:sonar.coverageReportPaths="coverage/SonarQube.xml"
	dotnet build "SonarPlaygroundGBActions.sln" && \
    dotnet sonarscanner end /d:sonar.login="$SONAR_TOKEN"

FROM build AS publish
RUN dotnet publish "SonarPlaygroundGBActions.sln" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "SonarPlaygroundGBActions.dll"]
