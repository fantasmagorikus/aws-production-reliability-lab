# ADR-0003: Network address plan

- Date: 2026-08-18
- Status: proposed

## Context

M1 creates the network foundation: one VPC across two Availability Zones, with three
tiers of subnets. The address plan has to be decided before any resource exists,
because renumbering a live network means touching security groups, route tables and
application configuration at the same time.

Constraints:

- Private address space only. The workload is reachable through the load balancer,
  never directly.
- Two Availability Zones, so every tier needs a subnet in each.
- Room to grow without renumbering.
- The lab may one day be connected to another network over VPN, so overlapping ranges
  must be avoided.

## Decision

VPC `10.20.0.0/16`, divided into six `/24` subnets:

| Subnet | CIDR | AZ | Tier |
|---|---|---|---|
| public-a | 10.20.0.0/24 | us-east-1a | ALB, NAT gateway |
| public-b | 10.20.1.0/24 | us-east-1b | ALB |
| app-a | 10.20.10.0/24 | us-east-1a | ECS Fargate tasks |
| app-b | 10.20.11.0/24 | us-east-1b | ECS Fargate tasks |
| db-a | 10.20.20.0/24 | us-east-1a | RDS |
| db-b | 10.20.21.0/24 | us-east-1b | RDS |

Third octet blocks: 0-9 public, 10-19 application, 20-29 database.

## Alternatives considered

**Private range.** RFC 1918 reserves `10.0.0.0/8`, `172.16.0.0/12` and
`192.168.0.0/16`. `192.168.x` is the common home router default, and reusing it would
create an overlap the moment a site-to-site VPN is introduced. `172.16.x` is Docker's
default bridge range on many hosts. `10.20.x` avoids both, and avoids `10.0.x`, which
is the range most AWS tutorials use and therefore the most likely to collide with a
second VPC later.

**VPC size.** A `/16` gives 65,536 addresses, of which this plan uses 1,536. A `/20`
would be sufficient today and tighter. The `/16` was chosen because a VPC CIDR cannot
be shrunk after creation, only extended with a secondary block, and the address space
is free. Oversizing here costs nothing and removes a future migration.

**Subnet size.** A `/24` yields 256 addresses, 251 usable after the five AWS reserves
in every subnet: network address, VPC router, DNS, a reserved address, and broadcast.
A `/26` (64 addresses) would fit the six-task workload and leave more room for future
subnets. The `/24` was chosen for readability: the boundary falls on an octet, so the
third octet alone identifies the tier and the fourth is the host. Ranges that do not
align to an octet are the common source of address-planning mistakes.

**Numbering scheme.** Sequential numbering (0, 1, 2, 3, 4, 5) is denser. The
ten-blocks scheme leaves gaps so that a third subnet in any tier stays adjacent to its
peers, and so the tier is readable from the address alone.

## Consequences

- Cost: none. VPCs, subnets and route tables are free.
- Complexity: none added.
- Security: private addressing is the outermost layer of the boundary. The database
  tier cannot receive an inbound connection from the internet because no route to it
  exists, not because a rule forbids it. That property survives a misconfigured
  security group.
- Reliability: two Availability Zones per tier is the precondition for surviving the
  loss of one zone.
- Learning value: address planning, CIDR arithmetic and the AWS reserved addresses are
  directly examined in the CloudOps associate networking domain.
- Reusability: the tier blocks make the plan portable to a second environment by
  changing only the second octet.

---

# ADR-0003 (PT-BR): Plano de enderecamento de rede

- Data: 2026-08-18
- Status: proposto

## Contexto

O M1 cria a fundacao de rede: uma VPC em duas Availability Zones, com tres camadas de
sub-redes. O plano de enderecamento precisa ser decidido antes de qualquer recurso
existir, porque renumerar uma rede em operacao significa mexer em security groups,
tabelas de rota e configuracao da aplicacao ao mesmo tempo.

Restricoes:

