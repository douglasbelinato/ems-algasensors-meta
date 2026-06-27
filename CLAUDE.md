# CLAUDE.md — AlgaSensors (meta-repo)

Repositório principal (meta) do projeto **AlgaSensors**. Não contém código de aplicação: agrega
os microsserviços como **submódulos Git** e guarda o ponteiro (commit) de cada um.

## Microsserviços

Cada um é um submódulo independente em `ms/`, com seu próprio `build.gradle`, wrapper Gradle e
`CLAUDE.md`. Consulte o `CLAUDE.md` do submódulo antes de trabalhar nele.

| Submódulo | Porta | Responsabilidade |
|-----------|-------|------------------|
| `ms/device-management` | 8080 | Cadastro/gestão de sensores (CRUD); orquestra o monitoramento |
| `ms/temperature-processing` | 8081 | Ingestão/processamento das leituras de temperatura |
| `ms/temperature-monitoring` | 8082 | Estado de monitoramento, histórico de leituras e alertas |

**Comunicação:** `device-management` (8080) chama `temperature-monitoring` (8082) via HTTP
(`RestClient`) para ligar/desligar o monitoramento dos sensores.

## Stack comum (todos os módulos)

- Java 25 · Spring Boot 4.1.0 · Gradle (use o wrapper `./gradlew` dentro de cada submódulo)
- Spring Web (MVC) · Lombok · hypersistence-tsid (IDs em TSID) · **Jackson 3**
- `device-management` e `temperature-monitoring` usam Spring Data JPA + H2;
  `temperature-processing` não tem banco.

Convenções e pegadinhas detalhadas (conversão de TSID, Jackson 3 `tools.jackson.*`, padrão de
testes e2e com `RestTestClient`, `@MockitoBean`, starter `restclient`) ficam em cada `CLAUDE.md`
de submódulo — não são repetidas aqui.

## Trabalhando com submódulos

```bash
# Clonar já trazendo os submódulos
git clone --recurse-submodules <url-do-meta-repo>

# Após um clone simples, inicializar/baixar os submódulos
git submodule update --init --recursive

# Ver o commit atual de cada submódulo
git submodule status
```

Comandos de build/test/run **rodam dentro de cada submódulo**, não na raiz. Ex.:
`cd ms/device-management && ./gradlew test`.

## Commit & push (a ordem importa)

**Primeiro os submódulos, depois o meta-repo** — o meta-repo só guarda o ponteiro de commit de
cada submódulo, então esses commits precisam existir no remoto antes.

1. Em cada submódulo alterado: confirme que está na branch `main` (não em *detached HEAD*),
   commite e `git push origin main`.
2. Na raiz: `git add ms/...` dos submódulos atualizados, commite ("Atualiza ponteiros dos
   submódulos") e `git push origin main`.

O passo a passo completo (incluindo recuperação de *detached HEAD* e comandos úteis como
`git submodule foreach`) está no `README.md` da raiz.
