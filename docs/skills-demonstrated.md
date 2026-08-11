# Skills demonstrated

This file records **only what this repository can prove**. Every row cites a file
that exists in the repo, and no metric appears here without a recorded
measurement behind it. Anything planned but not yet executed is listed in
"Not yet demonstrated" at the bottom, not implied above.

Scope as of 2026-08-11: milestone M0, preflight and guardrails. No AWS
infrastructure has been provisioned.

| Skill | Evidence |
|---|---|
| Removed the default CLI profile so an unqualified command fails closed instead of silently resolving to another identity, and recorded the negative control proving it (`NoCredentials`) | `evidence/m0-preflight/01-identity.txt` |
| Operate the account as a short-lived federated role assumed through IAM Identity Center, verified by an `assumed-role` caller ARN rather than an IAM user ARN | `evidence/m0-preflight/01-identity.txt` |
| Eliminated long-lived credentials as a class: zero IAM users on the account, zero root access keys, no static credentials on disk | `evidence/m0-preflight/02-account-security.txt` |
| Confirmed by API that the account holds zero customer managed KMS keys, so no per-key monthly charge exists, instead of assuming it from the console. Scoped honestly: this proves the key inventory, not the Identity Center replication configuration | `evidence/m0-preflight/02-account-security.txt` |
| Carried a control that could not be fixed that day as an explicit accepted risk - dated deadline plus the one-line command to re-verify it each session - instead of leaving it undocumented | `PROJECT_STATE.md` |
| Built a baseline that fails loudly: eight automated checks, seven pass and one fails, with the failure left visible in committed evidence rather than removed to make the report clean | `evidence/m0-preflight/00-summary.txt` |
| Established a zero-billable-resource baseline across seven resource classes before provisioning anything, so any future charge is attributable | `evidence/m0-preflight/04-resource-inventory.txt` |
| Verified budget alerting from the API instead of trusting the console: three thresholds confirmed at 50% actual, 80% actual and 100% forecasted | `evidence/m0-preflight/03-cost-controls.txt` |
| Automated evidence capture as a re-runnable script that masks twelve-digit account IDs and directory IDs on the way out, so raw output is safe to commit | `scripts/capture-evidence.sh` |
| Kept `set -euo pipefail` while still running commands that are supposed to fail, by guarding the deliberate-failure calls so the run completes and the failure is still recorded | `scripts/capture-evidence.sh` |
| Made the script fail fast with remediation instructions when the SSO session is expired, rather than emitting a directory of empty evidence files | `scripts/capture-evidence.sh` |
| Recorded an architecture decision with alternatives and their trade-offs, and identified the single irreversible element in it: Identity Center pins its primary Region at creation | `docs/adr/0001-aws-region.md` |
| Documented the limit of a cost control instead of overselling it: AWS Budgets notifies and does not stop resources, so teardown discipline is primary and the alert is the second line of defence | `docs/cost-controls.md` |
| Designed a four-tag convention in which one tag exists specifically to expose resources that a stack teardown will not remove, because they were created by hand | `docs/cost-controls.md` |
| Kept personal figures out of tracked content while the repository was still local-only, before any remote existed: no personal monetary ceiling appears in any reachable commit | `PROJECT_STATE.md` |
| Denied secrets at the repository boundary by pattern, with an allowlist for examples: env files, private keys, credentials files, Terraform state and tfvars excluded, `.example` variants permitted | `.gitignore` |
| Wrote operational documentation bilingually, English first and PT-BR below, so the same record serves both an international reviewer and local study use | `docs/cost-controls.md` |

## Not yet demonstrated

This repository contains no evidence for any of the following. They are planned
in `PLAN.md` and remain unproven until a milestone produces measured output. This
section is deliberate: it is what keeps the table above honest.

- VPC design and routing - no VPC, subnets, route tables or NAT Gateway exist
- ECS Fargate - no cluster, task definition or service exists
- Application Load Balancer - none created, no health check behaviour observed
- RDS PostgreSQL operations - no instance, no backup, restore or failover measured
- CloudWatch alarms and dashboards - none created, no metric filter in place
- CloudFormation - no template, stack or change set exists
- Terraform - no module, remote state or drift detection exists
- Load testing - no latency or error-rate measurement of any kind has been taken
- Incident response drills - no fault has been injected, so no detection or
  recovery time has been measured

---

# Habilidades demonstradas (PT-BR)

Este arquivo registra **apenas o que este repositorio consegue provar**. Cada
linha cita um arquivo que existe no repo, e nenhuma metrica aparece aqui sem uma
medicao registrada por tras dela. O que esta planejado mas ainda nao foi
executado esta listado em "O que ainda nao foi demonstrado" no final, e nao
insinuado acima.

Escopo em 2026-08-11: milestone M0, preflight e guardrails. Nenhuma
infraestrutura AWS foi provisionada.

