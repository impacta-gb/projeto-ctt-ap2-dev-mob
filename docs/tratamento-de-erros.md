---
icon: lucide/shield-check
tags:
  - erros
  - go
  - fundamentals
---

# Tratamento de Erros em Go

## Navegação rápida
- [Por que tratar erros](#por-que-tratar-erros)
- [Erro como valor](#erro-como-valor)
- [Verificação de erros](#verificacao-de-erros)
- [Criação de erros](#criacao-de-erros)
- [Encadeamento e wrapping](#encadeamento-e-wrapping)
- [Sentinela, tipos e inspeção](#sentinela-tipos-e-inspecao)
- [Pânico e recover](#panico-e-recover)
- [Boas práticas](#boas-praticas)

## Por que tratar erros

Em Go, o tratamento de erros é explícito. Erros fazem parte do fluxo normal de execução e devem ser verificados sempre que funções retornam um valor do tipo `error`.

```go
func lerArquivo(nome string) ([]byte, error) {
    dados, err := os.ReadFile(nome)
    if err != nil {
        return nil, err
    }
    return dados, nil
}
```

??? note "Erros não são exceções"
    Go usa valores de erro em vez de exceções. O retorno explícito de `error` ajuda a tornar o fluxo de controle mais previsível.

## Erro como valor

O tipo `error` é uma interface embutida em Go:

```go
type error interface {
    Error() string
}
```

Isso significa que qualquer tipo que implemente o método `Error() string` é um erro válido.

### Função `errors.New`

```go
import "errors"

var ErrNaoEncontrado = errors.New("registro não encontrado")
```

### `fmt.Errorf` com formatação

```go
import "fmt"

func validar(id int) error {
    if id <= 0 {
        return fmt.Errorf("id inválido: %d", id)
    }
    return nil
}
```

## Verificação de erros

A verificação de erros deve ser feita imediatamente após a chamada de função.

```go
dados, err := lerArquivo("config.json")
if err != nil {
    log.Fatalf("falha ao ler arquivo: %v", err)
}
```

### Quando retornar o erro

Em geral, uma função deve retornar o erro para o chamador, a menos que ela possa tratar o erro de forma completa.

```go
func carregarConfiguracao(caminho string) (*Config, error) {
    dados, err := os.ReadFile(caminho)
    if err != nil {
        return nil, err
    }
    return parseConfig(dados)
}
```

## Criação de erros

| Método | Descrição | Exemplo |
|--------|-----------|---------|
| `errors.New` | Cria um erro simples | `errors.New("erro genérico")` |
| `fmt.Errorf` | Cria um erro formatado | `fmt.Errorf("falha: %w", err)` |
| `os.ErrNotExist` | Erro pré-definido do pacote `os` | `errors.Is(err, os.ErrNotExist)` |
| `error` customizado | Permite adicionar contexto e campos | `type MeuErro struct { ... }` |

```go
import "errors"

func dividir(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("divisão por zero")
    }
    return a / b, nil
}
```

!!! warning "Evite mensagens de erro genéricas"
    Mensagens como `"erro interno"` ou `"falha"` dificultam a depuração. Inclua contexto útil.

## Encadeamento e wrapping

A partir do Go 1.13, o operador `%w` em `fmt.Errorf` permite envolver erros anteriores.

```go
import "fmt"

func carregar(caminho string) error {
    _, err := os.ReadFile(caminho)
    if err != nil {
        return fmt.Errorf("falha ao carregar %s: %w", caminho, err)
    }
    return nil
}
```

### Inspeção com `errors.Is` e `errors.As`

- `errors.Is` verifica se um erro corresponde a um erro específico.
- `errors.As` extrai um erro de um tipo específico.

```go
import (
    "errors"
    "fmt"
)

if errors.Is(err, os.ErrNotExist) {
    fmt.Println("arquivo não existe")
}

var pathErr *os.PathError
if errors.As(err, &pathErr) {
    fmt.Println("caminho falhou:", pathErr.Path)
}
```

## Sentinela, tipos e inspeção

### Erros sentinela

Erros sentinela são valores de erro definidos globalmente.

```go
var ErrUsuarioInvalido = errors.New("usuário inválido")
```

Use `errors.Is` para compará-los:

```go
if errors.Is(err, ErrUsuarioInvalido) {
    // tratamento específico
}
```

### Erros customizados

```go
type ValidationError struct {
    Field   string
    Message string
}

func (e ValidationError) Error() string {
    return fmt.Sprintf("%s: %s", e.Field, e.Message)
}
```

```go
var err error = ValidationError{Field: "email", Message: "inválido"}
if ve, ok := err.(ValidationError); ok {
    fmt.Println("campo com erro:", ve.Field)
}
```

## Pânico e recover

`panic` é usado para erros irreversíveis ou condições inesperadas. Ele não substitui o tratamento normal de erros.

```go
func main() {
    panic("erro crítico")
}
```

### Quando usar `panic`

- inicialização que falha e não pode continuar
- invariantes violadas internamente
- erros de programação graves

### Recover em `defer`

```go
func safeExecute() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("recuperado do pânico:", r)
        }
    }()

    panic("algo deu errado")
}
```

!!! note "Use `panic` com cuidado"
    `panic` deve ser usado apenas em casos excepcionais. Para erros esperados, prefira retornar `error`.

## Boas práticas

- Sempre verifique erros imediatamente.
- Retorne erros com contexto em vez de apenas repassá-los.
- Prefira mensagens de erro claras e legíveis.
- Use `errors.Is` e `errors.As` para inspeção segura.
- Evite `panic` para fluxo normal de controle.
- Não ignore erros com `if err != nil {}` vazios.

### Comparação rápida

| Situação | Usar | Não usar |
|----------|------|----------|
| Falha esperada de E/S | `error` retornado | `panic` |
| Validação de entrada | `error` retornado | `panic` |
| Erro de sistema crítico | `panic` | ignorar o erro |
| Propagar contexto | `fmt.Errorf("...: %w", err)` | `fmt.Errorf("...: %v", err)` |

## Veja também

- [Sintaxe Básica e Variáveis em Go](sintaxe-basica-go.md)
- [Structs e Métodos em Go](structs-metodos-go.md)
