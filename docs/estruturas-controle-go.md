---
icon: lucide/git-branch
---

# Estruturas de Controle em Go

## Navegação rápida
- [Condicional If/Else](#condicional-ifelse)
- [Switch](#switch)
- [Loops For](#loops-for)
- [Break e Continue](#break-e-continue)
- [Boas práticas](#boas-práticas)
- [Teste no Go Playground](#teste-no-go-playground)

## Condicional If/Else

A estrutura `if` em Go permite executar código condicionalmente. Diferentemente de outras linguagens, não há parênteses obrigatórios.

```go
package main

import "fmt"

func main() {
    idade := 18

    if idade >= 18 {
        fmt.Println("Maior de idade")
    } else {
        fmt.Println("Menor de idade")
    }
}
```

### If com inicializador

Em Go, você pode inicializar uma variável antes da condição:

```go
if resultado := 10 + 5; resultado > 10 {
    fmt.Println("Resultado é maior que 10:", resultado)
} else {
    fmt.Println("Resultado é menor ou igual a 10")
}
```

??? warning "Escopo da variável"
    A variável `resultado` só existe dentro do bloco `if/else`. Fora dele, não está acessível.

### If/Else if/Else

Para múltiplas condições:

```go
idade := 25

if idade < 13 {
    fmt.Println("Criança")
} else if idade < 18 {
    fmt.Println("Adolescente")
} else if idade < 60 {
    fmt.Println("Adulto")
} else {
    fmt.Println("Idoso")
}
```

## Switch

A estrutura `switch` é uma forma elegante de substituir múltiplos `if/else`.

### Switch básico

```go
dia := "segunda"

switch dia {
case "segunda", "terça", "quarta", "quinta", "sexta":
    fmt.Println("Dia útil")
case "sábado", "domingo":
    fmt.Println("Fim de semana")
default:
    fmt.Println("Dia inválido")
}
```

??? note "Múltiplos valores por case"
    Você pode separar valores com vírgula no mesmo `case`.

### Switch sem expressão

```go
pontos := 85

switch {
case pontos >= 90:
    fmt.Println("A")
case pontos >= 80:
    fmt.Println("B")
case pontos >= 70:
    fmt.Println("C")
default:
    fmt.Println("Reprovado")
}
```

### Switch com fallthrough

A palavra-chave `fallthrough` permite "cair" para o próximo `case`:

```go
numero := 2

switch numero {
case 1:
    fmt.Println("Um")
    fallthrough
case 2:
    fmt.Println("Um ou Dois")
    fallthrough
case 3:
    fmt.Println("Um, Dois ou Três")
default:
    fmt.Println("Outro número")
}
// Output:
// Um ou Dois
// Um, Dois ou Três
```

??? warning "Cuidado com fallthrough"
    O `fallthrough` executa o código do próximo `case` automaticamente. Use com moderação.

## Loops For

Go possui apenas um tipo de loop: `for`. Mas ele é muito flexível e pode ser usado de várias formas.

### For tradicional

```go
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
// Output: 0, 1, 2, 3, 4
```

### For como While

```go
contador := 0

for contador < 3 {
    fmt.Println(contador)
    contador++
}
// Output: 0, 1, 2
```

### For infinito

```go
for {
    fmt.Println("Loop infinito")
    break // Para sair do loop
}
```

### For com range

O `range` itera sobre slices, arrays, maps e strings:

```go
numeros := []int{10, 20, 30}

for indice, valor := range numeros {
    fmt.Printf("Índice: %d, Valor: %d\n", indice, valor)
}

// Apenas valores
for _, valor := range numeros {
    fmt.Println(valor)
}

// Apenas índices
for indice := range numeros {
    fmt.Println(indice)
}
```

### For em Strings

```go
texto := "Go"

for indice, caractere := range texto {
    fmt.Printf("Índice: %d, Caractere: %c\n", indice, caractere)
}
```

| Iteração | Tipo          | Exemplo              |
|----------|---------------|----------------------|
| For tradicional | `for i := 0; i < n; i++` | Contagem controlada |
| For condicional | `for condição` | Substituir While |
| For infinito | `for` | Sem parada automática |
| For range | `for i, v := range dados` | Sobre coleções |

## Break e Continue

### Break

Sai imediatamente do loop:

```go
for i := 0; i < 10; i++ {
    if i == 5 {
        break
    }
    fmt.Println(i)
}
// Output: 0, 1, 2, 3, 4
```

### Continue

Pula para a próxima iteração:

```go
for i := 0; i < 5; i++ {
    if i == 2 {
        continue
    }
    fmt.Println(i)
}
// Output: 0, 1, 3, 4
```

### Break com label

Para sair de loops aninhados:

```go
externo:
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if i == 1 && j == 1 {
            break externo
        }
        fmt.Printf("(%d, %d) ", i, j)
    }
}
```

## Boas práticas

- Prefira `switch` quando tiver múltiplas condições sobre um mesmo valor.
- Use `if` para condições simples ou lógica complexa.
- Mantenha blocos `if` curtos e legíveis; considere retornar cedo em funções.
- Evite loops aninhados profundos; considere usar funções auxiliares.
- Use `range` em vez de índices manuais quando possível (mais idiomático em Go).
- Sempre inicialize variáveis antes de usá-las em condições.

## Exemplo prático completo

```go
package main

import "fmt"

func main() {
    notas := []int{75, 85, 92, 68, 88}

    for indice, nota := range notas {
        var conceito string

        switch {
        case nota >= 90:
            conceito = "A"
        case nota >= 80:
            conceito = "B"
        case nota >= 70:
            conceito = "C"
        default:
            conceito = "F"
        }

        fmt.Printf("Aluno %d: Nota %d - Conceito %s\n", indice+1, nota, conceito)
    }
}
```

## Teste no Go Playground

Use o Go Playground para executar os exemplos sem precisar instalar nada localmente.

```go
package main

import "fmt"

func main() {
    notas := []int{75, 85, 92, 68, 88}

    for indice, nota := range notas {
        var conceito string

        switch {
        case nota >= 90:
            conceito = "A"
        case nota >= 80:
            conceito = "B"
        case nota >= 70:
            conceito = "C"
        default:
            conceito = "F"
        }

        fmt.Printf("Aluno %d: Nota %d - Conceito %s\n", indice+1, nota, conceito)
    }
}
```

??? note "Abrir no Go Playground"
    Copie o código acima e cole em: [https://go.dev/play/](https://go.dev/play/)

> Dica: o Go Playground permite compartilhar o código gerando um link permanente depois de salvar.

## Veja também

- [Sintaxe Básica e Variáveis](sintaxe-basica-go.md)
- [Introdução e Instalação do Go](introducao-instalacao-go.md)
- [Como Funciona o Zensical](como-funciona.md)
