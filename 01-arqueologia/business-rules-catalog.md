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

## Regras de CALCDSCT.NSN

> **Programa:** [`legado-sifap/natural-programs/CALCDSCT.NSN`](legado-sifap/natural-programs/CALCDSCT.NSN) (203 linhas) — cálculo de descontos e deduções compulsórias sobre o benefício (online).
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md), seções RN-021/022/023.
> **Achado que fecha lacuna:** RN-021 (nota) e a seção 6 do doc registravam a exceção judicial ao teto de 30% como "mencionada, mas **não confirmada no código** (CALCDSCT tinha acesso restrito)". A leitura **confirma** a exceção em [#L165-L169](legado-sifap/natural-programs/CALCDSCT.NSN#L165). Lacuna documental fechada (ver CD-08).

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| CD-01 | Ao buscar o pagamento informado, se o CPF do pagamento corresponder ao CPF informado, o sistema deve marcá-lo como localizado e capturar valor bruto e competência. | Event-driven | [CALCDSCT.NSN#L75-L79](legado-sifap/natural-programs/CALCDSCT.NSN#L75) | Inferida | Validação de consistência pagamento↔CPF. Sem suporte documental direto. |
| CD-02 | Se o pagamento informado não for encontrado, o sistema deve exibir "PAGAMENTO NAO ENCONTRADO" e encerrar sem calcular descontos. | Unwanted | [CALCDSCT.NSN#L82-L85](legado-sifap/natural-programs/CALCDSCT.NSN#L82) | Inferida | `ESCAPE ROUTINE`. Tratamento de erro. |
| CD-03 | Se o beneficiário do CPF informado não existir, o sistema deve exibir "BENEFICIARIO NAO ENCONTRADO" e encerrar. | Unwanted | [CALCDSCT.NSN#L91-L94](legado-sifap/natural-programs/CALCDSCT.NSN#L91) | Inferida | `ESCAPE ROUTINE`. Tratamento de erro. |
| CD-04 | O sistema deve sempre aplicar uma contribuição social obrigatória, com alíquota progressiva pela primeira faixa de bruto cujo limite seja ≥ ao valor bruto (500→3%, 1000→5%, 2000→7%, 9999,99→9%). | Ubiquitous | [CALCDSCT.NSN#L99](legado-sifap/natural-programs/CALCDSCT.NSN#L99), [#L58-L65](legado-sifap/natural-programs/CALCDSCT.NSN#L58), [#L192-L201](legado-sifap/natural-programs/CALCDSCT.NSN#L192) | Mistério | <!-- mystery: a existência da contribuição casa com RN-022 (tipo 03, previdenciária), mas as alíquotas progressivas 3/5/7/9% e os limites de faixa NÃO constam em nenhuma seção do doc. Origem das alíquotas desconhecida --> |
| CD-05 | O sistema deve limitar o total de descontos a 30% do valor bruto do benefício. | State-driven | [CALCDSCT.NSN#L102-L105](legado-sifap/natural-programs/CALCDSCT.NSN#L102), [#L165-L169](legado-sifap/natural-programs/CALCDSCT.NSN#L165) | Confirmada | Casa exatamente com RN-021 (teto de 30% do bruto). |
| CD-06 | Ao processar cada desconto cadastrado, se o desconto estiver fora da vigência (encerrado antes de hoje, ou ainda não iniciado), o sistema deve ignorá-lo. | Event-driven | [CALCDSCT.NSN#L112-L118](legado-sifap/natural-programs/CALCDSCT.NSN#L112) | Inferida | Dois `ESCAPE TOP` por `DT-FIM-DSCT`/`DT-INICIO-DSCT`. Regra de vigência não documentada em RN-021/022/023. |
| CD-07 | Para cada desconto cadastrado, o sistema deve calcular o valor conforme o tipo: judicial/pensão/administrativo → valor fixo se informado, senão percentual do bruto; imposto → percentual do bruto; sindical → 1% fixo do bruto. | Event-driven | [CALCDSCT.NSN#L122-L162](legado-sifap/natural-programs/CALCDSCT.NSN#L122) | Mistério | <!-- mystery: os tipos no código são LETRAS (J/I/S/P/A/C) mas RN-022 documenta CÓDIGOS NUMÉRICOS (01-05). Não há mapeamento entre os dois esquemas. Além disso, o sindical de 1% (magic 0.01) não consta no doc --> |
| CD-08 | Ao aplicar o teto de 30%, o sistema não deve limitar descontos do tipo judicial (`'J'`) — retenções judiciais podem ultrapassar 30%. | State-driven | [CALCDSCT.NSN#L131-L132](legado-sifap/natural-programs/CALCDSCT.NSN#L131), [#L165-L169](legado-sifap/natural-programs/CALCDSCT.NSN#L165) | Confirmada | **Fecha lacuna:** RN-021 (nota) e seção 6 listavam a exceção judicial como não confirmada no código. Confirmada aqui: o teto só se aplica quando `#TIPO-DSCT NE 'J'`. |
| CD-09 | Após calcular o total de descontos, o sistema deve persistir o valor no campo de desconto do pagamento (UPDATE). | Event-driven | [CALCDSCT.NSN#L179-L183](legado-sifap/natural-programs/CALCDSCT.NSN#L179) | Inferida | `FIND PAGAMENTO-V` + `UPDATE` + `END TRANSACTION`. |
| CD-10 | O sistema deve truncar (não arredondar) os valores de desconto para 2 casas decimais. | Ubiquitous | [CALCDSCT.NSN#L104-L105](legado-sifap/natural-programs/CALCDSCT.NSN#L104), [#L174-L176](legado-sifap/natural-programs/CALCDSCT.NSN#L174) | Confirmada | Mesmo padrão de truncamento (×100 // /100) de RN-014. |

### Rascunho EARS (regras confirmadas)

- **REQ (CD-05):** O sistema **deverá** limitar o total de descontos aplicados a um benefício a, no máximo, 30% do seu valor bruto.
- **REQ (CD-08):** _Onde_ o desconto for do tipo judicial (`'J'`), o sistema **deverá** permitir que o desconto ultrapasse o teto de 30% do valor bruto.
- **REQ (CD-10):** _Quando_ o sistema calcula valores de desconto, ele **deverá** truncar (não arredondar) o resultado para 2 casas decimais.

### Confronto com BATCHPGT (mistério M-BP-05 do batch)

- O `CALCDSCT` aplica **teto de 30% com tipos diferenciados e exceção judicial** (CD-05/CD-07/CD-08), enquanto o `BATCHPGT` aplica **3% fixo acima de R$ 500** inline (BP-13). **São algoritmos de desconto incompatíveis.** Como o ciclo mensal usa o cálculo inline do batch (não há `CALLNAT` para CALCDSCT — ver M-BP-01), o desconto efetivo no pagamento mensal **não passa** por esta lógica. O `CALCDSCT` parece ser acionado apenas no fluxo online/avulso. → item de alta prioridade para `/catalog-mysteries` e para decisão de spec.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-CD-01** — Tipos de desconto no código são letras (`J/I/S/P/A/C`); RN-022 documenta códigos numéricos (`01-05`). Sem mapeamento conhecido.
- **M-CD-02** — RN-023 descreve descarte por prioridade numérica ao atingir 30%; o código processa na ordem do grupo PE e aplica o teto cumulativo por item, sem descarte por prioridade.
- **M-CD-03** — Alíquotas da contribuição social progressiva (3/5/7/9%) e seus limites de faixa não constam no doc.
- **M-CD-04** — O tipo `'C'` (contribuição) é tratado por sub-rotina separada (`CALC-CONTRIB-SOCIAL`) e somado **antes** do laço de teto; a interação entre a contribuição e o teto de 30% não é explícita.

---

## Regras de BATCHCON.NSN

> **Programa:** [`legado-sifap/natural-programs/BATCHCON.NSN`](legado-sifap/natural-programs/BATCHCON.NSN) (272 linhas) — batch de conciliação entre pagamentos do SIFAP e o arquivo de retorno bancário CNAB 240 do Banco do Brasil, com geração de trilha de auditoria.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** a doc de 2012 declara explicitamente (Seção 6) que as "Regras de conciliação financeira (BATCHCON)" **não foram levantadas** (responsável transferida para DEGED). Portanto **nenhuma** regra abaixo está confirmada na doc — todas são inferidas direto do código. O programa ainda carrega um bloco morto da extinta integração "Banco Real" (comentado) e reusa o status `'P'` com semântica ambígua.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| BC-01 | Antes de processar, o batch lê a tabela AUDITORIA em ordem decrescente de SEQ-AUDIT e captura o maior número de sequência existente, para continuar a numeração da auditoria a partir dele. | Event-driven | [BATCHCON.NSN#L87-L91](legado-sifap/natural-programs/BATCHCON.NSN#L87) | Inferida | Sequência de auditoria é gerada pela aplicação (não pelo banco). Sem trava de concorrência. |
| BC-02 | Cada registro lido do arquivo de retorno CNAB é parseado por posições fixas (banco 1-3, lote 4-7, tipo 8). | Ubiquitous | [BATCHCON.NSN#L110-L113](legado-sifap/natural-programs/BATCHCON.NSN#L110) | Inferida | Layout posicional rígido específico do CNAB 240 BB. |
| BC-03 | Somente registros de detalhe (tipo de registro = '3') são conciliados; qualquer outro tipo é ignorado (header/trailer/lote). | State-driven | [BATCHCON.NSN#L116-L118](legado-sifap/natural-programs/BATCHCON.NSN#L116) | Inferida | Magic value `'3'`. |
| BC-04 | Os campos relevantes do detalhe são extraídos por posição fixa: CPF (44-54), valor (120-134), data pgto (140-147), cód. retorno (231-232) e nº documento (74-83). | Ubiquitous | [BATCHCON.NSN#L120-L124](legado-sifap/natural-programs/BATCHCON.NSN#L120) | Inferida | Posições "mágicas" do layout BB. |
| BC-05 | O valor do retorno bancário vem em centavos e é convertido para reais dividindo por 100. | Ubiquitous | [BATCHCON.NSN#L129-L132](legado-sifap/natural-programs/BATCHCON.NSN#L129) | Inferida | Magic number `100`. |
| BC-06 | Um pagamento é considerado correspondente ao registro do banco quando o nº do documento bate com NUM-PAGTO **e** o CPF do beneficiário **e** a competência coincidem. | Event-driven | [BATCHCON.NSN#L139-L144](legado-sifap/natural-programs/BATCHCON.NSN#L139) | Inferida | Chave de casamento tripla (doc + CPF + competência). |
| BC-07 | Quando não há pagamento correspondente, o registro é contabilizado como "não encontrado", uma mensagem é escrita no relatório e o registro é descartado (segue para o próximo). | Unwanted | [BATCHCON.NSN#L146-L152](legado-sifap/natural-programs/BATCHCON.NSN#L146) | Inferida | Não-encontrados não geram auditoria, apenas log. |
| BC-08 | A diferença entre o valor líquido do SIFAP e o valor do banco é calculada em módulo (valor absoluto). | Ubiquitous | [BATCHCON.NSN#L155-L158](legado-sifap/natural-programs/BATCHCON.NSN#L155) | Inferida | Normaliza sinal antes da comparação de tolerância. |
| BC-09 | Se a diferença absoluta de valor for maior que R$ 0,01, o pagamento é marcado como divergente e gera registro de auditoria de divergência (ação 'DV'). | State-driven | [BATCHCON.NSN#L160-L167](legado-sifap/natural-programs/BATCHCON.NSN#L160) | Inferida | Tolerância de 1 centavo (magic number `0.01`). |
| BC-10 | Se a diferença estiver dentro da tolerância, o pagamento é conciliado e seu status é atualizado conforme o código de retorno bancário. | State-driven | [BATCHCON.NSN#L168-L171](legado-sifap/natural-programs/BATCHCON.NSN#L168) | Inferida | Entra no `DECIDE ON #COD-RET`. |
| BC-11 | Código de retorno '00': status do pagamento vira 'P', grava a data de pagamento, fixa COD-BANCO = 1 e registra o código de retorno. | State-driven | [BATCHCON.NSN#L172-L180](legado-sifap/natural-programs/BATCHCON.NSN#L172) | Mistério | `'P'` aqui parece significar "Pago", mas em BATCHPGT/doc Seção 5 `'P'` = "Pendente"; COD-BANCO = 1 é magic number sem tabela. <!-- mystery: significado do status 'P' conflita entre BATCHPGT (pendente) e BATCHCON (pago?); origem do COD-BANCO=1 fixo --> |
| BC-12 | Código de retorno '01': status do pagamento vira 'D' e grava o código de retorno. | State-driven | [BATCHCON.NSN#L181-L187](legado-sifap/natural-programs/BATCHCON.NSN#L181) | Mistério | `'D'` provavelmente "Devolvido", não confirmado. <!-- mystery: dicionário de status 'D'/'E' e dos códigos de retorno '00'/'01'/'02' não documentado --> |
| BC-13 | Código de retorno '02': status do pagamento vira 'E' e grava o código de retorno. | State-driven | [BATCHCON.NSN#L188-L194](legado-sifap/natural-programs/BATCHCON.NSN#L188) | Mistério | `'E'` colide com status 'E' = "Erro" usado no batch de pagamento (doc Seção 5.2). <!-- mystery: 'E' significa 'Erro' ou 'Estornado'? colisão semântica com BATCHPGT --> |
| BC-14 | Código de retorno fora de {'00','01','02'} (NONE): nenhum status é alterado; apenas escreve "COD RETORNO DESCONHECIDO" no relatório. | Unwanted | [BATCHCON.NSN#L195-L199](legado-sifap/natural-programs/BATCHCON.NSN#L195) | Inferida | Conciliado conta +1 mesmo com retorno desconhecido (BC-10 já incrementou), mas status fica intocado. |
| BC-15 | Toda conciliação bem-sucedida gera registro de auditoria com ação 'CO', usuário 'BATCH', tabela 'PAGAMENTO' e descrição com o código de retorno. | Event-driven | [BATCHCON.NSN#L201](legado-sifap/natural-programs/BATCHCON.NSN#L201), [BATCHCON.NSN#L231-L246](legado-sifap/natural-programs/BATCHCON.NSN#L231) | Inferida | Subrotina GRAVA-AUDITORIA-CONC. |
| BC-16 | Toda divergência gera registro de auditoria com ação 'DV', guardando valor anterior (SIFAP) e valor novo (banco). | Event-driven | [BATCHCON.NSN#L167](legado-sifap/natural-programs/BATCHCON.NSN#L167), [BATCHCON.NSN#L248-L266](legado-sifap/natural-programs/BATCHCON.NSN#L248) | Inferida | Subrotina GRAVA-AUDITORIA-DIVERG. |
| BC-17 | Ao final, o batch imprime um resumo com totais de registros lidos, conciliados, divergentes, não encontrados e registros de auditoria gerados. | Event-driven | [BATCHCON.NSN#L213-L222](legado-sifap/natural-programs/BATCHCON.NSN#L213) | Inferida | Totalizadores de controle. |
| BC-18 | A integração com o "Banco Real" (cód. 356), com layout próprio (CPF 30-43, valor 100-112), está desativada — o bloco está comentado e mantido apenas como referência histórica. | Optional | [BATCHCON.NSN#L206-L229](legado-sifap/natural-programs/BATCHCON.NSN#L206) | Mistério | Código morto desde a aquisição pelo Santander (2007). <!-- mystery: confirmar se a conciliação multi-banco ainda é requisito ou se BB é o único canal vivo --> |

### Rascunho EARS (regras confirmadas)

> Nenhuma regra deste programa é **Confirmada** pela doc de 2012 (a própria doc declara BATCHCON como não levantado, Seção 6). O rascunho EARS abaixo lista as regras **inferidas de maior confiança estrutural**, a serem validadas no Estágio 2 antes de virarem REQ formais.

- **REQ (BC-03):** *While* o registro CNAB lido não for do tipo `'3'` (detalhe), the system *shall* ignorar o registro e seguir para o próximo.
- **REQ (BC-06):** *When* um registro de detalhe é processado, the system *shall* localizar o pagamento correspondente exigindo igualdade simultânea de número do documento, CPF do beneficiário e competência.
- **REQ (BC-07):** *When* nenhum pagamento correspondente é encontrado, the system *shall* contabilizar o registro como "não encontrado", registrá-lo no relatório e descartá-lo sem alterar dados.
- **REQ (BC-09):** *When* a diferença absoluta entre o valor líquido do SIFAP e o valor retornado pelo banco exceder R$ 0,01, the system *shall* marcar o pagamento como divergente e gravar auditoria de divergência preservando valor anterior e novo.
- **REQ (BC-10):** *When* a diferença de valor estiver dentro de R$ 0,01, the system *shall* conciliar o pagamento e atualizar seu status conforme o código de retorno bancário.
- **REQ (BC-15):** *When* uma conciliação é concluída, the system *shall* gerar um registro de auditoria com ação, usuário, tabela de referência, chave e descrição.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-BC-01** — Semântica do status `'P'` é contraditória: em BATCHCON o cód. retorno '00' (sucesso) grava `STATUS-PGTO = 'P'` (BC-11), mas a doc Seção 5.1 e o batch de pagamento usam `'P'` = "Pendente". Mesmo símbolo, dois significados opostos (pago vs. pendente). Necessita dicionário de status oficial.
- **M-BC-02** — Dicionário ausente: os valores de `COD-RETORNO` ('00','01','02') e os status resultantes ('P','D','E') não constam em nenhuma tabela/documento. 'E' colide com 'E' = "Erro" do BATCHPGT (doc Seção 5.2). Precisa de tabela de domínio confirmada.
- **M-BC-03** — `COD-BANCO = 1` é gravado como literal fixo na conciliação '00' (BC-11), sem tabela de bancos referenciada. Origem e significado do código 1 desconhecidos.
- **M-BC-04** — Conciliação multi-banco morta: o bloco "Banco Real" (cód. 356) está comentado desde 2005/2007 (BC-18). Indefinido se a conciliação deve suportar múltiplos bancos/layouts no sistema moderno ou apenas o BB. A doc de 2012 lista BATCHCON inteiro como pendente, sem mencionar o Banco Real.
- **M-BC-05** — Tolerância de R$ 0,01 (BC-09) e conversão centavos→reais por `/100` (BC-05) são magic numbers sem regra de negócio documentada; confirmar se a tolerância é política oficial ou heurística do desenvolvedor.
- **M-BC-06** — Registros com código de retorno desconhecido (NONE, BC-14) são contados como "conciliados" mas têm status preservado e geram auditoria 'CO' — comportamento possivelmente não intencional (concilia sem reconhecer o retorno).

---

## Regras de BATCHREL.NSN

> **Programa:** [`legado-sifap/natural-programs/BATCHREL.NSN`](legado-sifap/natural-programs/BATCHREL.NSN) (211 linhas) — gera relatório consolidado mensal sumarizando pagamentos por região, status e total geral, com saída flat-file para impressão em mainframe.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** O documento de 2012 não cobre BATCHREL (relatório consolidado) — a única menção próxima é "RELAUDIT" (módulo de auditoria, escopo diferente). Logo, quase todas as regras são **Inferidas** apenas do código. Há duas discrepâncias graves: (a) o mapeamento de região em faixas de 5 (`1-5`, `6-10`...) **contradiz** a RN-005 (regiões `01-27` = UFs); (b) o arredondamento usa `+0.005` ("round"), com comentário no código admitindo que **difere** do truncamento do CALCBENF (RN-014).

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| BRL-01 | O relatório é sempre parametrizado por uma competência informada pelo operador (`#COMPETENCIA`), e somente pagamentos dessa competência são considerados. | Event-driven | [BATCHREL.NSN#L107-L113](legado-sifap/natural-programs/BATCHREL.NSN#L107) | Inferida | `INPUT 'COMPETENCIA RELATORIO:'` + `READ ... BY COMPETENCIA` com `ESCAPE BOTTOM` ao sair da faixa. |
| BRL-02 | Para cada pagamento, a região do beneficiário é obtida buscando o `BENEFICIARIO` pelo CPF; se não encontrado, a região assume 0. | Event-driven | [BATCHREL.NSN#L115-L119](legado-sifap/natural-programs/BATCHREL.NSN#L115) | Inferida | `MOVE 0 TO #COD-REG` antes do `FIND`; se o FIND não retorna, permanece 0 → cai na região "default" (índice 5). |
| BRL-03 | O código de região é mapeado para 1 de 5 grupos por faixas numéricas: 1–5→1, 6–10→2, 11–15→3, 16–20→4, demais→5. | State-driven | [BATCHREL.NSN#L121-L138](legado-sifap/natural-programs/BATCHREL.NSN#L121) | **Mistério** | Contradiz RN-005 (regiões válidas `01-27` por UF). Faixas de 5 não correspondem às 5 macro-regiões nomeadas (NORTE…CENTRO-OESTE). <!-- mystery: mapeamento de COD-REGIAO em faixas de 5 não bate com RN-005 (01-27 = UF) nem com o nome das 5 macrorregiões; código 0 (beneficiário não encontrado) cai no grupo 5 (CENTRO-OESTE), poluindo o total. --> |
| BRL-04 | O valor bruto é "arredondado" somando 0,005 e reduzindo a 2 casas antes de acumular por região e no total geral. | Ubiquitous | [BATCHREL.NSN#L140-L147](legado-sifap/natural-programs/BATCHREL.NSN#L140) | **Mistério** | Comentário no próprio código: "ARREDONDAMENTO DIFERE DO CALCBENF (ROUND VS TRUNCATE)". RN-014 manda truncar; aqui há round half-up. Pode gerar divergência entre relatório e valores pagos. <!-- mystery: BATCHREL usa round (+0.005) enquanto RN-014/CALCBENF trunca; relatório consolidado pode não fechar com o pago. --> |
| BRL-05 | O valor de desconto e o valor líquido são acumulados por região **sem** o arredondamento aplicado ao bruto. | Ubiquitous | [BATCHREL.NSN#L148-L150](legado-sifap/natural-programs/BATCHREL.NSN#L148) | Inferida | Apenas `VLR-BRUTO` passa por `#VLR-ARR`; desconto e líquido são somados diretos — inconsistência interna de arredondamento. |
| BRL-06 | Os pagamentos são sumarizados por status em 5 categorias: G→Gerado(1), P→Pago(2), C→Cancelado(3), D→Devolvido(4), E→Estornado(5). | State-driven | [BATCHREL.NSN#L153-L171](legado-sifap/natural-programs/BATCHREL.NSN#L153) | Inferida | `DECIDE ON FIRST VALUE OF STATUS-PGTO`. Códigos de status só aparecem aqui; o doc não os cataloga. |
| BRL-07 | Qualquer status não previsto (G/P/C/D/E) é contabilizado como "GERADO" (índice 1). | Unwanted | [BATCHREL.NSN#L169-L170](legado-sifap/natural-programs/BATCHREL.NSN#L169) | **Mistério** | Cláusula `NONE → MOVE 1 TO #IDX-STS` mascara status inválidos dentro de "Gerado", distorcendo o resumo silenciosamente. <!-- mystery: status desconhecido é silenciosamente somado a GERADO; pode esconder dados inconsistentes na base. --> |
| BRL-08 | O total geral acumula o bruto arredondado, desconto, líquido e quantidade de todos os pagamentos lidos. | Ubiquitous | [BATCHREL.NSN#L173-L177](legado-sifap/natural-programs/BATCHREL.NSN#L173) | Inferida | Total geral usa `#VLR-ARR` (consistente com região), reforçando divergência só no eixo desconto/líquido. |
| BRL-09 | O relatório é paginado para impressora mainframe com 66 linhas por página e cabeçalho repetido a cada nova página. | Ubiquitous | [BATCHREL.NSN#L74-L77](legado-sifap/natural-programs/BATCHREL.NSN#L74), [#L199-L210](legado-sifap/natural-programs/BATCHREL.NSN#L199) | Inferida | `MOVE 66 TO #MAX-LINHAS`, `MOVE 99 TO #LINHA` força cabeçalho na 1ª página; subrotina `IMPRIME-CABECALHO` com form-feed. |
| BRL-10 | O relatório imprime três blocos: resumo por região (5 linhas), resumo por status (5 linhas) e uma linha de total geral. | Ubiquitous | [BATCHREL.NSN#L180-L198](legado-sifap/natural-programs/BATCHREL.NSN#L180) | Inferida | Estrutura fixa de saída; rótulos de região/status vêm de arrays inicializados em [#L73-L90](legado-sifap/natural-programs/BATCHREL.NSN#L73). |

### Rascunho EARS (regras confirmadas)

> Nenhuma regra deste programa pôde ser marcada como **Confirmada** contra o documento de 2012 — BATCHREL não é coberto por ele. As regras abaixo são as candidatas mais estáveis (lógica de relatório, não-financeira), propostas como EARS a partir do código:

- **REQ (BRL-01):** Quando o operador informa uma competência, o sistema DEVE incluir no relatório consolidado somente os pagamentos cuja competência seja igual à informada.
- **REQ (BRL-06):** O sistema DEVE sumarizar os pagamentos por status nas cinco categorias Gerado, Pago, Cancelado, Devolvido e Estornado, conforme os códigos `G`, `P`, `C`, `D`, `E`.
- **REQ (BRL-09):** O sistema DEVE paginar o relatório a cada 66 linhas, repetindo o cabeçalho (sistema, competência, data, página) no início de cada página.
- **REQ (BRL-10):** O sistema DEVE emitir três blocos consolidados — por região, por status e total geral — com quantidade e valores acumulados.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-BRL-01** — Mapeamento de região em faixas de 5 (`1-5`, `6-10`, `11-15`, `16-20`, resto) contradiz a RN-005 (regiões válidas `01-27` por UF) e não corresponde às 5 macro-regiões nomeadas no código (NORTE…CENTRO-OESTE). O critério de agrupamento é desconhecido e pode estar incorreto desde 1999. ([L121-L138](legado-sifap/natural-programs/BATCHREL.NSN#L121))
- **M-BRL-02** — Arredondamento por `+0.005` (round half-up) diverge do truncamento exigido pela RN-014/CALCBENF; o próprio código reconhece a diferença em comentário. Risco de o relatório consolidado não bater com os valores efetivamente pagos. ([L140-L147](legado-sifap/natural-programs/BATCHREL.NSN#L140))
- **M-BRL-03** — Inconsistência interna: o bruto é arredondado, mas desconto e líquido são acumulados sem arredondamento, dentro do mesmo relatório. ([L148-L150](legado-sifap/natural-programs/BATCHREL.NSN#L148))
- **M-BRL-04** — Beneficiário não encontrado no `FIND` (região = 0) e status desconhecido (`NONE`) são silenciosamente agregados aos grupos 5 (CENTRO-OESTE) e 1 (GERADO), respectivamente, sem nenhuma sinalização de erro — distorce os totais sem deixar rastro. ([L115-L119](legado-sifap/natural-programs/BATCHREL.NSN#L115), [L169-L170](legado-sifap/natural-programs/BATCHREL.NSN#L169))
- **M-BRL-05** — Os códigos de status de pagamento (`G/P/C/D/E`) e seus nomes só existem neste relatório; não há catálogo oficial no documento de 2012. Necessário confirmar o domínio canônico de `STATUS-PGTO` com BATCHPGT/CALCBENF.

---

## Regras de CADBENEF.NSN

> **Programa:** [`legado-sifap/natural-programs/CADBENEF.NSN`](legado-sifap/natural-programs/CADBENEF.NSN) (271 linhas) — cadastro de beneficiário (inclusão/alteração) no arquivo Adabas 150 `BENEFICIARIO`, com validação de CPF por mod-11 e definição de status inicial.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** o programa **captura** NIS, renda familiar, código de região, código de programa e número de dependentes na tela `INPUT`, mas **não valida nenhum deles** — a única validação real é operação (I/A), CPF (presença + mod-11), nome, data de nascimento e sexo, mais unicidade na inclusão. Toda a lógica de elegibilidade/limites descrita no doc 2012 (RN-003/004/005/006/007) está ausente aqui. Além disso, a alteração de 2011 ("AJUSTE STATUS IDOSO") introduziu um status `'S'` para idade > 75 que **não existe** no domínio documentado A/E da RN-011.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| CB-01 | A operação informada deve ser `'I'` (inclusão) ou `'A'` (alteração); qualquer outro valor é rejeitado com mensagem e encerra o fluxo. | Unwanted | [CADBENEF.NSN#L99-L103](legado-sifap/natural-programs/CADBENEF.NSN#L99) | Inferida | Sem correspondente no doc. |
| CB-02 | O CPF é obrigatório: se `#CPF = 0`, o cadastro é rejeitado com "CPF OBRIGATORIO". | Unwanted | [CADBENEF.NSN#L105-L109](legado-sifap/natural-programs/CADBENEF.NSN#L105) | Confirmada (RN-001) | RN-001 exige CPF válido; aqui a obrigatoriedade é o primeiro gate. |
| CB-03 | O CPF deve ter dígitos verificadores válidos pelo algoritmo módulo 11; CPF com DV incorreto é rejeitado. | Unwanted | [CADBENEF.NSN#L111-L117](legado-sifap/natural-programs/CADBENEF.NSN#L111) | Confirmada (RN-001) | Implementação completa na subrotina `VALIDA-CPF` — ver CB-13/CB-14. |
| CB-04 | O nome é obrigatório: se `#NOME` estiver em branco, o cadastro é rejeitado. | Unwanted | [CADBENEF.NSN#L119-L123](legado-sifap/natural-programs/CADBENEF.NSN#L119) | Inferida | Não citada explicitamente no doc. |
| CB-05 | A data de nascimento é obrigatória: se `#DT-NASC = 0`, rejeita. | Unwanted | [CADBENEF.NSN#L125-L129](legado-sifap/natural-programs/CADBENEF.NSN#L125) | Confirmada (RN-006) | RN-006 declara data de nascimento obrigatória; **a idade mínima de 16 anos da RN-006 NÃO é validada aqui** (ver M-CB-04). |
| CB-06 | O sexo deve ser `'M'` ou `'F'`; outro valor é rejeitado. | Unwanted | [CADBENEF.NSN#L131-L135](legado-sifap/natural-programs/CADBENEF.NSN#L131) | Inferida | Sem correspondente no doc. |
| CB-07 | Na inclusão (`#OPER = 'I'`), se já existir beneficiário com o mesmo CPF no arquivo 150, rejeita com "BENEFICIARIO JA CADASTRADO". | Unwanted | [CADBENEF.NSN#L139-L147](legado-sifap/natural-programs/CADBENEF.NSN#L139) | Confirmada (RN-002) | A `FIND ... WITH CPF` não filtra por status; ver M-CB-02 sobre reinclusão de excluídos. |
| CB-08 | Na alteração (`#OPER = 'A'`), se não existir beneficiário com o CPF, rejeita com "BENEFICIARIO NAO ENCONTRADO PARA ALTERACAO". | Unwanted | [CADBENEF.NSN#L149-L153](legado-sifap/natural-programs/CADBENEF.NSN#L149) | Inferida | Comportamento de pré-condição de alteração; não detalhado no doc. |
| CB-09 | A idade do beneficiário é calculada como `ano atual − ano de nascimento` (extraídos por `DIVIDE 10000 REMAINDER`). | Ubiquitous | [CADBENEF.NSN#L155-L159](legado-sifap/natural-programs/CADBENEF.NSN#L155) | Inferida | Cálculo só por ano (ignora mês/dia) → impreciso perto do aniversário. |
| CB-10 | Na inclusão, o status inicial do beneficiário é definido como `'A'` (ativo). | State-driven | [CADBENEF.NSN#L162-L164](legado-sifap/natural-programs/CADBENEF.NSN#L162) | Confirmada (RN-011) | RN-011 confirma `'A'` como situação ativa. |
| CB-11 | Se a idade for maior que 75 anos, o status é sobrescrito para `'S'`. | State-driven | [CADBENEF.NSN#L166-L169](legado-sifap/natural-programs/CADBENEF.NSN#L166) | **Mistério** | `'S'` não existe no domínio A/E da RN-011; introduzido pela alteração 2011 "AJUSTE STATUS IDOSO" (cabeçalho L7). Ver M-CB-01. <!-- mystery: status 'S' para idade>75 não documentado, conflita com domínio A/E da RN-011 --> |
| CB-12 | Se houver erro acumulado (`#ERRO`), a mensagem é exibida e a rotina encerra sem gravar. | Unwanted | [CADBENEF.NSN#L171-L174](legado-sifap/natural-programs/CADBENEF.NSN#L171) | Inferida | Guard final antes da persistência. |
| CB-13 | Na inclusão, todos os campos informados são gravados (`STORE`) e a transação é confirmada (`END TRANSACTION`); na alteração, o registro é relido por CPF e atualizado (`UPDATE`). | Event-driven | [CADBENEF.NSN#L177-L222](legado-sifap/natural-programs/CADBENEF.NSN#L177) | Inferida | Na alteração, `DT-CADASTRO` e `COD-PROGRAMA` **não** são atualizados; apenas `DT-ATUALIZACAO` é reescrita. |
| CB-14 | CPF é válido quando o 1º DV (pesos 10→2 sobre os 9 primeiros dígitos, `resto = soma mod 11`, DV = 0 se resto<2 senão 11−resto) confere com o 10º dígito **e** o 2º DV (pesos 11→2 sobre os 10 primeiros dígitos) confere com o 11º dígito. | Ubiquitous | [CADBENEF.NSN#L226-L271](legado-sifap/natural-programs/CADBENEF.NSN#L226) | Confirmada (RN-001) | Algoritmo mod-11 padrão brasileiro; resto calculado por `#SOMA − ((#SOMA/11)*11)` (aritmética inteira Natural). |

### Rascunho EARS (regras confirmadas)

- **REQ (CB-02):** The system shall reject beneficiary registration if the CPF is absent (zero).
- **REQ (CB-03/CB-14):** The system shall reject beneficiary registration if the CPF fails módulo-11 check-digit validation (both verifier digits).
- **REQ (CB-05):** The system shall require a non-zero date of birth for every beneficiary registration.
- **REQ (CB-07):** If the operation is inclusion and a beneficiary with the same CPF already exists, then the system shall reject the registration as duplicate.
- **REQ (CB-10):** When a beneficiary is included, the system shall set the initial status to `'A'` (active).

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-CB-01** — Status `'S'` para idade > 75 ([L166-L169](legado-sifap/natural-programs/CADBENEF.NSN#L166)) **não existe** no domínio documentado A/E (RN-011) e não há explicação no doc 2012 sobre o que `'S'` significa nem como o batch de pagamento o trata. Introduzido pela alteração de 10/01/2011 "AJUSTE STATUS IDOSO" ([L7](legado-sifap/natural-programs/CADBENEF.NSN#L7)). Risco: beneficiários idosos podem ser filtrados/ignorados por processos que só consideram `'A'`. <!-- mystery: domínio real de STATUS é A/E/S, não A/E -->
- **M-CB-02** — Unicidade de CPF (CB-07) usa `FIND ... WITH CPF = #CPF` **sem filtrar por status** ([L139-L147](legado-sifap/natural-programs/CADBENEF.NSN#L139)). A RN-002 prevê reinclusão de beneficiários excluídos logicamente (`'E'`), mas este código bloquearia qualquer CPF já existente, inclusive os excluídos. Contradição direta com RN-002. <!-- mystery: reinclusão de excluído ('E') parece impossível neste programa -->
- **M-CB-03** — RN-001 exige validação de NIS via `VALNISN`. O campo `#NIS` é capturado ([L94](legado-sifap/natural-programs/CADBENEF.NSN#L94)) e gravado ([L221](legado-sifap/natural-programs/CADBENEF.NSN#L221)), mas **nunca validado**. Nenhuma chamada a `VALNISN`/`VALCPF` externa — a validação de CPF é inline (`VALIDA-CPF`), divergindo do subprograma `VALCPF` citado na RN-001.
- **M-CB-04** — Regras do doc ausentes no código: idade mínima 16 anos (RN-006), máximo 3 dependentes (RN-004), região válida 01-27/99 (RN-005), vínculo a programa ativo (RN-003), dados bancários obrigatórios (RN-007). Todos os campos correspondentes são gravados sem qualquer verificação. A idade calculada (CB-09) só é usada para o status `'S'`, nunca para barrar menores de 16.
- **M-CB-05** — Não há operação de exclusão neste programa (só I/A), embora RN-011/RN-012 descrevam exclusão lógica (`'A'`→`'E'`) com bloqueio por pagamento pendente. A exclusão deve residir em outro programa ainda não localizado — investigar no `/map-dependencies`.

---

## Regras de CADDEPEND.NSN

> **Programa:** [`legado-sifap/natural-programs/CADDEPEND.NSN`](legado-sifap/natural-programs/CADDEPEND.NSN) (133 linhas) — cadastro interativo de dependentes vinculados a um beneficiário titular (ARQ/FNR 150), gravando-os num grupo periódico (PE) dentro do próprio registro do titular.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** O limite de dependentes está **hard-coded em 5** (`IF #NUM-DEP > 5`, L63), enquanto a RN-004 documenta máximo **3**. O cabeçalho do programa registra `ALTERADO: 14/03/2008 - ROBERTO MENDES - AJUSTE PE GROUP` — esta é exatamente a "manutenção recente" que a analista Ana Cristina suspeitou em 2012 mas não conseguiu confirmar. **Smoking gun confirmado.** Além disso, o programa usa códigos de status `'C'`/`'D'` (cancelado/desligado), divergentes do par `'A'`/`'E'` (ativo/excluído) documentado para `BN-CD-SIT`.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| CDP-01 | Se o CPF do titular informado não existir no arquivo de beneficiários (FNR 150), o sistema recusa a operação e encerra (ESCAPE ROUTINE). | Unwanted: *If the titular CPF is not found, then the system shall reject the operation and terminate.* | [CADDEPEND.NSN#L46](legado-sifap/natural-programs/CADDEPEND.NSN#L46)-L54 | Inferida | Pré-condição de integridade referencial; coerente com modelo de vínculo dependente→titular, mas o bloco em si não está na doc. |
| CDP-02 | Não é permitido incluir dependentes quando o titular está com status `'C'` (cancelado) ou `'D'` (desligado). | State-driven / Unwanted: *While the titular status is 'C' or 'D', the system shall not allow dependent inclusion.* | [CADDEPEND.NSN#L56](legado-sifap/natural-programs/CADDEPEND.NSN#L56)-L59 | Mistério | Códigos `'C'`/`'D'` não batem com `BN-CD-SIT` = `'A'`/`'E'` da doc (RN-002/RN-011). Origem do par C/D desconhecida. <!-- mystery: status 'C'/'D' aqui vs 'A'/'E' documentado --> |
| CDP-03 | O número de dependentes não pode ultrapassar **5**; atingido o limite, o loop de inclusão é encerrado. | Unwanted: *If the dependent count exceeds 5, then the system shall stop accepting new dependents.* | [CADDEPEND.NSN#L63](legado-sifap/natural-programs/CADDEPEND.NSN#L63)-L66 | Inferida | **Contradiz RN-004 (máx 3).** Confirma a NOTA da doc sobre alteração para 5. Ver achado estrutural. |
| CDP-04 | O nome do dependente é obrigatório; em branco, a inclusão é rejeitada com erro. | Unwanted: *If the dependent name is blank, then the system shall reject the entry.* | [CADDEPEND.NSN#L79](legado-sifap/natural-programs/CADDEPEND.NSN#L79)-L82 | Inferida | Validação de campo obrigatório; não documentada (seção de desvinculação/dependentes é "Baixa prioridade / não documentada"). |
| CDP-05 | O grau de parentesco deve ser um de `FI`, `CO`, `IR` ou `OU`; qualquer outro valor é rejeitado. | Unwanted: *If the parentesco code is not FI/CO/IR/OU, then the system shall reject the entry.* | [CADDEPEND.NSN#L84](legado-sifap/natural-programs/CADDEPEND.NSN#L84)-L88 | Inferida | Domínio fechado de valores; DEFINE DATA (L20) comenta apenas `FI=FILHO CO=CONJUGE IR=IRMAO`, mas o código também aceita `OU` (outro) — comentário desatualizado. |
| CDP-06 | Um mesmo CPF de dependente (≠ 0) não pode ser cadastrado duas vezes para o mesmo titular. | Unwanted: *If the dependent CPF already exists for the titular (and CPF ≠ 0), then the system shall reject it as duplicate.* | [CADDEPEND.NSN#L95](legado-sifap/natural-programs/CADDEPEND.NSN#L95)-L103 | Inferida | A condição `#CPF-DEP NE 0` permite múltiplos dependentes sem CPF (CPF=0) sem disparar duplicidade — possível brecha. |
| CDP-07 | Após validação, o dependente é gravado no grupo PE do titular, incrementando `NUM-DEPENDENTES`, com UPDATE + END TRANSACTION. | Event-driven: *When a valid dependent is confirmed, the system shall append it to the titular PE group and commit.* | [CADDEPEND.NSN#L110](legado-sifap/natural-programs/CADDEPEND.NSN#L110)-L123 | Inferida | Persiste atualizando o registro do titular (PE group), não um arquivo separado de dependentes. END TRANSACTION por inclusão (commit unitário). |
| CDP-08 | Após cada inclusão, o operador é perguntado se deseja incluir outro; resposta diferente de `'S'` encerra o loop. | Event-driven / Optional: *When the operator answers other than 'S', the system shall end the inclusion loop.* | [CADDEPEND.NSN#L125](legado-sifap/natural-programs/CADDEPEND.NSN#L125)-L128 | Inferida | Controle de fluxo interativo (tela de terminal). Não há regra de negócio na doc para isto. |

### Rascunho EARS (regras confirmadas)

> Nenhuma regra deste programa está **Confirmada** com correspondência direta na RN-2012. A regra mais próxima (limite de dependentes) **contradiz** a documentação, portanto entra como discrepância em vez de confirmação. As demais são **Inferidas** do código. Rascunhos preliminares abaixo, sujeitos a validação no Estágio 2:

- **REQ (CDP-01):** *If the informed titular CPF does not exist in the beneficiary file, then the system shall reject the dependent registration and not proceed.*
- **REQ (CDP-03):** *The system shall allow at most N dependents per titular* — **N pendente de decisão de negócio (código=5, doc RN-004=3).** Não redigir como confirmada até resolver M-CDP-02.
- **REQ (CDP-05):** *The system shall only accept dependent kinship codes in the set {FI, CO, IR, OU}.*
- **REQ (CDP-06):** *If a dependent CPF (non-zero) is already registered for the titular, then the system shall reject it as a duplicate.*

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-CDP-01** — **Códigos de status divergentes.** A doc define `BN-CD-SIT` com valores `'A'` (ativo) e `'E'` (excluído logicamente) (RN-002, RN-011). O CADDEPEND testa `STATUS = 'C' OR 'D'` (cancelado/desligado) em L56. Origem e significado de `'C'`/`'D'` desconhecidos — pode haver um conjunto de status maior que o documentado, ou divergência entre a view local e o DDM real. Investigar o DDM BENEFICIARIO e demais programas que escrevem em STATUS.
- **M-CDP-02** — **Limite de dependentes 5 vs 3.** L63 impõe `> 5`; RN-004 afirma máximo 3, com NOTA explícita suspeitando de alteração para 5 não confirmada. O cabeçalho (`14/03/2008 - ROBERTO MENDES - AJUSTE PE GROUP`) sugere que esta alteração elevou o limite. **Resolver qual é a regra de negócio vigente** antes de especificar — impacta cálculo de benefício (RN-013 usa `QT-DEPEND` como multiplicador de acréscimo).
- **M-CDP-03** — **Dependentes sem CPF.** A verificação de duplicidade em L97 ignora registros com `#CPF-DEP = 0`, permitindo múltiplos dependentes sem CPF sem checagem de duplicidade (ex.: menores). Confirmar se é intencional ou brecha de integridade.
- **M-CDP-04** — **Comentário de parentesco desatualizado.** O DEFINE DATA (L20) documenta apenas `FI/CO/IR`, mas a validação real (L84-L85) também aceita `OU`. Indício de manutenção no código sem atualização do comentário; alinhar domínio oficial de parentesco no Estágio 2.
- **M-CDP-05** — **Regra de desvinculação ausente.** Este programa só inclui dependentes; não há lógica de exclusão/desvinculação. A doc lista "Regras de desvinculação de dependentes — não documentadas" (seção 6). Confirmar se existe outro programa responsável pela remoção ou se é lacuna funcional do sistema.


---

## Regras de CADPROG.NSN

> **Programa:** [`legado-sifap/natural-programs/CADPROG.NSN`](legado-sifap/natural-programs/CADPROG.NSN) (122 linhas) — cadastro (inclusão) e consulta de programas sociais na tabela `PROGRAMA-SOCIAL` (arq. Adabas 155); menu interativo por terminal com operação `I`/`C`.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** o **FATOR-K** — citado na doc de 2012 como "multiplicador que ninguém soube explicar" (seções 2.1 e 6, prioridade Alta) — está **materializado aqui** como constante mágica `0.347215` em [CADPROG.NSN#L87](legado-sifap/natural-programs/CADPROG.NSN#L87). Além disso, o campo `VLR-BASE` é gravado já **inflado** pelo fator, e há vários campos capturados na tela (`TIPO`, `COD-ELEG`, idades, datas, renda) que **nunca são validados** antes do `STORE`.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| CP-01 | A operação informada deve ser `I` (inclusão) ou `C` (consulta); qualquer outro valor exibe "OPERACAO INVALIDA" e encerra a rotina. | Unwanted | [CADPROG.NSN#L51-L54](legado-sifap/natural-programs/CADPROG.NSN#L51) | Inferida | Validação de domínio de entrada; sem equivalente na doc 2012. |
| CP-02 | Quando a operação é `C`, o sistema executa a sub-rotina de consulta e encerra sem entrar no fluxo de inclusão. | Event-driven | [CADPROG.NSN#L56-L59](legado-sifap/natural-programs/CADPROG.NSN#L56) | Inferida | Bifurcação consulta vs. inclusão. |
| CP-03 | Não é permitido incluir um programa cujo `COD-PROGRAMA` já exista na base; nesse caso exibe "PROGRAMA JA CADASTRADO" e encerra. | Unwanted | [CADPROG.NSN#L76-L84](legado-sifap/natural-programs/CADPROG.NSN#L76) | Inferida | Análogo de unicidade ao RN-002 (que trata beneficiários, não programas). Aqui **não há filtro de status** — ver M-CP-04. |
| CP-04 | Na inclusão, calcula-se o fator de ajuste `FATOR-K = 1,00 + (FATOR-REAJUSTE × 0,347215)`. | Event-driven | [CADPROG.NSN#L87](legado-sifap/natural-programs/CADPROG.NSN#L87) | Mistério | Constante `0,347215` sem origem documentada; corresponde ao "FATOR-K" não explicado na doc 2012 (§2.1, §6). |
| CP-05 | O valor-base gravado é o valor informado multiplicado pelo fator-K (`VLR-CALC = VLR-BASE × FATOR-K`), e é esse valor ajustado que vai para o campo `VLR-BASE` do registro. | Event-driven | [CADPROG.NSN#L88](legado-sifap/natural-programs/CADPROG.NSN#L88), [#L93](legado-sifap/natural-programs/CADPROG.NSN#L93) | Mistério | O valor original informado **não é persistido** — só o ajustado. Ver M-CP-03. |
| CP-06 | Todo programa recém-incluído recebe status ativo (`STATUS-PROG = 'A'`) automaticamente. | Event-driven | [CADPROG.NSN#L97](legado-sifap/natural-programs/CADPROG.NSN#L97) | Inferida | Relaciona-se a "programa social ativo" do RN-003, mas o campo/flag difere (`STATUS-PROG='A'` aqui vs. `PS-IN-ATIVO='S'` na doc). |
| CP-07 | A inclusão é confirmada com `STORE` + `END TRANSACTION` e a mensagem de sucesso exibe o valor ajustado calculado. | Event-driven | [CADPROG.NSN#L102-L105](legado-sifap/natural-programs/CADPROG.NSN#L102) | Inferida | Persistência transacional unitária. |
| CP-08 | Na consulta, se nenhum registro for encontrado para o código informado (`*NUMBER = 0`), exibe "PROGRAMA NAO ENCONTRADO". | Event-driven | [CADPROG.NSN#L116-L118](legado-sifap/natural-programs/CADPROG.NSN#L116) | Inferida | Tratamento de não-encontrado na sub-rotina `CONSULTA-PROG`. |

### Rascunho EARS (regras confirmadas)

> Nenhuma regra deste programa tem correspondência **direta e validada** na doc 2012 (a doc cobre beneficiários/cálculo/descontos, não o cadastro de programas). Os rascunhos abaixo são as regras de domínio mais estáveis, marcadas como *Inferidas* — devem ser confirmadas no Estágio 2 antes de virar REQ definitivo.

- **REQ (CP-01):** *When* o operador informa um código de operação diferente de `I` ou `C`, *the* sistema *shall* rejeitar a entrada com mensagem de operação inválida e não prosseguir.
- **REQ (CP-03):** *When* o operador solicita inclusão de um programa social cujo código já existe, *the* sistema *shall* bloquear a inclusão e informar que o programa já está cadastrado.
- **REQ (CP-06):** *When* um programa social é incluído com sucesso, *the* sistema *shall* defini-lo com status ativo por padrão.
- **REQ (CP-08):** *When* uma consulta é feita para um código de programa inexistente, *the* sistema *shall* informar que o programa não foi encontrado.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-CP-01** — `FATOR-K` revelado: a constante `0,347215` em [#L87](legado-sifap/natural-programs/CADPROG.NSN#L87) é exatamente o "fator multiplicador chamado FATOR-K que [Marcos Antônio] não soube explicar" (REGRAS-NEGOCIO-2012 §2.1 e §6, prioridade Alta). Origem, base legal e por que `0,347215` permanecem desconhecidos. <!-- mystery: origem da constante 0.347215 do FATOR-K em CADPROG L87; nenhuma referência a decreto/índice no código -->
- **M-CP-02** — `TIPO` sem validação: o comentário em [#L16](legado-sifap/natural-programs/CADPROG.NSN#L16) e a tela em [#L65](legado-sifap/natural-programs/CADPROG.NSN#L65) declaram o domínio `A/P/T`, mas o código **nunca valida** o valor digitado antes do `STORE` — qualquer caractere é persistido. <!-- mystery: TIPO aceita qualquer A1 apesar do domínio documentado A/P/T -->
- **M-CP-03** — Perda do valor-base original: o campo persistido `VLR-BASE` recebe o valor **já ajustado** pelo fator-K ([#L88](legado-sifap/natural-programs/CADPROG.NSN#L88), [#L93](legado-sifap/natural-programs/CADPROG.NSN#L93)). Programas a jusante (ex.: `CALCBENF`) leem um base já inflado, podendo causar dupla aplicação do reajuste. Investigar impacto no cálculo de benefícios. <!-- mystery: VLR-BASE gravado inflado pelo FATOR-K; risco de reajuste em cascata no CALCBENF -->
- **M-CP-04** — Reinclusão bloqueada sem filtro de status: o `FIND` em [#L77-L79](legado-sifap/natural-programs/CADPROG.NSN#L77) marca `#FOUND` sem checar `STATUS-PROG`, então um programa inativo/encerrado também bloqueia a reinclusão — diferente do comportamento de reinclusão lógica permitido para beneficiários (RN-002). É intencional para programas? <!-- mystery: CADPROG bloqueia reinclusão de qualquer programa existente, sem distinguir ativos de inativos -->
- **M-CP-05** — Campos capturados e ignorados: `COD-ELEG` (alteração de 2012 "NOVOS COD ELEG", [#L7](legado-sifap/natural-programs/CADPROG.NSN#L7)), `RENDA-MAX`, `IDADE-MIN`, `IDADE-MAX`, `DT-INICIO`, `DT-FIM` são lidos da tela ([#L67-L72](legado-sifap/natural-programs/CADPROG.NSN#L67)) e gravados **sem nenhuma validação** (faixa, consistência idade-min ≤ idade-max, datas, código de elegibilidade válido). A alteração de 2012 mencionada no cabeçalho não aparece como lógica de validação no corpo do programa. <!-- mystery: alteração 2012 'NOVOS COD ELEG' não tem validação visível no código; campos de elegibilidade gravados sem checagem -->


---

## Regras de CALCBENF.NSN

> **Programa:** [`legado-sifap/natural-programs/CALCBENF.NSN`](legado-sifap/natural-programs/CALCBENF.NSN) (325 linhas) — cálculo ONLINE do valor do benefício mensal (bruto, desconto, líquido) para um CPF/competência e gravação de um registro em PAGAMENTO (arq. 150/155/160).
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** O cálculo principal (L225-226) é **MULTIPLICATIVO** — `BASE × FATOR_REG × FATOR_FAM × FATOR_RND × FATOR_IDADE` — e contradiz frontalmente a **RN-013**, que documenta uma fórmula **ADITIVA** (`BASE + ACRESCIMO_DEPEND × QT_DEPEND`). Nenhum dos quatro fatores multiplicadores (regional, familiar, renda, idade) nem o "13º"/abono natalino estão na documentação de 2012; a própria RN-013 admite que "existem pelo menos mais 3 variações no código". Forte candidato ao lendário "FATOR-K" citado na seção 6 do doc. Divergência online (CALCBENF) × batch (BATCHPGT) sinalizada abaixo.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| CBF-01 | A competência informada é decomposta em ano/mês; se o mês for menor que 1 ou maior que 12, o cálculo é abortado com "COMPETENCIA INVALIDA". | Unwanted | [CALCBENF.NSN#L138-L144](legado-sifap/natural-programs/CALCBENF.NSN#L138) | Inferida | Validação de borda; não há regra documentada equivalente. |
| CBF-02 | Se o beneficiário (CPF) não existir no arquivo BENEFICIARIO (arq. 150), o cálculo é abortado. | Unwanted | [CALCBENF.NSN#L147-L158](legado-sifap/natural-programs/CALCBENF.NSN#L147) | Inferida | Pré-condição de leitura. |
| CBF-03 | O cálculo só prossegue se `STATUS = 'A'` (ativo); qualquer outro status aborta com mensagem. | State-driven | [CALCBENF.NSN#L160-L163](legado-sifap/natural-programs/CALCBENF.NSN#L160) | Confirmada (RN-016 § 4.2 / elegibilidade) | Alinha com "situação cadastral ativa" do VALELEG, mas aqui é checado direto no cálculo. |
| CBF-04 | Se o programa social do beneficiário não existir no arquivo PROGRAMA-SOCIAL (arq. 155), o cálculo é abortado. | Unwanted | [CALCBENF.NSN#L166-L177](legado-sifap/natural-programs/CALCBENF.NSN#L166) | Inferida | Carrega VLR-BASE, TIPO e FATOR-REAJUSTE do programa. |
| CBF-05 | O fator regional vem de uma tabela fixa de 27 posições; se `COD-REGIAO` ∈ [1,25] usa o valor tabelado, senão aplica 1.0000. | State-driven | [CALCBENF.NSN#L91-L117](legado-sifap/natural-programs/CALCBENF.NSN#L91), [#L179-L184](legado-sifap/natural-programs/CALCBENF.NSN#L179) | Mistério (cf. RN-005) | RN-005 diz regiões 01-27 + 99 reservado. Aqui só 1-25 entram na tabela; 26/27 são "RESERVA"=1.0 e **a região 99 cai silenciosamente no `ELSE` → fator 1.0**, sem bypass. Valores tabelados (Norte/Nordeste ~1.28-1.40; Sudeste/Sul ~1.03-1.12) não constam em lugar nenhum da doc. |
| CBF-06 | Fator familiar por faixas de dependentes: 0 dep → 1.0; 1-2 dep → 1.0 + (dep×0.05); 3-4 dep → 1.10 + ((dep-2)×0.03); >4 dep → 1.16 + ((dep-4)×0.02). | State-driven | [CALCBENF.NSN#L186-L199](legado-sifap/natural-programs/CALCBENF.NSN#L186) | Inferida | Código não impõe teto de dependentes; aceita >4 (a RN-004 documenta máx. 3, com nota de que "talvez 5"). Contradiz o limite cadastral. |
| CBF-07 | O fator de renda é o multiplicador da primeira faixa cujo teto seja ≥ renda familiar; faixas: ≤300→1.0; ≤600→0.85; ≤1000→0.70; ≤1500→0.55; ≤9999.99→0.40. | State-driven | [CALCBENF.NSN#L119-L129](legado-sifap/natural-programs/CALCBENF.NSN#L119), [#L303-L311](legado-sifap/natural-programs/CALCBENF.NSN#L303) | Confirmada (RN-018) + Inferida (valores) | A *mecânica* (primeira faixa ≥ renda) confirma RN-018. Mas RN-017/RN-018 dizem que as faixas vivem no DDM PROGRAMA-SOCIAL (campos PE por exercício); aqui estão **hardcoded no programa**, não parametrizadas. |
| CBF-08 | Fator idade por limiares: idade ≥65 → 1.15; ≥60 → 1.10; <18 → 1.05; senão 1.0. Idade = ano da competência − ano de nascimento. | State-driven | [CALCBENF.NSN#L204-L219](legado-sifap/natural-programs/CALCBENF.NSN#L204) | Inferida | Não documentado. Idade calculada só por ano (ignora mês/dia). |
| CBF-09 | Valor do benefício mensal = `VLR_BASE × FATOR_REG × FATOR_FAM × FATOR_RND × FATOR_IDADE`. | Ubiquitous | [CALCBENF.NSN#L222-L226](legado-sifap/natural-programs/CALCBENF.NSN#L222) | Mistério (contradiz RN-013) | **Núcleo da divergência.** RN-013 = fórmula aditiva por dependente; código = produto de 4 fatores. São modelos de cálculo incompatíveis. |
| CBF-10 | Sobre o benefício já calculado, aplica reajuste do programa: `VLR = VLR × (1 + FATOR_REAJUSTE)`. | Ubiquitous | [CALCBENF.NSN#L228-L229](legado-sifap/natural-programs/CALCBENF.NSN#L228) | Mistério (cf. RN-019/RN-020) | RN-020 diz que o reajuste incide **só sobre o valor base**; aqui incide sobre o valor já multiplicado por todos os fatores. Divergência explícita com a doc (que já vinha marcada "não validada"). |
| CBF-11 | O valor do benefício é truncado para 2 casas decimais (×100, /100 em inteiro), não arredondado. | Ubiquitous | [CALCBENF.NSN#L231-L233](legado-sifap/natural-programs/CALCBENF.NSN#L231) | Confirmada (RN-014) | Padrão de truncamento aplicado também ao 13º, abono e líquido. |
| CBF-12 | Em dezembro (mês=12), tipo de pagamento vira 'D' e soma-se um 13º: `VLR_13 = VLR_BASE × FATOR_REG × FATOR_IDADE`, truncado e somado ao bruto. | Event-driven | [CALCBENF.NSN#L238-L248](legado-sifap/natural-programs/CALCBENF.NSN#L238) | Mistério (cf. RN-014 / seção 6) | O comentário (L240) descreve `VLR_BASE × FATOR_REG × (MESES_ATIVOS/12)`, mas o **código usa FATOR_IDADE e ignora "meses ativos"** → comentário ≠ código. É exatamente o "cálculo do 13º não documentado" listado como Alta prioridade na seção 6 do doc. |
| CBF-13 | Em dezembro, para programas TIPO = 'A', acrescenta abono natalino de 15% do benefício mensal (`VLR_BENF × 0.15`), truncado e somado ao bruto; senão abono = 0. | Event-driven + Optional | [CALCBENF.NSN#L250-L260](legado-sifap/natural-programs/CALCBENF.NSN#L250) | Inferida (cf. seção 6) | "Abono natalino" aparece só como pendência na doc (sem fórmula). 15% e a condição TIPO='A' são exclusivos do código. |
| CBF-14 | Valor líquido = bruto − desconto; se negativo, é fixado em 0 (piso zero). | Ubiquitous | [CALCBENF.NSN#L265-L269](legado-sifap/natural-programs/CALCBENF.NSN#L265) | Inferida | Sem regra documentada de piso. |
| CBF-15 | O valor líquido também é truncado para 2 casas decimais. | Ubiquitous | [CALCBENF.NSN#L271-L273](legado-sifap/natural-programs/CALCBENF.NSN#L271) | Confirmada (RN-014) | — |
| CBF-16 | Desconto (rotina interna simplificada): se bruto > 500,00, desconta 3% de "contribuição social"; senão zero. | State-driven | [CALCBENF.NSN#L313-L323](legado-sifap/natural-programs/CALCBENF.NSN#L313) | Mistério (cf. RN-021/RN-022/RN-023) | O próprio código admite ser placeholder ("VER CALCDSCT P/ COMPLETO", L314). Ignora o teto de 30% (RN-021), os tipos de desconto (RN-022) e a ordem de prioridade (RN-023). O online NÃO chama CALCDSCT. |
| CBF-17 | Após o cálculo, grava um registro em PAGAMENTO com `STATUS-PGTO = 'G'` (gerado), TIPO-PGTO e abono, encerrando com END TRANSACTION. | Event-driven | [CALCBENF.NSN#L275-L287](legado-sifap/natural-programs/CALCBENF.NSN#L275) | Mistério (cf. § 5.1) | O batch (RN § 5.1) grava status **'P' (pendente)**; o online grava **'G'**. Dois caminhos gravam no mesmo arquivo com status divergentes. |

### Rascunho EARS (regras confirmadas)

- **REQ (CBF-09):** O sistema deverá calcular o valor do benefício mensal como o produto do valor-base do programa pelos fatores regional, familiar, de renda e de idade. *(Ubiquitous — mas em conflito direto com RN-013; ver mistério M-CBF-01.)*
- **REQ (CBF-11/CBF-15):** O sistema deverá truncar (não arredondar) todos os valores monetários calculados para duas casas decimais. *(Confirma RN-014.)*
- **REQ (CBF-07):** Quando determinar a faixa de renda, o sistema deverá aplicar o multiplicador da primeira faixa cujo teto seja maior ou igual à renda familiar declarada. *(Confirma a mecânica de RN-018.)*
- **REQ (CBF-03):** Enquanto o beneficiário não estiver com status 'A' (ativo), o sistema deverá recusar o cálculo do benefício. *(State-driven; alinha com elegibilidade § 4.2.)*
- **REQ (CBF-12):** Quando a competência for de dezembro (mês 12), o sistema deverá adicionar um 13º benefício ao valor bruto e marcar o pagamento como tipo 'D'. *(Event-driven; fórmula a confirmar — ver M-CBF-04.)*

### Confronto com BATCHPGT (cálculo inline)

- **Modelo de fórmula:** CALCBENF (online) usa o produto de 4 fatores (L225-226). É preciso ler BATCHPGT.NSN para confirmar se o cálculo *inline* do batch reproduz a mesma fórmula multiplicativa, usa a aditiva (RN-013) ou uma terceira variante. **Se BATCHPGT for multiplicativo de forma diferente (p.ex. outro conjunto de fatores), online e batch produzem valores divergentes para o mesmo beneficiário.** ← divergência a investigar.
- **Status gravado:** online grava `STATUS-PGTO = 'G'` (CBF-17, L283); o batch, segundo a doc § 5.1, grava `'P'`. Reconciliação financeira (BATCHCON) pode depender desse status.
- **Descontos:** o online aplica apenas 3% simplificado internamente (CBF-16) e **não invoca CALCDSCT**; a doc § 5.1 diz que o batch chama CALCDSCT (a partir da v4.0) com teto de 30%. Logo o líquido do online e o do batch tendem a divergir.
- **Reajuste:** confirmar se BATCHPGT também aplica `(1 + FATOR_REAJUSTE)` sobre o valor já multiplicado (CBF-10) ou só sobre a base (RN-020).
- **13º/abono:** confirmar se a lógica de dezembro (CBF-12/CBF-13) existe igual no batch ou só no online.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-CBF-01** — Fórmula multiplicativa (4 fatores) no código vs. aditiva na RN-013. Qual está em produção? Provável "FATOR-K"/"3 variações" citados no doc. Decisão de negócio bloqueante para a spec moderna. <!-- mystery: CALCBENF L225-226 multiplicativa vs RN-013 aditiva -->
- **M-CBF-02** — Origem e valores da tabela de fatores regionais (1.03 a 1.40 por UF). Não há fonte documental. E a região 99 (que RN-005 chama de "reservada/bypass do Roberto") aqui só resulta em fator neutro 1.0 — o bypass de elegibilidade citado no doc está em VALELEG, não aqui. <!-- mystery: tabela regional sem fonte; região 99 cai no ELSE=1.0 -->
- **M-CBF-03** — Fator familiar escalona até "acima de 4 dependentes", mas RN-004 limita a 3 (com nota de "talvez 5"). Qual o teto real de dependentes? <!-- mystery: fator familiar aceita >4 dep vs RN-004 máx 3 -->
- **M-CBF-04** — Cálculo do 13º: comentário (proporcional por meses ativos) ≠ código (usa FATOR_IDADE, sem pro rata). Não há regra de pro rata no doc (seção 6, Alta prioridade). <!-- mystery: comentário do 13º diverge do código -->
- **M-CBF-05** — Reajuste aplicado sobre o valor já multiplicado pelos fatores, contrariando RN-020 (incidência só sobre a base). RN-020 já estava marcada "não validada com equipe técnica". <!-- mystery: reajuste sobre valor com fatores vs RN-020 só base -->
- **M-CBF-06** — Desconto simplificado embutido (3% > R$500) diverge de todo o módulo de descontos documentado (RN-021/022/023). O caminho online ignora CALCDSCT — o líquido online pode ser sistematicamente diferente do batch. <!-- mystery: desconto placeholder no online ignora CALCDSCT -->
- **M-CBF-07** — Mesmo arquivo PAGAMENTO recebe status 'G' (online) e 'P' (batch). Confirmar máquina de estados do pagamento e impacto na conciliação (BATCHCON). <!-- mystery: status 'G' online vs 'P' batch -->

---

## Regras de CALCCORR.NSN

> **Programa:** [`legado-sifap/natural-programs/CALCCORR.NSN`](legado-sifap/natural-programs/CALCCORR.NSN) (191 linhas) — recálculo retroativo de pagamentos por variação acumulada do índice IPCA, gravando valor corrigido no registro de PAGAMENTO (ARQ 160).
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** a tabela IPCA está hardcoded apenas para 2010–2012 (cabeçalho diz "ULTIMA CARGA: 2014"), e a sub-rotina `CALC-INDICE-ACUM` — apesar do nome e do comentário "ÍNDICE ACUMULADO DO PERÍODO" — aplica somente o índice do **mês da própria competência**, sem acumular do início ao fim do intervalo. Há ainda um bloco "Plano Verão" comentado preservado como histórico.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| CC-01 | A tabela de índices IPCA é carregada em memória com 12 valores mensais por ano, somente para os anos 2010, 2011 e 2012. | Ubiquitous | [CALCCORR.NSN#L56-L96](legado-sifap/natural-programs/CALCCORR.NSN#L56) | Inferida | Cabeçalho diz "ULTIMA CARGA: 2014" mas só há 3 anos carregados; competências fora de 2010–2012 não recebem índice. <!-- mystery: lacuna 2013-2014 --> |
| CC-02 | Se a competência inicial informada for maior que a competência final, o sistema rejeita a operação e encerra sem processar. | Unwanted | [CALCCORR.NSN#L119-L122](legado-sifap/natural-programs/CALCCORR.NSN#L119) | Inferida | `ESCAPE ROUTINE` após mensagem "PERIODO INVALIDO". |
| CC-03 | O recálculo processa apenas pagamentos do CPF informado; ao mudar de CPF, a leitura encerra. | Event-driven | [CALCCORR.NSN#L128-L131](legado-sifap/natural-programs/CALCCORR.NSN#L128) | Inferida | `READ ... BY CPF-BENEF` + `ESCAPE BOTTOM` quando CPF difere. |
| CC-04 | Pagamentos com competência anterior à competência inicial do intervalo são ignorados. | Event-driven | [CALCCORR.NSN#L133-L135](legado-sifap/natural-programs/CALCCORR.NSN#L133) | Inferida | `ESCAPE TOP` (pula registro). |
| CC-05 | Ao alcançar competência posterior à competência final, a leitura é encerrada. | Event-driven | [CALCCORR.NSN#L136-L138](legado-sifap/natural-programs/CALCCORR.NSN#L136) | Inferida | `ESCAPE BOTTOM`. |
| CC-06 | Pagamentos já marcados como corrigidos (`IND-CORRIGIDO = 'S'`) são ignorados (idempotência). | State-driven | [CALCCORR.NSN#L140-L142](legado-sifap/natural-programs/CALCCORR.NSN#L140) | Inferida | Garante que a correção não seja aplicada duas vezes. |
| CC-07 | O valor corrigido é o valor bruto original multiplicado pelo índice acumulado calculado para a competência. | Event-driven | [CALCCORR.NSN#L145-L152](legado-sifap/natural-programs/CALCCORR.NSN#L145) | Inferida | Base de cálculo é `VLR-BRUTO`, não o líquido. |
| CC-08 | O valor corrigido é truncado em centavos (não arredondado). | Ubiquitous | [CALCCORR.NSN#L153-L155](legado-sifap/natural-programs/CALCCORR.NSN#L153) | Confirmada (RN-014) | `*100` / `/100` via inteiro `#VLR-TEMP` = truncamento; coincide com RN-014 ("arredondado para baixo / truncamento"). |
| CC-09 | A correção só é gravada quando a diferença é positiva (`VLR-DIFF > 0`); caso contrário, nada é alterado. | Optional | [CALCCORR.NSN#L156-L166](legado-sifap/natural-programs/CALCCORR.NSN#L156) | Inferida | Marca `IND-CORRIGIDO='S'`, grava data e `END TRANSACTION` por registro. |
| CC-10 | A cada pagamento corrigido, acumula a diferença em total geral e incrementa o contador de registros, exibidos ao final. | Event-driven | [CALCCORR.NSN#L164-L173](legado-sifap/natural-programs/CALCCORR.NSN#L164) | Inferida | Totalizadores `#VLR-TOTAL-CORR` e `#QTD-REG`. |
| CC-11 | O índice da competência é obtido localizando o ano na tabela e multiplicando o acumulado por `(1 + IPCA do mês)`; se o ano não existir na tabela, o acumulado permanece 1,0 (sem correção, silenciosamente). | Event-driven | [CALCCORR.NSN#L176-L189](legado-sifap/natural-programs/CALCCORR.NSN#L176) | Inferida | A sub-rotina aplica **só o mês da competência corrente**, não acumula do início ao fim do período. <!-- mystery: nome/comentario divergem da logica --> |
| CC-12 | Bloco de correção "Plano Verão" (01/1989–01/1991, transição Cruzado→Cruzeiro, fatores 2,7500 e 1,4289) permanece comentado e inativo, preservado como histórico. | — (código morto) | [CALCCORR.NSN#L98-L111](legado-sifap/natural-programs/CALCCORR.NSN#L98) | Mistério | Não executa; instrução explícita "NAO REMOVER (HISTORICO)". <!-- mystery: fatores nao explicados --> |

### Rascunho EARS (regras confirmadas)

- **REQ (CC-08):** O sistema deve truncar o valor corrigido em duas casas decimais (centavos), sem arredondamento matemático. *(confirmada por RN-014)*

> Observação: as demais regras (CC-01 a CC-12) são **inferidas do código** ou **mistérios** — a correção retroativa por IPCA não está descrita em REGRAS-NEGOCIO-2012.md (o doc menciona apenas reajuste anual de janeiro via `CALCIDX` em RN-019, que é programa e mecanismo distintos). Não emitir EARS confirmada sem validação.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-CC-01** — `CALC-INDICE-ACUM` não acumula o período: apesar do comentário "CALCULAR ÍNDICE ACUMULADO DO PERÍODO" e do nome `#IND-ACUM`, a sub-rotina multiplica o acumulado apenas pelo índice do **mês da competência do registro corrente** ([L176-L189](legado-sifap/natural-programs/CALCCORR.NSN#L176)). Não há laço encadeando de `#COMP-INI` até a competência. Verificar se o comportamento pretendido era correção composta multi-mês — possível bug de cálculo financeiro.
- **M-CC-02** — Tabela IPCA incompleta: cabeçalho declara "ULTIMA CARGA: 2014" ([L44](legado-sifap/natural-programs/CALCCORR.NSN#L44)) mas só há valores para 2010–2012 ([L56-L96](legado-sifap/natural-programs/CALCCORR.NSN#L56)). Competências de 2013/2014 ou anteriores a 2010 caem no caso "ano não encontrado" e resultam em índice 1,0 → correção zero, **sem erro nem aviso**. Risco de subcorreção silenciosa.
- **M-CC-03** — Bloco "Plano Verão" comentado ([L98-L111](legado-sifap/natural-programs/CALCCORR.NSN#L98)): fatores mágicos 2,7500 e 1,4289 para período 1989–1991 sem fonte documental. Marcado "NAO REMOVER". Investigar origem regulatória e se deveria estar ativo para correções de competências antigas.
- **M-CC-04** — Correção retroativa por IPCA não consta em REGRAS-NEGOCIO-2012.md. O doc só cita reajuste anual de janeiro por decreto via `CALCIDX` (RN-019). Confirmar se `CALCCORR` é processo paralelo/posterior, sua governança e quem o dispara.
- **M-CC-05** — Correção apenas para diferenças positivas ([L156-L166](legado-sifap/natural-programs/CALCCORR.NSN#L156)): se o índice resultar em valor menor/igual ao original, o registro nunca é marcado como corrigido, podendo ser reprocessado indefinidamente em execuções futuras. Confirmar se é intencional.

---

## Regras de CONSBENF.NSN

> **Programa:** [`legado-sifap/natural-programs/CONSBENF.NSN`](legado-sifap/natural-programs/CONSBENF.NSN) (191 linhas) — consulta online (tela 3270 via MAP) que exibe dados cadastrais de um beneficiário e seu histórico de pagamentos (últimos 12), com CPF mascarado.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** programa de **leitura/consulta** (nenhum `STORE`/`UPDATE`); usa `FIND` por CPF ou NIS e `READ` físico do histórico. Dois pontos sensíveis: (a) o conjunto de códigos de status `A/S/C/I/D` **não bate** com o `BN-CD-SIT` `A/E` documentado (RN-002/RN-011); (b) a sub-rotina `MASCARA-CPF` carrega um **bug de mascaramento conhecido e propositalmente não corrigido** (comentário em L170-174).

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| CSB-01 | Se a abertura do MAP `CONSBENF-M01` falhar (`*ERROR-NR NE 0`), o sistema deve apresentar uma tela alternativa de entrada sem MAP solicitando tipo de busca, CPF e NIS. | Unwanted | [CONSBENF.NSN#L69-L78](legado-sifap/natural-programs/CONSBENF.NSN#L69) | Inferida | Fallback de UI; não documentado. |
| CSB-02 | Quando o tipo de busca for informado em branco, o sistema deve assumir busca por CPF (`'C'`). | Event-driven | [CONSBENF.NSN#L80-L82](legado-sifap/natural-programs/CONSBENF.NSN#L80) | Inferida | Valor default implícito. |
| CSB-03 | O sistema deve localizar o beneficiário por CPF quando o tipo for `'C'` e por NIS quando o tipo for `'N'`; qualquer outro tipo deve exibir "TIPO BUSCA INVALIDO" e encerrar. | Event-driven | [CONSBENF.NSN#L85-L98](legado-sifap/natural-programs/CONSBENF.NSN#L85) | Inferida | `DECIDE ON` com ramo `NONE` → `ESCAPE ROUTINE`. CPF e NIS são chaves de busca (`FIND`). |
| CSB-04 | Quando nenhum beneficiário for encontrado, o sistema deve exibir "BENEFICIARIO NAO ENCONTRADO" e encerrar a consulta. | Unwanted | [CONSBENF.NSN#L100-L103](legado-sifap/natural-programs/CONSBENF.NSN#L100) | Inferida | — |
| CSB-05 | O sistema deve mascarar o CPF do beneficiário antes de exibi-lo, para ocultar dados sensíveis. | Ubiquitous | [CONSBENF.NSN#L105-L107](legado-sifap/natural-programs/CONSBENF.NSN#L105), [#L170-L189](legado-sifap/natural-programs/CONSBENF.NSN#L170) | Inferida | Alinhado ao changelog "15/06/2003 - INC MASCARA CPF". Doc 2012 não cita mascaramento. |
| CSB-06 | A formatação da máscara de CPF deve variar conforme o tamanho do CPF armazenado: se `CPF < 10000000000` (menos de 11 dígitos) usa um formato; caso contrário mascara os 6 primeiros dígitos e revela posições 7-11. | State-driven | [CONSBENF.NSN#L177-L188](legado-sifap/natural-programs/CONSBENF.NSN#L177) | Mistério | Comportamento reconhecidamente inconsistente. Ver M-CSB-02. <!-- mystery: ramo IF (CPF<11 dígitos) só preenche #CPF-P1 e compõe '.***.***-**', revelando os 3 PRIMEIROS dígitos; ramo ELSE revela os ÚLTIMOS 5. Comentário L170-174 proíbe correção sem aprovação da auditoria. --> |
| CSB-07 | O sistema deve traduzir o código de status do beneficiário em descrição: `A`=ATIVO, `S`=SUSPENSO, `C`=CANCELADO, `I`=INATIVO, `D`=DESLIGADO; qualquer outro valor exibe "DESCONHECIDO". | State-driven | [CONSBENF.NSN#L110-L123](legado-sifap/natural-programs/CONSBENF.NSN#L110) | Mistério | Conjunto `A/S/C/I/D` conflita com `BN-CD-SIT` `A`(ativo)/`E`(excluído) de RN-002/RN-011. Ver M-CSB-01. |
| CSB-08 | O sistema deve exibir o histórico de pagamentos do beneficiário lendo registros do arquivo PAGAMENTO pelo CPF do beneficiário. | Ubiquitous | [CONSBENF.NSN#L151-L164](legado-sifap/natural-programs/CONSBENF.NSN#L151) | Inferida | `READ ... BY CPF-BENEF`. |
| CSB-09 | A leitura do histórico deve parar assim que o CPF do registro lido diferir do CPF do beneficiário consultado. | Event-driven | [CONSBENF.NSN#L152-L154](legado-sifap/natural-programs/CONSBENF.NSN#L152) | Inferida | Controle de quebra manual (`ESCAPE BOTTOM`). |
| CSB-10 | O sistema deve exibir no máximo os 12 primeiros pagamentos do histórico, interrompendo a leitura ao ultrapassar 12 registros. | State-driven | [CONSBENF.NSN#L155-L158](legado-sifap/natural-programs/CONSBENF.NSN#L155) | Inferida | Magic number `12` (também no título "ULTIMOS 12"). Ordem real = ordem do `READ`; não há garantia explícita de "mais recentes". Ver M-CSB-03. |
| CSB-11 | Quando o beneficiário não possuir nenhum pagamento, o sistema deve exibir "NENHUM PAGAMENTO ENCONTRADO". | Unwanted | [CONSBENF.NSN#L166-L168](legado-sifap/natural-programs/CONSBENF.NSN#L166) | Inferida | — |

### Rascunho EARS (regras confirmadas)

> Nenhuma regra deste programa foi **confirmada** contra o documento 2012: o documento descreve cadastro, cálculo, descontos, elegibilidade e batch — não cobre a **consulta online** (CONSBENF). As regras de status e mascaramento até tocam temas do documento, mas **divergem** dele (status) ou **não constam** nele (máscara), portanto são `Inferida`/`Mistério`, não `Confirmada`. Os rascunhos abaixo são candidatos a promoção após esclarecer os mistérios M-CSB-01 e M-CSB-02.

- **REQ (CSB-05):** O sistema de consulta SEMPRE deve mascarar o CPF do beneficiário antes de exibi-lo em tela ou relatório. *(pendente de M-CSB-02 sobre o formato correto)*
- **REQ (CSB-03):** QUANDO o operador informar o tipo de busca, o sistema deve aceitar busca por CPF (`'C'`) ou por NIS (`'N'`) e rejeitar qualquer outro valor.
- **REQ (CSB-10):** ENQUANTO percorrer o histórico de pagamentos de um beneficiário, o sistema deve exibir no máximo 12 registros. *(pendente de M-CSB-03 sobre o critério de ordenação)*

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-CSB-01** — **Conjunto de status divergente.** CONSBENF decodifica `STATUS` em 5 valores (`A`=ATIVO, `S`=SUSPENSO, `C`=CANCELADO, `I`=INATIVO, `D`=DESLIGADO) em [#L110-L123](legado-sifap/natural-programs/CONSBENF.NSN#L110), mas RN-002/RN-011 do documento 2012 só mencionam `BN-CD-SIT` `A`(ativo)/`E`(excluído). Um beneficiário "excluído logicamente" (`'E'`) cairia em "DESCONHECIDO" nesta tela. Pergunta: o campo `BENEFICIARIO.STATUS` desta view é o mesmo `BN-CD-SIT`? Existem dois campos de situação? Os valores `S/C/I/D` foram introduzidos depois de 2012?
- **M-CSB-02** — **Bug de mascaramento de CPF "intencional".** A sub-rotina `MASCARA-CPF` ([#L177-L188](legado-sifap/natural-programs/CONSBENF.NSN#L177)) revela os **3 primeiros** dígitos quando o CPF tem menos de 11 dígitos e os **últimos 5** caso contrário, e o comentário em [#L170-L174](legado-sifap/natural-programs/CONSBENF.NSN#L170) determina "NAO CORRIGIR SEM APROVACAO DA AUDITORIA". Risco de vazamento de dado sensível (LGPD). Pergunta: qual é o formato de mascaramento correto/aprovado? Por que a auditoria travou a correção?
- **M-CSB-03** — **Critério dos "últimos 12" pagamentos não garantido.** A tela anuncia "HISTORICO PAGAMENTOS (ULTIMOS 12)" ([#L141](legado-sifap/natural-programs/CONSBENF.NSN#L141)), mas o `READ ... BY CPF-BENEF` ([#L151](legado-sifap/natural-programs/CONSBENF.NSN#L151)) corta nos **primeiros 12** registros na ordem do descritor — não há `DESCENDING` nem ordenação por competência/data. Pergunta: a ordem física do arquivo PAGAMENTO coincide com "mais recentes primeiro"? Caso não, a tela mostra os 12 **mais antigos** rotulados como "últimos 12".

---

## Regras de RELAUDIT.NSN

> **Programa:** [`legado-sifap/natural-programs/RELAUDIT.NSN`](legado-sifap/natural-programs/RELAUDIT.NSN) (234 linhas) — relatório da trilha de auditoria do SIFAP: lê o DDM `AUDITORIA` por data e lista eventos filtrados por período/ação/usuário/tabela, com saída em tela ou impressora (arquivo 170).
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** o módulo de auditoria foi declarado **fora do escopo** do levantamento de 2012 (linha 249 do doc: *"Regras de auditoria (RELAUDIT) — Módulo não coberto pelo escopo inicial"*), portanto quase todas as regras abaixo são **inferidas** apenas do código. O ponto mais sensível é que eventos de **exclusão (`ACAO = 'EX'`) são silenciosamente suprimidos** do relatório de trilha de auditoria — o oposto do que se esperaria de uma trilha de auditoria.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| RA-01 | Quando o tipo de saída não é informado (branco), o sistema assume saída em **tela** (`'T'`). | Event-driven | [RELAUDIT.NSN#L80-L82](legado-sifap/natural-programs/RELAUDIT.NSN#L80) | Inferida | Default de UX; sem referência na doc. |
| RA-02 | Quando a data inicial não é informada (0), o sistema assume **19970101** como início do período. | Event-driven | [RELAUDIT.NSN#L84-L86](legado-sifap/natural-programs/RELAUDIT.NSN#L84) | Mistério | Magic number `19970101`: por que 1997? Sistema iniciou nessa data? Auditoria só existe a partir daí? <!-- mystery: origem da data 19970101 como piso do período de auditoria --> |
| RA-03 | Quando a data final não é informada (0), o sistema assume a **data de hoje** (`*DATN`) como fim do período. | Event-driven | [RELAUDIT.NSN#L87-L89](legado-sifap/natural-programs/RELAUDIT.NSN#L87) | Inferida | Default razoável de período aberto. |
| RA-04 | Os registros de auditoria são lidos **ordenados por data do evento** (`READ ... BY DT-EVENTO`); registros antes da data inicial são pulados (`ESCAPE TOP`) e a leitura encerra ao passar da data final (`ESCAPE BOTTOM`). | State-driven | [RELAUDIT.NSN#L92-L98](legado-sifap/natural-programs/RELAUDIT.NSN#L92) | Inferida | Leitura lógica indexada por `DT-EVENTO`; eficiente para janelas de tempo. |
| RA-05 | Eventos cuja ação é **`'EX'` (exclusão) nunca são exibidos** no relatório de auditoria — são contados como filtrados e descartados. | Unwanted | [RELAUDIT.NSN#L105-L108](legado-sifap/natural-programs/RELAUDIT.NSN#L105) | Mistério | Crítico: uma trilha de auditoria que **oculta exclusões** contraria o propósito de auditoria. Intencional (LGPD/privacidade?) ou bug? Comentário no código diz apenas "EXCLUSOES NAO SAO EXIBIDAS". <!-- mystery: por que eventos de exclusão (ACAO='EX') são suprimidos da trilha de auditoria --> |
| RA-06 | Quando o filtro de ação é informado, apenas eventos com ação igual ao filtro são exibidos; os demais são descartados. | Optional | [RELAUDIT.NSN#L111-L116](legado-sifap/natural-programs/RELAUDIT.NSN#L111) | Inferida | Filtro opcional de usuário. |
| RA-07 | Quando o filtro de usuário é informado, apenas eventos daquele usuário são exibidos. | Optional | [RELAUDIT.NSN#L119-L124](legado-sifap/natural-programs/RELAUDIT.NSN#L119) | Inferida | Filtro opcional. |
| RA-08 | Quando o filtro de tabela é informado, apenas eventos da tabela referenciada são exibidos. | Optional | [RELAUDIT.NSN#L127-L132](legado-sifap/natural-programs/RELAUDIT.NSN#L127) | Inferida | Filtro opcional. |
| RA-09 | Cada evento exibido é classificado por código de ação em uma das categorias: `IN`=Inclusão, `AL`=Alteração, `CO`=Conciliação, `CN`=Consulta, `DV`=Divergência; qualquer outro código cai em "Outra". | Ubiquitous | [RELAUDIT.NSN#L137-L161](legado-sifap/natural-programs/RELAUDIT.NSN#L137) | Inferida | Catálogo de códigos de ação não documentado. Note inconsistência: `CO`=Conciliação mas `CN`=Consulta (não alfabético). `EX` (RA-05) não aparece aqui pois já foi filtrado. <!-- mystery: catálogo oficial dos códigos de ACAO (IN/AL/CO/CN/DV/EX) não consta na doc --> |
| RA-10 | A hora do evento (`HHMMSS`) é formatada para exibição como `HH:MM:SS`. | Ubiquitous | [RELAUDIT.NSN#L163-L166](legado-sifap/natural-programs/RELAUDIT.NSN#L163) | Inferida | Formatação de apresentação, sem regra de negócio. |
| RA-11 | Uma nova página/cabeçalho é impressa quando a linha atual atinge o limite de página menos 5 (`66 - 5 = 61`). | State-driven | [RELAUDIT.NSN#L164-L166](legado-sifap/natural-programs/RELAUDIT.NSN#L164), [#L46](legado-sifap/natural-programs/RELAUDIT.NSN#L46) | Inferida | `#MAX-LINHAS=66` (folha 66 linhas); margem de 5 linhas para rodapé. |
| RA-12 | A saída em **tela** omite a coluna DESCRICAO; a saída em **impressora** inclui a DESCRICAO completa do evento. | State-driven | [RELAUDIT.NSN#L169-L186](legado-sifap/natural-programs/RELAUDIT.NSN#L169) | Inferida | Tela usa `WRITE` (largura 100); impressora usa `PRINT` (largura 120) com coluna extra. |
| RA-13 | Ao final, o relatório emite um resumo com totais: registros totais lidos, exibidos, filtrados, e contagem por tipo de ação (inclusões, alterações, consultas, conciliações, divergências, outras). | Ubiquitous | [RELAUDIT.NSN#L190-L206](legado-sifap/natural-programs/RELAUDIT.NSN#L190) | Inferida | Note: o resumo **não** reporta as exclusões suprimidas em RA-05, mascarando-as inclusive na contagem de "filtrados" agregada. |

### Rascunho EARS (regras confirmadas)

> Nenhuma regra deste programa é **Confirmada** contra a doc de 2012 — o módulo de auditoria foi explicitamente declarado fora de escopo (linha 249). As regras abaixo são as candidatas mais sólidas (inferidas do código) para futura validação com a área de negócio.

- **REQ (RA-04):** O sistema deve listar os eventos de auditoria ordenados por data do evento, restritos à janela `[data inicial, data final]` informada pelo operador.
- **REQ (RA-06/RA-07/RA-08):** Onde o operador informar filtros de ação, usuário ou tabela, o sistema deve exibir somente os eventos que satisfaçam todos os filtros informados.
- **REQ (RA-09):** O sistema deve classificar cada evento de auditoria exibido em uma categoria de ação (Inclusão, Alteração, Conciliação, Consulta, Divergência ou Outra) com base no código de ação registrado.
- **REQ (RA-13):** Ao concluir a listagem, o sistema deve apresentar totais consolidados por tipo de ação e os contadores de registros totais, exibidos e filtrados.

### Discrepâncias código × documentação (para `/catalog-mysteries`)

- **M-RA-01** — Eventos de exclusão (`ACAO = 'EX'`) são **suprimidos** da trilha de auditoria ([L105-L108](legado-sifap/natural-programs/RELAUDIT.NSN#L105)). Isso contraria a expectativa de uma trilha de auditoria e não tem qualquer respaldo na doc de 2012. Decidir se é requisito legítimo (privacidade?) ou defeito histórico antes de replicar no sistema moderno.
- **M-RA-02** — Magic number `19970101` como piso default do período ([L84-L86](legado-sifap/natural-programs/RELAUDIT.NSN#L84)). Confirmar se corresponde à data de início da operação do SIFAP / início da gravação de auditoria.
- **M-RA-03** — O catálogo de códigos de ação (`IN/AL/CO/CN/DV/EX`) só existe implicitamente no código ([L137-L161](legado-sifap/natural-programs/RELAUDIT.NSN#L137)); a doc menciona auditoria automática (RN-010) e ocorrências tipo `'B'`/`'DV'` (linhas 194 e RN-021 do doc) mas **não** lista os códigos. Reconciliar com `LOGAUDIT` (subprograma citado na RN-010) para obter o conjunto canônico.
- **M-RA-04** — Módulo declarado fora de escopo na doc de 2012 (linha 249), porém a RN-021 do doc afirma que descontos acima de 30% "geram ocorrência de auditoria" e a regra de elegibilidade (linha 194) bloqueia beneficiário com "ocorrência de auditoria não resolvida tipo 'B'". Há dependências de negócio sobre AUDITORIA que este relatório não cobre — mapear os produtores de registros de auditoria (ex.: `LOGAUDIT`, `BATCHCON`) antes de modernizar.

---

## Regras de RELPGT.NSN

> **Programa:** [`legado-sifap/natural-programs/RELPGT.NSN`](legado-sifap/natural-programs/RELPGT.NSN) (≈216 linhas) — relatório analítico (impressora mainframe, 66 linhas/página) que lista pagamentos por faixa de competência, com quebra/subtotal por programa e totais gerais.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** É um programa de **saída/relatório read-only** (só `READ`/`FIND`/`PRINT`, nenhum `STORE`/`UPDATE`). O levantamento de 2012 **não cobre RELPGT** (citado apenas como "RELAUDIT" fora de escopo na seção 6), logo quase todas as regras aqui são **Inferidas do código**. O ponto crítico é a **decodificação dos status de pagamento (L128-141), que contradiz a semântica de status descrita na seção 5 do doc** — pendência real de domínio.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| RP-01 | O operador informa competência inicial, competência final e código de programa (0 = todos) via tela antes do processamento. | Event-driven | [RELPGT.NSN#L75-L79](legado-sifap/natural-programs/RELPGT.NSN#L75) | Inferida | Tela `INPUT` interativa; `#COD-PROG-FILTRO = 0` significa "todos os programas". |
| RP-02 | A leitura percorre PAGAMENTO em ordem de COMPETENCIA, iniciando na competência inicial informada. | Ubíqua | [RELPGT.NSN#L82](legado-sifap/natural-programs/RELPGT.NSN#L82) | Inferida | `READ PAGAMENTO-V BY COMPETENCIA = #COMP-INI` (leitura lógica por descritor). |
| RP-03 | Quando a competência do registro for maior que a competência final, a leitura é encerrada. | State-driven | [RELPGT.NSN#L83-L85](legado-sifap/natural-programs/RELPGT.NSN#L83) | Inferida | `ESCAPE BOTTOM` — limite superior do período. |
| RP-04 | Quando há filtro de programa (≠0) e o registro pertence a outro programa, o registro é ignorado. | Optional / Event-driven | [RELPGT.NSN#L87-L90](legado-sifap/natural-programs/RELPGT.NSN#L87) | Inferida | `ESCAPE TOP` pula o registro mas continua a leitura. |
| RP-05 | Sempre que o código de programa muda (e não é o primeiro registro), imprime-se o subtotal do programa anterior e zeram-se os acumuladores parciais. | Event-driven (control break) | [RELPGT.NSN#L93-L99](legado-sifap/natural-programs/RELPGT.NSN#L93) | Inferida | Quebra por `COD-PROGRAMA`; `PERFORM IMPRIME-SUBTOTAL` + reset de `#SUB-*`. Alteração de 2010 ("INC SUBTOTAL PROG", L7). |
| RP-06 | Para cada pagamento, o nome (30 primeiros caracteres) e a UF do beneficiário são buscados pelo CPF; se não encontrado, ficam em branco. | Ubíqua | [RELPGT.NSN#L101-L107](legado-sifap/natural-programs/RELPGT.NSN#L101) | Inferida | `FIND BENEFICIARIO-V WITH CPF`; `SUBSTR(...,1,30)`. Sem `FIND`-match → campos brancos (default L102-103). |
| RP-07 | O CPF é exibido mascarado no formato `***.NNN.NNN-NN` (três primeiros dígitos ocultos). | Ubíqua | [RELPGT.NSN#L109-L113](legado-sifap/natural-programs/RELPGT.NSN#L109) | Inferida | Possível requisito de privacidade; não documentado em 2012 (anterior à LGPD). |
| RP-08 | O tipo de pagamento é traduzido: N→NORMAL, D→DECIMO, T→TERCEIRO; qualquer outro→OUTRO. | State-driven | [RELPGT.NSN#L116-L125](legado-sifap/natural-programs/RELPGT.NSN#L116) | Inferida | `DECIDE ON FIRST VALUE`. "DECIMO"/"TERCEIRO" sugerem 13º/abono — ver M-RP-02. |
| RP-09 | O status de pagamento é traduzido: G→GERADO, P→PAGO, C→CANCELAD, D→DEVOLVID, E→ESTORNAD; qualquer outro→OUTRO. | State-driven | [RELPGT.NSN#L128-L141](legado-sifap/natural-programs/RELPGT.NSN#L128) | **Mistério** | Contradiz a seção 5 do doc (P=pendente, E=erro). Ver M-RP-01. |
| RP-10 | A paginação assume 66 linhas/página; um novo cabeçalho é impresso quando a linha atual atinge (máx − 5). | Ubíqua | [RELPGT.NSN#L62-L65, L144-L146](legado-sifap/natural-programs/RELPGT.NSN#L144) | Inferida | `#LINHA` inicia em 99 (L64) para forçar cabeçalho no 1º registro; cabeçalho reseta `#LINHA=6`. |
| RP-11 | Cada linha de detalhe é impressa com competência, CPF mascarado, nome, UF, bruto, desconto, líquido, status e tipo. | Ubíqua | [RELPGT.NSN#L149-L157](legado-sifap/natural-programs/RELPGT.NSN#L149) | Inferida | Layout fixo de relatório. |
| RP-12 | Para cada pagamento listado, acumulam-se bruto, desconto, líquido, abono e quantidade nos totais gerais e parciais. | Ubíqua | [RELPGT.NSN#L160-L168](legado-sifap/natural-programs/RELPGT.NSN#L160) | Inferida | **Nenhum filtro de status nos acumuladores** — cancelados/estornados também somam. Ver M-RP-03. |
| RP-13 | Ao fim da leitura, imprime-se o último subtotal de programa e, em seguida, o total geral (qtd, bruto, desconto, líquido) e o total de abono. | Event-driven | [RELPGT.NSN#L172-L185](legado-sifap/natural-programs/RELPGT.NSN#L172) | Inferida | `IF #PROG-ANT NE 0` evita subtotal espúrio em base vazia. |
| RP-14 | O cabeçalho exibe o período solicitado, a data corrente do sistema e o número de página. | Ubíqua | [RELPGT.NSN#L61, L188-L200](legado-sifap/natural-programs/RELPGT.NSN#L188) | Inferida | `#DT-HOJE = *DATN`; `PRINT '/'` = form-feed mainframe. |

### Rascunho EARS (regras confirmadas)

> Nenhuma regra deste programa foi **confirmada** contra o doc de 2012 — RELPGT está fora do escopo daquele levantamento. Todas as regras acima são **Inferidas** (lidas direto do código) ou **Mistério**. Os rascunhos EARS abaixo são candidatos a validação pelo Estágio 2, ainda sem `RN-NNN` correspondente.

- **REQ (RP-02/RP-03):** *Ubíqua* — "O relatório de pagamentos DEVE listar os registros de PAGAMENTO cuja COMPETENCIA esteja entre a competência inicial e a final informadas, em ordem crescente de competência."
- **REQ (RP-04):** *Opcional* — "Onde um código de programa for informado (diferente de zero), o relatório DEVE restringir a listagem aos pagamentos daquele programa."
- **REQ (RP-05/RP-13):** *Event-driven* — "Quando o código de programa muda durante a leitura, o sistema DEVE emitir um subtotal (quantidade, bruto, líquido) do programa anterior."
- **REQ (RP-07):** *Ubíqua* — "O relatório DEVE exibir o CPF de forma mascarada, ocultando os três primeiros dígitos (`***.NNN.NNN-NN`)."

### Discrepâncias código × documentação (para `/catalog-mysteries`)

<!-- mystery: RELPGT L128-141 decodifica STATUS-PGTO como G=GERADO, P=PAGO, C=CANCELADO, D=DEVOLVIDO, E=ESTORNADO; mas REGRAS-NEGOCIO-2012 secao 5.1 diz que BATCHPGT grava status 'P'=PENDENTE e secao 5.2 diz 'E'=ERRO. Significados conflitantes do mesmo codigo. -->
- **M-RP-01 — Semântica dos status de pagamento conflitante.** RELPGT (L128-141) lê `P` como **PAGO** e `E` como **ESTORNADO**. A doc 2012 (seção 5.1) diz que o batch grava status `P` = **pendente**, e (seção 5.2) que `E` = **erro**. Não é possível dizer pelo código se houve mudança de semântica entre versões ou se a doc está errada. *Investigar:* dicionário oficial de domínio de `STATUS-PGTO`/`PG-CD-STATUS` e qual versão do batch está vigente.

<!-- mystery: RELPGT acumula VLR-ABONO em #TOT-ABONO (L164, L184) e decodifica TIPO 'D'=DECIMO / 'T'=TERCEIRO (L119-122), mas o doc trata 13o/abono natalino como regra NAO documentada (secao 6, prioridade Alta). -->
- **M-RP-02 — Abono / 13º (VLR-ABONO, tipos DECIMO/TERCEIRO).** O programa possui campo `VLR-ABONO`, acumulador `#TOT-ABONO` e tipos `D=DECIMO`/`T=TERCEIRO` (L119-122, L164, L184), mas **nunca exibe o abono na linha de detalhe** — só no total final. A doc 2012 marca o cálculo do 13º/abono natalino e o "FATOR-K" como **não documentados / alta prioridade** (seção 6). *Investigar:* origem de `VLR-ABONO`, relação com 13º benefício e por que não aparece por linha.

<!-- mystery: RELPGT acumula totais (L160-168) sem filtrar por status; registros C=CANCELADO/D=DEVOLVIDO/E=ESTORNADO entram nos totais gerais e subtotais. -->
- **M-RP-03 — Totais incluem pagamentos cancelados/devolvidos/estornados.** Os acumuladores (L160-168) somam **todos** os registros lidos, sem excluir status `C`/`D`/`E`. Não há na doc nenhuma regra que diga se o "total geral" de pagamentos deve considerar apenas pagamentos efetivados. *Investigar:* definição de negócio do que compõe o "TOTAL GERAL" do relatório (bruto pago vs. bruto gerado).

---

## Regras de VALBENEF.NSN

> **Programa:** [`legado-sifap/natural-programs/VALBENEF.NSN`](legado-sifap/natural-programs/VALBENEF.NSN) (279 linhas) — subrotina de validação dos dados cadastrais do beneficiário, chamada antes da gravação no arquivo 150 (BENEFICIARIO).
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** o programa valida CPF (módulo-11 inline, **não** via subprograma `VALCPF` citado na RN-001), data de nascimento, nome, UF e status — mas **não** valida idade mínima (RN-006), dados bancários (RN-007) nem NIS/NIT (RN-001). O conjunto de status aceito (`A/S/C/I/D`) diverge do documentado (`A`→`E`). Há um backdoor explícito para CPFs `000…` ("teste governo").

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| VB-01 | Ao validar um beneficiário, o CPF deve passar na validação de dígito verificador por módulo-11 (dois DVs); se inválido, o resultado é marcado 'I' e gera erro "CPF INVALIDO - DIGITO VERIFICADOR". | Event-driven | [VALBENEF.NSN#L115-L120](legado-sifap/natural-programs/VALBENEF.NSN#L115) · [#L178-L239](legado-sifap/natural-programs/VALBENEF.NSN#L178) | Confirmada (RN-001) | RN-001 cita subprograma `VALCPF`; aqui a validação é **inline** (`VALIDA-CPF-COMPLETO`). Divergência de implementação — ver M-VB-01. |
| VB-02 | CPF com todos os 11 dígitos iguais é considerado inválido. | Unwanted behavior | [VALBENEF.NSN#L188-L203](legado-sifap/natural-programs/VALBENEF.NSN#L188) | Inferida | Não documentado na RN-001; regra clássica de validação de CPF. |
| VB-03 | Exceção: CPF com todos os dígitos iguais **e** iniciado por `000` é tratado como VÁLIDO (comentário "TESTE GOVERNO"). | Unwanted behavior | [VALBENEF.NSN#L195-L203](legado-sifap/natural-programs/VALBENEF.NSN#L195) | Mistério | Backdoor de teste em produção; possível paralelo ao "bypass região 99" / "bypass do Roberto" da RN-005. Ver M-VB-02. |
| VB-04 | Ao validar, a data de nascimento deve ter ano entre 1900 e o ano atual, mês entre 1 e 12 e dia entre 1 e o nº de dias do mês; senão gera "DATA NASCIMENTO INVALIDA". | Event-driven | [VALBENEF.NSN#L125-L130](legado-sifap/natural-programs/VALBENEF.NSN#L125) · [#L242-L259](legado-sifap/natural-programs/VALBENEF.NSN#L242) | Inferida | RN-006 exige idade ≥ 16 — **não há** essa checagem no código. Ver M-VB-03. |
| VB-05 | Fevereiro sempre aceita até 29 dias (tabela fixa `#DIAS-MES(2)=29`, sem cálculo de ano bissexto). | State-driven | [VALBENEF.NSN#L96](legado-sifap/natural-programs/VALBENEF.NSN#L96) · [#L256](legado-sifap/natural-programs/VALBENEF.NSN#L256) | Mistério | Comentário "CONSIDERA BISSEXTO" — aceita 29/02 em qualquer ano. Possível bug. Ver M-VB-04. |
| VB-06 | O nome não pode ser branco e deve conter pelo menos um espaço em posição > 1 (nome + sobrenome); senão gera "NOME INVALIDO". | Event-driven | [VALBENEF.NSN#L135-L140](legado-sifap/natural-programs/VALBENEF.NSN#L135) · [#L262-L277](legado-sifap/natural-programs/VALBENEF.NSN#L262) | Inferida | Regra incluída em 30/11/2010 (cabeçalho, L7). Não documentada. |
| VB-07 | Quando UF é informada (≠ branco), deve constar na tabela interna das 27 UFs; senão gera "UF INVALIDA". | State-driven | [VALBENEF.NSN#L145-L159](legado-sifap/natural-programs/VALBENEF.NSN#L145) · [#L66-L92](legado-sifap/natural-programs/VALBENEF.NSN#L66) | Inferida | UF branca é aceita (validação só ocorre se preenchida). Relaciona-se à RN-005 (região 01-27) mas usa sigla, não código numérico. |
| VB-08 | O status deve ser um de `A`, `S`, `C`, `I` ou `D`; qualquer outro gera "STATUS INVALIDO". | State-driven | [VALBENEF.NSN#L164-L169](legado-sifap/natural-programs/VALBENEF.NSN#L164) | Mistério | RN-011 documenta apenas `A` (ativo) e `E` (excluído). O código **rejeita** `E` e aceita `S/C/I/D` não documentados. Ver M-VB-05. |
| VB-09 | A validação acumula múltiplos erros (`#QTD-ERROS`, até 10 mensagens) e só marca 'V' se nenhum erro ocorrer; todos os erros são listados ao final. | Ubiquitous | [VALBENEF.NSN#L108-L109](legado-sifap/natural-programs/VALBENEF.NSN#L108) · [#L172-L175](legado-sifap/natural-programs/VALBENEF.NSN#L172) | Inferida | Buffer `#MSG-ERRO` tem 10 posições; sem proteção contra estouro se >10 erros (improvável com 5 validações). |

### Rascunho EARS (regras confirmadas)

- **REQ (VB-01):** When the system validates a beneficiary record before persisting it (file 150), the system shall reject the record if the CPF fails módulo-11 check-digit validation (both verifier digits), producing the error "CPF INVALIDO - DIGITO VERIFICADOR". *(source_legacy: VALBENEF.NSN#L115-L239)*
- **REQ (VB-04):** When the system validates a beneficiary record, the system shall reject any birth date whose year is outside 1900..current-year, month outside 1..12, or day outside the valid range for the month. *(source_legacy: VALBENEF.NSN#L125-L259)*
- **REQ (VB-06):** When the system validates a beneficiary record, the system shall require the name to be non-blank and to contain at least one space after the first position (given name + surname). *(source_legacy: VALBENEF.NSN#L135-L277)*
- **REQ (VB-09):** The system shall accumulate all validation failures for a record and return result 'V' only when no failure occurred, otherwise 'I' with the list of error messages. *(source_legacy: VALBENEF.NSN#L108-L175)*

### Discrepâncias código × documentação (para `/catalog-mysteries`)

<!-- mystery: VALBENEF valida CPF inline via VALIDA-CPF-COMPLETO, mas RN-001 afirma que a validação ocorre no subprograma VALCPF. Existem duas implementações de validação de CPF? Qual é a autoritativa? -->
- **M-VB-01** — RN-001 diz que CPF é validado pelo subprograma `VALCPF`, mas VALBENEF tem rotina **própria** de módulo-11 ([#L178-L239](legado-sifap/natural-programs/VALBENEF.NSN#L178)). Investigar se há duplicidade/divergência entre `VALCPF` e `VALIDA-CPF-COMPLETO`.

<!-- mystery: backdoor "teste governo" aceita CPFs 00000000000..00099999999 com todos os dígitos iguais. Origem, autorização e impacto em produção desconhecidos. -->
- **M-VB-02** — Backdoor em [#L195-L203](legado-sifap/natural-programs/VALBENEF.NSN#L195): CPFs com dígitos iguais iniciados por `000` passam como válidos. Não documentado. Possível paralelo ao "bypass" mencionado na RN-005. Verificar quem usa e se há registros assim em produção.

<!-- mystery: RN-006 exige idade mínima de 16 anos, mas VALBENEF não calcula idade — apenas valida formato da data. Onde a idade é validada (se for)? -->
- **M-VB-03** — RN-006 exige idade ≥ 16 anos na inclusão, porém VALBENEF **não calcula idade** ([#L242-L259](legado-sifap/natural-programs/VALBENEF.NSN#L242) só valida formato). A regra de idade está em outro programa (CADBENEF?) ou não é aplicada?

<!-- mystery: fevereiro fixo em 29 dias sem cálculo de bissexto — aceita 29/02 em anos não-bissextos. Bug ou intencional? -->
- **M-VB-04** — `#DIAS-MES(2)=29` fixo ([#L96](legado-sifap/natural-programs/VALBENEF.NSN#L96)) faz a validação aceitar 29/02 em qualquer ano. Confirmar se é bug histórico e qual o impacto.

<!-- mystery: VALBENEF aceita status A/S/C/I/D e rejeita E; RN-011 documenta apenas A e E. Significado de S/C/I/D e por que E é rejeitado? -->
- **M-VB-05** — Conjunto de status divergente: código aceita `A/S/C/I/D` ([#L164-L165](legado-sifap/natural-programs/VALBENEF.NSN#L164)) e **rejeita** `E` (excluído), enquanto RN-011 só cita `A`/`E`. Levantar o significado de `S`, `C`, `I`, `D` e por que `E` não é aceito na validação cadastral.

<!-- mystery: RN-007 torna dados bancários obrigatórios para beneficiários ativos, mas VALBENEF não valida banco/agência/conta. Onde isso é validado? -->
- **M-VB-06** — Ausência: RN-007 exige dados bancários obrigatórios para ativos, mas VALBENEF não os valida (campos banco/agência/conta nem estão na VIEW, [#L13-L20](legado-sifap/natural-programs/VALBENEF.NSN#L13)). Localizar onde a validação bancária (e NIS/NIT da RN-001) ocorre.

---

## Regras de VALDOCS.NSN

> **Programa:** [`legado-sifap/natural-programs/VALDOCS.NSN`](legado-sifap/natural-programs/VALDOCS.NSN) (184 linhas) — subrotina de validação de documentos do beneficiário (CPF, RG e "documentos especiais") associada ao arquivo 150 (BENEFICIARIO); captura também Título de Eleitor e CTPS na tela, mas não os valida.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** o programa valida CPF por módulo-11 **inline** (rotina `VALIDA-CPF-DOC`), **não** via subprograma `VALCPF` citado na RN-001 — é a **terceira** implementação de CPF encontrada (junto de `VALCPF` e do `VALIDA-CPF-COMPLETO` de VALBENEF). Diferente de VALBENEF, **não rejeita CPF com dígitos repetidos**. Valida RG (≥5 caracteres, regra de 2003 não documentada). **Não valida NIS/NIT** exigido pela RN-001, e ignora Título/CTPS que captura na tela. Há um **backdoor** (`CHECK-DOC-ESPECIAL`, incluído por Roberto Mendes em 07/06/2011) que, para 8 prefixos de CPF (`000/001/002/010/011/099/100/999`), força `RESULTADO='V'`, zera os erros e **anula até a validação de RG**.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| VD-01 | Ao validar documentos, se o CPF for igual a 0 (zero/branco), o CPF é considerado inválido e a rotina encerra imediatamente. | Unwanted behavior | [VALDOCS.NSN#L102-L105](legado-sifap/natural-programs/VALDOCS.NSN#L102) | Inferida | Guarda inicial em `VALIDA-CPF-DOC`. |
| VD-02 | O CPF deve passar na validação de dois dígitos verificadores por módulo-11 (peso 10→2 no 1º DV; peso 11→2 no 2º DV; resto < 2 ⇒ DV = 0, senão DV = 11−resto); qualquer DV divergente torna o CPF inválido. | Event-driven | [VALDOCS.NSN#L100-L143](legado-sifap/natural-programs/VALDOCS.NSN#L100) | Confirmada (RN-001) | RN-001 atribui a validação ao subprograma `VALCPF`; aqui é **inline**. Ver M-VD-01. **Não** há checagem de dígitos repetidos (ao contrário de VALBENEF/VB-02). Ver M-VD-05. |
| VD-03 | Quando o CPF é inválido, acumula a mensagem "CPF INVALIDO" e marca o resultado como 'I'. | Event-driven | [VALDOCS.NSN#L68-L73](legado-sifap/natural-programs/VALDOCS.NSN#L68) | Inferida | Fluxo principal após `PERFORM VALIDA-CPF-DOC`. |
| VD-04 | Quando o RG está em branco, o RG é considerado inválido e a rotina de RG encerra. | Event-driven | [VALDOCS.NSN#L148-L151](legado-sifap/natural-programs/VALDOCS.NSN#L148) | Inferida | RG não documentado em nenhuma RN. Ver M-VD-06. |
| VD-05 | O RG (quando preenchido) deve ter pelo menos 5 caracteres significativos; senão é inválido. | State-driven | [VALDOCS.NSN#L154-L162](legado-sifap/natural-programs/VALDOCS.NSN#L154) | Inferida | Comprimento medido por `EXAMINE ... GIVING POSITION`; se não houver branco, assume 15. |
| VD-06 | Quando o RG é inválido ou tem formato incorreto, acumula a mensagem "RG INVALIDO OU FORMATO INCORRETO" e marca o resultado como 'I'. | Event-driven | [VALDOCS.NSN#L78-L83](legado-sifap/natural-programs/VALDOCS.NSN#L78) | Inferida | Regra de validação de RG introduzida em 22/09/2003 (cabeçalho, L5). |
| VD-07 | Se os 3 primeiros dígitos do CPF pertencerem ao conjunto {`000`,`001`,`002`,`010`,`011`,`099`,`100`,`999`}, o documento é tratado como "especial": força CPF válido, `RESULTADO='V'` e **zera todos os erros acumulados** (inclusive de RG). | Unwanted behavior | [VALDOCS.NSN#L49-L56](legado-sifap/natural-programs/VALDOCS.NSN#L49) · [#L168-L182](legado-sifap/natural-programs/VALDOCS.NSN#L168) | Mistério | Backdoor incluído por Roberto Mendes em 07/06/2011 ("AJUSTE CHECK ESPEC", L6). Anula toda a validação anterior. Ver M-VD-02. |
| VD-08 | A validação acumula até 5 mensagens de erro (`#MSG(A60/5)`) e o resultado só permanece 'V' se nenhuma validação falhar. | Ubiquitous | [VALDOCS.NSN#L45-L46](legado-sifap/natural-programs/VALDOCS.NSN#L45) · [#L21](legado-sifap/natural-programs/VALDOCS.NSN#L21) | Inferida | Buffer de 5 posições; sem proteção contra estouro (improvável com 2 validações). |

### Rascunho EARS (regras confirmadas)

- **REQ (VD-02):** When the system validates a beneficiary's documents (file 150), the system shall reject the CPF if it fails módulo-11 check-digit validation of both verifier digits, recording the error "CPF INVALIDO". *(source_legacy: VALDOCS.NSN#L100-L143)*

### Discrepâncias código × documentação (para `/catalog-mysteries`)

<!-- mystery: VALDOCS valida CPF inline via VALIDA-CPF-DOC, mas RN-001 afirma que a validação ocorre no subprograma VALCPF; VALBENEF usa ainda um terceiro caminho (VALIDA-CPF-COMPLETO). Qual é a implementação autoritativa de CPF? -->
- **M-VD-01** — RN-001 diz que o CPF é validado pelo subprograma `VALCPF`, mas VALDOCS tem rotina **própria** de módulo-11 ([#L100-L143](legado-sifap/natural-programs/VALDOCS.NSN#L100)). Já são **três** caminhos de validação de CPF no sistema (`VALCPF`, `VALIDA-CPF-COMPLETO` em VALBENEF, `VALIDA-CPF-DOC` aqui). Levantar qual é o canônico e onde divergem.

<!-- mystery: backdoor CHECK-DOC-ESPECIAL aceita CPFs com prefixos 000/001/002/010/011/099/100/999 e força RESULTADO='V', anulando inclusive a validação de RG. Incluído por Roberto Mendes em 07/06/2011. Origem, autorização e impacto desconhecidos. -->
- **M-VD-02** — Backdoor em [#L168-L182](legado-sifap/natural-programs/VALDOCS.NSN#L168) (prefixos carregados em [#L49-L56](legado-sifap/natural-programs/VALDOCS.NSN#L49)): 8 prefixos de CPF passam como válidos e **zeram todos os erros**, anulando até a validação de RG. Adicionado por Roberto Mendes ("AJUSTE CHECK ESPEC", [#L6](legado-sifap/natural-programs/VALDOCS.NSN#L6)). Mesma família do backdoor de VALBENEF (M-VB-02) e do "bypass do Roberto"/região 99 da RN-005. Mapear todos os bypasses e quem os usa.

<!-- mystery: RN-001 exige validação de NIS/NIT via subprograma VALNISN, mas VALDOCS não valida NIS/NIT em nenhum ponto. Onde a validação de NIS ocorre? -->
- **M-VD-03** — RN-001 exige NIS/NIT válido via subprograma `VALNISN`, porém VALDOCS **não valida NIS/NIT** em nenhum ponto. Localizar onde (e se) a validação de NIS é executada no fluxo cadastral.

<!-- mystery: VALDOCS captura TITULO ELEITOR e CTPS via INPUT (#TITULO, #CTPS) mas nunca os valida nem usa. Entradas mortas ou validação removida? -->
- **M-VD-04** — Título de Eleitor (`#TITULO`) e CTPS (`#CTPS`) são lidos na tela ([#L58-L62](legado-sifap/natural-programs/VALDOCS.NSN#L58)) mas **nunca validados ou usados**. Verificar se a validação foi removida ou se são campos mortos.

<!-- mystery: o módulo-11 de VALDOCS não rejeita CPF com 11 dígitos iguais, ao contrário de VALIDA-CPF-COMPLETO em VALBENEF. Regras de CPF inconsistentes entre programas. -->
- **M-VD-05** — `VALIDA-CPF-DOC` **não rejeita** CPFs com dígitos repetidos (sem o guard presente em VALBENEF/VB-02). Confirmar a inconsistência entre as implementações de CPF e o impacto (ex.: `11111111111` aceito aqui, recusado em VALBENEF).

<!-- mystery: validação de RG (>=5 caracteres, desde 22/09/2003) não consta em nenhuma RN documentada. -->
- **M-VD-06** — A validação de RG ([#L146-L163](legado-sifap/natural-programs/VALDOCS.NSN#L146)), introduzida em 22/09/2003, **não está documentada** em nenhuma RN. Levantar a regra de negócio original (por que ≥5 caracteres) e se ainda é desejada.

---

## Regras de VALELEG.NSN

> **Programa:** [`legado-sifap/natural-programs/VALELEG.NSN`](legado-sifap/natural-programs/VALELEG.NSN) (244 linhas) — subrotina online de **validação de elegibilidade** de um beneficiário (arquivo 150 BENEFICIARIO) frente a um programa social (arquivo 155 PROGRAMA-SOCIAL): lê os dois registros, deriva idade/renda/região, e acumula até 10 motivos de inelegibilidade em `#MOTIVO`.
> **Lido por:** `@archaeologist` (`/extract-business-rules`) em 2026-06-10, bloco a bloco, de cima a baixo.
> **Doc cruzada:** [`legacy-docs/REGRAS-NEGOCIO-2012.md`](legado-sifap/legacy-docs/REGRAS-NEGOCIO-2012.md).
> **Achado estrutural:** o **bypass da região 99** existe e é brutal — `IF #COD-REG = 99` força `#ELEGIVEL=TRUE`, escreve "REGIAO ESPECIAL" e faz `ESCAPE ROUTINE` **antes de qualquer validação** ([#L107-L111](legado-sifap/natural-programs/VALELEG.NSN#L107)), confirmando o "bypass do Roberto" da RN-005; o cabeçalho atribui a inclusão a Anderson Lima em 05/04/2013 ("INC REGIAO 99", [#L8](legado-sifap/natural-programs/VALELEG.NSN#L8)). **Nenhum** bloqueio por **ocorrência de auditoria tipo 'B'** existe no código, apesar de a doc (seção 4.2) afirmar que ele existe — o DDM AUDITORIA sequer é lido. A doc também descreve o programa com "~1.200 linhas de lógica condicional complexa"; o arquivo real tem 244 linhas. Idade é calculada **só por ano** (`#ANO-ATUAL - #ANO-NASC`), ignorando mês/dia ([#L72-L73](legado-sifap/natural-programs/VALELEG.NSN#L72)). Vários requisitos da doc 4.2 (dados bancários, máx. 2 programas, atualização ≤24 meses) **não aparecem** no código.

| # | Declaração da Regra | Candidato EARS | Fonte | Classificação | Notas |
|---|---|---|---|---|---|
| VE-01 | Se o CPF informado não for encontrado no arquivo de beneficiários (150), o sistema escreve "BENEFICIARIO NAO ENCONTRADO" e encerra sem avaliar elegibilidade. | Unwanted behavior | [VALELEG.NSN#L81-L84](legado-sifap/natural-programs/VALELEG.NSN#L81) | Inferida | `ESCAPE ROUTINE` aborta tudo. |
| VE-02 | Se o código de programa informado não for encontrado no arquivo de programas (155), o sistema escreve "PROGRAMA NAO ENCONTRADO" e encerra. | Unwanted behavior | [VALELEG.NSN#L94-L97](legado-sifap/natural-programs/VALELEG.NSN#L94) | Inferida | — |
| VE-03 | Se o status do programa social (`STATUS-PROG`) for diferente de 'A' (ativo), o sistema escreve "PROGRAMA INATIVO" e encerra. | State-driven | [VALELEG.NSN#L99-L102](legado-sifap/natural-programs/VALELEG.NSN#L99) | Confirmada (RN-003) | RN-003 exige vínculo a programa **ativo**. Aqui via `STATUS-PROG`, não `PS-IN-ATIVO`. Ver M-VE-04. |
| VE-04 | Se a região do beneficiário (`COD-REGIAO`) for igual a 99, o beneficiário é declarado **elegível** imediatamente, pulando todas as demais validações (status, idade, renda, tipo, docs, NIS). | Unwanted behavior | [VALELEG.NSN#L104-L111](legado-sifap/natural-programs/VALELEG.NSN#L104) | Mistério | "Bypass do Roberto" da RN-005; incluído por Anderson Lima 05/04/2013 ([#L8](legado-sifap/natural-programs/VALELEG.NSN#L8)). Ver M-VE-01. |
| VE-05 | Se o status do beneficiário for 'S' (suspenso), acumula o motivo "BENEFICIARIO SUSPENSO" e marca inelegível. | Event-driven | [VALELEG.NSN#L116-L120](legado-sifap/natural-programs/VALELEG.NSN#L116) | Inferida | — |
| VE-06 | Se o status do beneficiário for 'C' (cancelado) ou 'D' (desligado), acumula "BENEFICIARIO CANCELADO/DESLIGADO" e marca inelegível. | Event-driven | [VALELEG.NSN#L122-L125](legado-sifap/natural-programs/VALELEG.NSN#L122) | Inferida | — |
| VE-07 | Se o status do beneficiário for 'I' (inativo), acumula "BENEFICIARIO INATIVO" e marca inelegível. | Event-driven | [VALELEG.NSN#L127-L131](legado-sifap/natural-programs/VALELEG.NSN#L127) | Inferida | Status válidos implícitos: A/S/C/D/I. Ver M-VE-05. |
| VE-08 | Se o programa define idade mínima (>0) e a idade do beneficiário é menor que esse mínimo, acumula "IDADE INFERIOR AO MINIMO DO PROGRAMA" e marca inelegível. | State-driven | [VALELEG.NSN#L139-L145](legado-sifap/natural-programs/VALELEG.NSN#L139) | Inferida | Idade só por ano. Ver M-VE-03. |
| VE-09 | Se o programa define idade máxima (>0) e a idade do beneficiário é maior que esse máximo, acumula "IDADE SUPERIOR AO MAXIMO DO PROGRAMA" e marca inelegível. | State-driven | [VALELEG.NSN#L146-L152](legado-sifap/natural-programs/VALELEG.NSN#L146) | Inferida | — |
| VE-10 | Se o programa define renda máxima (>0) e a renda familiar do beneficiário a excede, acumula "RENDA FAMILIAR ACIMA DO TETO DO PROGRAMA" e marca inelegível. | State-driven | [VALELEG.NSN#L157-L163](legado-sifap/natural-programs/VALELEG.NSN#L157) | Inferida | RN-018 fala em renda **per capita**; aqui é renda familiar bruta. Ver M-VE-06. |
| VE-11 | Para programa tipo 'A' (assistencial), se a renda for maior que R$ 600,00 **e** o beneficiário tiver menos de 1 dependente, acumula "PROG ASSISTENCIAL: RENDA > 600 SEM DEPENDENTES" e marca inelegível. | State-driven | [VALELEG.NSN#L169-L177](legado-sifap/natural-programs/VALELEG.NSN#L169) | Inferida | Magic number 600,00 hardcoded. |
| VE-12 | Para programa tipo 'A', se a documentação não estiver OK (`#DOCS-OK NE 'S'`), acumula "DOCUMENTACAO INCOMPLETA" e marca inelegível. | State-driven | [VALELEG.NSN#L178-L182](legado-sifap/natural-programs/VALELEG.NSN#L178) | Inferida | Depende do flag `DOCUMENTOS-OK` (provável saída de VALDOCS). |
| VE-13 | Para programa tipo 'P' (previdenciário), se a idade for menor que 60, acumula "PROG PREVIDENCIARIO: IDADE < 60" e marca inelegível. | State-driven | [VALELEG.NSN#L183-L189](legado-sifap/natural-programs/VALELEG.NSN#L183) | Inferida | Magic number 60 hardcoded, paralelo a IDADE-MIN paramétrico. |
| VE-14 | Para programa tipo 'T' (trabalho), se a idade for menor que 16 ou maior que 65, acumula "PROG TRABALHO: IDADE FORA DA FAIXA 16-65" e marca inelegível. | State-driven | [VALELEG.NSN#L190-L196](legado-sifap/natural-programs/VALELEG.NSN#L190) | Inferida | Piso 16 ecoa RN-006, mas só para tipo 'T'. Ver M-VE-02. |
| VE-15 | Se o tipo de programa não for 'A', 'P' nem 'T', acumula "TIPO PROGRAMA DESCONHECIDO" e marca inelegível. | Unwanted behavior | [VALELEG.NSN#L197-L200](legado-sifap/natural-programs/VALELEG.NSN#L197) | Inferida | Ramo `NONE` do `DECIDE`. |
| VE-16 | Se o programa possui código de elegibilidade específico (`#COD-ELEG NE ' '`), executa a sub-rotina de validação específica. | Optional feature | [VALELEG.NSN#L206-L208](legado-sifap/natural-programs/VALELEG.NSN#L206) | Inferida | `COD-ELEGIBILIDADE` é A5 posicional. |
| VE-17 | Se o 1º caractere do código de elegibilidade for 'R', o beneficiário deve ter NIS cadastrado (`NIS NE 0`); senão acumula "NIS NAO CADASTRADO" e marca inelegível. | State-driven | [VALELEG.NSN#L226-L233](legado-sifap/natural-programs/VALELEG.NSN#L226) | Inferida | Código posicional não documentado. Ver M-VE-07. |
| VE-18 | Se o 2º caractere do código de elegibilidade for 'D', o beneficiário deve ter ao menos 1 dependente; senão acumula "PROGRAMA REQUER DEPENDENTES" e marca inelegível. | State-driven | [VALELEG.NSN#L234-L241](legado-sifap/natural-programs/VALELEG.NSN#L234) | Inferida | — |
| VE-19 | A idade do beneficiário é calculada apenas pela diferença de anos (`#ANO-ATUAL - #ANO-NASC`), sem considerar mês/dia. | Ubiquitous | [VALELEG.NSN#L59](legado-sifap/natural-programs/VALELEG.NSN#L59) · [#L72-L73](legado-sifap/natural-programs/VALELEG.NSN#L72) | Inferida | Pode superestimar a idade em até ~1 ano em todas as faixas (VE-08/09/13/14). Ver M-VE-03. |

### Rascunho EARS (regras confirmadas)

- **REQ (VE-03):** When the system validates a beneficiary's eligibility for a social program, the system shall reject the program if its program status (`STATUS-PROG`) is not 'A' (active), reporting "PROGRAMA INATIVO". *(source_legacy: VALELEG.NSN#L99-L102)*

### Discrepâncias código × documentação (para `/catalog-mysteries`)

<!-- mystery: VALELEG concede elegibilidade total quando COD-REGIAO = 99, com ESCAPE ROUTINE antes de qualquer validacao. Cabecalho atribui a Anderson Lima (05/04/2013, "INC REGIAO 99"), mas RN-005 chama de "bypass do Roberto". Origem, autorizacao e impacto desconhecidos. -->
- **M-VE-01** — Bypass da região 99 ([#L104-L111](legado-sifap/natural-programs/VALELEG.NSN#L104)): força `#ELEGIVEL=TRUE` e `ESCAPE ROUTINE` antes de toda validação. Confirma a suspeita da RN-005 ("bypass do Roberto" / valor 99 reservado), mas o cabeçalho credita a inclusão a **Anderson Lima em 2013**, não a Roberto. Mesma família dos backdoors da região/prefixos em VALBENEF (M-VB-02) e VALDOCS (M-VD-02). Levantar quem autorizou, quantos registros usam 99 em produção e se deve permanecer.

<!-- mystery: doc 4.2 afirma que o beneficiario nao pode ter ocorrencia de auditoria nao resolvida tipo 'B' (bloqueio) no DDM AUDITORIA, mas VALELEG nao le AUDITORIA nem verifica nada disso. -->
- **M-VE-02** — A doc (seção 4.2) afirma que VALELEG bloqueia beneficiário com **ocorrência de auditoria não resolvida tipo 'B'** no DDM AUDITORIA — mas o código **não lê AUDITORIA** nem faz qualquer checagem de bloqueio. Também atribui idade mínima global de 16 (RN-006), porém o piso 16 só existe para o tipo 'T' (VE-14). Verificar se essa lógica existiu e foi removida, ou se a doc descreve outro programa/versão.

<!-- mystery: idade calculada apenas por ano (ANO-ATUAL - ANO-NASC), sem mes/dia; pode classificar errado beneficiarios proximos da faixa (60, 16, 65). -->
- **M-VE-03** — Cálculo de idade só por ano ([#L72-L73](legado-sifap/natural-programs/VALELEG.NSN#L72)) pode superestimar a idade e aprovar/reprovar incorretamente quem está na fronteira das faixas (IDADE-MIN/MAX, 60 previdenciário, 16/65 trabalho). Confirmar a regra de negócio correta para cálculo de idade.

<!-- mystery: VE-03 valida STATUS-PROG = 'A', mas RN-003 documenta o flag de programa ativo como PS-IN-ATIVO = 'S'. Campos/dominios divergentes. -->
- **M-VE-04** — A RN-003 documenta "programa ativo" como `PS-IN-ATIVO = 'S'`, mas o código verifica `STATUS-PROG NE 'A'`. Confirmar se são o mesmo campo com domínios diferentes ou dois campos distintos no DDM PROGRAMA-SOCIAL.

<!-- mystery: doc 4.2 lista regras de elegibilidade (dados bancarios validos, maximo 2 programas BN-QT-PROG, atualizacao <=24 meses BN-DT-ULT-ATUAL) que NAO existem em VALELEG. -->
- **M-VE-05** — A doc (seção 4.2) lista verificações que **não existem** neste código: dados bancários válidos/completos, máximo de 2 programas simultâneos (`BN-QT-PROG`), e atualização cadastral ≤ 24 meses (`BN-DT-ULT-ATUAL`). O domínio de status do beneficiário (A/S/C/D/I) também não está documentado. Localizar onde (e se) essas regras são aplicadas. A doc descreve o programa com ~1.200 linhas; o real tem 244 — possível versão divergente.

<!-- mystery: VE-10 usa renda familiar bruta contra RENDA-MAX, mas RN-018 determina faixa por renda per capita (BN-VL-RENDA-PC). Base de comparacao divergente. -->
- **M-VE-06** — VE-10 compara `RENDA-FAMILIAR` (bruta) com `RENDA-MAX`, enquanto RN-018 determina a faixa por **renda per capita** (`BN-VL-RENDA-PC`). Esclarecer qual base de renda rege a elegibilidade vs. o cálculo do valor.

<!-- mystery: COD-ELEGIBILIDADE (A5) e interpretado posicionalmente: pos1='R' exige NIS, pos2='D' exige dependentes. As 5 posicoes e seus codigos nao estao documentados em nenhuma RN. -->
- **M-VE-07** — O campo `COD-ELEGIBILIDADE` (A5) é interpretado **posicionalmente** (pos. 1 = 'R' exige NIS; pos. 2 = 'D' exige dependentes), mas só 2 das 5 posições são usadas e nenhuma RN documenta esse esquema de códigos. Levantar o dicionário completo das 5 posições e quais valores são válidos.

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
