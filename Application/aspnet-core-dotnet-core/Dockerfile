FROM microsoft/aspnetcore-build:1.1 AS base
WORKDIR /app


FROM microsoft/dotnet:2.2-sdk AS pre-build
RUN dotnet tool install --global dotnet-sonarscanner
RUN dotnet tool install --global coverlet.console
RUN apt-get update && apt-get install -y openjdk-8-jre
ENV PATH="$PATH:/root/.dotnet/tools"

FROM pre-build AS build
RUN dotnet sonarscanner begin /k:"SathyaAnji" /d:sonar.host.url="#{sonaruri}#" /d:sonar.login="91d07de11d59a84cd8439d5c45758bbdfd27b873"

# Copy csproj and restore as distinct layers
COPY . /app
#RUN ls -al /app
#COPY ["Application/aspnet-core-dotnet-core/aspnet-core-dotnet-core.csproj", "Application/aspnet-core-dotnet-core/"]
RUN dotnet restore "/app/aspnet-core-dotnet-core.csproj"

# Copy everything else and build
RUN dotnet build "app/aspnet-core-dotnet-core.csproj"
RUN dotnet sonarscanner end /d:sonar.login="91d07de11d59a84cd8439d5c45758bbdfd27b873"
RUN dotnet publish -c Release -o out

# Build runtime image
FROM microsoft/aspnetcore:1.1
WORKDIR /app
COPY --from=build /app/out .
ENTRYPOINT ["dotnet", "aspnet-core-dotnet-core.dll"]
