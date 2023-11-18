# Golang

## O que é Go (Golang)?
Go, também conhecida como Golang, é uma linguagem de programação de código aberto desenvolvida pela Google. Foi criada em 2009 com o intuito de oferecer eficiência, simplicidade e alto desempenho para o desenvolvimento de sistemas distribuídos, aplicativos web e serviços de rede.

## Instalação do Go
Para começar a programar em Go, siga estas etapas para instalar o compilador e configurar o ambiente de desenvolvimento:

### Baixe e instale o Go:

Acesse o site oficial [Go](https://go.dev) e baixe a versão adequada para o seu sistema operacional.
Siga as instruções de instalação fornecidas.

### Configuração do PATH:

Após a instalação, é importante configurar o PATH para que o sistema possa localizar os binários do Go.
Adicione o diretório bin do Go ao seu PATH. Isso permite que você execute comandos Go a partir do terminal/cmd.

### Verificação da Instalação:

Abra o terminal ou prompt de comando e digite go version.
Se a instalação foi bem-sucedida, você verá a versão do Go instalada.

## Iniciando um Projeto em Go

A estrutura básica de um projeto em Go envolve pacotes (packages). Cada pacote é armazenado em um diretório com o mesmo nome do pacote. Veja como iniciar um projeto simples

### Estrutura Básica do Projeto:

Crie um diretório para o seu projeto, por exemplo, meu-projeto.
Dentro desse diretório, crie um arquivo chamado main.go. Esse arquivo é o ponto de entrada padrão para programas em Go.

### Exemplo de Código em Go (Nosso famoso "Olá Mundo"):

```
package main

import "fmt"

func main() {
    fmt.Println("Olá Mundo!")
}
```

Este código simplesmente imprime "Olá Mundo!" quando executado.

- Abra o terminal ou prompt de comando.
- Navegue até o diretório onde seu arquivo main.go (normalmente criado na raiz do projeto, ou também em pastas como cmd).
- Execute o comando go run main.go. Isso compilará e executará o programa.

## Build

Para compilar o projeto em Go, você pode executar o seguinte comando:

```
go build
```

Este comando identifica automaticamente o sistema operacional e a arquitetura atual. Se desejar especificar o sistema operacional e a arquitetura durante a compilação, você pode usar o comando a seguir:

```
GOOS=linux GOARCH=amd64 go build main.go
```
Neste exemplo, estou direcionando a compilação para o sistema operacional Linux com arquitetura x64, mesmo estando realizando a compilação em uma máquina Windows.

### Documentação Geral Golang
https://go.dev/doc/
