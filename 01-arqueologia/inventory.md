<!-- markdownlint-disable MD013 MD025 MD026 MD028 MD029 MD034 MD040 MD051 MD060 -->

# Inventário Legado — [Nome da Equipe]

> **Data:** 2026-06-10
> **Agente:** `@archaeologist` · prompt `/archaeology-kickoff`
> **Escopo escaneado:** `01-arqueologia/legado-sifap/`
> **Status:** 🔍 Primeira passada — baseada **apenas em nomes de arquivos e estrutura de pastas**. Nenhum arquivo foi aberto/lido. Será revisada conforme a equipe ler os programas individuais (`/extract-business-rules`, `/map-dependencies`).

---

## Estrutura de Pastas

```text
legado-sifap/
├── README.md
├── COMO-LER-NATURAL.md
├── natural-programs/
│   ├── README.md
│   ├── BATCHCON.NSN
│   ├── BATCHPGT.NSN
│   ├── BATCHREL.NSN
│   ├── CADBENEF.NSN
│   ├── CADDEPEND.NSN
│   ├── CADPROG.NSN
│   ├── CALCBENF.NSN
│   ├── CALCCORR.NSN
│   ├── CALCDSCT.NSN
│   ├── CONSBENF.NSN
│   ├── RELAUDIT.NSN
│   ├── RELPGT.NSN
│   ├── VALBENEF.NSN
│   ├── VALDOCS.NSN
│   └── VALELEG.NSN
├── adabas-ddms/
│   ├── README.md
│   ├── AUDITORIA.ddm
│   ├── BENEFICIARIO.ddm
│   ├── PAGAMENTO.ddm
│   └── PROGRAMA-SOCIAL.ddm
└── legacy-docs/
    ├── README.md
    ├── ARQUITETURA-ORIGINAL-1997.md
    ├── ARQUITETURA-ORIGINAL-1997.docx
    ├── MANUAL-TECNICO-SIFAP-2008.md
    ├── MANUAL-TECNICO-SIFAP-2008.docx
    ├── REGRAS-NEGOCIO-2012.md
    └── REGRAS-NEGOCIO-2012.docx
```

**Total de diretórios:** 4 — `legado-sifap/` (raiz) + 3 subdiretórios (`natural-programs/`, `adabas-ddms/`, `legacy-docs/`).

> ℹ️ Verificação sugerida (uma segunda pessoa roda e confere):
>
> ```bash
> find 01-arqueologia/legado-sifap -type d            # → 4 diretórios
> find 01-arqueologia/legado-sifap -type f | wc -l    # → contagem total de arquivos
> ```

---

## Contagem de Arquivos por Tipo

| Extensão | Contagem | Finalidade provável |
| -------- | -------- | ------------------- |
| `.NSN`   | 15       | Programa-fonte Natural (Software AG / mainframe) |
| `.ddm`   | 4        | Data Definition Module — definição lógica de acesso a arquivo Adabas |
| `.md`    | 8        | Markdown — READMEs de navegação + documentação legada convertida |
| `.docx`  | 3        | Documento Word — formato original da documentação legada |

**Total de arquivos:** 30.

> ℹ️ Contagem por extensão verificável com:
>
> ```bash
> find 01-arqueologia/legado-sifap -name '*.NSN'  | wc -l   # → 15
> find 01-arqueologia/legado-sifap -name '*.ddm'  | wc -l   # → 4
> find 01-arqueologia/legado-sifap -name '*.md'   | wc -l   # → 8
> find 01-arqueologia/legado-sifap -name '*.docx' | wc -l   # → 3
> ```

> ⚠️ "Finalidade provável" usa apenas conhecimento genérico de Natural/Adabas. O conteúdo real de cada arquivo só será confirmado na leitura (Estágios seguintes).

---

## Padrões de Convenção de Nomes

Agrupamento pelo prefixo dos 15 programas `.NSN` (sem abrir os arquivos).

| Prefixo | Contagem | Arquivos | Hipótese (conhecimento genérico Natural) |
| ------- | -------- | -------- | ---------------------------------------- |
| `BATCH` | 3 | `BATCHCON`, `BATCHPGT`, `BATCHREL` | Programas de processamento batch (entry points de job/scheduler) |
| `CAD`   | 3 | `CADBENEF`, `CADDEPEND`, `CADPROG` | Programas de cadastro/CRUD (`CAD` = cadastro) |
| `CALC`  | 3 | `CALCBENF`, `CALCCORR`, `CALCDSCT` | Rotinas de cálculo (`CALC`) — provável lógica financeira |
| `VAL`   | 3 | `VALBENEF`, `VALDOCS`, `VALELEG` | Rotinas de validação (`VAL`) |
| `REL`   | 2 | `RELAUDIT`, `RELPGT` | Geração de relatórios (`REL` = relatório) |
| `CONS`  | 1 | `CONSBENF` | Consulta (`CONS`) — singleton, ver "Itens Incomuns" |

