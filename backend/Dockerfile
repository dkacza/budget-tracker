FROM --platform=$BUILDPLATFORM golang:1.26.2-alpine AS builder

WORKDIR /app
COPY go.mod ./
RUN go mod download
COPY . .

ARG VERSION=dev
ARG BUILD_TIME=unknown
ARG TARGETARCH

RUN CGO_ENABLED=0 GOOS=linux GOARCH=$TARGETARCH go build \
    -ldflags="-X main.version=${VERSION} -X main.buildTime=${BUILD_TIME}" \
    -o backend .

FROM scratch
COPY --from=builder /app/backend /backend
EXPOSE 8080
ENTRYPOINT ["/backend"]
