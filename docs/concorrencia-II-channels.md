---
icon: lucide/zap
---

# Concorrência II: Channels

Channels são a forma idiomatic em Go para comunicação entre goroutines. Eles permitem sincronização e troca segura de dados entre tarefas concorrentes.

## Tabela de Conteúdos

- [Visão Geral](#visao-geral)
- [Criando Channels](#criando-channels)
- [Enviando e Recebendo Dados](#enviando-e-recebendo-dados)
- [Channels Direcionados](#channels-direcionados)
- [Select Statement](#select-statement)
- [Buffered vs Unbuffered](#buffered-vs-unbuffered)
- [Padrões Comuns](#padroes-comuns)
- [Boas Práticas](#boas-praticas)

## Visão Geral

Channels são primitivas que permitem:

- ✅ Comunicação segura entre goroutines
- ✅ Sincronização sem usar locks ou mutexes
- ✅ Passagem de dados entre tarefas concorrentes
- ✅ Implementação de padrões de concorrência robustos

!!! info

    Go segue o lema: **"Não comunique compartilhando memória; compartilhe memória comunicando"**. Channels são a forma recomendada de fazer isso.

## Criando Channels

### Sintaxe Básica

``` go title="Criar um channel"
// Channel de inteiros
var ch chan int

// Channel de strings
var chStr chan string

// Channel de structs
var chData chan MyStruct

// Inicializar um channel (recomendado)
ch := make(chan int)
chStr := make(chan string)
```

### Tipos de Channels

| Tipo | Sintaxe | Propósito |
|------|---------|-----------|
| **Unbuffered** | `make(chan T)` | Síncrono - ambos os lados precisam estar prontos |
| **Buffered** | `make(chan T, n)` | Assíncrono - buffer de `n` elementos |

!!! warning

    Channels unbuffered exigem que tanto o sender quanto o receiver estejam prontos. Caso contrário, causarão deadlock!

## Enviando e Recebendo Dados

### Envio e Recepção Simples

``` go title="Enviar e receber dados em channel"
package main

import "fmt"

func main() {
    ch := make(chan string)
    
    // Enviar dados (bloqueante se receiver não estiver pronto)
    go func() {
        ch <- "Olá, Channels!"
    }()
    
    // Receber dados (bloqueante até dados chegarem)
    message := <-ch
    fmt.Println(message) // Output: Olá, Channels!
}
```

### Verificar se Channel está Aberto

``` go title="Verificar status de channel"
package main

import "fmt"

func main() {
    ch := make(chan int)
    
    // Receber com verificação
    value, ok := <-ch
    if !ok {
        fmt.Println("Channel está fechado")
    } else {
        fmt.Println("Valor recebido:", value)
    }
}
```

### Fechar Channels

``` go title="Fechar um channel"
package main

import "fmt"

func main() {
    ch := make(chan int, 1)
    ch <- 42
    close(ch) // Fechar channel
    
    // Tentar enviar em channel fechado causa panic
    // ch <- 43 // ❌ ERRO: send on closed channel
    
    // Mas receber funciona (retorna zero value)
    v, ok := <-ch
    fmt.Println(v, ok) // 42 true
    
    v2, ok2 := <-ch
    fmt.Println(v2, ok2) // 0 false
}
```

!!! error

    **Nunca envie em um channel fechado!** Isso causa panic. Apenas o sender deve fechar o channel.

## Channels Direcionados

Channels podem ser direcionados (send-only ou receive-only) para melhorar segurança:

### Send-Only e Receive-Only

``` go title="Channels direcionados"
package main

import "fmt"

func enviarDados(ch chan<- string) {
    ch <- "Dados enviados"
    // v := <-ch  // ❌ ERRO: cannot receive from send-only channel
}

func receberDados(ch <-chan string) {
    message := <-ch
    fmt.Println(message)
    // ch <- "Resposta"  // ❌ ERRO: cannot send to receive-only channel
}

func main() {
    ch := make(chan string)
    
    go enviarDados(ch)
    receberDados(ch)
}
```

### Conversão de Tipos

``` go title="Conversão de channels"
package main

func main() {
    // Bidirectional channel
    ch := make(chan int)
    
    // Pode ser passado como send-only
    sendData := (chan<- int)(ch)
    _ = sendData
    
    // Ou como receive-only
    getData := (<-chan int)(ch)
    _ = getData
    
    // O inverso NÃO é permitido
    // ch := (chan int)(receiveOnly) // ❌ ERRO
}
```

## Select Statement

`select` permite aguardar múltiplos channels:

### Sintaxe Básica

``` go title="Select básico"
package main

import (
    "fmt"
    "time"
)

func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)
    
    go func() {
        time.Sleep(1 * time.Second)
        ch1 <- "Um"
    }()
    
    go func() {
        time.Sleep(2 * time.Second)
        ch2 <- "Dois"
    }()
    
    // Aguardar o primeiro a ficar pronto
    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-ch1:
            fmt.Println("Recebido de ch1:", msg1)
        case msg2 := <-ch2:
            fmt.Println("Recebido de ch2:", msg2)
        }
    }
}
```

### Select com Default

``` go title="Select com default (non-blocking)"
package main

import "fmt"

func main() {
    ch := make(chan int)
    
    select {
    case v := <-ch:
        fmt.Println("Recebido:", v)
    default:
        fmt.Println("Nenhum dado disponível (non-blocking)")
    }
}
```

### Select com Timeout

``` go title="Select com timeout"
package main

import (
    "fmt"
    "time"
)

func main() {
    ch := make(chan string)
    
    select {
    case result := <-ch:
        fmt.Println("Resultado:", result)
    case <-time.After(2 * time.Second):
        fmt.Println("Timeout! Nenhuma resposta em 2 segundos")
    }
}
```

## Buffered vs Unbuffered

### Unbuffered (Síncrono)

``` go title="Channel unbuffered"
package main

import "fmt"

func main() {
    ch := make(chan int) // Sem buffer
    
    go func() {
        fmt.Println("Goroutine: iniciando envio")
        ch <- 42
        fmt.Println("Goroutine: envio completo")
    }()
    
    fmt.Println("Main: aguardando recepção")
    v := <-ch
    fmt.Println("Main: recebido:", v)
}

// Output:
// Main: aguardando recepção
// Goroutine: iniciando envio
// Goroutine: envio completo
// Main: recebido: 42
```

### Buffered (Assíncrono)

``` go title="Channel buffered"
package main

import "fmt"

func main() {
    ch := make(chan int, 2) // Buffer de 2 elementos
    
    // Enviar sem bloquear (buffer tem espaço)
    ch <- 1
    ch <- 2
    fmt.Println("Dois valores enviados")
    
    // Receber
    fmt.Println(<-ch) // 1
    fmt.Println(<-ch) // 2
}
```

### Comparação

| Aspecto | Unbuffered | Buffered |
|---------|-----------|----------|
| **Sincronização** | Síncrona | Assíncrona |
| **Capacidade** | 0 | n elementos |
| **Envio Bloqueante** | Sim, até receiver estar pronto | Não, enquanto buffer não está cheio |
| **Recebimento Bloqueante** | Sim, até sender enviar | Não, enquanto buffer não está vazio |
| **Uso** | Sincronizar goroutines | Desacoplar tasks |

## Padrões Comuns

### Worker Pool

``` go title="Padrão Worker Pool"
package main

import (
    "fmt"
    "sync"
)

func worker(id int, jobs <-chan int, results chan<- int) {
    for job := range jobs {
        fmt.Printf("Worker %d processando job %d\n", id, job)
        results <- job * 2
    }
}

func main() {
    jobs := make(chan int, 100)
    results := make(chan int, 100)
    
    // Criar 3 workers
    for i := 1; i <= 3; i++ {
        go worker(i, jobs, results)
    }
    
    // Enviar 9 jobs
    for j := 1; j <= 9; j++ {
        jobs <- j
    }
    close(jobs)
    
    // Coletar resultados
    for j := 1; j <= 9; j++ {
        fmt.Println(<-results)
    }
}
```

### Generator Pattern

``` go title="Padrão Generator"
package main

import (
    "fmt"
)

func counter(max int) <-chan int {
    ch := make(chan int)
    go func() {
        for i := 1; i <= max; i++ {
            ch <- i
        }
        close(ch)
    }()
    return ch
}

func main() {
    for num := range counter(5) {
        fmt.Println(num)
    }
}

// Output: 1 2 3 4 5
```

### Fan-Out/Fan-In

``` go title="Padrão Fan-Out/Fan-In"
package main

import (
    "fmt"
)

// Fan-out: distribuir trabalho
func distribute(in <-chan int, out1, out2 chan<- int) {
    for {
        value := <-in
        select {
        case out1 <- value:
        case out2 <- value:
        }
    }
}

// Fan-in: coletar resultados
func merge(ch1, ch2 <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for {
            select {
            case v := <-ch1:
                out <- v
            case v := <-ch2:
                out <- v
            }
        }
    }()
    return out
}

func main() {
    in := make(chan int)
    out1, out2 := make(chan int), make(chan int)
    
    go distribute(in, out1, out2)
    result := merge(out1, out2)
    
    go func() {
        for i := 1; i <= 5; i++ {
            in <- i
        }
    }()
    
    for i := 0; i < 5; i++ {
        fmt.Println(<-result)
    }
}
```

### Timeout com Context

``` go title="Timeout com context"
package main

import (
    "context"
    "fmt"
    "time"
)

func operation(ctx context.Context) <-chan int {
    ch := make(chan int)
    go func() {
        select {
        case <-time.After(2 * time.Second):
            ch <- 42
        case <-ctx.Done():
            fmt.Println("Operação cancelada")
        }
    }()
    return ch
}

func main() {
    ctx, cancel := context.WithTimeout(
        context.Background(),
        1*time.Second,
    )
    defer cancel()
    
    select {
    case result := <-operation(ctx):
        fmt.Println("Resultado:", result)
    case <-ctx.Done():
        fmt.Println("Timeout!")
    }
}
```

## Boas Práticas

### ✅ Faça

!!! success

    **Feche channels apenas do lado do sender** - Receivers não devem fechar
    
    **Use canais para sincronização** - Evite locks quando possível
    
    **Prefira `range` para ler channels** - `for value := range ch {}`
    
    **Use select com timeout** - Evite deadlocks em operações bloqueantes
    
    **Documente a ownership do channel** - Deixe claro quem envia/recebe

### ❌ Não Faça

!!! error

    **Não envie em channel fechado** - Causa panic imediato
    
    **Não feche de múltiplos goroutines** - Apenas um sender deve fechar
    
    **Não ignore erros de channel** - Sempre verifique `ok` em recebimentos
    
    **Não use channels para tudo** - Às vezes, um mutex é mais apropriado
    
    **Não compartilhe dados mutáveis** - Channels devem passar valores, não referências compartilhadas

### Checklist de Segurança

``` go title="Checklist - Padrão Seguro"
package main

import "sync"

func safeConcurrentPattern() {
    // 1. Usar WaitGroup para sincronização
    var wg sync.WaitGroup
    
    // 2. Channel com buffer adequado
    results := make(chan int, 10)
    
    // 3. Adicionar goroutines ao WaitGroup
    wg.Add(1)
    go func() {
        defer wg.Done()
        results <- 42
    }()
    
    // 4. Fechar channel quando tudo terminar
    go func() {
        wg.Wait()
        close(results)
    }()
    
    // 5. Consumir todos os dados
    for v := range results {
        _ = v
    }
}
```

## Exemplo Completo: Pipeline

``` go title="Exemplo: Pipeline de Processamento"
package main

import (
    "fmt"
)

// Stage 1: Gerar números
func generate(max int) <-chan int {
    out := make(chan int)
    go func() {
        for i := 1; i <= max; i++ {
            out <- i
        }
        close(out)
    }()
    return out
}

// Stage 2: Elevar ao quadrado
func square(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for num := range in {
            out <- num * num
        }
        close(out)
    }()
    return out
}

// Stage 3: Dobrar valor
func double(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for num := range in {
            out <- num * 2
        }
        close(out)
    }()
    return out
}

func main() {
    // Pipeline: Gerar -> Quadrado -> Dobrar
    for result := range double(square(generate(5))) {
        fmt.Println(result)
    }
    // Output: 2, 8, 18, 32, 50
}
```

## Recursos Adicionais

- [Effective Go - Channels](https://go.dev/doc/effective_go#channels)
- [Go Concurrency Patterns](https://go.dev/blog/pipelines)
- [Context Package](https://pkg.go.dev/context)
- [Mutex vs Channels](https://go.dev/blog/context)

---

**Última atualização:** 2026

Para mais informações sobre Go, consulte os outros capítulos desta documentação.
