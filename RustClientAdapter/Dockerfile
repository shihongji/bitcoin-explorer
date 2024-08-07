# Build stage
FROM rust:1.74 as builder

WORKDIR /usr/src/app

# Copy over your manifests
COPY Cargo.toml .

RUN touch Cargo.lock

# This is a dummy build to get the dependencies cached
RUN mkdir src && \
    echo "fn main() {println!(\"if you see this, the build broke\")}" > src/main.rs && \
    cargo build --release && \
    rm -rf src

# Now copy in your actual source code
COPY src ./src

# This builds your application using the cached dependencies
RUN cargo build --release

# Runtime stage
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    libpq-dev \
    ca-certificates \
    libssl3 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/src/app

COPY --from=builder /usr/src/app/target/release/rust_client_adapter .

EXPOSE 3001

CMD ["./rust_client_adapter"]
