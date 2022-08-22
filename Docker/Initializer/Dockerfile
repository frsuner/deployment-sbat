#Initiial Stage Binary Building 

FROM golang:alpine as builder
WORKDIR /build
ADD . /build/
RUN CGO_ENABLED=0 go build -o initialize .

#Second Stage - Actual Deployment Container

FROM alpine:3.16.0


#Config for self signed certs

COPY _wildcard.example.com-key.pem /usr/local/share/ca-certificates/_wildcard.example.com-key.crt
COPY _wildcard.example.com.pem /usr/local/share/ca-certificates/_wildcard.example.com.crt
COPY _wildcard.sbat.demo-key.pem /usr/local/share/ca-certificates/_wildcard.sbat.demo-key.crt
COPY _wildcard.sbat.demo.pem /usr/local/share/ca-certificates/_wildcard.sbat.demo.crt
RUN cat /usr/local/share/ca-certificates/_wildcard.example.com-key.crt >> /etc/ssl/certs/ca-certificates.crt && \
cat /usr/local/share/ca-certificates/_wildcard.example.com.crt >> /etc/ssl/certs/ca-certificates.crt && \
cat /usr/local/share/ca-certificates/_wildcard.sbat.demo-key.crt >> /etc/ssl/certs/ca-certificates.crt && \
cat /usr/local/share/ca-certificates/_wildcard.sbat.demo.crt >> /etc/ssl/certs/ca-certificates.crt


#Creating the Initializer container

RUN apk --no-cache add curl libc6-compat gcompat
RUN addgroup -S sob-group && adduser -S sob-user -G sob-group

RUN mkdir -p "/opt/sob/config"

WORKDIR "/opt/sob"
COPY config config
COPY --from=builder /build/initialize .

RUN chmod 500 /opt/sob/initialize
RUN chown -R sob-user:sob-group /opt

USER sob-user