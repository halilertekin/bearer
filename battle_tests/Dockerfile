# FROM golang:alpine AS builder
FROM golang:latest AS builder

ARG BEARER_VERSION
ARG BATTLE_TEST_SHA
ARG ATTEMPT
ARG LANGUAGE
ARG S3_BUCKET

RUN mkdir /app
ADD . /app/
WORKDIR /app

# RUN apk update && apk upgrade && apk add --no-cache build-base git

RUN go build -ldflags="-X 'github.com/bearer/bearer/battle_tests/build.Version=${BEARER_VERSION}' -X 'github.com/bearer/bearer/battle_tests/build.BattleTestSHA=${BATTLE_TEST_SHA}' -X 'github.com/bearer/bearer/battle_tests/build.Attempt=${ATTEMPT}' -X 'github.com/bearer/bearer/battle_tests/build.Language=${LANGUAGE}' -X 'github.com/bearer/bearer/battle_tests/build.S3Bucket=${S3_BUCKET}'" \
  -a -o dist/battle_tests ./battle_tests/battle_tests.go

# FROM alpine
FROM golang:latest

COPY --from=builder /app/dist/ /app/

# RUN apk update && apk upgrade && apk add --no-cache git libgcc libstdc++ musl-utils file
# required to run bearer on alpine
# https://github.com/gliderlabs/docker-alpine/issues/219
# /lib64/ld-linux-x86-64.so.2 is provided by gcompat
# To get this info, I ran file and ldd on /app/bearer
# https://askubuntu.com/questions/1388695/another-no-such-file-or-directory-but-the-file-exists/1388699#1388699
# RUN apk add --no-cache gcompat procps perf libc6-compat

WORKDIR /app

RUN mkdir battle-test-tmp
RUN chmod +x /app/bearer

CMD ["/app/battle_tests"]