**Padrões com 2+ arquivos identificados:** 5 (`BATCH`, `CAD`, `CALC`, `VAL`, `REL`).

> Observação estrutural: quatro grupos (`BATCH`, `CAD`, `CALC`, `VAL`) têm exatamente 3 membros; `REL` tem 2 e `CONS` tem 1 — assimetria a investigar na leitura.

---

## Itens Incomuns (Top 3)

| # | Item | File path | O que o torna incomum | Ação de investigação sugerida |
| - | ---- | --------- | --------------------- | ----------------------------- |
| 1 | Documentos `.docx` binários | `01-arqueologia/legado-sifap/legacy-docs/*.docx` | Única extensão binária do acervo (3 arquivos), duplicando o conteúdo dos `.md` de mesmo nome — risco de divergência entre fonte e cópia | Comparar cada par `.docx`/`.md`; decidir qual é a fonte da verdade antes de citar como evidência |
| 2 | `CONSBENF.NSN` (prefixo singleton) | `01-arqueologia/legado-sifap/natural-programs/CONSBENF.NSN` | Único programa com prefixo `CONS`, enquanto os demais formam grupos de 3 — padrão de nome que ocorre uma única vez | Verificar se é realmente standalone ou se há consultas relacionadas com outro prefixo (ex.: telas 3270) |
| 3 | Grupo `REL` incompleto vs. demais | `01-arqueologia/legado-sifap/natural-programs/RELAUDIT.NSN`, `RELPGT.NSN` | Grupo de relatórios tem 2 membros enquanto `BATCH`/`CAD`/`CALC`/`VAL` têm 3 — quebra de simetria estrutural | Confirmar se falta um relatório esperado ou se um relatório usa prefixo diferente |

> Nota: a contagem de "incomum" é feita só por nome/estrutura. Tamanho de arquivo e profundidade de aninhamento são uniformes (2 níveis) neste acervo; por isso os critérios usados foram **extensão única** e **padrão de nome de ocorrência única/assimétrica**.

---

## Ordem de Leitura Proposta

> ⚠️ **Hipótese de leitura, não verdade.** A ordem real mudará quando a equipe rastrear dependências (`CALLNAT`, `READ`/`STORE`). Use isto apenas como ponto de partida.

1. **DDMs primeiro — entender os dados antes do código** (`adabas-ddms/`):
   `BENEFICIARIO.ddm` → `PROGRAMA-SOCIAL.ddm` → `PAGAMENTO.ddm` → `AUDITORIA.ddm`.
2. **Entry points batch — revelam o fluxo de negócio** (`BATCH*` costuma orquestrar via `CALLNAT`):
   `BATCHPGT.NSN` → `BATCHCON.NSN` → `BATCHREL.NSN`.
3. **Programas mais conectados — cálculo e validação** (prováveis subprogramas chamados pelos batch/cadastro):
   `CALCBENF.NSN`, `CALCCORR.NSN`, `CALCDSCT.NSN`, `VALBENEF.NSN`, `VALDOCS.NSN`, `VALELEG.NSN`.
4. **Cadastro/CRUD — entidades centrais** (`CAD*`):
   `CADBENEF.NSN`, `CADDEPEND.NSN`, `CADPROG.NSN`.
5. **Consulta e relatórios — caminhos de leitura** (`CONS*`, `REL*`):
   `CONSBENF.NSN`, `RELPGT.NSN`, `RELAUDIT.NSN`.

**Justificativa:** dados antes de lógica (DDMs primeiro); batch como porta de entrada do fluxo; cálculo/validação por serem candidatos a maior conectividade `CALLNAT`; cadastro como produtor das entidades; consulta/relatório por serem majoritariamente consumidores de leitura.

---

## Próximos Passos

- [ ] Equipe preenche `[Nome da Equipe]` no título.
- [ ] Segunda pessoa valida as contagens com `find` (comandos acima).
- [ ] Iniciar `/map-dependencies` para confirmar/revisar a ordem de leitura.
- [ ] Iniciar `/extract-business-rules` programa a programa.
- [ ] Revisitar este inventário ao fim do Estágio 1 e marcar divergências encontradas.
