---
icon: lucide/code
---

# Sintaxe Básica e Variáveis em Go

## Navegação rápida
- [Estrutura de um programa Go](#estrutura-de-um-programa-go)
- [Declaracao de variaveis](#declaracao-de-variaveis)
- [Tipos de variaveis](#tipos-de-variaveis)
- [Inicializacao e valores zero](#inicializacao-e-valores-zero)
- [Declaracao curta](#declaracao-curta)
- [Constantes](#constantes)
- [Boas praticas](#boas-praticas)

## Estrutura de um programa Go

Um programa em Go começa com um pacote e, para gerar um executável, deve usar `package main`.

```go
package main

import "fmt"

func main() {
    fmt.Println("Olá, Go!")
}
```

??? note "Pacote e função principal"
    Um programa Go executável exige `package main` e `func main()`. Sem isso, o código não compila como aplicação.

## Declaracao de variaveis

Em Go, as variáveis podem ser declaradas com a palavra-chave `var` ou usando a declaração curta `:=` dentro de funções.

```go
var nome string = "Gopher"
var idade int = 25
var ativo = true
var nota, faltas = 9.5, 1
```

### Declaração com `var`

A declaração `var` permite especificar tipo e valor inicial explicitamente.

```go
var frase string = "Olá, mundo"
var quantidade int
quantidade = 10
```

### Declaração múltipla

```go
var (
    nome  string = "Maria"
    idade int    = 29
    ativo bool   = true
)
```

## Tipos de variaveis

| Tipo     | Descrição                        | Exemplo                 |
|----------|----------------------------------|-------------------------|
| `int`    | Inteiro com sinal                | `var i int = 42`        |
| `string` | Texto                           | `var s string = "Go"`  |
| `bool`   | Verdadeiro ou falso             | `var ok bool = true`    |
| `float64`| Número de ponto flutuante       | `var x float64 = 3.14`  |
| `rune`   | Código Unicode para um caractere| `var r rune = 'ç'`      |
| `byte`   | Alias de `uint8`                | `var b byte = 0x7F`     |

??? warning "Tipo padrão do float"
    Em `var x = 3.14`, o tipo inferido será `float64`, não `float32`.

## Inicializacao e valores zero

Variáveis declaradas sem inicializador recebem o valor zero do tipo.

```go
var contador int
var texto string
var ligado bool

fmt.Println(contador) // 0
fmt.Println(texto)    // ""
fmt.Println(ligado)   // false
```

### Zero values úteis

- `0` para tipos numéricos
- `""` para `string`
- `false` para `bool`
- `nil` para ponteiros, slices, maps, canais, funções e interfaces

## Declaracao curta

A declaração curta é a forma mais comum de criar variáveis dentro de funções.

```go
nome := "GoLang"
idade := 33
ativo := true
```

??? warning "Uso restrito de :="
    A sintaxe `:=` só funciona dentro de funções. Para variáveis de pacote, use `var`.

### Atualização de variáveis existentes

```go
contador := 0
contador += 1
```

### Múltiplas variáveis com declaração curta

```go
x, y := 10, 20
```

## Constantes

Use `const` para valores que não mudam.

```go
const pi = 3.14159
const saudacao string = "Bem-vindo ao Go"
```

??? note "Constantes"
    Constantes em Go devem ser definidas com um valor determinável em tempo de compilação.

## Boas praticas

- Prefira nomes claros e curtos para variáveis locais.
- Use `var` quando precisar declarar a variável antes de atribuir ou quando quiser documentar o tipo.
- Use `:=` para inicializar rapidamente dentro de funções.
- Evite declarar variáveis que não são usadas — o compilador Go rejeita código com variáveis declaradas e não utilizadas.

## Exemplo completo

```go
package main

import "fmt"

func main() {
    var nome string = "Ana"
    idade := 28
    ativo := true

    fmt.Printf("Nome: %s\nIdade: %d\nAtivo: %t\n", nome, idade, ativo)
}
```

## Teste no Go Playground

Use o Go Playground para executar o exemplo sem precisar instalar nada localmente.

```go
package main

import "fmt"

func main() {
    var nome string = "Ana"
    idade := 28
    ativo := true

    fmt.Printf("Nome: %s\nIdade: %d\nAtivo: %t\n", nome, idade, ativo)
}
```

??? note "Abrir no Go Playground"
    Copie o código acima e cole em: [https://go.dev/play/](https://go.dev/play/)

> Dica: o Go Playground permite compartilhar o código gerando um link permanente depois de salvar.

## Veja também

- [Introdução e Instalação do Go](introducao-instalacao-go.md)
- [Como Funciona o Zensical](como-funciona.md)
