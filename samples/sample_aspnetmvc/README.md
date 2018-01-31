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

# Push the image to ICP image repo

## login to docker
```
docker login prd.icp:8500
```
### id and password for docker repo
```
user:admin
password:admin
```

## tag the image
```
docker tag aspnetmvcapp prd.icp:8500/dev/aspnetmvcapp:1.0
```

## push the image
```
docker push prd.icp:8500/dev/aspnetmvcapp:1.0
```

# Deploy the image
Refer to [deploy custom image](https://github.ibm.com/icp-ap/Learning/blob/master/demo-with-ipad.md#demo-2-custom-docker-image)

General Tab

name | value
-----| -----
Name | js-dotnet-kestrelapp

Container settings Tab

name | value
-----| -----
Name | js-dotnet-kestrelapp
Image | prd.icp:8500/dev/aspnetmvcapp:1.0
Image Pull Secrets | registry-secret
Container port | 80

# Expose the deployment with Service
Refer to [deploy custom image](https://github.ibm.com/icp-ap/Learning/blob/master/demo-with-ipad.md#demo-2-custom-docker-image)

General Tab

name | value
-----| -----
Name | js-dotnet-kestrelapp-service
Type | NodePort

Ports Tab

name | value
-----| -----
TCP | http, 5000, 80

Selectors Tab

name | value
-----| -----
app | js-dotnet-kestrelapp

# Access the application
[link to the deployed application](http://192.168.64.221:31798/)
![app](https://github.ibm.com/icp-ap/dotnet/blob/master/images/app.png)

# Scale the application
Refer to [deploy custom image](https://github.ibm.com/icp-ap/Learning/blob/master/demo-with-ipad.md#demo-2-custom-docker-image)

# Demo Script
1. Create Deployment Video
2. Create Service Video
3. Update Deployment Video
4. Rollback app video
4. Scale Up Video
5. Self healing Video

# Create Helm charset
**todo**
