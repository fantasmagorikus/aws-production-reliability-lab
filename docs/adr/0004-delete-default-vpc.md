# ADR-0004: Delete the default VPC

- Date: 2026-08-18
- Status: accepted

## Context

Every AWS account receives a default VPC in each region, created without being asked.
In us-east-1 this account has one, with six subnets, one per Availability Zone. It
costs nothing and currently holds no resources.

Once the lab VPC exists, the account will contain two VPCs. Any query that does not
filter returns both, and the console frequently preselects the default one in
resource creation forms.

## Decision

Delete the default VPC in us-east-1, after confirming it holds no resources.

## Alternatives considered

**Keep it and filter every query.** Costs nothing and breaks nothing. Requires
`--filters Name=isDefault,Values=false` on every describe call, and vigilance in the
console. The failure mode is silent: a subnet created in the wrong VPC does not error,
it simply cannot reach the rest of the stack. Time is then spent debugging routing for
a problem that is actually placement.

**Keep it as a scratch area.** Convenient for throwaway tests. Rejected because a
scratch area inside the same account is exactly where forgotten billable resources
accumulate, and because it undermines the discipline this lab is built to practise.

## Consequences

- Cost: none either way. Deleting removes nothing billable.
- Complexity: reduced. Every subsequent query returns exactly one VPC.
- Security: the default VPC ships with a permissive default security group and subnets
  that assign public IPs automatically. Removing it eliminates a path where a resource
  created carelessly lands somewhere internet-facing.
- Reversibility: a default VPC can be recreated with `create-default-vpc` if it is
  ever needed. This is not a one-way door.
- Learning value: understanding what the default VPC provides, and why production
  accounts commonly remove it, is part of the networking domain.
- Risk: deletion is irreversible for that specific VPC and its subnets. Mitigated by
  verifying it is empty first and by capturing the pre-deletion state as evidence.

---

# ADR-0004 (PT-BR): Deletar a VPC padrao

- Data: 2026-08-18
- Status: aceito

## Contexto

Toda conta AWS recebe uma VPC padrao em cada regiao, criada sem ser solicitada. Em
us-east-1 esta conta possui uma, com seis sub-redes, uma por Availability Zone. Ela
nao custa nada e atualmente nao contem recursos.

Assim que a VPC do laboratorio existir, a conta tera duas VPCs. Qualquer consulta sem
filtro retorna as duas, e o console frequentemente pre-seleciona a padrao nos
formularios de criacao de recursos.

## Decisao

Deletar a VPC padrao em us-east-1, apos confirmar que nao contem recursos.

## Alternativas consideradas

**Manter e filtrar toda consulta.** Nao custa nada e nao quebra nada. Exige
`--filters Name=isDefault,Values=false` em toda chamada describe, e vigilancia no
console. O modo de falha e silencioso: uma sub-rede criada na VPC errada nao gera
erro, simplesmente nao alcanca o resto da stack. O tempo entao e gasto depurando
roteamento para um problema que na verdade e de posicionamento.

**Manter como area de rascunho.** Conveniente para testes descartaveis. Rejeitado
porque uma area de rascunho dentro da mesma conta e exatamente onde recursos cobraveis
esquecidos se acumulam, e porque contraria a disciplina que este laboratorio existe
para praticar.

## Consequencias

- Custo: nenhum nos dois casos. Deletar nao remove nada cobravel.
- Complexidade: reduzida. Toda consulta seguinte retorna exatamente uma VPC.
- Seguranca: a VPC padrao vem com um security group padrao permissivo e sub-redes que
  atribuem IP publico automaticamente. Remove-la elimina um caminho onde um recurso
  criado sem atencao acaba exposto a internet.
- Reversibilidade: uma VPC padrao pode ser recriada com `create-default-vpc` se um dia
  for necessaria. Nao e uma porta de mao unica.
- Valor de aprendizado: entender o que a VPC padrao fornece, e por que contas de
  producao comumente a removem, faz parte do dominio de rede.
- Risco: a delecao e irreversivel para aquela VPC especifica e suas sub-redes.
  Mitigado verificando primeiro que esta vazia e capturando o estado anterior como
  evidencia.
