## Description
Image based on WindowsServerCode to host Web API projects dependent on the 
Databricks ODBC driver.

## How To use
In the default Dockerfile created with an ASP.Net Core Web API project, use 

    FROM martinmason1989/databricks-odbc 

instead of the default 

    FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-nanoserver-1903 AS base

## Limitations
This image was created explicitly targeting .NET 5.0. If you need to target
a different version, the targeted version is specified in the Dockerfile. You
can modify the Dockerfile and rebuild your own image. The Simba Spark Databricks ODBC driver install will need to be in a drivers subdirectory of the folder containing the Dockerfile for the image to be successfully built.

## Git Repository
