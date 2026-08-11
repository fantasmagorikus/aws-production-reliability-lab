# Cost controls

## Budget

- A fixed monthly cost budget is configured in AWS Budgets.
- Alerts fire at 50% actual, 80% actual, and 100% forecasted.
- AWS Budgets is notification-only. It does not stop resources. Destroy
  discipline is the primary control; the alert is the second line of defence.

## Tagging convention

Every billable resource carries these four tags:

| Key | Value | Purpose |
|---|---|---|
| project | aws-reliability-lab | filter this lab's spend in Cost Explorer |
| env | lab | separate from any future prod-like environment |
| owner | @fantasmagorikus | accountability |
| managed-by | cloudformation \| terraform \| console | find anything created by hand |

The managed-by tag is the important one: any resource tagged console was created
outside infrastructure as code and will not be removed by a stack teardown.

## Main cost drivers (us-east-1, on-demand, approximate list prices)

| Resource | While running | Note |
|---|---|---|
| NAT Gateway | ~USD 0.045/h + data | bills every hour it exists |
| Application Load Balancer | ~USD 0.023/h + usage | same |
| RDS db.t4g.micro | ~USD 0.016/h single-AZ | storage bills even when stopped |
| ECS Fargate, 2 small tasks | ~USD 0.025/h combined | per running task |

Full stack running: roughly USD 0.12-0.18 per hour, so a short working session
costs cents while an environment left up for a month costs two orders of
magnitude more. That gap is the entire argument for the destroy policy below.

Figures to be verified against the AWS Pricing Calculator before Phase 1.

## Destroy policy

- No session ends without a destroy decision, recorded in PROJECT_STATE.md.
- Verify after teardown: no NAT Gateway, no load balancer, no RDS instance,
  no Elastic IP left allocated.
- Anything intentionally kept running is listed in PROJECT_STATE.md with a reason.

---

# Controles de custo (PT-BR)

## Orcamento

- Um orcamento mensal fixo esta configurado no AWS Budgets.
- Alertas disparam em 50% realizado, 80% realizado e 100% previsto.
- O AWS Budgets apenas notifica. Ele nao desliga recursos. A disciplina de
  destruir o ambiente e o controle principal; o alerta e a segunda linha de defesa.

## Convencao de tags

Todo recurso cobravel carrega estas quatro tags:

| Chave | Valor | Proposito |
|---|---|---|
| project | aws-reliability-lab | filtrar o gasto deste laboratorio no Cost Explorer |
| env | lab | separar de qualquer ambiente futuro parecido com producao |
| owner | @fantasmagorikus | responsabilidade |
| managed-by | cloudformation \| terraform \| console | achar o que foi criado manualmente |

A tag managed-by e a mais importante: qualquer recurso marcado como console foi
criado fora da infraestrutura como codigo e nao sera removido por um teardown de stack.

## Principais geradores de custo (us-east-1, sob demanda, precos de tabela aproximados)

| Recurso | Enquanto ligado | Observacao |
|---|---|---|
| NAT Gateway | ~USD 0,045/h + trafego | cobra a cada hora que existe |
| Application Load Balancer | ~USD 0,023/h + uso | igual |
| RDS db.t4g.micro | ~USD 0,016/h single-AZ | armazenamento cobra mesmo com a instancia parada |
| ECS Fargate, 2 tarefas pequenas | ~USD 0,025/h no total | por tarefa em execucao |

Stack completa ligada: aproximadamente USD 0,12-0,18 por hora. Uma sessao curta
custa centavos, enquanto um ambiente esquecido ligado por um mes custa duas ordens
de grandeza a mais. Essa diferenca e todo o argumento da politica de destruicao abaixo.

Valores a verificar na AWS Pricing Calculator antes da Fase 1.

## Politica de destruicao

- Nenhuma sessao termina sem uma decisao de destruir, registrada em PROJECT_STATE.md.
- Verificar apos o teardown: nenhum NAT Gateway, nenhum load balancer, nenhuma
  instancia RDS, nenhum Elastic IP alocado.
- Qualquer coisa mantida ligada de proposito fica listada em PROJECT_STATE.md com o motivo.
