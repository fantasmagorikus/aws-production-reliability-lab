# ADR-0002: Java Spring Boot as the reference workload, Rust as the reusability proof

- Date: 2026-08-17
- Status: accepted

## Context

The lab needs a containerised HTTP service to deploy: a health endpoint, basic CRUD
against PostgreSQL, a deliberately slow endpoint and structured logs. Roughly 150
lines of application code.

The operator has no prior experience in either Java or Rust, so familiarity is not a
deciding factor. What matters is which runtime best serves a reliability lab.

## Decision

Java with Spring Boot is the M2 reference workload. Rust is deployed in M6 as the
second workload that proves the platform modules are reusable.

## Alternatives considered

| Option | Image size | Task memory | Failure modes available | Learning cost |
|---|---|---|---|---|
| Java / Spring Boot | ~200-400 MB | 512 MB-1 GB | rich | medium |
| Rust / Axum | ~15-30 MB | 128-256 MB | few | high |
| Python / FastAPI | ~120 MB | ~256 MB | medium | low |

## Consequences

- The lab exists to study failure. The JVM supplies failure modes that the drills in
  M3 and M4 depend on: garbage collector pauses, thread pool exhaustion, heap pressure
  inside a container memory limit, and the classic case where the JVM ignores the
  cgroup limit and the container is OOMKilled. Rust is designed to have none of these.
  Choosing it would mean studying pathology on a healthy patient.
- Cost and performance favour Rust, but neither is a constraint at this scale.
- Learning cost: Java's ceremony is front-loaded and the Spring Initializr absorbs most
  of it. Rust's borrow checker, lifetimes and async runtime would consume hours that
  belong to VPC routing and IAM, which is what this lab is actually teaching.
- Interview legibility: JVM tuning and GC behaviour are common SRE ground. Rust in
  production is a narrower conversation.
- Reusability: deploying a second Java service in M6 would prove little. Deploying Rust
  proves a great deal — different language, an image roughly twenty times smaller, no
  JVM runtime, yet the same ALB, target group, task role pattern and CloudWatch logs.
  It converts "the platform is reusable" from a claim into a demonstration, and the
  image size and memory comparison becomes a measured number for `evidence/`.

---

# ADR-0002 (PT-BR): Java Spring Boot como carga de referencia, Rust como prova de reuso

- Data: 2026-08-17
- Status: aceito

## Contexto

O laboratorio precisa de um servico HTTP conteinerizado: endpoint de saude, CRUD
basico contra PostgreSQL, um endpoint deliberadamente lento e logs estruturados.
Cerca de 150 linhas de codigo de aplicacao.

O operador nao tem experiencia previa em Java nem em Rust, entao familiaridade nao e
fator de decisao. O que importa e qual runtime serve melhor a um laboratorio de
confiabilidade.

## Decisao

Java com Spring Boot e a carga de referencia do M2. Rust entra no M6 como segunda
carga, provando que os modulos da plataforma sao reutilizaveis.

## Alternativas consideradas

| Opcao | Tamanho da imagem | Memoria da task | Modos de falha | Custo de aprendizado |
|---|---|---|---|---|
| Java / Spring Boot | ~200-400 MB | 512 MB-1 GB | ricos | medio |
| Rust / Axum | ~15-30 MB | 128-256 MB | poucos | alto |
| Python / FastAPI | ~120 MB | ~256 MB | medios | baixo |

## Consequencias

- O laboratorio existe para estudar falha. A JVM fornece os modos de falha dos quais os
  drills do M3 e M4 dependem: pausas do coletor de lixo, esgotamento de thread pool,
  pressao de heap dentro do limite de memoria do container, e o caso classico em que a
  JVM ignora o limite do cgroup e o container sofre OOMKill. Rust foi projetado para
  nao ter nada disso. Escolhe-lo seria estudar patologia em um paciente saudavel.
- Custo e desempenho favorecem Rust, mas nenhum dos dois e restricao nesta escala.
- Custo de aprendizado: a cerimonia do Java concentra-se no inicio e o Spring
  Initializr absorve a maior parte. O borrow checker, lifetimes e runtime async do Rust
  consumiriam horas que pertencem a roteamento de VPC e IAM, que e o que este
  laboratorio realmente ensina.
- Legibilidade em entrevista: tuning de JVM e comportamento de GC sao terreno comum de
  SRE. Rust em producao e uma conversa mais estreita.
- Reuso: implantar um segundo servico Java no M6 provaria pouco. Implantar Rust prova
  muito — linguagem diferente, imagem cerca de vinte vezes menor, sem runtime de JVM, e
  ainda assim o mesmo ALB, target group, padrao de task role e logs no CloudWatch.
  Converte "a plataforma e reutilizavel" de afirmacao em demonstracao, e a comparacao
  de tamanho de imagem e memoria vira um numero medido para `evidence/`.
