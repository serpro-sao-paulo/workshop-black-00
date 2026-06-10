# Instruções do GitHub Copilot — Workshop de Modernização de Legado

> Contexto, stack, convenções e regras que se aplicam a todo o repositório. Confie nestas instruções; pesquise no código só quando estiverem incompletas ou incorretas.

## Contexto do Projeto

Modernização do legado **SIFAP** (Sistema de Fiscalização e Administração de Pagamentos) — Natural/Adabas, 29 anos — para Java 21 + Next.js 15. Código legado em [`01-arqueologia/legado-sifap/`](../01-arqueologia/legado-sifap/) (15 programas `.NSN` + 4 DDMs). Dois níveis de agentes: persona-kit por pessoa + agente de estágio por equipe ([`06-agentes-de-estagio/README.md`](../06-agentes-de-estagio/README.md)).

## Project Layout

The new application code is created by the team during Stage 3. When it exists, the expected layout is:

```
backend/          # Java 21 + Spring Boot 3.3 (Maven wrapper)
frontend/         # Next.js 15 App Router (pnpm)
infra/            # Terraform modules (Azure provider)
specs/NNN-feature/  # Spec-Kit feature specs (spec.md, plan.md, tasks.md)
```

Legacy code (read-only reference) lives under `01-arqueologia/legado-sifap/`:
- `natural-programs/` — 15 `.NSN` Natural source files
- `adabas-ddms/` — 4 `.ddm` Adabas data definitions

## Build, Test & Lint Commands

CI runs via `.github/workflows/ci.yml` with path-based detection — jobs only run when relevant files change.

### Backend (Java)

```bash
cd backend
./mvnw -B verify                          # build + all tests
./mvnw -B test -pl . -Dtest=MyTest        # single test class
./mvnw -B test -pl . -Dtest='MyTest#method' # single test method
```

### Frontend (Next.js)

```bash
cd frontend
pnpm install --frozen-lockfile
pnpm lint                                 # ESLint
pnpm typecheck                            # tsc --noEmit
pnpm test --run --coverage                # all Vitest tests
pnpm test --run src/path/to/file.test.ts  # single test file
```

### Infrastructure (Terraform)

```bash
cd infra
terraform fmt -check -recursive           # format check
terraform init -backend=false && terraform validate  # syntax validation
```

### Spec & Doc Quality

The `spec-quality.yml` workflow runs markdownlint on all `.md` files and checks that every `REQ-ID` in `specs/` has a `source_legacy:` line within 20 lines of its declaration (the `legacy-traceability` gate).

## Ferramentas Aprovadas — Somente Estas

Toolchain fixa; misturar ferramentas quebra a rastreabilidade spec → code → test e as demos.

- **VS Code** / VS Code Insiders — editor único (as extensões e os kits Copilot do repositório assumem isso)
- **GitHub Copilot** (Ask + Plan + Agent) — IA principal; Copilot Workspace permitido para Issue → PR
- **GitHub Copilot CLI** *(opcional)* — tarefas em terminal
- **GitHub Spec-Kit** (`Specify CLI` + `/speckit.*`) — Spec-Driven Development
- **GitHub** (Issues, PRs, Actions, Projects) — fonte da verdade
- **Docker / Docker Compose** — paridade local · **Terraform** — IaC (Azure provider)

**Não use** outros assistentes de IA (Cursor, Windsurf, Codex, Cline, Continue, Aider, Codeium, Tabnine), IDEs alternativos (IntelliJ, Eclipse, Neovim), UIs web de chat para gerar código, nem frameworks SDD alternativos (Kiro etc.).

## Stack-Alvo

- **Backend:** Java 21 + Spring Boot 3.3 + JPA/Hibernate + PostgreSQL 16
- **Frontend:** Next.js 15 (App Router) + TypeScript 5 (strict) + Tailwind CSS + shadcn/ui
- **Containers:** Docker + Docker Compose
- **IaC:** Terraform (Azure provider ~> 3.x)
- **CI/CD:** GitHub Actions
- **Testing:** JUnit 5 + Testcontainers (backend); Vitest + Testing Library (frontend)

## Regras de Geração de Código

### Java
- Use recursos do Java 21: records para DTOs, sealed interfaces para uniões discriminadas, pattern matching, virtual threads
- Use `Optional` corretamente — nunca retorne `null` de métodos públicos
- `@Transactional` somente na camada de service, nunca em repositories
- Valide entradas na camada de controller com `@Valid` + Bean Validation
- Nomes de classes em inglês; comentários em inglês
- Testes unitários são obrigatórios para lógica de negócio
- Nunca exponha dados sensíveis (CPF, valores de benefício) em logs — mascare-os

### TypeScript / Next.js
- `strict: true` em `tsconfig.json` — sem exceções
- Use server actions para mutations; nunca exponha secrets em client components
- Prefira `async/await` a cadeias `.then()`
- Somente named exports — sem default exports em arquivos de componentes

