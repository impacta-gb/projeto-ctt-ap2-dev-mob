# Structs e Métodos em Go

## Índice de Conteúdo

1. [Introdução](#introducao)
2. [Structs](#structs)
   - [Definicao Basica](#definicao-basica)
   - [Campos de Struct](#campos-de-struct)
   - [Inicializacao](#inicializacao)
   - [Embedded Structs](#embedded-structs)
3. [Metodos](#metodos)
   - [Declaracao de Metodos](#declaracao-de-metodos)
   - [Receptores por Valor](#receptores-por-valor)
   - [Receptores por Ponteiro](#receptores-por-ponteiro)
4. [Exemplos Praticos](#exemplos-praticos)
5. [Boas Praticas](#boas-praticas)

---

## Introducao

Structs (estruturas) são tipos de dados compostos que permitem agrupar múltiplos campos de diferentes tipos. Métodos são funções associadas a um tipo específico, permitindo programação orientada a objetos em Go.

> **Nota**: Go não é orientada a objetos no sentido tradicional, mas oferece recursos para trabalhar com dados estruturados e comportamento através de métodos.

---

## Structs

### Definicao Basica

Uma struct é definida usando a palavra-chave `type` seguida pelo nome da struct e a palavra-chave `struct`:

```go
package main

type Pessoa struct {
    Nome  string
    Idade int
    Email string
}
```

!!! info "Info"
    Os nomes dos campos em structs que começam com letra maiúscula são **exportados** (públicos) e podem ser acessados fora do pacote. Nomes com letra minúscula são **privados**.

### Campos de Struct

| Aspecto | Descrição | Exemplo |
|---------|-----------|---------|
| **Tipo** | Pode ser qualquer tipo Go | `int`, `string`, `bool` |
| **Visibilidade** | Maiúscula = pública, minúscula = privada | `Nome`, `idade` |
| **Tags** | Metadados para serialização | `` `json:"nome"` `` |
| **Campos anônimos** | Herança de tipos | `Endereco` (sem nome) |

```go
package main

type Funcionario struct {
    Nome      string
    Salario   float64
    Ativo     bool
    Departamento string `json:"depto"`
}
```

### Inicializacao

Existem várias formas de criar instâncias de structs:

#### 1. Inicialização com Ordem Posicional

```go
package main

import "fmt"

type Carro struct {
    Marca string
    Ano   int
    Cor   string
}

func main() {
    // Ordem dos valores importa
    c1 := Carro{"Toyota", 2022, "Branco"}
    fmt.Println(c1) // {Toyota 2022 Branco}
}
```

#### 2. Inicialização com Nomes de Campos

```go
package main

import "fmt"

type Carro struct {
    Marca string
    Ano   int
    Cor   string
}

func main() {
    // Muito mais seguro e legível
    c2 := Carro{
        Marca: "Honda",
        Ano:   2023,
        Cor:   "Preto",
    }
    fmt.Println(c2) // {Honda 2023 Preto}
}
```

#### 3. Inicialização Parcial

```go
package main

import "fmt"

type Produto struct {
    Nome   string
    Preco  float64
    Estoque int
}

func main() {
    // Campos não especificados recebem valor zero
    p := Produto{
        Nome:  "Notebook",
        Preco: 2500.00,
    }
    fmt.Println(p.Estoque) // 0
}
```

!!! warning "Aviso"
    Se você usar a inicialização posicional e adicionar novos campos no meio da struct, seu código pode quebrar. Use nomes de campos sempre que possível!

### Embedded Structs

Embedded structs (composição) permitem reutilizar estruturas existentes:

```go
package main

import "fmt"

type Endereco struct {
    Rua       string
    Numero    int
    Cidade    string
    Estado    string
}

type Pessoa struct {
    Nome   string
    Idade  int
    Endereco // Campo anônimo - embutido
}

func main() {
    p := Pessoa{
        Nome:  "Ana",
        Idade: 30,
        Endereco: Endereco{
            Rua:    "Avenida Principal",
            Numero: 123,
            Cidade: "São Paulo",
            Estado: "SP",
        },
    }
    
    // Acesso aos campos
    fmt.Println(p.Rua)    // Acesso direto ao campo embutido
    fmt.Println(p.Nome)   // Ana
}
```

---

## Metodos

### Declaracao de Metodos

Um método é uma função com um **receptor** especial. O receptor fica entre a palavra-chave `func` e o nome do método:

```go
func (receptor TipoDoReceptor) NomeDoMetodo(parametros) RetornoOpcional {
    // Corpo do método
}
```

Exemplo básico:

```go
package main

import "fmt"

type Retangulo struct {
    Largura  float64
    Altura   float64
}

// Método com receptor por valor
func (r Retangulo) Area() float64 {
    return r.Largura * r.Altura
}

// Método com receptor por valor
func (r Retangulo) Perimetro() float64 {
    return 2 * (r.Largura + r.Altura)
}

func main() {
    ret := Retangulo{5.0, 3.0}
    fmt.Println(ret.Area())      // 15
    fmt.Println(ret.Perimetro()) // 16
}
```

### Receptores por Valor

Um receptor por valor significa que o método recebe uma **cópia** da estrutura:

```go
package main

import "fmt"

type Conta struct {
    Saldo float64
}

// Receptor por valor - cópia
func (c Conta) Sacar(valor float64) float64 {
    if valor > c.Saldo {
        return c.Saldo
    }
    c.Saldo -= valor
    return c.Saldo
}

func main() {
    conta := Conta{1000.0}
    resultado := conta.Sacar(200)
    
    fmt.Println(resultado)    // 800
    fmt.Println(conta.Saldo)  // 1000 (não foi modificado!)
}
```

!!! danger "Atenção"
    Com receptor por valor, **modificações não afetam o original**. Use receptores por valor para métodos que não precisam modificar a estrutura.

### Receptores por Ponteiro

Um receptor por ponteiro permite **modificar** a estrutura original:

```go
package main

import "fmt"

type Conta struct {
    Saldo float64
}

// Receptor por ponteiro - referência
func (c *Conta) Sacar(valor float64) float64 {
    if valor > c.Saldo {
        return c.Saldo
    }
    c.Saldo -= valor
    return c.Saldo
}

func (c *Conta) Depositar(valor float64) {
    if valor > 0 {
        c.Saldo += valor
    }
}

func main() {
    conta := &Conta{1000.0}
    
    conta.Sacar(200)
    fmt.Println(conta.Saldo)  // 800 (foi modificado!)
    
    conta.Depositar(500)
    fmt.Println(conta.Saldo)  // 1300
}
```

!!! success "Dica"
    Use receptores por ponteiro quando você precisa modificar o estado da estrutura ou quando a estrutura é grande (para evitar cópias desnecessárias).

---

## Comparação: Receptor por Valor vs. Ponteiro

| Aspecto | Por Valor | Por Ponteiro |
|---------|-----------|--------------|
| **Modificação** | Não modifica original | Modifica original |
| **Performance** | Cópia da struct | Sem cópia |
| **Tamanho** | Melhor para structs pequenas | Melhor para structs grandes |
| **Sintaxe** | `func (r Tipo) Metodo()` | `func (r *Tipo) Metodo()` |
| **Chamada** | Direta | Direta ou com `&` |

```go
package main

import "fmt"

type Usuario struct {
    Nome  string
    Score int
}

// Receptor por valor
func (u Usuario) IncrementarScoreValor() {
    u.Score += 10
}

// Receptor por ponteiro
func (u *Usuario) IncrementarScorePonteiro() {
    u.Score += 10
}

func main() {
    usuario := Usuario{"Alice", 100}
    
    usuario.IncrementarScoreValor()
    fmt.Println(usuario.Score) // 100 (não mudou)
    
    usuario.IncrementarScorePonteiro()
    fmt.Println(usuario.Score) // 110 (mudou!)
}
```

---

## Exemplos Praticos

### Exemplo 1: Sistema de Banco

```go
package main

import (
    "fmt"
    "time"
)

type ContaBancaria struct {
    Titular   string
    Agencia   string
    Conta     string
    Saldo     float64
    DataAber  time.Time
}

// Getter para saldo
func (c *ContaBancaria) ObterSaldo() float64 {
    return c.Saldo
}

// Depositar
func (c *ContaBancaria) Depositar(valor float64) error {
    if valor <= 0 {
        return fmt.Errorf("valor deve ser positivo")
    }
    c.Saldo += valor
    fmt.Printf("Depósito de R$%.2f realizado. Novo saldo: R$%.2f\n", valor, c.Saldo)
    return nil
}

// Sacar
func (c *ContaBancaria) Sacar(valor float64) error {
    if valor <= 0 {
        return fmt.Errorf("valor deve ser positivo")
    }
    if valor > c.Saldo {
        return fmt.Errorf("saldo insuficiente. Saldo: R$%.2f", c.Saldo)
    }
    c.Saldo -= valor
    fmt.Printf("Saque de R$%.2f realizado. Novo saldo: R$%.2f\n", valor, c.Saldo)
    return nil
}

func main() {
    conta := &ContaBancaria{
        Titular:  "João Silva",
        Agencia:  "1234",
        Conta:    "56789-0",
        Saldo:    5000.00,
        DataAber: time.Now(),
    }
    
    conta.Depositar(1000)
    conta.Sacar(500)
    conta.Sacar(10000) // Erro
}
```

### Exemplo 2: Manipulação de Geometria

```go
package main

import (
    "fmt"
    "math"
)

type Circulo struct {
    Raio float64
}

type Retangulo struct {
    Largura float64
    Altura  float64
}

// Métodos do Círculo
func (c Circulo) Area() float64 {
    return math.Pi * c.Raio * c.Raio
}

func (c Circulo) Perimetro() float64 {
    return 2 * math.Pi * c.Raio
}

func (c Circulo) DescreverArea() string {
    return fmt.Sprintf("Círculo com raio %.2f tem área %.2f", c.Raio, c.Area())
}

// Métodos do Retângulo
func (r Retangulo) Area() float64 {
    return r.Largura * r.Altura
}

func (r Retangulo) Perimetro() float64 {
    return 2 * (r.Largura + r.Altura)
}

func (r Retangulo) DescreverArea() string {
    return fmt.Sprintf("Retângulo %.2f x %.2f tem área %.2f", r.Largura, r.Altura, r.Area())
}

func main() {
    circ := Circulo{5}
    rect := Retangulo{4, 6}
    
    fmt.Println(circ.DescreverArea())
    fmt.Println(rect.DescreverArea())
}
```

---

## Boas Praticas

### 1. Escolha o Receptor Correto

| Situação | Use |
|----------|-----|
| Struct pequeno e sem mutação | Receptor por **valor** |
| Struct grande | Receptor por **ponteiro** |
| Precisa modificar campos | Receptor por **ponteiro** |
| Implementando interface | Receptor por **ponteiro** (geralmente) |

### 2. Nomeação Consistente

```go
// ✅ BOM - Nomes concisos para receptores
func (u *Usuario) Ativar() { }
func (p *Produto) AtualizarPreco(novo float64) { }

// ❌ RUIM - Nomes longos
func (usuario *Usuario) AtivarUsuario() { }
func (prod *Produto) AtualizarPrecoProduto(novo float64) { }
```

### 3. Métodos para Comportamento Relacionado

```go
// ✅ BOM - Métodos relacionados no mesmo lugar
type Pessoa struct {
    Nome string
    Idade int
}

func (p *Pessoa) Aniversario() {
    p.Idade++
}

func (p Pessoa) EhMaiorDeIdade() bool {
    return p.Idade >= 18
}

// ❌ RUIM - Lógica em funções soltas
func fazerAniversarioPessoa(p *Pessoa) { }
func pessoaEhMaiorDeIdade(p Pessoa) bool { }
```

### 4. Evitar Campos Públicos Desnecessários

```go
// ✅ BOM - Getters e Setters quando necessário
type Produto struct {
    nome  string  // privado
    preco float64 // privado
}

func (p Produto) Nome() string {
    return p.nome
}

func (p *Produto) SetPreco(novoPreco float64) error {
    if novoPreco < 0 {
        return fmt.Errorf("preço não pode ser negativo")
    }
    p.preco = novoPreco
    return nil
}

// ❌ RUIM - Campos públicos sem validação
type Produto struct {
    Nome  string
    Preco float64 // Qualquer um pode modificar!
}
```

### 5. Documentação de Métodos

```go
// Usuario representa um usuário do sistema.
type Usuario struct {
    ID   int
    Nome string
}

// ativar ativa a conta do usuário.
func (u *Usuario) Ativar() error {
    if u == nil {
        return fmt.Errorf("usuário nil")
    }
    // lógica de ativação
    return nil
}
```

!!! note "Nota Final"
    Structs e métodos são fundamentais em Go para criar código organizado e maintível. Lembre-se: Go favorece composição sobre herança!

---

**Última atualização**: Maio de 2026
