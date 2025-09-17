# ---- Build Stage ----
# Use a standard Ubuntu image as a 'builder' to get the OTP files
FROM ubuntu:22.04 as builder

# Install only the tools needed to download and unpack
RUN apt-get update && apt-get install -y wget unzip && rm -rf /var/lib/apt/lists/*

# Download and unpack OTP into a temporary location
WORKDIR /source
RUN wget https://github.com/ITSLeeds/TDS/releases/download/0.20.1/otp_TDS.zip && \
unzip otp_TDS.zip

# ---- Final Stage ----
# Start from a clean, official OpenJDK base image, which is minimal and secure
FROM eclipse-temurin:8-jre-focal

# Set the working directory
WORKDIR /app

# Copy the OTP application files from the 'builder' stage
COPY --from=builder /source/otp_TDS/ .

# Ensure the graphs directory exists, as OTP will look for it
RUN mkdir -p graphs

# Expose the port the application will run on
EXPOSE 8080

# Set the command to run the OTP server
CMD ["java", "-Xmx3g", "-jar", "otp-1.5.0-shaded.jar", "--router", "west-yorkshire", "--graphs", "/app/graphs", "--server", "--port", "8080"]