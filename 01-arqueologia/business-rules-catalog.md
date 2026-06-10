<!-- markdownlint-disable MD013 MD025 MD026 MD028 MD029 MD033 MD034 MD040 MD051 MD060 -->

# Catálogo de Regras de Negócio — SIFAP Legado

![ESTÁGIO 01 Arqueologia](https://img.shields.io/badge/ESTÁGIO-01%20Arqueologia-F25022?style=for-the-badge) ![TIPO Worksheet](https://img.shields.io/badge/TIPO-Worksheet-1A1A1A?style=for-the-badge) ![PREENCHA Durante S1](https://img.shields.io/badge/PREENCHA-Durante%20S1-737373?style=for-the-badge)

> 🗺 **Você está aqui:** [Kit PT-BR](../README.md) → [Estágio 1](README.md) → **business-rules-catalog**

> **Para quem é isto?** Este é um **artefato preenchido pelo time** durante o Estágio 1 (Arqueologia).
>
> **O que você terá ao final do estágio:**
>
> 1. Este documento totalmente preenchido com os dados reais do legado SIFAP
> 2. Rastreabilidade para `01-arqueologia/legado-sifap/` (programas `.NSN` e DDMs)
> 3. Base de evidência usada nas EARS do Estágio 2 (`source_legacy:`)
>
> 📘 **Guia passo a passo:** [`GUIDE.md`](GUIDE.md).

> Registre aqui todas as regras de negócio extraídas do código Natural/Adabas.
> Cada regra precisa ter rastreabilidade até o código-fonte.
>
> **REGRA DURA:** linhas com `Programa Fonte` vazio são **inválidas** e não contam para o gate do Estágio 2. Use o formato `01-arqueologia/legado-sifap/natural-programs/ARQUIVO.NSN#L<inicio>-L<fim>` sempre que possível. Mínimo aceito: nome do arquivo .NSN.

## Como pensar em "regra de negócio"

O que conta:

- Um `IF` que decide algo no domínio (ex.: _"se a UF é do Nordeste e o programa é Seca, valor base × 1.2"_)
- Uma constante numérica sem explicação (ex.: `0.075` num cálculo de imposto)
- Uma transição de status com regra (ex.: _"só de A para S, nunca de I para A"_)
- Um tratamento especial para um caso (ex.: _"se o CPF começa com 999, é teste"_)

O que NÃO conta: paginação de relatório, formatação de saída, manipulação de cursor Adabas, abertura de arquivo. Ignore esses detalhes de implementação.

## Níveis de Risco

| Nível       | Descrição                                                     |
| ----------- | ------------------------------------------------------------- |
| **CRÍTICO** | Regra financeira ou de segurança — erro causa prejuízo direto |
| **ALTO**    | Regra de negócio central — afeta fluxo principal              |
| **MÉDIO**   | Regra de validação ou formatação — afeta qualidade dos dados  |
| **BAIXO**   | Regra de apresentação ou conveniência — impacto limitado      |

## Regras Encontradas

| ID     | Regra de Negócio | Programa Fonte | Campos DDM | Nível de Risco | Notas |
| ------ | ---------------- | -------------- | ---------- | -------------- | ----- |
| BR-001 |                  |                |            |                |       |
| BR-002 |                  |                |            |                |       |
| BR-003 |                  |                |            |                |       |
| BR-004 |                  |                |            |                |       |
| BR-005 |                  |                |            |                |       |
| BR-006 |                  |                |            |                |       |
| BR-007 |                  |                |            |                |       |
| BR-008 |                  |                |            |                |       |
| BR-009 |                  |                |            |                |       |
| BR-010 |                  |                |            |                |       |
| BR-011 |                  |                |            |                |       |
| BR-012 |                  |                |            |                |       |
| BR-013 |                  |                |            |                |       |
| BR-014 |                  |                |            |                |       |
| BR-015 |                  |                |            |                |       |

> Adicione mais linhas conforme necessário. Lembre-se: existem **10 regras escondidas** no código!

## Exemplo de linha bem preenchida

| ID     | Regra de Negócio                                                                        | Programa Fonte                                   | Campos DDM                                                               | Nível de Risco | Notas                                      |
| ------ | --------------------------------------------------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------ | -------------- | ------------------------------------------ |
| BR-013 | Desconto total não pode exceder 30% do valor bruto, exceto descontos judiciais (tipo J) | `01-arqueologia/legado-sifap/natural-programs/CALCDSCT.NSN#L142-L148` | `PAGAMENTO.VLR-BRUTO`, `PAGAMENTO.VLR-TOTAL-DSCT`, `PAGAMENTO.TIPO-DSCT` | CRÍTICO        | Regra financeira. Tipo 'J' = exceção legal |

## Regras por Categoria

### Cálculos Financeiros

<!-- Liste aqui as regras relacionadas a cálculos de valores, benefícios, etc. -->

### Validações de Status

<!-- Liste aqui as regras de transição de status (A, S, C, I, D) -->

### Regras de Autorização

<!-- Liste aqui as regras de quem pode fazer o quê -->

### Regras de Negócio Temporais

<!-- Liste aqui regras com prazos, datas-limite, períodos -->

## Resumo Estatístico

- Total de regras encontradas: \_\_\_
- Regras críticas: \_\_\_
- Regras com duplicação: \_\_\_
- Regras sem documentação (escondidas): \_\_\_

---

## Regras de BATCHPGT.NSN

> **Programa:** [`legado-sifap/natural-programs/BATCHPGT.NSN`](legado-sifap/natural-programs/BATCHPGT.NSN) (377 linhas) — geração do ciclo mensal de pagamentos (batch).
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md) (LEVANTAMENTO PARCIAL — o próprio doc se declara não validado).
> **Achado estrutural:** o cabeçalho do programa diz "CHAMA CALCBENF E CALCDSCT", mas **não há nenhum `CALLNAT`** — toda a lógica de cálculo e desconto está **inline e simplificada**. Isso gera divergências com a versão online (ver mistérios M-BP-01/02/05).

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| BP-01 | Se o CPF lido for igual ao do registro imediatamente anterior, o sistema deve ignorar o registro (deduplicação dependente da ordenação por CPF). | Unwanted | [BATCHPGT.NSN#L188-L191](legado-sifap/natural-programs/BATCHPGT.NSN#L188) | Inferida | Depende do `READ ... BY CPF` (L182). Comentário L178-L179 alerta que sistemas downstream dependem da ordenação. Otimização técnica, não regra de domínio. |
| BP-02 | Quando o ciclo processa um beneficiário, se o STATUS for diferente de `'A'` (ativo), o sistema deve ignorá-lo e não gerar pagamento. | Unwanted | [BATCHPGT.NSN#L195-L198](legado-sifap/natural-programs/BATCHPGT.NSN#L195) | Confirmada | Casa com RN-002 / RN-011 (status do beneficiário: `'A'` ativo, `'E'` excluído) em REGRAS-NEGOCIO-2012.md. |
| BP-03 | Se já existe pagamento para o beneficiário na competência corrente, o sistema deve ignorá-lo (idempotência do ciclo mensal). | Unwanted | [BATCHPGT.NSN#L202-L210](legado-sifap/natural-programs/BATCHPGT.NSN#L202) | Inferida | `FIND PAGAMENTO-V` + comparação de `COMPETENCIA`. Sem suporte documental explícito; protege contra reprocessamento. |
| BP-04 | Quando gerar pagamento, se o programa social vinculado ao beneficiário não existir, o sistema deve registrar erro em log e não gerar pagamento. | Unwanted | [BATCHPGT.NSN#L214-L226](legado-sifap/natural-programs/BATCHPGT.NSN#L214) | Confirmada | Casa com RN-003 (benefício exige vínculo a programa social ativo). |
| BP-05 | Se o programa social vinculado não estiver ativo (`STATUS-PROG ≠ 'A'`), o sistema deve ignorar o beneficiário. | Unwanted | [BATCHPGT.NSN#L227-L230](legado-sifap/natural-programs/BATCHPGT.NSN#L227) | Confirmada | Complementa RN-003 (programa social **ativo**). |
| BP-06 | Ao calcular o benefício, o sistema deve aplicar um fator regional de uma tabela indexada pelo código de região, para regiões de 1 a 25; fora desse intervalo aplica fator 1,0000. | Event-driven | [BATCHPGT.NSN#L240-L244](legado-sifap/natural-programs/BATCHPGT.NSN#L240) | Mistério | <!-- mystery: a tabela #TAB-REG tem 27 entradas, RN-005 documenta regiões 01-27, mas o IF limita a <= 25; regiões 26/27 caem no ELSE=1,0000. Coincidência: tabela 26/27 também valem 1,0000, então não há bug observável — mas o limite 25 não tem origem clara --> |
| BP-07 | Ao calcular o benefício, o sistema deve aplicar fator familiar escalonado: 0 dependentes → 1,0; até 2 → 1,0 + 0,05×dep; até 4 → 1,10 + 0,03×(dep−2); acima → 1,16 + 0,02×(dep−4). | Event-driven | [BATCHPGT.NSN#L247-L259](legado-sifap/natural-programs/BATCHPGT.NSN#L247) | Mistério | <!-- mystery: a fórmula é MULTIPLICATIVA (fator), mas RN-013 documenta acréscimo ADITIVO por dependente (VALOR-BASE + ACRESCIMO × dependentes). Código e doc descrevem modelos de cálculo incompatíveis --> |
| BP-08 | Ao calcular o benefício, o sistema deve aplicar o fator da primeira faixa de renda cujo limite seja maior ou igual à renda familiar (5 faixas; renda menor → fator maior). | Event-driven | [BATCHPGT.NSN#L262](legado-sifap/natural-programs/BATCHPGT.NSN#L262), [#L367-L375](legado-sifap/natural-programs/BATCHPGT.NSN#L367) | Confirmada | Sub-rotina `DET-FAIXA-RENDA-BATCH`. Casa com RN-018 (faixa de renda: primeira faixa cujo limite ≥ renda). |
| BP-09 | Ao calcular o benefício, o sistema deve aplicar fator idade: ≥65 → 1,15; ≥60 → 1,10; <18 → 1,05; caso contrário 1,0. | Event-driven | [BATCHPGT.NSN#L265-L277](legado-sifap/natural-programs/BATCHPGT.NSN#L265) | Mistério | <!-- mystery: sem suporte documental. O bônus para <18 (titular menor de idade recebendo fator 1,05) é contraintuitivo e não consta em REGRAS-NEGOCIO-2012.md --> |
| BP-10 | O sistema deve calcular o valor do benefício como base × fator regional × fator familiar × fator renda × fator idade × (1 + fator reajuste), truncando para 2 casas decimais. | Ubiquitous | [BATCHPGT.NSN#L280-L285](legado-sifap/natural-programs/BATCHPGT.NSN#L280) | Mistério | <!-- mystery: o truncamento (×100 // /100) confirma RN-013/RN-014 (truncar, não arredondar), MAS a fórmula multiplicativa contradiz a fórmula aditiva de RN-013. Além disso, L281 (#FATOR-RND × #FATOR-IDADE) parece comentado/continuação — verificar se ambos os fatores realmente entram no produto --> |
| BP-11 | Quando a competência for dezembro (mês 12), o sistema deve adicionar 13º (base × fator regional × fator idade, truncado) ao valor bruto. | Event-driven | [BATCHPGT.NSN#L292-L297](legado-sifap/natural-programs/BATCHPGT.NSN#L292) | Confirmada | Casa com RN-014 (cálculo especial de dezembro: 13º). Nota: o 13º omite fator familiar e fator renda — inconsistente com a fórmula principal (BP-10). |
| BP-12 | Quando a competência for dezembro e o tipo do programa for `'A'`, o sistema deve adicionar abono natalino de 15% do valor do benefício. | Event-driven | [BATCHPGT.NSN#L298-L303](legado-sifap/natural-programs/BATCHPGT.NSN#L298) | Confirmada | Casa com RN-014 (abono natalino em dezembro). Magic number 0,15 hardcoded. |
| BP-13 | Se o valor bruto for maior que 500,00, o sistema deve aplicar desconto de 3% (truncado); caso contrário, desconto zero. | Event-driven | [BATCHPGT.NSN#L307-L312](legado-sifap/natural-programs/BATCHPGT.NSN#L307) | Mistério | <!-- mystery: comentado "CALC DESCONTOS SIMPLIFICADO". Contradiz RN-021 (desconto máximo 30%, com tipos e exceção judicial via CALCDSCT). O mesmo beneficiário pode receber líquido diferente conforme o caminho (batch 3% fixo vs. online CALCDSCT) --> |
| BP-14 | Após aplicar descontos, se o valor líquido for negativo, o sistema deve fixá-lo em zero (piso). | Unwanted | [BATCHPGT.NSN#L315-L318](legado-sifap/natural-programs/BATCHPGT.NSN#L315) | Inferida | Proteção contra líquido negativo. Sem suporte documental explícito. |
| BP-15 | Quando todos os fatores e descontos forem calculados, o sistema deve persistir o pagamento gerado (STORE) e acumular os totais do ciclo. | Event-driven | [BATCHPGT.NSN#L320-L335](legado-sifap/natural-programs/BATCHPGT.NSN#L320) | Inferida | `STORE PAGAMENTO-V` (L335). Acumuladores de bruto/desconto/líquido/abono para o resumo final. |
| BP-16 | A cada 1000 pagamentos gerados, o sistema deve emitir uma linha de log de progresso. | Ubiquitous | [BATCHPGT.NSN#L345-L347](legado-sifap/natural-programs/BATCHPGT.NSN#L345) | Inferida | Operacional (checkpoint de log), não regra de domínio. Baixo valor para a spec moderna. |

### Rascunho EARS (regras confirmadas)

- **REQ (BP-02):** _Quando_ o ciclo mensal processa um beneficiário, _se_ o `STATUS` do beneficiário for diferente de `'A'`, _então_ o sistema **deverá** não gerar pagamento e contabilizá-lo como ignorado.
- **REQ (BP-04):** _Quando_ o ciclo gera um pagamento, _se_ o programa social vinculado não existir, _então_ o sistema **deverá** registrar erro em log e não gerar o pagamento.
- **REQ (BP-05):** _Se_ o programa social vinculado não estiver ativo (`STATUS-PROG ≠ 'A'`), _então_ o sistema **deverá** ignorar o beneficiário no ciclo.
- **REQ (BP-08):** _Quando_ o sistema calcula o benefício, ele **deverá** aplicar o fator da primeira faixa de renda cujo limite seja maior ou igual à renda familiar do beneficiário.
- **REQ (BP-11):** _Quando_ a competência for dezembro, o sistema **deverá** adicionar o 13º (base × fator regional × fator idade) ao valor bruto do pagamento.
- **REQ (BP-12):** _Quando_ a competência for dezembro _e_ o tipo do programa for `'A'`, o sistema **deverá** adicionar abono natalino de 15% do valor do benefício.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-BP-01** — Cabeçalho declara `CHAMA CALCBENF E CALCDSCT`, mas não há `CALLNAT`; cálculo e desconto inlinados e simplificados. Risco de divergência batch vs. online.
- **M-BP-02** — Fórmula de benefício **multiplicativa** (BP-07/BP-10) contradiz a fórmula **aditiva** documentada em RN-013.
- **M-BP-03** — Limite de região `<= 25` (BP-06) não bate com a tabela de 27 entradas nem com RN-005 (regiões 01-27).
- **M-BP-04** — Fator idade para `<18` = 1,05 (BP-09) sem suporte documental; titular menor recebendo bônus.
- **M-BP-05** — Desconto fixo de 3% (BP-13) contradiz RN-021 (máx. 30%, tipos e exceção judicial).
- **M-BP-06** — 13º (BP-11) usa conjunto de fatores diferente do cálculo principal (omite fator familiar e fator renda).

---

### Continuar a leitura

<table width="100%">
<tr>
<td width="50%" valign="top" align="left">
<sub><strong>← ANTERIOR</strong></sub><br/>
<a href="GUIDE.md"><strong>GUIDE do Estágio 1</strong></a><br/>
<sub>Passo a passo do estágio.</sub>
</td>
<td width="50%" valign="top" align="right">
<sub><strong>PRÓXIMO →</strong></sub><br/>
<a href="dependency-map.md"><strong>dependency-map.md</strong></a><br/>
<sub>Mapa de quem chama quem.</sub>
</td>
</tr>
</table>

<sub>↑ <a href="README.md">Voltar ao Kit PT-BR</a></sub>
