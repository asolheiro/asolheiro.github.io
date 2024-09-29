---
title: Distribuições linux e binários Go
author: 01
date: 2024-04-12
categories: [linux, go]
tags: [go, linux, scratch]
publisehd: true
---

Na minha humilde opinião, um dos motivos que fazem programar em Go ser tão bom é a possibilidade de gerar um binário para execução com a facilidade de um go build .no terminal.

Essa pequena prática traz diversas vantagens para nós desenvolvedores:

os binários podem ser usados como forma de documentar as versões do código-fonte;
facilitamos o deploy da aplicação;
- otimizamos o desempenho ao compilar para determinada arquitetura;
- binários são mais difíceis de ler e alterar, logo aumentamos a segurança ao utilizá-los;
- trazemos mais independência do ambiente de desenvolvimento;
- temos mais eficiência no uso de recursos computacionais.

Poderíamos escrever arquivos inteiros sobre as vantagens de implementações usando binários, mas por hora isso é o bastante.

Para exemplificar, nesse artigo usaremos um código-fonte básico de uma API que retorna apenas um “Olá, mundo!”:

```go
package main

import (
 "fmt"
 "log"
 "net/http"
)

func main() {
 http.HandleFunc("/", func(rw http.ResponseWriter, r *http.Request) {
  fmt.Fprintf(rw, "Olá, mundo\\n")
 })

 log.Fatal(http.ListenAndServe(":8080", nil))
}
```

que após compilado rende um arquivo binário de miseráveis 6.8 MB, 

>![imagem001](/_posts/media_posts/001-distGomedia_posts/001-distGo/imagem001.jpg)
>***Figura 01: Código-fonte main.go e seu binário, main, resultante***

os quais ainda podemos reduzir mais se utilizarmos linker flags — que se comunicam direto com o linker, ferramenta que é responsável por vincular o código-fonte ao binário. Esse assunto rende uma discussão inteira, então não alongaremos mais.

Nesse caso, podemos fazer a build com duas flags em específico:

`-s` : omitirá a tabela de símbolos e informações para debug

`-w` : omite a tabela DWARF para debug

As quais passaremos no momento da compilação:

```bash
$ go build -ldflags="-s -w"
```

e nos permite reduzir nosso binário em 2.8MB, ou cerca de 16%, neste caso (há situações em que esse número pode ser maior).

>![imagem002](/_posts/media_posts/001-distGomedia_posts/001-distGo/imagem002.jpg)
>***Figura 02: Tamanho do código-fonte e do binário resultante usando as linker flags***

Vale salientar, também, que Go naturalmente cria binários compatíveis com a máquina em que estamos compilamos o código, mas isso não nos impede de fazer alterações visando resultados feitos para rodar em outros tipos sistemas operacionais e arquiteturas.

Essas informações especificamente são guardadas pelo compilador Go na forma de variáveis de sistema; basta executar `go env GOOS GOARCH` na linha de comando para ver como o compilador traz para si essas informações da máquina.

Dessa forma, podemos intuir que é possível passar diferentes valores para essas variáveis durante o processo de compilação. Go inclusive nos adianta quais os possíveis valores para elas na documentação

Por exemplo, fazendo `GOOS=windows GOARCH=amd64 go build .` instruímos o compilador a fazer um binário que rode especificamente em sistemas operacionais Windows com arquitetura de processamento AMD64.

Isso acaba se tornando uma mão na roda não só para quem está desenvolvendo, mas também para quem fará a implementação.

Intuitivamente, se pensarmos em fazer o deploy usando Docker escreveríamos o Dockerfile da seguinte maneira:

```go
FROM golang:latest

WORKDIR go/src/app

COPY . .

ENV CGO_ENABLED=0

ENV GOFLAGS="-ldflags=-s -w"

RUN ["go", "mod", "init", "hello-world"]

RUN ["go", "build", "."]

CMD ["./main"]
```
para criar a imagem com `docker buildx build -t go-golang .` de 898 MB. Essa imagem será nossa base de comparação para os outros experimentos.

>![imagem003](/_posts/media_posts/001-distGomedia_posts/001-distGo/imagem003.jpg)
>***Figura 03: No terminal, docker image ls***
Só que, se compreendermos que para rodar o binário não precisamos do compilador em si, mas só do kernel linux, podemos utilizar de técnicas de construção de imagens em Docker para otimizar ainda mais os recursos gastos por nossa imagem.

Nesse caso, em específico, podemos reduzir em centenas de vezes o espaço ocupado pela imagem sem comprometer o binário usando apenas o multi-stage no nosso Dockerfile.