| Habilidade | Evidencia |
|---|---|
| Removi o perfil default da CLI para que um comando sem qualificacao falhe fechado em vez de resolver silenciosamente para outra identidade, e registrei o controle negativo que prova isso (`NoCredentials`) | `evidence/m0-preflight/01-identity.txt` |
| Opero a conta como uma role federada de curta duracao assumida via IAM Identity Center, verificado por um ARN `assumed-role` em vez de ARN de usuario IAM | `evidence/m0-preflight/01-identity.txt` |
| Eliminei credenciais de longa duracao como classe: zero usuarios IAM na conta, zero access keys de root, nenhuma credencial estatica em disco | `evidence/m0-preflight/02-account-security.txt` |
| Confirmei por API que a conta nao tem nenhuma chave KMS gerenciada pelo cliente, portanto nao existe cobranca mensal por chave, em vez de presumir isso pelo console. Escopo honesto: isso prova o inventario de chaves, nao a configuracao de replicacao do Identity Center | `evidence/m0-preflight/02-account-security.txt` |
| Tratei um controle que nao podia ser corrigido naquele dia como risco aceito explicito - prazo com data mais o comando de uma linha para reverificar a cada sessao - em vez de deixar sem registro | `PROJECT_STATE.md` |
| Construi um baseline que falha de forma visivel: oito verificacoes automatizadas, sete passam e uma falha, com a falha mantida na evidencia commitada em vez de removida para o relatorio ficar limpo | `evidence/m0-preflight/00-summary.txt` |
| Estabeleci um baseline de zero recursos cobraveis em sete classes de recurso antes de provisionar qualquer coisa, para que qualquer cobranca futura seja atribuivel | `evidence/m0-preflight/04-resource-inventory.txt` |
| Verifiquei o alerta de orcamento pela API em vez de confiar no console: tres limiares confirmados em 50% realizado, 80% realizado e 100% previsto | `evidence/m0-preflight/03-cost-controls.txt` |
| Automatizei a captura de evidencias como script reexecutavel que mascara IDs de conta de doze digitos e IDs de diretorio na saida, deixando o output bruto seguro para commit | `scripts/capture-evidence.sh` |
| Mantive `set -euo pipefail` e ainda assim rodo comandos que devem falhar, protegendo as chamadas de falha deliberada para que a execucao termine e a falha continue registrada | `scripts/capture-evidence.sh` |
| Fiz o script falhar rapido com instrucoes de correcao quando a sessao SSO expira, em vez de gerar um diretorio de arquivos de evidencia vazios | `scripts/capture-evidence.sh` |
| Registrei uma decisao de arquitetura com alternativas e trade-offs, e identifiquei o unico elemento irreversivel dela: o Identity Center fixa a regiao primaria na criacao | `docs/adr/0001-aws-region.md` |
| Documentei o limite de um controle de custo em vez de superestima-lo: o AWS Budgets notifica e nao desliga recursos, entao a disciplina de teardown e o controle principal e o alerta e a segunda linha de defesa | `docs/cost-controls.md` |
| Desenhei uma convencao de quatro tags na qual uma tag existe especificamente para expor recursos que um teardown de stack nao vai remover, porque foram criados na mao | `docs/cost-controls.md` |
| Mantive valores pessoais fora do conteudo rastreado enquanto o repositorio ainda era apenas local, antes de existir qualquer remote: nenhum teto monetario pessoal aparece em nenhum commit alcancavel | `PROJECT_STATE.md` |
| Bloqueei segredos na fronteira do repositorio por padrao, com allowlist para exemplos: arquivos env, chaves privadas, arquivos de credenciais, state do Terraform e tfvars excluidos, variantes `.example` permitidas | `.gitignore` |
| Escrevi a documentacao operacional de forma bilingue, ingles primeiro e PT-BR abaixo, para que o mesmo registro sirva a um revisor internacional e ao estudo local | `docs/cost-controls.md` |

## O que ainda nao foi demonstrado

Este repositorio nao contem evidencia para nenhum dos itens abaixo. Eles estao
planejados em `PLAN.md` e seguem sem prova ate que um milestone produza saida
medida. Esta secao e deliberada: e o que mantem a tabela acima honesta.

- Design e roteamento de VPC - nao existe VPC, subnet, route table nem NAT Gateway
- ECS Fargate - nao existe cluster, task definition nem service
- Application Load Balancer - nenhum criado, nenhum comportamento de health check observado
- Operacao de RDS PostgreSQL - nenhuma instancia, nenhum backup, restore ou failover medido
- Alarmes e dashboards do CloudWatch - nenhum criado, nenhum metric filter no lugar
- CloudFormation - nao existe template, stack nem change set
- Terraform - nao existe modulo, remote state nem deteccao de drift
- Teste de carga - nenhuma medicao de latencia ou taxa de erro foi tomada
- Drills de resposta a incidente - nenhuma falha foi injetada, entao nenhum tempo
  de deteccao ou recuperacao foi medido
