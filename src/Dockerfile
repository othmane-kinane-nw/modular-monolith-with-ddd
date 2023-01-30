#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:3.1-buster AS prepare-restore-files
ENV PATH="${PATH}:/root/.dotnet/tools"
RUN dotnet tool install --global --no-cache dotnet-subset --version 0.3.2
WORKDIR /src
COPY . .
RUN dotnet subset restore "API/CompanyName.MyMeetings.API/CompanyName.MyMeetings.API.csproj" --root-directory /src --output /src/restore_subset/

FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
WORKDIR /src
COPY --from=prepare-restore-files /src/restore_subset .
RUN dotnet restore "API/CompanyName.MyMeetings.API/CompanyName.MyMeetings.API.csproj"
COPY . .
WORKDIR "/src/API/CompanyName.MyMeetings.API"
RUN dotnet build "CompanyName.MyMeetings.API.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "CompanyName.MyMeetings.API.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

ADD entrypoint.sh /

ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
