# Especificação Técnica de Requisitos

## 1. Requisitos Funcionais (RF) - Detalhamento

### Módulo Core (Núcleo)
* **RF.C01:** Autenticação Multi-fator (MFA) e controle de permissões por grupo (RBAC).
* **RF.C02:** Sistema de Logs (Audit Trail) para rastrear quem alterou qualquer dado sensível.

### Módulo de Estoque (Inventory)
* **RF.E01:** Gestão de múltiplos depósitos (Lojas físicas vs. Depósito Central).
* **RF.E02:** Cálculo de Custo Médio Ponderado (CMP) em cada entrada de nota.
* **RF.E03:** Suporte a variações de produto (Grade: cor, tamanho, voltagem).

### Módulo de Vendas & WhatsApp
* **RF.V01:** Geração de link de pagamento integrado via Pix/Cartão.
* **RF.V02:** Recuperação de carrinhos abandonados via bot do WhatsApp após X horas de inatividade.

---

## 2. Requisitos Não-Funcionais (RNF) - Qualidade
* **RNF.01 (Integridade):** O sistema deve utilizar **Transações Atômicas** no Django. Se o estoque não der baixa, a venda não pode ser confirmada.
* **RNF.02 (Segurança):** Senhas devem ser criptografadas usando Argon2 ou BCrypt (Padrão Django).
* **RNF.03 (Observabilidade):** Implementar monitoramento de erros (ex: Sentry) para capturar falhas em background workers (Celery/Redis).
* **RNF.04 (Internacionalização):** O sistema deve estar pronto para i18n (suporte a múltiplas moedas e fusos horários).
* **RNF.05 (Documentação):** API deve seguir o padrão RESTful com documentação auto-gerada via drf-spectacular (OpenAPI 3).
