# ems-algasensors-meta

Repositório principal (meta) do projeto AlgaSensors. Ele agrega os microsserviços como submódulos Git:

- `ms/device-management`
- `ms/temperature-monitoring`
- `ms/temperature-processing`

## Como commitar e enviar (push) as alterações

A ordem importa: **primeiro** os submódulos, **depois** o repositório principal. O repositório principal apenas guarda o ponteiro (commit) de cada submódulo, então os submódulos precisam ter seus commits enviados antes para que esses ponteiros existam no remoto.

### 1. Commitar e enviar cada submódulo alterado

Para cada submódulo que você modificou (ex.: `ms/device-management`):

```bash
cd ms/device-management

# Confira o que mudou e em qual branch você está (deve ser main)
git status
git branch

# Caso esteja em "detached HEAD", volte para a branch main antes de commitar
git checkout main

git add .
git commit -m "Descreva a alteração do submódulo"
git push origin main

cd ../..
```

Repita o processo para `ms/temperature-monitoring` e `ms/temperature-processing` se eles também tiverem mudanças.

### 2. Commitar e enviar o repositório principal (meta)

Depois que os submódulos foram enviados, atualize os ponteiros no repositório principal:

```bash
# Na raiz do projeto
git status                 # mostra os submódulos com novos commits
git add ms/device-management ms/temperature-monitoring ms/temperature-processing
# (ou simplesmente: git add .)

git commit -m "Atualiza ponteiros dos submódulos"
git push origin main
```

## Comandos úteis

```bash
# Ver o commit atual de cada submódulo
git submodule status

# Clonar o projeto já trazendo os submódulos
git clone --recurse-submodules git@github.com:douglasbelinato/ems-algasensors-meta.git

# Após um clone simples, inicializar/baixar os submódulos
git submodule update --init --recursive

# Atualizar todos os submódulos para o último commit da branch remota
git submodule update --remote --merge

# Rodar um comando em todos os submódulos de uma vez (ex.: push)
git submodule foreach 'git push origin main'
```

> Dica: se o `git push` do repositório principal falhar com erro indicando commits de submódulo ausentes no remoto, é porque você esqueceu de fazer o push de algum submódulo (passo 1).
