# Build, Test and Deploy a docker ASPNET MVC Core application

# Create sample application

```
$ mkdir sample_aspnetmvc
$ cd sample_aspnetmvc
$ mkdir sample_aspnetmvc.app
$ cd sample_aspnetmvc.app
$ dotnet new mvc
$ dotnet build
$ dotnet run
```

# Test the application

```
curl -I http://localhost:5000
```
## output
```
HTTP/1.1 200 OK
Date: Fri, 01 Dec 2017 03:28:53 GMT
Content-Type: text/html; charset=utf-8
Server: Kestrel
```

# Create a test project
From the project directory "**sample_aspnetmvc**"

```
$ mkdir sample_aspnetmvc.test
$ cd sample_aspnetmvc.test
$ dotnet new xunit
$ dotnet add reference ../sample_aspnetmvc.app/sample_aspnetmvc.app.csproj
$ dotnet test
```

# Create a Dockerfile

```
FROM microsoft/dotnet:2.0-sdk AS build-env

WORKDIR /solution

COPY sample_aspnetmvc.app/*.csproj ./sample_aspnetmvc.app/
RUN ls sample_aspnetmvc.app/
RUN dotnet restore ./sample_aspnetmvc.app/sample_aspnetmvc.app.csproj

COPY sample_aspnetmvc.test/*.csproj ./sample_aspnetmvc.test/
RUN dotnet restore ./sample_aspnetmvc.test/sample_aspnetmvc.test.csproj

COPY . .

RUN dotnet test ./sample_aspnetmvc.test/sample_aspnetmvc.test.csproj

RUN dotnet publish ./sample_aspnetmvc.app/sample_aspnetmvc.app.csproj --output /out/ --configuration Release

FROM microsoft/aspnetcore

WORKDIR /app
COPY --from=build-env /out .
ENTRYPOINT ["dotnet", "sample_aspnetmvc.app.dll"]
```

# Build the docker image

```
docker build . -t aspnetmvcapp
```

# Run the docker image
```
docker run -p 5000:80 aspnetmvcapp
```

test it as documented above
```
curl -I http://localhost:5000
```



# Reference
