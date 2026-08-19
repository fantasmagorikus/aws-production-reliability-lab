# ADR-0005: Private subnet egress topology

- Status: Accepted
- Date: 2026-08-18
- Supersedes: none
- Superseded by: none

## Context

The application tier (`lab-app-a`, `lab-app-b`) will run ECS Fargate tasks that must
reach four AWS services located outside the VPC:

- ECR API and ECR DKR, to pull the container image
- Amazon S3, which stores the image layers
- CloudWatch Logs, to ship container logs
- Secrets Manager, to read database credentials

Every one of these is an outbound, AWS-bound call. None requires inbound access.

The database tier (`lab-db-a`, `lab-db-b`) requires no egress at all and keeps a route
table containing only the local route.

Three topologies can give the application tier outbound connectivity.

## Options considered

### A. One NAT Gateway, single availability zone

us-east-1 list price: USD 0.045 per gateway-hour and USD 0.045 per GB processed, plus
approximately USD 0.005 per hour for the attached public IPv4 address.

Both application subnets route 0.0.0.0/0 to a gateway placed in `lab-public-a`.
Traffic originating in `lab-app-b` crosses an availability zone boundary to reach it.

### B. One NAT Gateway per availability zone

Roughly double the hourly cost of option A. Each application subnet routes to a
gateway in its own zone: no cross-zone dependency, no cross-zone data charge. This is
the topology AWS recommends for production workloads.

### C. No NAT Gateway, VPC endpoints only

Interface endpoints for ECR API, ECR DKR, CloudWatch Logs and Secrets Manager at
approximately USD 0.01 per endpoint per availability zone per hour, plus a gateway
endpoint for S3 at no charge.

Four interface endpoints across two zones is approximately USD 0.08 per hour, which
exceeds option A. The advantage is in data processing, approximately USD 0.01 per GB
against USD 0.045 per GB, and in the fact that traffic never traverses the public
internet.

## Decision

Adopt option A for milestone M1.

The gateway is created at the start of a working session and destroyed at the end. It
is never left running between sessions.

## Rationale

Option B duplicates a resource without demonstrating anything option A does not
already demonstrate. The high-availability argument is recorded in this document
rather than purchased.

Option C cannot be validated today. The application subnets contain no workload, so an
endpoint created now could not be exercised, and an untested control is an assertion
rather than evidence. Option C is also more expensive at this scale: a laboratory does
not move enough data for the per-gigabyte advantage to offset the hourly charge.

Option A additionally supplies the default route required by the M1 route failure
drill.

## Consequences accepted

- Outbound connectivity for the whole application tier depends on a single
  availability zone. If us-east-1a becomes unavailable, tasks in `lab-app-b` lose
  egress while remaining healthy themselves.
- Traffic from `lab-app-b` to the gateway crosses a zone boundary and incurs the
  associated data transfer charge.
- Application traffic to AWS services leaves the VPC and re-enters through public
  service endpoints, a path that is harder to constrain and to audit than a private
  one.

## Consequences avoided

- No always-on billable resource. The gateway exists only while a session is active.

## Follow-up

Milestone M2 evaluates replacing the gateway with the endpoint set described in option
C, once an ECS task exists to exercise it, and records the measured difference in cost
and network path.

---

# ADR-0005: Topologia de saida das sub-redes privadas

- Status: Aceita
- Data: 2026-08-18
- Substitui: nenhuma
- Substituida por: nenhuma

## Contexto

A camada de aplicacao (`lab-app-a`, `lab-app-b`) vai executar tarefas ECS Fargate que
precisam alcancar quatro servicos AWS fora da VPC:

- ECR API e ECR DKR, para puxar a imagem do container
- Amazon S3, onde ficam as camadas da imagem
- CloudWatch Logs, para enviar os logs do container
- Secrets Manager, para ler as credenciais do banco

Todas sao chamadas de saida, destinadas a servicos AWS. Nenhuma exige acesso de
entrada.

A camada de banco (`lab-db-a`, `lab-db-b`) nao precisa de saida alguma e mantem uma
tabela de rotas apenas com a rota local.

Tres topologias podem dar conectividade de saida a camada de aplicacao.

## Opcoes consideradas

### A. Um NAT Gateway, uma unica zona de disponibilidade

Preco de lista em us-east-1: USD 0,045 por hora de gateway e USD 0,045 por GB
processado, mais cerca de USD 0,005 por hora pelo endereco IPv4 publico associado.

As duas sub-redes de aplicacao roteiam 0.0.0.0/0 para um gateway colocado em
`lab-public-a`. O trafego originado em `lab-app-b` atravessa a fronteira de zona para
chegar ate ele.

### B. Um NAT Gateway por zona de disponibilidade

Cerca do dobro do custo horario da opcao A. Cada sub-rede de aplicacao roteia para um
gateway na propria zona: sem dependencia entre zonas e sem cobranca de dados entre
zonas. E a topologia que a AWS recomenda para producao.

### C. Sem NAT Gateway, apenas VPC endpoints

Interface endpoints para ECR API, ECR DKR, CloudWatch Logs e Secrets Manager a cerca
de USD 0,01 por endpoint por zona por hora, mais um gateway endpoint para S3 sem
custo.

Quatro interface endpoints em duas zonas dao cerca de USD 0,08 por hora, acima da
opcao A. A vantagem esta no processamento de dados, cerca de USD 0,01 por GB contra
USD 0,045 por GB, e no fato de o trafego nunca passar pela internet publica.

## Decisao

Adotar a opcao A para o milestone M1.

O gateway e criado no inicio de uma sessao de trabalho e destruido no fim. Nunca fica
ligado entre sessoes.

## Justificativa

A opcao B duplica um recurso sem demonstrar nada que a opcao A ja nao demonstre. O
argumento de alta disponibilidade fica registrado neste documento em vez de comprado.

A opcao C nao pode ser validada hoje. As sub-redes de aplicacao estao vazias, entao um
endpoint criado agora nao poderia ser exercitado, e um controle nao testado e uma
afirmacao, nao uma evidencia. A opcao C tambem e mais cara nesta escala: um
laboratorio nao move dados suficientes para a vantagem por gigabyte compensar a
cobranca horaria.

A opcao A ainda fornece a rota padrao exigida pelo drill de falha de rota do M1.

## Consequencias aceitas

- A conectividade de saida de toda a camada de aplicacao depende de uma unica zona de
  disponibilidade. Se us-east-1a ficar indisponivel, as tarefas em `lab-app-b` perdem
  saida mesmo permanecendo saudaveis.
- O trafego de `lab-app-b` ate o gateway cruza a fronteira de zona e gera a cobranca
  de transferencia correspondente.
- O trafego da aplicacao para servicos AWS sai da VPC e retorna por endpoints
  publicos, um caminho mais dificil de restringir e auditar do que um caminho privado.

## Consequencias evitadas

- Nenhum recurso cobravel permanentemente ligado. O gateway existe apenas enquanto uma
  sessao esta ativa.

## Acompanhamento

O milestone M2 avalia substituir o gateway pelo conjunto de endpoints descrito na
opcao C, assim que existir uma tarefa ECS para exercita-lo, e registra a diferenca
medida em custo e em caminho de rede.
