# Arrays, Slices e Maps em Go

## Índice de Navegação

- [Arrays](#arrays)
  - [Declaração e Inicialização](#declaração-e-inicialização)
  - [Operações com Arrays](#operações-com-arrays)
  - [Limitações](#limitações)
- [Slices](#slices)
  - [O que é um Slice?](#o-que-é-um-slice)
  - [Criando Slices](#criando-slices)
  - [Operações com Slices](#operações-com-slices)
  - [Slice Internals](#slice-internals)
- [Maps](#maps)
  - [Declaração e Inicialização](#declaração-e-inicialização-1)
  - [Operações com Maps](#operações-com-maps)
  - [Iteração em Maps](#iteração-em-maps)
- [Comparação](#comparação)
- [Boas Práticas](#boas-práticas)

---

## Arrays

### Declaração e Inicialização

Arrays em Go são coleções de elementos do mesmo tipo com **tamanho fixo**. Uma vez declarado, o tamanho não pode ser alterado.

```go
// Declaração com tamanho especificado
var arr [5]int
var nomes [3]string

// Inicialização com valores
var numeros = [5]int{1, 2, 3, 4, 5}
cores := [4]string{"vermelho", "verde", "azul", "amarelo"}

// Tamanho inferido do array
dias := [...]string{"segunda", "terça", "quarta", "quinta", "sexta"}
// O compilador infere que dias tem 5 elementos
```

> **ℹ️ Informação:** Arrays com tamanho diferente são considerados tipos diferentes em Go. Um `[3]int` é diferente de um `[4]int`.

### Operações com Arrays

```go
package main

import "fmt"

func main() {
    // Acessando elementos
    arr := [5]int{10, 20, 30, 40, 50}
    
    fmt.Println(arr[0])  // 10
    fmt.Println(arr[2])  // 30
    
    // Modificando elementos
    arr[1] = 99
    fmt.Println(arr[1])  // 99
    
    // Iteração com for
    for i := 0; i < len(arr); i++ {
        fmt.Println(i, arr[i])
    }
    
    // Iteração com range
    for indice, valor := range arr {
        fmt.Printf("Índice: %d, Valor: %v\n", indice, valor)
    }
    
    // Comprimento do array
    fmt.Println(len(arr))  // 5
}
```

### Limitações

⚠️ **Aviso:** Arrays em Go têm tamanho fixo e não podem ser redimensionados. Se você precisa de uma coleção dinâmica, use **Slices**.

| Característica | Array | Descrição |
|---|---|---|
| **Tamanho** | Fixo | Definido na declaração |
| **Mutável** | Sim | Pode alterar elementos |
| **Comparação** | Sim | Arrays do mesmo tipo podem ser comparados |
| **Cópia** | Profunda | Copia todos os elementos |

---

## Slices

### O que é um Slice?

Slices são coleções **dinâmicas** que encapsulam arrays. Eles são mais flexíveis que arrays e são a forma preferida de trabalhar com coleções em Go.

Um slice é composto por três componentes:
- **Pointer**: Aponta para o primeiro elemento do array subjacente
- **Length (len)**: Número de elementos atuais
- **Capacity (cap)**: Número máximo de elementos que o slice pode conter

```go
// Representação interna de um slice
type SliceHeader struct {
    Data uintptr
    Len  int
    Cap  int
}
```

### Criando Slices

```go
package main

import "fmt"

func main() {
    // Slice a partir de um array
    arr := [5]int{1, 2, 3, 4, 5}
    slice1 := arr[1:4]  // Elementos do índice 1 até 3
    fmt.Println(slice1) // [2 3 4]
    
    // Slice com valores iniciais usando make
    slice2 := make([]int, 5)           // len=5, cap=5
    slice3 := make([]int, 5, 10)       // len=5, cap=10
    
    // Slice com inicialização de valores
    slice4 := []string{"Go", "Python", "Rust"}
    
    // Slice vazio
    var slice5 []int
    
    // Slice literário
    slice6 := []int{10, 20, 30, 40}
}
```

### Operações com Slices

```go
package main

import "fmt"

func main() {
    // Criar slice
    numeros := []int{1, 2, 3, 4, 5}
    
    // append() - adiciona elementos
    numeros = append(numeros, 6, 7)
    fmt.Println(numeros)  // [1 2 3 4 5 6 7]
    
    // append() com spread operator
    maisNumeros := []int{8, 9, 10}
    numeros = append(numeros, maisNumeros...)
    fmt.Println(numeros)  // [1 2 3 4 5 6 7 8 9 10]
    
    // Verificar len e cap
    fmt.Printf("len=%d, cap=%d\n", len(numeros), cap(numeros))
    
    // Slicing um slice
    sub := numeros[2:5]
    fmt.Println(sub)  // [3 4 5]
    
    // Modificar elementos
    numeros[0] = 100
    fmt.Println(numeros)  // [100 2 3 4 5 6 7 8 9 10]
}
```

> **ℹ️ Informação:** Quando você faz append() em um slice que atingiu sua capacidade máxima, Go aloca um novo array subjacente com o dobro da capacidade.

### Slice Internals

Entender como os slices funcionam internamente é importante:

```go
package main

import "fmt"

func main() {
    arr := [10]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
    
    slice1 := arr[2:5]  // [2, 3, 4]
    slice2 := arr[2:7]  // [2, 3, 4, 5, 6]
    
    // Modificando slice1 afeta o array e slice2
    slice1[0] = 99
    fmt.Println(arr)    // [0 1 99 3 4 5 6 7 8 9]
    fmt.Println(slice2) // [99 3 4 5 6]
    
    // Evitar alterações inesperadas usando copy
    slice3 := make([]int, len(slice1))
    copy(slice3, slice1)
    
    slice3[0] = 10  // Não afeta slice1 ou o array original
    fmt.Println(slice1) // [99 3 4]
    fmt.Println(slice3) // [10 3 4]
}
```

⚠️ **Aviso:** Múltiplos slices podem compartilhar o mesmo array subjacente. Modificações em um slice podem afetar outro se compartilham dados. Use `copy()` para evitar comportamentos inesperados.

| Operação | Descrição | Exemplo |
|---|---|---|
| `append(slice, elem)` | Adiciona elemento ao slice | `slice = append(slice, 10)` |
| `len(slice)` | Retorna comprimento | `len(slice)` |
| `cap(slice)` | Retorna capacidade | `cap(slice)` |
| `copy(dst, src)` | Copia elementos | `copy(dest, source)` |
| `slice[i:j]` | Extrai sub-slice | `slice[1:4]` |

---

## Maps

### Declaração e Inicialização

Maps são coleções de pares chave-valor. São tabelas hash não ordenadas e dinâmicas.

```go
package main

import "fmt"

func main() {
    // Declaração e inicialização com make
    mapa1 := make(map[string]int)
    mapa1["alice"] = 28
    mapa1["bob"] = 35
    mapa1["carlos"] = 42
    
    // Inicialização com valores
    mapa2 := map[string]string{
        "pt": "Português",
        "en": "English",
        "es": "Español",
    }
    
    // Map vazio
    var mapa3 map[string]bool
    
    // Acessando valores
    fmt.Println(mapa2["pt"])     // Português
    fmt.Println(mapa2["fr"])     // "" (chave não existe)
    
    // Verificar se chave existe
    valor, existe := mapa2["fr"]
    fmt.Println(valor, existe)   // "" false
}
```

### Operações com Maps

```go
package main

import "fmt"

func main() {
    pessoa := map[string]interface{}{
        "nome":  "João",
        "idade": 30,
        "ativo": true,
    }
    
    // Adicionar/Modificar
    pessoa["email"] = "joao@example.com"
    pessoa["idade"] = 31
    
    // Deletar chave
    delete(pessoa, "ativo")
    
    // Verificar existência
    if valor, existe := pessoa["nome"]; existe {
        fmt.Printf("Nome: %v\n", valor)  // Nome: João
    }
    
    // Tamanho do map
    fmt.Println(len(pessoa))  // 3 (após deletar "ativo")
}
```

### Iteração em Maps

```go
package main

import "fmt"

func main() {
    idiomas := map[string]string{
        "Go":       "golang",
        "Python":   "python",
        "Rust":     "rust",
        "JavaScript": "js",
    }
    
    // Iteração - ordem não garantida
    for chave, valor := range idiomas {
        fmt.Printf("%s -> %s\n", chave, valor)
    }
    
    // Apenas chaves
    for chave := range idiomas {
        fmt.Println("Linguagem:", chave)
    }
    
    // Apenas valores
    for _, valor := range idiomas {
        fmt.Println("Extensão:", valor)
    }
}
```

> **ℹ️ Informação:** A ordem de iteração em maps é **aleatória** em Go. Se você precisa de ordem garantida, use um slice e ordene-o.

| Operação | Descrição | Exemplo |
|---|---|---|
| `map[chave]` | Acessa valor | `valor := map["chave"]` |
| `map[chave] = valor` | Atribui valor | `map["chave"] = 10` |
| `delete(map, chave)` | Remove entrada | `delete(map, "chave")` |
| `valor, ok := map[chave]` | Verifica existência | `v, ok := map["chave"]` |
| `len(map)` | Conta entradas | `len(map)` |

---

## Comparação

Aqui está uma comparação prática entre Arrays, Slices e Maps:

| Característica | Array | Slice | Map |
|---|---|---|---|
| **Tamanho** | Fixo | Dinâmico | Dinâmico |
| **Tipo de Índice** | Inteiro (0..n) | Inteiro (0..n) | Qualquer tipo hashable |
| **Inicialização** | `[n]T{...}` | `[]T{...}` ou `make()` | `map[K]V{...}` ou `make()` |
| **Ordenado** | Sim | Sim | Não |
| **Comparável** | Sim (mesma chave) | Não | Não |
| **Cópia** | Profunda | Superficial (referência) | Superficial (referência) |
| **Caso de Uso** | Coleção fixa | Coleção dinâmica | Busca rápida por chave |

### Exemplo Comparativo

```go
package main

import "fmt"

func main() {
    // Array - tamanho fixo
    array := [3]int{1, 2, 3}
    
    // Slice - dinâmico
    slice := []int{1, 2, 3}
    slice = append(slice, 4, 5)
    
    // Map - pares chave-valor
    mapa := map[string]int{
        "um":   1,
        "dois": 2,
        "três": 3,
    }
    
    fmt.Println("Array:", array, "len:", len(array))
    fmt.Println("Slice:", slice, "len:", len(slice))
    fmt.Println("Map:", mapa, "len:", len(mapa))
}
```

---

## Boas Práticas

### ✅ Recomendações

1. **Use Slices por padrão** - Arrays são raramente necessários em Go. Prefira slices para coleções dinâmicas.

```go
// ✅ Bom
func processarDados(dados []int) {
    // trabalhar com slice
}

// ❌ Evitar
func processarDados(dados [10]int) {
    // array fixo é inflexível
}
```

2. **Pre-aloque Slices quando souber o tamanho**

```go
// ✅ Bom - pré-aloca capacidade
numeros := make([]int, 0, 1000)
for i := 0; i < 1000; i++ {
    numeros = append(numeros, i)
}

// ❌ Ineficiente - crescimento gradual
var numeros []int
for i := 0; i < 1000; i++ {
    numeros = append(numeros, i)
}
```

3. **Use Maps para lookups rápidos**

```go
// ✅ Bom - O(1) lookup
usuarios := map[int]string{
    1: "Alice",
    2: "Bob",
    3: "Carlos",
}
nome := usuarios[2]  // Rápido

// ❌ Ineficiente - O(n) search
usuarios := []string{"Alice", "Bob", "Carlos"}
// precisa iterar para encontrar
```

4. **Sempre verifique existência em Maps**

```go
// ✅ Bom
valor, existe := mapa["chave"]
if existe {
    fmt.Println(valor)
}

// ❌ Problemático
valor := mapa["chave"]  // valor zero se não existir
if valor == nil {
    // pode não funcionar como esperado
}
```

5. **Use copy() para evitar compartilhamento não intencional**

```go
// ✅ Bom - cópia segura
original := []int{1, 2, 3}
copia := make([]int, len(original))
copy(copia, original)
copia[0] = 999  // não afeta original

// ❌ Problemático - compartilha dados
original := []int{1, 2, 3}
copia := original  // apenas referência
copia[0] = 999     // modifica original também
```

### ⚠️ Armadilhas Comuns

1. **Modificar slice durante iteração**

```go
// ❌ Perigoso
numeros := []int{1, 2, 3, 4, 5}
for i, v := range numeros {
    if v == 3 {
        numeros = append(numeros, 10)  // pode causar problemas
    }
}
```

2. **Esquecer que nil slice é diferente de slice vazio**

```go
var vazio []int         // nil
vazio2 := []int{}       // não-nil, vazio
vazio3 := make([]int, 0)  // não-nil, vazio

fmt.Println(vazio == nil)   // true
fmt.Println(vazio2 == nil)  // false
fmt.Println(vazio3 == nil)  // false
```

3. **Usar map[interface{}]interface{} sem necessidade**

```go
// ✅ Melhor - mais específico
mapa := map[string]int{}

// ❌ Evitar - muito genérico
mapa := map[interface{}]interface{}{}
```

---

## Referências e Recursos Adicionais

- [Go Slices: usage and internals](https://go.dev/blog/slices-intro)
- [Effective Go - Maps](https://go.dev/doc/effective_go#maps)
- [The Go Programming Language Specification](https://go.dev/ref/spec)

