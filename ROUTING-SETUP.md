# Routing Engine Setup Guide

> High-level overview for deploying OpenTripPlanner as a routing engine.

## Overview

This guide provides a minimal approach to deploying a Dockerized OpenTripPlanner (OTP) server for transport planning applications.

## Core Components

1. **OpenTripPlanner (OTP)** - The routing engine
2. **Graph Data** - Transport network (GTFS, OSM)
3. **Docker** - Containerization
4. **Reverse Proxy** (optional) - HTTPS/SSL termination

## Basic Steps

### 1. Prepare the Environment

- Install Docker on your server
- Ensure network connectivity (port 8080 or custom)
- Set up domain/DNS if using HTTPS

### 2. Deploy OTP

The easiest method is using Docker:

```bash
# Pull the image
docker pull itsleeds/tds:latest

# Run the container
docker run -d -p 8080:8080 \
  -v /path/to/graph:/opt/otp/graph \
  itsleeds/tds:latest
```

For custom deployments, see the [Dockerfile in dstp](https://github.com/tdscience/dstp/blob/main/routing-engine/Dockerfile).

### 3. Load Graph Data

OTP requires a router graph built from:
- GTFS feeds (public transit)
- OpenStreetMap data (roads/paths)

Build the graph:
```bash
docker exec -it otp-container /bin/bash
# Run OTP build tools
```

### 4. Configure Routing

Key OTP parameters:
- `router.id` - Router identifier
- `transit` - Enable/disable transit routing
- `pedestrian` - Walking settings
- `bicycle` - Cycling settings
- `car` - Car routing settings

### 5. Verify Installation

```bash
curl http://localhost:8080/otp/routers/default
```

Should return router info with version details.

## Resources

- **Full deployment guide:** [routing-engine.qmd](https://github.com/tdscience/dstp/blob/main/routing-engine.qmd)
- **Docker setup:** [routing-engine/Dockerfile](https://github.com/tdscience/dstp/blob/main/routing-engine/Dockerfile)
- **ITSLeeds TDS releases:** https://github.com/ITSLeeds/TDS/releases
- **OTP documentation:** https://docs.opentripplanner.org/

## Notes

- Graph building can take 10-60 minutes depending on data size
- Ensure adequate RAM (8GB+ recommended for large regions)
- Consider using Spot/Preemptible VMs for cost efficiency
- Set up monitoring for production deployments
