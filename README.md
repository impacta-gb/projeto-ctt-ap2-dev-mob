# Go Docs — Grupo X

## 👥 Integrantes
- Kauã Queiroga Oliveira André
- Caio Eduardo de Carvalho
- Arthur Lopes Placência
- Augusto Meirelles
- Felipe de Oliveira

## 🔀 Fluxo de Trabalho
1. Cada membro cria uma branch (ex: `feat/doc-goroutines`)
2. Após concluir, abre um Pull Request para a `main`
3. Outro membro revisa, comenta e aprova
4. Merge é feito após aprovação

## 🛡️ Proteção de Branch
A branch `main` está protegida:
- Commits diretos bloqueados
- Requer aprovação de 1 revisor
- Status checks obrigatórios (CI deve passar)

## ⚙️ Arquitetura do Workflow (GitHub Actions)
### Triggers
- `pull_request` → valida o build antes do merge
- `push` → faz o deploy após o merge
- `schedule (cron)` → roda toda semana automaticamente

### Jobs
| Job | Descrição |
|-----|-----------|
| `build_site` | Valida em Python 3.10 e 3.11 (matrix), usa cache pip |
| `deploy_site` | Aguarda build, faz deploy no GitHub Pages |

### Condicional de Segurança
O job `deploy_site` usa `if: github.event_name != 'pull_request'`
para garantir que o deploy só ocorra em push ou schedule.
