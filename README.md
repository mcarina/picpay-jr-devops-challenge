# Desafio DevOps jr PicPay

Neste projeto, atualizei o Dockerfile das imagens para melhorar a estrutura e a funcionalidade do ambiente de desenvolvimento e produção. Abaixo estão os detalhes das mudanças realizadas:

## Dockerfile da Imagem Node.js do Frontend
>
>## Versão Anterior:
>```
>FROM node:latest
>
>WORKDIR /app
>
>ADD . .
>
>RUN npm install -g serve
>
>EXPOSE 5000
>
>CMD "serve"
>
>```
>Na versão anterior do Dockerfile, a imagem Node.js é a mais recente, diretório de trabalho definido como /app, exposto na porta 5000 e comando "serve" era usado para inciar o projeto.
>
>## Versão Atualizada:
>```
>FROM node:latest
>
>WORKDIR /app
>
>ADD . .
>
>RUN npm install -g serve
>
>EXPOSE 3000
>
>CMD ["serve", "-s", "."]
>
>```
>Na versão atualizada, mantive a imagem base como node:latest, defini o diretório de trabalho como /app, adicionei todos os arquivos ao diretório de trabalho, a instalação dos pacote do serve permanece global com o npm, exposto a porta 3000 e iniciando o servidor serve com o comando serve -s .
>
>
## Dockerfile da Imagem Go

>## Versão Anterior:
>```
>FROM golang:1.16
>
>WORKDIR /go/src/github.com/PicPay/picpay-jr-devops-challenge/services/go
>
>ADD . /go/src/github.com/PicPay/picpay-jr-devops-challenge/services/go
>
>EXPOSE 8080
>
>CMD ["go","run","main.go"]
>
>```
>A versão anterior do Dockerfile definia o diretório de trabalho e adicionava os arquivos diretamente no diretório de origem do Go, especificando a porta 8080 para expor o serviço e executando o arquivo main.go diretamente com o comando go run.
>
>## Versão Atualizada:
>```
>FROM golang:1.16
>
>WORKDIR /app
>
>COPY main.go ./
>
>RUN go mod init github.com/PicPay/picpay-jr-devops-challenge/reader
>
>RUN go get github.com/go-redis/redis
>
>RUN go get github.com/rs/cors
>
>COPY . .
>
>EXPOSE 8081
>
>CMD ["go", "run", "main.go"]
>
>```
> Na versão atualizada, melhorei a estrutura do Dockerfile ao definir um diretório de trabalho mais genérico (/app), copiar apenas o arquivo main.go necessário, inicializar o módulo Go para o projeto, instalar as dependências necessárias (go-redis e rs/cors) e expor a porta 8081 para garantir a comunicação adequada do serviço.
>
>Outro erro, era no arquivo main.go, na linha 27:
>
>_./main.go:27:26: too many arguments in call to client.cmdable.Get
>        have (context.Context, string)
>        want (string)
>./main.go:28:20: cannot use key (type *redis.StringCmd) as type string in argument to fmt.Fprintf_
>
> <h3>Resolução:</h3>
>
>```key := client.Get("SHAREDKEY")```

## Dockerfile da Imagem Python
>
>## Versão Anterior:
>```
>FROM python:3.9
>
>WORKDIR /app
>
>ADD . /app
>
>EXPOSE 8081
>
>CMD ["python"]
>
>ENTRYPOINT ["main.py"]
>```
>A versão anterior do Dockerfile usava a imagem Python 3.9 como base, definia o diretório de trabalho como /app, adicionava todos os arquivos diretamente no diretório de trabalho, especificava a porta 8081 para expor o serviço e executava o arquivo main.py como ponto de entrada.
>
>## Versão Atualizada:
>```
>FROM python:3.9-slim
>
>WORKDIR /app
>
>COPY requirements.txt ./
>RUN pip install --no-cache-dir -r requirements.txt
>
>ADD . /app
>
>EXPOSE 8080
>
>CMD ["python3", "main.py"]
>
>```
> Na versão atualizada, mudei a imagem base para python:3.9-slim, primeiro sendo a copia do arquivo requirements.txt e a instalação das dependências com o comando pip install, adicionar os arquivos ao diretório de trabalho, especificar a porta 8080 para expor o serviço e atualizei o comando CMD para _python3 main.py_ para garantir a execução correta do arquivo main.py. O arquivo _requirements.txt_ foi adicionado, visto que o main.py precisa do _redis_ com dependência.
>

## Alterações do Docker-compose:
>Não havendo muitas alterações, no docker-compose, apenas ajustei a porta do frontend de 5000 para 3000, alinhando com o Dockerfile do frontend. Anteriormente, a rede incluía apenas o backend, agora ela inclui também o frontend:

>```
>services:
>  web:
>    build: services/frontend
>    ports:
>      - "3000:3000"
>    networks:
>      - frontend
>      - backend```

>```networks:
>  frontend:
>  backend:
>```
>Essas mudanças foram feitas para garantir que a aplicação frontend seja servida corretamente na porta 3000, conforme configurado no Dockerfile correspondente.