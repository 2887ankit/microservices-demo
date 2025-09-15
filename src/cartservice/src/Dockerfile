# syntax=docker/dockerfile:1.4
FROM mcr.microsoft.com/dotnet/sdk:9.0.101-noble AS builder
WORKDIR /app

# Speed up restore / reduce flakiness
ENV DOTNET_CLI_TELEMETRY_OPTOUT=1 \
    DOTNET_NOLOGO=1 \
    NUGET_PACKAGES=/root/.nuget/packages

COPY cartservice.csproj .
# Use a cache mount for NuGet packages and reduce parallelism
RUN --mount=type=cache,target=/root/.nuget/packages \
    dotnet restore cartservice.csproj -a ${TARGETARCH:-amd64} --disable-parallel --verbosity minimal

COPY . .
RUN --mount=type=cache,target=/root/.nuget/packages \
    dotnet publish cartservice.csproj -c Release -a ${TARGETARCH:-amd64} -o /out

FROM mcr.microsoft.com/dotnet/runtime-deps:9.0.1-noble-chiseled
WORKDIR /app
COPY --from=builder /out .
ENTRYPOINT ["./cartservice"]
