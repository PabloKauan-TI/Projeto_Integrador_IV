# Especificação de Requisitos e Casos de Uso - API WhatsApp (Evolution API)

Este documento reúne os Requisitos Funcionais (RF), Requisitos Não Funcionais (RNF) e a especificação detalhada dos Casos de Uso para a integração com a Evolution API.

---

## 1. Requisitos do Sistema

### 1.1 Requisitos Funcionais (RF)

#### [RF-01] Recebimento de Mensagens via Webhook
* **Descrição:** A API deve receber e processar eventos de novas mensagens enviados pela Evolution API.
* **Ator:** Evolution API.
* **Prioridade:** Essencial.
* **Pré-condições:** Instância da Evolution API conectada ao WhatsApp e rota de webhook configurada.
* **Fluxo Principal:**
  1. A Evolution API envia uma requisição POST com os dados da mensagem.
  2. A API valida a autenticidade e o formato do payload.
  3. A API extrai o identificador da mensagem (provider_message_id), o remetente e o conteúdo.
  4. A API responde com status HTTP 200 OK.
* **Fluxo Alternativo (A1 - Mensagem Repetida):**
  * Se o provider_message_id já existir na base, o sistema descarta o novo processamento e retorna HTTP 200 OK imediatamente.

#### [RF-02] Envio de Mensagens de Texto
* **Descrição:** A API deve disponibilizar funcionalidade para envio de mensagens de texto aos contatos via Evolution API.
* **Ator:** Orquestrador Interno / Backend.
* **Prioridade:** Essencial.
* **Pré-condições:** Número de telefone válido e instância da Evolution API em estado open.
* **Fluxo Principal:**
  1. O backend solicita o disparo de texto informando o número de destino e o conteúdo.
  2. A API valida os dados e dispara a chamada HTTP para a Evolution API.
  3. A API recebe a confirmação de envio e retorna status HTTP 200 OK com o ID da mensagem.
* **Fluxo Alternativo (A1 - Falha de Conexão):**
  * Se a Evolution API reportar desconexão do WhatsApp, a API retorna status HTTP 503 Service Unavailable.

---

### 1.2 Requisitos Não Funcionais (RNF)

#### [RNF-01] Latência de Resposta do Webhook
* **Descrição:** O endpoint de webhook deve responder com código 200 OK em tempo hábil para evitar retransmissões automáticas da Evolution API.
* **Métrica Objetiva:** Tempo de resposta menor ou igual a 1000ms medido no servidor.

#### [RNF-02] Rastreabilidade e Idempotência
* **Descrição:** Todas as mensagens recebidas devem ter o identificador único registrado para evitar duplicidade de processamento no fluxo interno.
* **Métrica Objetiva:** 100% das requisições duplicadas tratadas sem novo registro de mensagem no sistema.
