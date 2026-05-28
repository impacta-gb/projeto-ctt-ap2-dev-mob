---
icon: lucide/test-tubes
---

# Testes Automatizados em Go

Testes automatizados são essenciais para garantir a qualidade do código. Go possui um framework de testes integrado na linguagem, tornando fácil escrever e executar testes.

## Tabela de Conteúdos

- [Visão Geral](#visão-geral)
- [Configuração Básica](#configuração-básica)
- [Escrevendo Testes Unitários](#escrevendo-testes-unitários)
- [Tabelas de Testes](#tabelas-de-testes)
- [Subtestes](#subtestes)
- [Testes de Benchmark](#testes-de-benchmark)
- [Cobertura de Testes](#cobertura-de-testes)
- [Boas Práticas](#boas-práticas)

## Visão Geral

O pacote `testing` padrão de Go fornece:

- ✅ Framework de testes integrado
- ✅ Suporte a testes unitários e de benchmark
- ✅ Análise de cobertura de código
- ✅ Subtestes e testes em paralelo
- ✅ Ferramentas de linha de comando simples

!!! info

    Go trata testes como um recurso de primeira classe da linguagem. Não é necessário instalar frameworks externos para começar com testes.

## Configuração Básica

### Estrutura de Arquivo

Testes em Go devem ser colocados em arquivos com sufixo `_test.go`:

``` text title="Estrutura de projeto com testes"
projeto/
├── main.go
├── main_test.go          # Testes para main.go
├── utils/
│   ├── utils.go
│   └── utils_test.go     # Testes para utils.go
└── handler/
    ├── handler.go
    └── handler_test.go   # Testes para handler.go
```

### Importar o Pacote testing

``` go title="Estrutura básica de um arquivo de teste"
package main

import (
    "testing"
)

// Seu teste aqui
```

!!! warning

    Testes devem estar no **mesmo pacote** do código que estão testando. Use `package main` se o arquivo principal é `main.go`.

## Escrevendo Testes Unitários

### Teste Simples

A função de teste deve:
- Começar com `Test` (maiúscula)
- Receber um ponteiro `*testing.T` como argumento
- Ter assinatura: `func TestNome(t *testing.T)`

``` go title="Exemplo de teste unitário"
package main

import "testing"

func Add(a, b int) int {
    return a + b
}

func TestAdd(t *testing.T) {
    result := Add(2, 3)
    expected := 5
    
    if result != expected {
        t.Errorf("Add(2, 3) = %d; expected %d", result, expected)
    }
}
```

### Métodos Úteis de testing.T

| Método | Propósito | Continua Teste? |
|--------|-----------|-----------------|
| `t.Error()` | Marca falha e exibe mensagem | ✅ Sim |
| `t.Errorf()` | Marca falha com formato | ✅ Sim |
| `t.Fatal()` | Para o teste imediatamente | ❌ Não |
| `t.Fatalf()` | Para o teste com formato | ❌ Não |
| `t.Log()` | Registra mensagem | ✅ Sim |
| `t.Logf()` | Registra mensagem com formato | ✅ Sim |
| `t.Skip()` | Pula o teste | - |
| `t.SkipNow()` | Pula o teste imediatamente | - |

## Tabelas de Testes

Tabelas de testes permitem testar múltiplos casos com uma estrutura limpa:

``` go title="Teste com tabela de casos"
package main

import "testing"

func TestAddMultiplesCases(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"Positivos", 2, 3, 5},
        {"Negativos", -2, -3, -5},
        {"Zero", 0, 5, 5},
        {"Misto", -2, 3, 1},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; expected %d",
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

!!! success

    Tabelas de testes são a forma padrão em Go para testar múltiplos casos. Cada caso fica bem documentado e é fácil adicionar novos.

## Subtestes

Subtestes permitem organizar testes com estrutura hierárquica:

``` go title="Usando subtestes explícitos"
package main

import "testing"

func TestDivisao(t *testing.T) {
    t.Run("Números positivos", func(t *testing.T) {
        result := Divide(10, 2)
        if result != 5 {
            t.Errorf("Divide(10, 2) = %v; expected 5", result)
        }
    })

    t.Run("Divisão por zero", func(t *testing.T) {
        result, err := DivideWithError(10, 0)
        if err == nil {
            t.Error("Expected error for division by zero")
        }
    })

    t.Run("Números negativos", func(t *testing.T) {
        result := Divide(-10, 2)
        if result != -5 {
            t.Errorf("Divide(-10, 2) = %v; expected -5", result)
        }
    })
}
```

### Executar Subtestes Específicos

``` bash title="Executar subtestes"
# Executar todos os subtestes de TestDivisao
go test -run TestDivisao -v

# Executar apenas "Números positivos"
go test -run TestDivisao/Números -v

# Executar em paralelo
go test -run TestDivisao -parallel 4 -v
```

## Testes de Benchmark

Benchmarks medem a performance do código:

``` go title="Teste de benchmark"
package main

import "testing"

func BenchmarkAdd(b *testing.B) {
    // b.N é o número de vezes que a operação será executada
    for i := 0; i < b.N; i++ {
        Add(2, 3)
    }
}

func BenchmarkAddComplex(b *testing.B) {
    // Pode fazer setup antes
    numbers := []int{1, 2, 3, 4, 5}
    
    b.ResetTimer() // Inicia o cronômetro após setup
    
    for i := 0; i < b.N; i++ {
        sum := 0
        for _, n := range numbers {
            sum += n
        }
    }
}
```

### Executar Benchmarks

``` bash title="Executar benchmarks"
# Executar todos os benchmarks
go test -bench=. -v

# Executar benchmark específico
go test -bench=BenchmarkAdd -v

# Executar com mais iterações
go test -bench=. -benchtime=10s

# Comparar com versão anterior
go test -bench=. -benchmem
```

### Compreender Resultado de Benchmark

``` text title="Saída típica de benchmark"
BenchmarkAdd-8          1000000000  1.234 ns/op
│              │        │           │
│              │        │           └─ Tempo por operação
│              │        └─ Número de iterações
│              └─ Número de CPUs usados
└─ Nome do benchmark
```

## Cobertura de Testes

Verifique quantos % do seu código está sendo testado:

``` bash title="Gerar relatório de cobertura"
# Gerar arquivo de cobertura
go test -cover

# Detalhado por função
go test -cover -v

# Gerar arquivo HTML
go test -coverprofile=coverage.out
go tool cover -html=coverage.out
```

### Entender Métricas de Cobertura

| Cobertura | Significado |
|-----------|-------------|
| **< 50%** | ⚠️ Insuficiente - Muita código sem teste |
| **50-70%** | ⚠️ Razoável - Cobrir casos críticos |
| **70-85%** | ✅ Boa - Cobertura satisfatória |
| **85-95%** | ✅ Excelente - Quase tudo testado |
| **> 95%** | 🎯 Ideal - Cobertura quase total |

!!! note

    100% de cobertura não garante qualidade. Foque em testar casos importantes e situações de erro.

## Comandos Úteis

### Executar Testes

``` bash title="Comandos de teste"
# Executar todos os testes
go test ./...

# Testes verbose (detalhado)
go test -v ./...

# Testes específicos
go test -run TestAdd

# Testar em paralelo
go test -parallel 8 ./...

# Parar no primeiro erro
go test -failfast ./...

# Listar testes sem executar
go test -list . -v
```

### Salvar Output

``` bash title="Salvar resultados"
# Salvar em arquivo
go test ./... > test_results.txt

# Salvar com formato JSON
go test -json ./... > test_results.json
```

## Boas Práticas

### ✅ Faça

!!! success

    **Nomeie testes claramente** - `TestUserCreation`, `TestInvalidEmail` são melhores que `TestUser1`, `TestUser2`

    **Use tabelas de testes** - Organize múltiplos casos em estruturas bem definidas

    **Teste casos extremos** - Valores nulos, zeros, strings vazias, limites

    **Teste erros** - Verifique se o código falha quando deve falhar

    **Execute testes frequentemente** - Use `go test` antes de cada commit

    **Organize em subtestes** - Agrupe testes relacionados com `t.Run()`

### ❌ Não Faça

!!! error

    **Não dependa de ordem de testes** - Testes devem ser independentes e isolados

    **Não ignore erros** - Sempre verifique o resultado de operações que podem falhar

    **Não use testes para setup de dados** - Prefira fixtures ou helpers dedicados

    **Não escreva testes muito complexos** - Testes devem ser simples e fáceis de entender

    **Não deixe teste comentado** - Delete testes obsoletos ou use `t.Skip()`

### Estrutura Recomendada

``` go title="Estrutura completa de teste"
package main

import (
    "fmt"
    "testing"
)

// Função auxiliar para testes
func setup() {
    // Preparar dados para testes
}

func teardown() {
    // Limpar dados após testes
}

// Teste simples
func TestSimple(t *testing.T) {
    setup()
    defer teardown()
    
    result := FuncaoParaTestar()
    if result != expected {
        t.Errorf("Expected %v, got %v", expected, result)
    }
}

// Teste com tabela
func TestMultiplesCases(t *testing.T) {
    tests := []struct {
        name    string
        input   string
        want    string
    }{
        {"Case 1", "input1", "output1"},
        {"Case 2", "input2", "output2"},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := FuncaoParaTestar(tt.input)
            if got != tt.want {
                t.Errorf("got %q, want %q", got, tt.want)
            }
        })
    }
}

// Benchmark
func BenchmarkFuncao(b *testing.B) {
    for i := 0; i < b.N; i++ {
        FuncaoParaTestar()
    }
}
```

## Exemplo Completo

``` go title="Exemplo prático completo"
package calculator

import "testing"

func Multiply(a, b int) int {
    return a * b
}

func TestMultiply(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"Positivos", 3, 4, 12},
        {"Com zero", 5, 0, 0},
        {"Negativos", -2, -3, 6},
        {"Um negativo", -2, 3, -6},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Multiply(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Multiply(%d, %d) = %d; expected %d",
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}

func BenchmarkMultiply(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Multiply(123, 456)
    }
}
```

## Recursos Adicionais

- [Testing Package Documentation](https://pkg.go.dev/testing)
- [Go Testing Best Practices](https://go.dev/doc/effective_go#testing)
- [Table Driven Tests](https://golang.org/wiki/TableDrivenTests)
- [Benchmarking Guide](https://go.dev/blog/pprof)

---

**Última atualização:** 2026

Para mais informações sobre Go, consulte os outros capítulos desta documentação.
