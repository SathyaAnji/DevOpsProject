FROM microsoft/aspnetcore-build:1.1 AS base
WORKDIR /app


FROM microsoft/dotnet:2.2-sdk AS pre-build
RUN dotnet tool install --global dotnet-sonarscanner
RUN dotnet tool install --global coverlet.console
RUN apt-get update && apt-get install -y openjdk-8-jre
ENV PATH="$PATH:/root/.dotnet/tools"

FROM pre-build AS build
RUN dotnet sonarscanner begin /k:"BarcodesReader" /d:sonar.host.url="#{sonaruri}#" /d:sonar.login="#{sonarlogin}#"

# Copy csproj and restore as distinct layers
COPY . .
COPY ["aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj", "aspnet-core-dotnet-core"/]
RUN dotnet restore

# Copy everything else and build
RUN dotnet build "aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj"
RUN dotnet sonarscanner end /d:sonar.login="#{sonarlogin}#"
RUN dotnet publish -c Release -o out

# Build runtime image
FROM microsoft/aspnetcore:1.1
WORKDIR /app
COPY --from=build /app/out .
ENTRYPOINT ["dotnet", "aspnet-core-dotnet-core.dll"]
