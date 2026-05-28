---
icon: lucide/package
---

# Gerenciamento de Pacotes (Go Modules)

Go Modules é o sistema padrão de gerenciamento de dependências em Go desde a versão 1.11. Este guia abrange como trabalhar com módulos, gerenciar versões e organizar projetos.

## Tabela de Conteúdos

- [Visão Geral](#visão-geral)
- [Inicializando um Módulo](#inicializando-um-módulo)
- [Adicionando Dependências](#adicionando-dependências)
- [Gerenciando Versões](#gerenciando-versões)
- [Arquivo go.mod e go.sum](#arquivo-gomod-e-gosum)
- [Comandos Úteis](#comandos-úteis)
- [Boas Práticas](#boas-práticas)

## Visão Geral

Go Modules resolve dependências de forma declarativa e permite que você:

- Especifique exatamente quais versões de pacotes seu projeto precisa
- Reproduza compilações exatamente da mesma forma em diferentes máquinas
- Evite conflitos de versão entre dependências

!!! info

    Go Modules foi introduzido em Go 1.11 como experimental e se tornou o padrão de gerenciamento de dependências em Go 1.13+

## Inicializando um Módulo

Para criar um novo módulo Go, use o comando `go mod init`:

``` bash title="Inicializar um módulo"
go mod init github.com/seu-usuario/seu-projeto
```

Este comando cria um arquivo `go.mod` na raiz do seu projeto:

``` go title="go.mod (exemplo)"
module github.com/seu-usuario/seu-projeto

go 1.21

require (
    github.com/gorilla/mux v1.8.0
)
```

!!! warning

    O nome do módulo geralmente deve ser o caminho de um repositório (por exemplo, `github.com/usuario/repo`) para evitar conflitos com outros módulos.

### Estrutura do Projeto

Uma estrutura bem organizada ajuda no gerenciamento de pacotes:

``` text
seu-projeto/
├── go.mod
├── go.sum
├── main.go
├── cmd/
│   └── main/
│       └── main.go
├── internal/
│   ├── handler/
│   │   └── handler.go
│   └── service/
│       └── service.go
└── pkg/
    └── utils/
        └── utils.go
```

## Adicionando Dependências

### Importar e Usar um Pacote

Simplesmente importe um pacote em seu código:

``` go title="Exemplo de import"
package main

import (
    "fmt"
    "github.com/gorilla/mux"
)

func main() {
    router := mux.NewRouter()
    fmt.Println("Router criado!")
}
```

Então execute:

``` bash title="Baixar dependências automaticamente"
go mod tidy
```

Este comando:
- Baixa pacotes necessários
- Remove pacotes não utilizados
- Atualiza o arquivo `go.mod`

### Adicionar um Pacote Específico

``` bash title="Adicionar um pacote específico"
go get github.com/gorilla/mux
```

Para especificar uma versão:

``` bash title="Adicionar versão específica"
go get github.com/gorilla/mux@v1.8.0
```

Para usar a versão mais recente:

``` bash title="Adicionar versão mais recente"
go get -u github.com/gorilla/mux
```

## Gerenciando Versões

### Versões Semânticas

Go Modules usam Versionamento Semântico (SemVer):

| Versão | Descrição | Exemplo |
|--------|-----------|---------|
| **Major** | Mudanças incompatíveis (quebras de API) | 1.0.0 → 2.0.0 |
| **Minor** | Novas funcionalidades compatíveis | 1.2.0 → 1.3.0 |
| **Patch** | Correções de bugs | 1.2.3 → 1.2.4 |
| **Pre-release** | Versões de teste | v1.2.0-alpha, v1.2.0-beta.1 |
| **Metadata** | Informações adicionais | v1.2.0+build.123 |

### Especificadores de Versão

| Especificador | Significado | Exemplo |
|---------------|-------------|---------|
| `v1.2.3` | Versão exata | Usa exatamente 1.2.3 |
| `v1.2` | Versão compatível com patch | Permite 1.2.x (até 1.3.0) |
| `v1` | Qualquer versão v1 | Permite 1.x.x |
| `>=v1.2.0` | Maior ou igual | Permite 1.2.0 e superiores |
| `<v2.0.0` | Menor que | Permite versões antes de 2.0.0 |
| `>=v1.2.0, <v1.3.0` | Intervalo | Entre 1.2.0 e 1.3.0 |

### Atualizar Dependências

``` bash title="Verificar atualizações disponíveis"
go list -u -m all
```

``` bash title="Atualizar um pacote"
go get -u github.com/gorilla/mux
```

``` bash title="Atualizar todos os pacotes"
go get -u ./...
```

``` bash title="Downgrade de versão"
go get github.com/gorilla/mux@v1.7.0
```

!!! warning

    Tenha cuidado ao atualizar pacotes com mudanças **major** de versão, pois podem quebrar seu código.

## Arquivo go.mod e go.sum

### go.mod

Define o módulo e suas dependências:

``` go title="Exemplo completo de go.mod"
module github.com/seu-usuario/seu-projeto

go 1.21

require (
    github.com/gorilla/mux v1.8.0
    github.com/lib/pq v1.10.0
)

require (
    github.com/stretchr/testify v1.7.0 // indirect
)

exclude github.com/bad-package v1.0.0
replace github.com/bad-package => github.com/good-package v1.0.0
```

**Diretivas importantes:**

| Diretiva | Propósito |
|----------|-----------|
| `require` | Dependências diretas do projeto |
| `indirect` | Dependências indiretas (de outras dependências) |
| `exclude` | Versões que não devem ser usadas |
| `replace` | Substituir um pacote por outro (útil em desenvolvimento) |

### go.sum

Contém hashes SHA-256 para verificar integridade:

``` text title="go.sum (exemplo)"
github.com/gorilla/mux v1.8.0 h1:i40ztqXznAHQMstJ+lqDJeP6k/...
github.com/gorilla/mux v1.8.0/go.mod h1:DVbg23sWSpFRCP0d9KM5d...
github.com/lib/pq v1.10.0 h1:Zx+LV+...
```

!!! note

    Sempre commite `go.sum` no controle de versão. Ele garante que todos os desenvolvedores usem as mesmas versões.

## Comandos Úteis

### Análise e Limpeza

``` bash title="Verificar dependências não utilizadas"
go mod tidy
```

``` bash title="Visualizar grafo de dependências"
go mod graph
```

``` bash title="Listar todas as dependências"
go list -m all
```

``` bash title="Listar apenas dependências diretas"
go list -m -direct all
```

### Desenvolvimento Local

Para trabalhar com um pacote em desenvolvimento local, use `replace`:

``` go title="go.mod com replace"
module github.com/seu-usuario/seu-projeto

go 1.21

replace github.com/seu-usuario/outro-pacote => ../outro-pacote

require github.com/seu-usuario/outro-pacote v0.0.0
```

### Verificação

``` bash title="Verificar integridade de dependências"
go mod verify
```

``` bash title="Fazer download de todas as dependências"
go mod download
```

``` bash title="Vendorizar dependências (copiar para local)"
go mod vendor
```

!!! info

    `go mod vendor` cria uma pasta `vendor/` com todas as dependências. Útil em ambientes sem acesso à internet ou CI/CD.

## Boas Práticas

### ✅ Faça

!!! success

    **Use versões explícitas** - Especifique versões exatas ou intervalos bem definidos em `go.mod`

    **Commite go.sum** - Garanta reprodutibilidade das compilações

    **Atualize regularmente** - Mantenha as dependências atualizadas para segurança

    **Use `go mod tidy`** - Antes de fazer commit, limpe dependências não utilizadas

    **Versionize seu módulo** - Se publicar um pacote, siga SemVer rigorosamente

### ❌ Não Faça

!!! error

    **Não ignore go.sum** - Sempre inclua no versionamento

    **Não use `go get` sem cuidado** - Verifique a documentação do pacote antes de integrar

    **Não commite vendor/** - A menos que seja um requisito específico do projeto

    **Não atualize tudo de uma vez** - Atualize por partes e teste cada mudança

### Exemplo Completo de Workflow

``` bash title="Workflow de gerenciamento de pacotes"
# 1. Inicializar novo projeto
go mod init github.com/seu-usuario/seu-projeto

# 2. Adicionar uma dependência
go get github.com/gorilla/mux@v1.8.0

# 3. Organizar imports
go mod tidy

# 4. Verificar integridade
go mod verify

# 5. Visualizar dependências
go list -m all

# 6. Atualizar uma dependência específica
go get -u github.com/gorilla/mux

# 7. Commit no repositório
git add go.mod go.sum
git commit -m "feat: adicionar gorilla/mux v1.8.0"
```

## Recursos Adicionais

- [Go Modules Documentation](https://go.dev/wiki/Modules)
- [Semantic Versioning](https://semver.org/)
- [Go Package Discovery](https://pkg.go.dev/)
- [Go Modules Reference](https://go.dev/ref/mod)

---

**Última atualização:** 2026

Para mais informações sobre Go, consulte os outros capítulos desta documentação.