- Apenas espaco de enderecamento privado. A carga e alcancada pelo load balancer,
  nunca diretamente.
- Duas Availability Zones, entao cada camada precisa de uma sub-rede em cada.
- Espaco para crescer sem renumerar.
- O laboratorio pode um dia ser conectado a outra rede por VPN, entao faixas
  sobrepostas devem ser evitadas.

## Decisao

VPC `10.20.0.0/16`, dividida em seis sub-redes `/24`:

| Sub-rede | CIDR | AZ | Camada |
|---|---|---|---|
| public-a | 10.20.0.0/24 | us-east-1a | ALB, NAT gateway |
| public-b | 10.20.1.0/24 | us-east-1b | ALB |
| app-a | 10.20.10.0/24 | us-east-1a | tasks ECS Fargate |
| app-b | 10.20.11.0/24 | us-east-1b | tasks ECS Fargate |
| db-a | 10.20.20.0/24 | us-east-1a | RDS |
| db-b | 10.20.21.0/24 | us-east-1b | RDS |

Blocos do terceiro octeto: 0-9 publica, 10-19 aplicacao, 20-29 banco.

## Alternativas consideradas

**Faixa privada.** A RFC 1918 reserva `10.0.0.0/8`, `172.16.0.0/12` e
`192.168.0.0/16`. `192.168.x` e o padrao comum de roteador domestico, e reutiliza-la
criaria sobreposicao no momento em que uma VPN site-to-site fosse introduzida.
`172.16.x` e a faixa padrao da bridge do Docker em muitos hosts. `10.20.x` evita as
duas, e evita `10.0.x`, que e a faixa usada pela maioria dos tutoriais de AWS e
portanto a mais provavel de colidir com uma segunda VPC no futuro.

**Tamanho da VPC.** Um `/16` da 65.536 enderecos, dos quais este plano usa 1.536. Um
`/20` seria suficiente hoje e mais justo. O `/16` foi escolhido porque o CIDR de uma
VPC nao pode ser reduzido apos a criacao, apenas estendido com um bloco secundario, e
o espaco de enderecamento e gratuito. Superdimensionar aqui nao custa nada e remove
uma migracao futura.

**Tamanho da sub-rede.** Um `/24` da 256 enderecos, 251 utilizaveis apos as cinco
reservas que a AWS faz em toda sub-rede: endereco de rede, roteador da VPC, DNS, um
endereco reservado e broadcast. Um `/26` (64 enderecos) caberia na carga de seis tasks
e deixaria mais espaco para sub-redes futuras. O `/24` foi escolhido por legibilidade:
a fronteira cai em um octeto, entao o terceiro octeto sozinho identifica a camada e o
quarto e o host. Faixas que nao se alinham a octeto sao a origem mais comum de erro em
planejamento de enderecamento.

**Esquema de numeracao.** Numeracao sequencial (0, 1, 2, 3, 4, 5) e mais densa. O
esquema de blocos de dez deixa lacunas para que uma terceira sub-rede de qualquer
camada permaneca adjacente as suas pares, e para que a camada seja legivel a partir do
endereco.

## Consequencias

- Custo: nenhum. VPCs, sub-redes e tabelas de rota sao gratuitas.
- Complexidade: nada adicionado.
- Seguranca: enderecamento privado e a camada mais externa da fronteira. A camada de
  banco nao pode receber conexao de entrada da internet porque nao existe rota ate
  ela, e nao porque uma regra proibe. Essa propriedade sobrevive a um security group
  mal configurado.
- Confiabilidade: duas Availability Zones por camada e a precondicao para sobreviver a
  perda de uma zona.
- Valor de aprendizado: planejamento de enderecamento, aritmetica de CIDR e os
  enderecos reservados da AWS sao cobrados diretamente no dominio de rede da
  certificacao CloudOps associate.
- Reuso: os blocos por camada tornam o plano portavel para um segundo ambiente
  mudando apenas o segundo octeto.
