# ADR-0001: Use us-east-1 as the lab region

- Date: 2026-08-11
- Status: accepted

## Context

The lab has a single operator and no end users. Cost is the binding constraint:
the environment runs under a fixed monthly ceiling with budget alerts. Every
resource is provisioned as code, so the region is a low-regret choice for most
services.

One exception is not reversible in practice: IAM Identity Center pins a primary
Region at creation, and changing it requires deleting the instance and
recreating users, permission sets and assignments.

## Decision

Use us-east-1 (US East, N. Virginia) for all lab resources, including the
IAM Identity Center primary Region.

## Alternatives considered

| Region | Cost | Notes |
|---|---|---|
| us-east-1 | lowest | universal default in AWS documentation and exam material |
| us-east-2 | comparable | fewer new services land here first |
| sa-east-1 | ~25-50% higher on core services | only justified when serving users in that geography |

Latency was not a deciding factor: the only traffic is CLI and console from a
single operator, where a difference of tens of milliseconds is invisible.

## Consequences

- Cost: lowest available. Directly protects the monthly ceiling.
- Complexity: none added.
- Security: no impact.
- Reliability: no impact for a single-operator lab.
- Learning value: matches the region assumed by most AWS documentation.
- Reusability: infrastructure as code makes redeployment elsewhere cheap,
  except for the Identity Center instance.

---

# ADR-0001 (PT-BR): Usar us-east-1 como regiao do laboratorio

- Data: 2026-08-11
- Status: aceito

## Contexto

O laboratorio tem um unico operador e nenhum usuario final. Custo e a restricao
determinante: o ambiente opera sob um teto mensal fixo com alertas de orcamento.
Todo recurso e provisionado como codigo, entao a regiao e uma escolha de baixo
arrependimento para a maioria dos servicos.

Uma excecao nao e reversivel na pratica: o IAM Identity Center fixa uma regiao
primaria na criacao, e troca-la exige deletar a instancia e recriar usuarios,
permission sets e atribuicoes.

## Decisao

Usar us-east-1 (US East, N. Virginia) para todos os recursos do laboratorio,
incluindo a regiao primaria do IAM Identity Center.

## Alternativas consideradas

| Regiao | Custo | Observacoes |
|---|---|---|
| us-east-1 | mais baixo | padrao universal na documentacao AWS e no material de certificacao |
| us-east-2 | comparavel | menos servicos novos chegam primeiro aqui |
| sa-east-1 | ~25-50% mais caro nos servicos principais | so se justifica ao atender usuarios naquela geografia |

Latencia nao foi fator decisivo: o unico trafego e CLI e console de um operador,
onde uma diferenca de dezenas de milissegundos e invisivel.

## Consequencias

- Custo: o mais baixo disponivel. Protege diretamente o teto mensal.
- Complexidade: nada adicionado.
- Seguranca: sem impacto.
- Confiabilidade: sem impacto para um laboratorio de operador unico.
- Valor de aprendizado: alinhado com a regiao assumida pela maior parte da documentacao AWS.
- Reuso: infraestrutura como codigo torna barato reimplantar em outra regiao,
  exceto pela instancia do Identity Center.
