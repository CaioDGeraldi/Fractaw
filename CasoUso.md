# Cenários de Operação e Casos de Uso

## 1. Caso de Uso: Venda Omnichannel com Baixa Instantânea
**Ator:** Vendedor Externo (via Mobile).
**Objetivo:** Realizar venda no cliente e garantir que o estoque físico seja reservado.

1.  **Acesso:** Vendedor acessa o módulo de vendas via tablet.
2.  **Consulta:** O sistema busca no **Redis** a disponibilidade do item X (Evita consulta pesada ao Postgres).
3.  **Reserva:** Ao adicionar ao carrinho, o sistema cria uma "reserva temporária" de 15 min no Redis.
4.  **Fechamento:** Vendedor finaliza o pedido.
5.  **Persistência:** O Django encerra a transação no **PostgreSQL**, atualizando o saldo real e deletando a reserva no Redis.
6.  **Notificação:** O worker (Celery) dispara o XML da nota e o PDF do pedido para o WhatsApp do cliente.

---

## 2. Caso de Uso: Inteligência Logística (Picking & Packing)
**Ator:** Operador de Depósito.
**Objetivo:** Minimizar erros de expedição.

1.  **Trigger:** Venda faturada no módulo de Vendas.
2.  **Cenário:** O módulo de Logística recebe o sinal e gera uma "Lista de Separação".
3.  **Ação:** O operador usa um coletor de dados (ou celular) para bipar o EAN do produto.
4.  **Validação:** O sistema valida se o EAN bipado condiz com o pedido.
5.  **Finalização:** Ao fechar a caixa, o status do pedido muda para "Em Trânsito".
6.  **Rastreio:** O link de rastreio da transportadora é capturado via API e enviado ao cliente via WhatsApp automaticamente.

---

## 3. Cenário de Exceção: Falha de API de Terceiros
**Contexto:** O Módulo do WhatsApp tenta enviar uma mensagem mas a API da Meta está fora do ar.

1.  **Detecção:** O Worker do Celery recebe um erro de timeout da API.
2.  **Estratégia:** O sistema entra em modo **Exponential Backoff** (Tenta novamente em 1min, depois 5min, depois 30min).
3.  **Alerta:** Se após 5 tentativas falhar, o sistema move a tarefa para uma fila de "Dead Letter" e avisa o administrador no dashboard do Fractaw.
4.  **Resultado:** Nenhuma mensagem é perdida; o sistema é resiliente a falhas externas.