Em resumo, essa técnica nos permitirá construir nossa imagem de container em duas etapas:

Estágio de build, onde copiaremos os arquivos da máquina local para o container montado com uma imagem basegolang:latest que servirá apenas para que compilemos o código-fonte;
Estágio de execução, quando trazemos o binário já compilado para uma imagem base linux (sem o Go instalado) e o executamos.
Veja como poderíamos fazer isso usando uma distribuição popular do linux como Ubuntu:

```go
FROM golang:1.22 as build

WORKDIR /go/src/app

COPY . .

ENV CGO_ENABLED=0

ENV GOFLAGS="-ldflags=-s -w"

RUN ["go", "mod", "init", "hello-world"]

RUN ["go", "build", "."]

FROM ubuntu

WORKDIR /app

COPY --from=build /go/src/app /app

CMD ["./main"]
```

Construindo essa imagem com `docker buildx build -t go-ubuntu .` já temos uma boa diferença no recurso utilizado. Uma imagem mais de 10 vezes menor: 87.5 MB.

>![imagem004](/_posts/media_posts/001-distGomedia_posts/001-distGo/imagem004.jpg)
>***Figura 04: No terminal, docker image ls***

E seguindo nessa linha podemos comprimir ainda mais nossa imagem. Usando `alpine:latest`, uma imagem base muito conhecida por quem costuma reduzir o tamanho dos containers, temos 17 MB.

>![imagem005](/_posts/media_posts/001-distGomedia_posts/001-distGo/imagem005.jpg)
>***Figura 05: No terminal, docker image ls***

Ou, se quisermos esticar ainda mais a corda, podemos usar `scratch`, que basicamente é uma “imagem” linux mínima, vazia, que contém nenhum binário, biblioteca ou qualquer outra coisa, essa “distro” é muito comum quando queremos rodar um único binário. O resultado é uma imagem de 9.59 MB.

>![imagem006](/_posts/media_posts/001-distGomedia_posts/001-distGo/imagem006.jpg)
>***Figura 06: No terminal, docker image ls***

Dessa maneira, conseguimos reduzir ainda mais o espaço ocupado pela nossa imagem. Nesse caso, uma imagem quase 94 vezes menor! Uma redução absurda.

Relembrando, nosso Dockerfile está montado da seguinte maneira:

```go
FROM golang:1.22 as build

WORKDIR /go/src/app

COPY . .

ENV CGO_ENABLED=0

ENV GOFLAGS="-ldflags=-s -w"

RUN ["go", "mod", "init", "hello-world"]

RUN ["go", "build", "."]

FROM scratch

WORKDIR /app

COPY --from=build /go/src/app /app

CMD ["./main"]
```

Essa forma de escrever nossos containers levanta muitos outros assuntos que poderemos discorrer sobre no futuro, mas agora vale a pena ressaltar que como a imagem scratch é mínima, ou seja, não possui pacotes, suas vulnerabilidades serão mínimas também; nesse caso: zero.

Podemos fazer várias verificações dentro desse tema, mas aqui temos um demonstrativo usando o Docker Scout, que já vem pré-instalado com o próprio Docker, para checar as CVE’s (Common Vulnerabilities and Exposures) da nossa imagem: 6 pacotes instalados e nenhuma vulnerabilidade conhecida.

>![imagem007](/_posts/media_posts/001-distGomedia_posts/001-distGo/imagem007.jpg)
>***Figura 07: No terminal, docker install para a imagem “go-scratch”***

Até agora vimos só as vantagens de trabalhar dessa maneira e, obviamente, não são só rosas nessa área.

Apesar da imagem Scratch ser bem legal e gerar resultados impressionantes à primeira vista, ela pode nos gerar alguns problemas ou dificuldades bem peculiares.

Se precisarmos rodar em um usuário não root, teremos que configurar tudo manualmente; ou ainda, se precisarmos fazer chamadas https, precisaremos instalar todas as cadeias de certificado manualmente. Nesses casos, usar um Linux Alpine já se torna muito mais cômodo para a construção do container.

Esse é um assunto que provoca muitos debates. Cada assunto mencionado desencadeia ramificações extensas para discussão.

Aqui tratamos sobre Go, mas como ficaria o empacotamento de aplicações em linguagens que não geram binários, como Python, por exemplo?

E se não quisermos/pudermos usar o scratch, como podemos fazer para reduzir as vulnerabilidades da nossa imagem?

Que outras flags de vinculação (linker flags) podemos usar? Existem só essas? E as flags de compilação, o que são?

De toda forma, são duvidas que só poderemos sanar estudando, testando e trocando figurinhas com os outros da forma que fiz com vocês aqui!