# Sistema de Pagamentos PIX (Microserviços)

## Objetivo
Construir um ecossistema de microserviços que simule a realização de pagamentos via PIX, respeitando boas práticas de arquitetura distribuída.

## Descrição
O sistema deve ser composto por 3 microserviços independentes:

### MS-Transações
- Recebe requisições de pagamento (`pagadorId`, `recebedorId`, `valor`, `chavePix`).
- Valida dados básicos (saldo, formato de chave Pix, campos obrigatórios).
- Envia a transação para uma **fila** (Kafka/RabbitMQ).
- Endpoints:
  - `POST /pix/pagamento` → envia pagamento para fila.
  - `GET /pix/historico/{clienteId}` → consulta histórico de pagamentos.

### MS-Processamento
- Consome eventos da fila enviados pelo MS-Transações.
- Regras de negócio:
  - Rejeitar pagamentos fora do horário permitido.
  - Bloquear pagamentos acima de um limite configurável.
  - Marcar como suspeito clientes em lista de fraude.
- Persiste dados no **Postgres** (status: aprovado, rejeitado, suspeito).

### MS-Notificações
- Recebe eventos do MS-Processamento.
- Envia notificações simuladas (log, e-mail fake ou endpoint mockado).
- Endpoint:
  - `POST /notificacao` → simula envio de notificação.

### Requisitos Técnicos
- Banco: Postgres.
- Cache: Redis (últimos 10 pagamentos por cliente).
- Autenticação: JWT entre serviços.
- Documentação: Swagger/OpenAPI.
- Infraestrutura: `docker-compose` com Postgres, Redis, Kafka/RabbitMQ e os 3 MS.

## Entregáveis
- Código em repositório GitHub.
- `docker-compose.yml` para levantar o ambiente.
- Documentação mínima de endpoints.
- Instruções de execução e testes.
