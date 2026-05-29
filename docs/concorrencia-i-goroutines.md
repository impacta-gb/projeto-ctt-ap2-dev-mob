# Concorrência I: Goroutines

## Índice
- [O que é uma Goroutine?](#o-que-e-uma-goroutine)
- [Criando Goroutines](#criando-goroutines)
- [Sincronização Básica](#sincronizacao-basica)
- [WaitGroup](#waitgroup)
- [Boas Práticas](#boas-praticas)
- [Exemplos Práticos](#exemplos-praticos)

## O que é uma Goroutine?

Uma **goroutine** é uma função ou método que é executada de forma concorrente com outras goroutines. Ela é gerenciada pela runtime do Go e é muito mais leve do que uma thread do sistema operacional.

| Aspecto | Goroutine | Thread |
|--------|-----------|--------|
| **Tamanho em Memória** | ~2 KB | ~1-2 MB |
| **Tempo de Criação** | Microsegundos | Milissegundos |
| **Quantidade Viável** | Milhões | Centenas/Milhares |
| **Gerenciamento** | Runtime do Go | Sistema Operacional |
| **Custo de Troca** | Muito baixo | Alto |

> **ℹ️ Informação:** Go é projetado para trabalhar com concorrência de forma eficiente, permitindo que você crie milhares de goroutines sem problemas.

## Criando Goroutines

### Sintaxe Básica

Para criar uma goroutine, basta usar a palavra-chave `go` seguida de uma chamada de função:

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    // Função tradicional
    go minhafuncao()
    
    // Função anônima
    go func() {
        fmt.Println("Executando de forma concorrente!")
    }()
    
    // Aguarda um pouco para a goroutine terminar
    time.Sleep(1 * time.Second)
}

func minhafuncao() {
    fmt.Println("Olá de uma goroutine!")
}
```

### Exemplo com Loop

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    for i := 1; i <= 5; i++ {
        go func(numero int) {
            fmt.Printf("Goroutine %d iniciada\n", numero)
            time.Sleep(1 * time.Second)
            fmt.Printf("Goroutine %d finalizada\n", numero)
        }(i)
    }
    
    time.Sleep(3 * time.Second)
}
```

> ⚠️ **Aviso:** Note que passamos `i` como parâmetro `numero`. Isso é importante para evitar que todas as goroutines utilizem o mesmo valor de `i` (que muda durante o loop).

## Sincronização Básica

### Sleep para Sincronização (NÃO RECOMENDADO)

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    go func() {
        fmt.Println("Tarefa 1")
    }()
    
    time.Sleep(1 * time.Second) // Aguarda a goroutine
}
```

> ❌ **Não use `time.Sleep()` para sincronização em produção!** Essa é apenas uma forma didática de ver goroutines funcionando.

## WaitGroup

A forma **correta** e **recomendada** de sincronizar goroutines é usando `sync.WaitGroup`:

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var wg sync.WaitGroup
    
    // Adicionamos 3 goroutines ao WaitGroup
    wg.Add(3)
    
    for i := 1; i <= 3; i++ {
        go func(numero int) {
            defer wg.Done() // Marca como concluída
            fmt.Printf("Goroutine %d executando\n", numero)
        }(i)
    }
    
    // Aguarda todas as goroutines terminarem
    wg.Wait()
    fmt.Println("Todas as goroutines terminaram!")
}
```

### Como Funciona o WaitGroup

| Método | Descrição |
|--------|-----------|
| `Add(n)` | Adiciona `n` goroutines ao grupo |
| `Done()` | Decrementa o contador (goroutine terminou) |
| `Wait()` | Bloqueia até todas as goroutines terminarem |

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

func processarDados(id int, wg *sync.WaitGroup) {
    defer wg.Done()
    
    fmt.Printf("Processando %d...\n", id)
    time.Sleep(2 * time.Second)
    fmt.Printf("Processado %d\n", id)
}

func main() {
    var wg sync.WaitGroup
    
    for i := 1; i <= 5; i++ {
        wg.Add(1)
        go processarDados(i, &wg)
    }
    
    wg.Wait()
    fmt.Println("Todos os dados foram processados!")
}
```

## Boas Práticas

| Prática | Descrição |
|---------|-----------|
| ✅ **Use WaitGroup** | Para sincronização segura de goroutines |
| ✅ **Passe valores** | Como parâmetros, não acesse variáveis do loop |
| ✅ **Use Channels** | Para comunicação entre goroutines |
| ❌ **Evite sleep()** | Para sincronização em produção |
| ❌ **Global races** | Não compartilhe variáveis sem sincronização |

> **🔑 Dica:** Sempre use `defer wg.Done()` dentro da goroutine para garantir que o contador seja decrementado mesmo se houver um erro.

## Exemplos Práticos

### Exemplo 1: Download Paralelo

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

func baixarArquivo(url string, wg *sync.WaitGroup) {
    defer wg.Done()
    fmt.Printf("Iniciando download: %s\n", url)
    time.Sleep(2 * time.Second)
    fmt.Printf("Download concluído: %s\n", url)
}

func main() {
    var wg sync.WaitGroup
    urls := []string{"arquivo1.zip", "arquivo2.zip", "arquivo3.zip"}
    
    for _, url := range urls {
        wg.Add(1)
        go baixarArquivo(url, &wg)
    }
    
    wg.Wait()
    fmt.Println("Todos os arquivos foram baixados!")
}
```

### Exemplo 2: Processamento de Tarefas

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

func processarTarefa(id int, duracao time.Duration, wg *sync.WaitGroup) {
    defer wg.Done()
    
    fmt.Printf("[Tarefa %d] Iniciando...\n", id)
    time.Sleep(duracao)
    fmt.Printf("[Tarefa %d] Concluída em %v\n", id, duracao)
}

func main() {
    var wg sync.WaitGroup
    
    tarefas := map[int]time.Duration{
        1: 1 * time.Second,
        2: 2 * time.Second,
        3: 3 * time.Second,
    }
    
    for id, duracao := range tarefas {
        wg.Add(1)
        go processarTarefa(id, duracao, &wg)
    }
    
    wg.Wait()
    fmt.Println("Todas as tarefas foram processadas!")
}
```

---

**Próximos Tópicos:** [Concorrência II: Channels](#)