### REST APIs
- Convenção de path: `/api/v1/{resource}`
- Use verbos HTTP corretamente (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`)
- Retorne status codes apropriados (`201` para criação, `204` para sem conteúdo, `409` para conflito)
- Todos os endpoints devem ter annotations OpenAPI/Swagger

### Terraform
- Todo recurso deve ter `tags` incluindo `project`, `environment`, `owner`
- Secrets somente via `azurerm_key_vault_secret` — nunca em `locals` ou `variables`
- Um módulo por área de serviço Azure (networking, compute, database, monitoring)
- `terraform fmt` e `terraform validate` devem passar antes do commit

## Regras de Segurança (OWASP Top 10)

- Valide entradas em toda fronteira do sistema
- Nunca faça hardcode de secrets, API keys ou credenciais
- Consultas SQL somente via JPA/JPQL — sem concatenação de strings
- CORS configurado explicitamente — sem wildcard `*` em produção
- Autenticação via OAuth2/JWT (Spring Security no backend)
- Todos os recursos Azure usam Managed Identity para autenticação serviço-a-serviço

## Spec-Driven Development (Spec-Kit)

- Todo requisito usa **notação EARS** (Easy Approach to Requirements Syntax)
- Todo requisito tem um **REQ-ID** único no formato `REQ-NNN`
- **Todo requisito carrega uma linha `source_legacy:`** apontando para `01-arqueologia/legado-sifap/natural-programs/*.NSN`, `01-arqueologia/legado-sifap/adabas-ddms/*.ddm` ou `[GREENFIELD] + justificativa`. O job de CI `legacy-traceability` rejeita PRs que violam isso. Consulte [`01-arqueologia/LEGACY-EXPLORATION-CHECKLIST.md`](../01-arqueologia/LEGACY-EXPLORATION-CHECKLIST.md).
- Testes rastreiam para REQ-IDs por comentários inline
- Estratégia de branch: `spec/<NNN>-<feature>` → `develop` → `main` (sem `stage`; ver [`00-GIT-WORKFLOW.md`](../00-GIT-WORKFLOW.md))
- Antes de escrever EARS no Estágio 2, o par DEVE ter lido os programas Natural atribuídos (HARD GATE — ver checklist acima)

### Spec-Kit Workflow (recommended order)

1. `/speckit.specify` — generate `spec.md` with EARS requirements and `source_legacy:` lines
2. `/speckit.clarify` — ask targeted questions until no critical gaps remain
3. `/speckit.plan` — produce `plan.md` with architecture and risks
4. `/speckit.tasks` — break plan into ordered, testable tasks in `tasks.md`
5. `/speckit.analyze` — cross-artifact consistency check (non-destructive)
6. `/speckit.implement` — execute tasks (or use Agent Mode)

Feature specs live in `specs/NNN-feature-name/` (see `specs/001-example-feature/` for the template).

## Branch & Commit Conventions

Branches follow persona-based prefixes from `develop`:

| Prefix | Stage | Who |
|--------|-------|-----|
| `spec/<NNN>-<feature>` | 2 — Spec | RE + SA |
| `impl/<module>-<feature>` | 3 — Impl | Dev + DBA |
| `test/<feature>` | 3 — Tests | QA |
| `infra/<component>` | 4 — Infra | DevOps |
| `docs/<topic>` | Transversal | Tech Writer |
| `agent/<issue-NN>` | 4 — Delegation | Copilot Agent |

Commit messages use conventional prefixes and must cite REQ-IDs when applicable:

```
feat: Implements REQ-PAY-001 (ciclo mensal de pagamento)
fix: corrige cálculo de desconto judicial (REQ-PAY-DSCT-01)
test: cobertura de aceitação para REQ-BEN-03
db: V2__add_payment_status (REQ-PAY-04)
docs: ADR-002 sobre transações Spring
```

Merge strategy: **squash merge** to `develop`. Only the team lead merges `develop → main`.

## Regras Rígidas — Não Faça Isto

- ❌ Não gere código da nova aplicação sem antes ler o legado em `01-arqueologia/legado-sifap/` e ter um REQ-ID com `source_legacy:`
- ❌ Não adicione dependências sem justificativa em um ADR
- ❌ Não escreva testes depois do fato — escreva-os enquanto implementa
- ❌ Não exponha secrets em mensagens de commit, logs ou descrições de PR
- ❌ Não faça merge em `main` sem pelo menos uma revisão entre pares
- ❌ Não pule as conversas guiadas de passagem nas transições de estágio ([`00-TEAM-FLOW.md`](../00-TEAM-FLOW.md))

## Copilot Agent Ecosystem

This repository ships a rich set of Copilot customizations in `.github/`:

- **29 agents** (`.github/agents/`) — persona agents (e.g., `builder.agent.md`, `dba.agent.md`) and Spec-Kit workflow agents (`speckit.*.agent.md`)
- **60+ prompts** (`.github/prompts/`) — stage-specific and persona-specific prompt templates
- **14 skills** (`.github/skills/`) — reusable skills like `tdd-workflow`, `ears-validate`, `adr-draft`, `iac-review`
- **11 file-scoped instructions** (`.github/instructions/`) — auto-applied by Copilot based on file glob patterns (see `README.md` in that directory for the index)

## Referências

- 3 modos do Copilot (Ask · Plan · Agent): [`09-cheat-sheets/copilot-3-modes.md`](../09-cheat-sheets/copilot-3-modes.md)
- Persona kits (já incluídos no `.github/` deste repositório): [`05-personas/`](../05-personas/) · Agentes de estágio: [`06-agentes-de-estagio/`](../06-agentes-de-estagio/)
- A nova aplicação (backend Java + frontend Next.js + infra) é criada pelo time com o Spec-Kit no Estágio 3 — não há código de aplicação pronto neste repositório
- Spec-Kit SDD: <https://github.com/github/spec-kit>

<!-- SPECKIT START -->
Spec-Kit is configured in `.specify/` with sequential branch numbering and Copilot integration.
Feature specs go in `specs/NNN-feature-name/` — see `specs/001-example-feature/` for the template structure.
<!-- SPECKIT END -->
