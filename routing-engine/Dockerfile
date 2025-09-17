FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    wget curl openjdk-8-jdk unzip && \
    rm -rf /var/lib/apt/lists/*

# Download and setup OTP (adapt from your R script)
WORKDIR /app
RUN wget https://github.com/ITSLeeds/TDS/releases/download/0.20.1/otp_TDS.zip && \
    unzip otp_TDS.zip && \
    rm otp_TDS.zip

# Copy any custom graphs or configs if needed
# COPY graphs /app/otp_TDS/graphs

EXPOSE 8080

CMD ["/usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java", \
     "-Xmx10000M", \
     "-d64", \
     "-jar", \
     "otp_TDS/otp-1.5.0-shaded.jar", \
     "--router", \
     "west-yorkshire", \
     "--graphs", \
     "otp_TDS/graphs", \
     "--server", \
     "--port", \
     "8080"]