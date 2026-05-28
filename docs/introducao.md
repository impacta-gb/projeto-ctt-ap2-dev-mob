# Introdução e Instalação do Go

Go é uma linguagem de programação de código aberto desenvolvida pela Google, focada em produtividade, simplicidade e concorrência eficiente.

:::tip[Por que usar Go?]
Go compila direto para código de máquina, o que a torna extremamente rápida, quase tanto quanto C ou C++.
:::

## Como Instalar

1. Acesse o site oficial: [go.dev/dl](https://go.dev/dl/)
2. Baixe o instalador para o seu sistema operacional (Windows, Linux ou macOS).
3. Siga o instalador padrão avançando até o final.

:::warning[Atenção com as Variáveis de Ambiente]
No Windows, o instalador costuma configurar o `PATH` automaticamente. Para testar se deu certo, abra um terminal e digite `go version`.
:::

## Nosso Primeiro Código em Go

Veja abaixo a estrutura básica de um arquivo Go:

```go
package main

import "fmt"

func main() {
    fmt.Println("Olá, Mundo! O Zensical está funcionando!")
}