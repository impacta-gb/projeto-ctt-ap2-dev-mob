---
icon: lucide/code
---

# Introdução e Instalação do Go

Bem-vindo(a) ao guia de introdução e instalação da linguagem Go. Aqui você encontra os conceitos básicos, os principais comandos e um passo a passo para instalar o Go no Windows.

## Navegação rápida

- [O que é Go?](#o-que-e-go)
- [Por que usar Go?](#por-que-usar-go)
- [Instalação no Windows](#instalacao-no-windows)
- [Verificação pós-instalação](#verificacao-pos-instalacao)
- [Primeiro programa](#primeiro-programa)
- [Boas práticas e avisos](#boas-praticas-e-avisos)

## O que é Go?

Go, também conhecida como Golang, é uma linguagem de programação criada pelo Google para:

- ser simples e eficiente
- oferecer compilação rápida
- ter suporte nativo a concorrência
- gerar binários estáticos prontos para produção

### Características principais

- Tipagem estática e inferência de tipo
- Gerenciamento automático de memória (garbage collector)
- Sistema de pacotes simples
- Concor­rência com goroutines e channels

!!! note "Lembrete"
    Go foi projetado para código legível e manutenção simples. Prefira composição em vez de herança.

## Por que usar Go?

Go é ideal para aplicações de backend, APIs, ferramentas de linha de comando e serviços distribuídos.

### Vantagens

- Compilação rápida
- Execução em binários leves
- Concurrency simples com goroutines
- Deploy fácil para servidores

### Tipos de variáveis comuns

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| `int` | Inteiro de 32/64 bits | `var x int = 42` |
| `float64` | Ponto flutuante | `price := 19.90` |
| `string` | Texto | `name := "Go"` |
| `bool` | Verdadeiro/Falso | `ok := true` |
| `rune` | Caractere Unicode | `ch := 'G'` |

## Instalação no Windows

O Go pode ser instalado pelo instalador oficial ou via terminal usando `winget`.

### Opção 1: Instalador oficial

1. Acesse `https://go.dev/dl/`
2. Baixe o arquivo `go<versão>.windows-amd64.msi`
3. Execute o instalador e aceite as configurações padrão

### Opção 2: Instalar com winget

```powershell
winget install Google.Go
```

!!! warning "Atenção"
    Antes de instalar, verifique se não existem versões antigas do Go no `PATH`. Versões conflitantes podem quebrar builds.

### Configuração do `GOPATH`

O instalador oficial já define o `GOROOT`. Para projetos locais, use um diretório de trabalho como `C:\Users\seu-usuario\go`.

```powershell
mkdir C:\Users\seu-usuario\go
setx GOPATH "C:\Users\seu-usuario\go"
```

## Verificação pós-instalação

Após instalar, confirme a versão e o ambiente:

```powershell
go version
go env GOROOT
go env GOPATH
```

### Comandos úteis do Go

| Comando | Descrição |
|---------|-----------|
| `go version` | Mostra a versão instalada |
| `go env` | Exibe variáveis de ambiente do Go |
| `go run` | Compila e executa código rapidamente |
| `go build` | Compila para binário |
| `go test` | Executa testes |
| `go mod init` | Cria um módulo Go |

## Primeiro programa

Crie um arquivo `ola.go` com o conteúdo abaixo:

```go
package main

import "fmt"

func main() {
    fmt.Println("Olá, mundo!")
}
```

Execute:

```powershell
go run ola.go
```

### Build do programa

```powershell
go build -o ola.exe ola.go
.
```

!!! note "Dica"
    Use `go run` durante o desenvolvimento e `go build` quando for gerar o binário final.

## Boas práticas e avisos

### Concorrência com responsabilidade

Go facilita criar goroutines, mas é preciso controlar o acesso a recursos compartilhados.

!!! warning "Cuidado ao usar goroutines"
    Não use goroutines ilimitadas sem controle. Isso pode causar uso excessivo de memória e problemas de sincronização.

### Organize seu projeto

- Use `go mod init <nome-do-modulo>` para iniciar o módulo
- Separe código em pacotes claros
- Mantenha funções pequenas e legíveis

### Exemplo de módulo básico

```powershell
go mod init exemplo/go
```

## Próximos passos

- Aprender sobre `go fmt` e `go vet`
- Estudar `channels` e `select`
- Criar APIs ou ferramentas CLI com Go

> Explore o poder do Go com código simples, concisão e desempenho.
