---
icon: lucide/book-open
---

# Como Funciona o Zensical

Este guia explica a estrutura e o funcionamento básico do Zensical, o gerador de documentação utilizado neste projeto.

## O que é Zensical?

Zensical é um **gerador de documentação estática** que transforma arquivos Markdown em um site HTML profissional. É similar ao MkDocs ou Sphinx, mas foi desenvolvido especificamente para ser simples e poderoso.

## Estrutura do Projeto

```
projeto-ctt-ap2-dev-mob/
├── zensical.toml          # Arquivo de configuração principal
├── docs/                  # Pasta com arquivo de documentação (Markdown)
│   ├── index.md          # Página inicial
│   ├── markdown.md       # Guia de sintaxe Markdown
│   └── como-funciona.md  # Este arquivo
└── site/                 # Pasta com o site compilado (gerado automaticamente)
    ├── index.html
    ├── assets/
    └── ...
```

### zensical.toml

Este arquivo contém toda a **configuração do projeto**:
- `site_name` - Nome do site (aparece no cabeçalho)
- `site_description` - Descrição para mecanismos de busca
- `site_author` - Autor do site
- `site_url` - URL do site (opcional, para publicação online)
- `copyright` - Informação de direitos autorais
- `nav` - Estrutura de navegação (opcional)

### Pasta docs/

Aqui estão todos os arquivos **Markdown (.md)** que compõem sua documentação:
- Cada arquivo `.md` se torna uma página HTML
- A estrutura de pastas é refletida na navegação
- O arquivo `index.md` é a página inicial

### Pasta site/

Esta pasta é **gerada automaticamente** quando você executa os comandos Zensical:
- Contém todos os arquivos HTML compilados
- Inclui assets (CSS, JavaScript, imagens)
- NÃO precisa ser editada manualmente

## Comandos Principais

### 1. Servir Localmente (Desenvolvimento)

```bash
zensical serve -a localhost:8080
```

Este comando:
- Inicia um servidor web local na porta 8080
- Abre a documentação em `http://localhost:8080`
- **Recarrega automaticamente** quando você edita arquivos `.md`
- Ideal para visualizar mudanças em tempo real

### 2. Fazer Build (Produção)

```bash
zensical build
```

Este comando:
- Compila toda a documentação em HTML estático
- Gera a pasta `site/` com todos os arquivos
- Você pode copiar a pasta `site/` para qualquer servidor web

### 3. Criar Novo Projeto

```bash
zensical new
```

Cria um novo projeto Zensical (você já tem um, então não precisa usar este comando agora).

## Como Adicionar Páginas

### Passo 1: Criar um arquivo Markdown

Crie um novo arquivo na pasta `docs/`. Exemplo: `docs/guia-rapido.md`

```markdown
---
icon: lucide/zap
---

# Guia Rápido

Seu conteúdo aqui...
```

### Passo 2: Editar zensical.toml (Opcional)

Se quiser controlar a **ordem de navegação**, descomente e edite a seção `nav`:

```toml
nav = [
  { "Início" = "index.md" },
  { "Como Funciona" = "como-funciona.md" },
  { "Guia Rápido" = "guia-rapido.md" },
  { "Markdown" = "markdown.md" },
]
```

### Passo 3: Visualizar

O servidor `zensical serve` já vai detectar a nova página automaticamente!

## Sintaxe Markdown Suportada

O Zensical suporta:
- **Headings** (títulos com #)
- **Listas** (ordenadas e não-ordenadas)
- **Ênfase** (negrito e itálico)
- **Blocos de código** com syntax highlighting
- **Admonições** (notas, avisos, etc.)
- **Tabelas**
- **Links e imagens**
- **E muito mais!**

Consulte a [documentação oficial do Markdown](https://www.markdownguide.org/) para mais exemplos e recursos avançados.

## YAML Front Matter

Os arquivos `.md` podem começar com um bloco YAML com metadados:

```yaml
---
icon: lucide/rocket
title: Página Personalizada
description: Descrição da página
---
```

Isso permite personalizar:
- `icon` - Ícone que aparece no cabeçalho (usa Lucide Icons)
- `title` - Título da página (se não especificado, usa o primeiro # do arquivo)
- `description` - Descrição da página

## Fluxo de Trabalho Típico

1. **Editar** arquivo em `docs/` → Arquivo `.md`
2. **Visualizar** → Abra `http://localhost:8080` (servidor já recarrega!)
3. **Revisar** → Verifique se tudo está correto
4. **Fazer commit** → Envie para repositório Git
5. **Deploy** → Execute `zensical build` e copie `site/` para servidor

## Próximos Passos

- [ ] Edite `zensical.toml` com suas informações
- [ ] Explore a [documentação oficial do Zensical](https://zensical.org/docs/)
- [ ] Experimente adicionar novas páginas
- [ ] Customize o CSS se necessário

## Dúvidas Frequentes

??? question "Como mudo o nome do site?"
    Edite `site_name` no arquivo `zensical.toml` e reinicie o servidor.

??? question "Como adiciono um logo?"
    Verifique a documentação do Zensical sobre tema e customização.

??? question "Posso usar HTML dentro dos arquivos .md?"
    Sim! Você pode inserir tags HTML diretamente nos arquivos Markdown.

??? question "Como faço deploy do site?"
    Copie a pasta `site/` gerada para seu servidor web (Apache, Nginx, etc.) ou plataforma de hosting.

---

**Última atualização:** Maio de 2026
