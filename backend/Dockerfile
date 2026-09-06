# ==========================================
# STAGE 1: Build the Go application binary
# ==========================================
FROM golang:1.23-alpine AS builder

# Set the working directory inside the build container
WORKDIR /app

# Copy dependency manifests first to leverage Docker caching
COPY go.mod go.sum ./
RUN go mod download

# Copy the rest of the application source code
COPY . .

# Compile a statically linked binary for production
# CGO_ENABLED=0 disables dynamic C0 dependencies for maximum portability
# -ldflags="-s -w" strips debug information to minimize binary size
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o dev-backend .

# ==========================================
# STAGE 2: Create a minimal deployment image
# ==========================================
FROM alpine:3.20

# Create a non-root system user for security compliance
RUN adduser -D -u 10001 appuser

# Set working directory for the runtime container
WORKDIR /app

# Copy the compiled binary from the builder stage
COPY --from=builder /app/dev-backend .

# Switch away from root to the non-privileged user
USER appuser

# Document the port your web application listens on
EXPOSE 8080

# Execute the application binary
ENTRYPOINT ["./dev-backend"]
