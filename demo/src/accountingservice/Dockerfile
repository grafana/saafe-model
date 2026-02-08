# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM mcr.microsoft.com/dotnet/aspnet:8.0@sha256:0d4a76c0a692c7419acf59529ba6dae8552f4fd59e8453538b5291710a29fb9d AS base
USER app
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:8.0@sha256:1682f3cd0cb51464568de4fa88c719bd3be55539653d97540718bdd5b87062d4 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["/src/accountingservice/", "AccountingService/"]
COPY ["/pb/demo.proto", "AccountingService/proto/"]
RUN dotnet restore "./AccountingService/AccountingService.csproj"
WORKDIR "/src/AccountingService"

RUN dotnet build "./AccountingService.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./AccountingService.csproj" --use-current-runtime -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .

USER root
RUN mkdir -p "/var/log/opentelemetry/dotnet"
RUN chown app "/var/log/opentelemetry/dotnet"
RUN chown app "/app/instrument.sh"
USER app

ENTRYPOINT ["./instrument.sh", "dotnet", "AccountingService.dll"]
