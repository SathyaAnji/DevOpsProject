FROM microsoft/aspnetcore-build:1.1 AS sathya
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM microsoft/aspnetcore:1.1
WORKDIR /app
COPY --from=sathya /app/out .
ENTRYPOINT ["dotnet", "aspnet-core-dotnet-core.dll"]
