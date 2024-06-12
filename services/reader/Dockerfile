FROM golang:1.16

WORKDIR /app

COPY main.go ./

RUN go mod init github.com/PicPay/picpay-jr-devops-challenge/reader

RUN go get github.com/go-redis/redis

RUN go get github.com/rs/cors

COPY . .

EXPOSE 8081

CMD ["go", "run", "main.go"]