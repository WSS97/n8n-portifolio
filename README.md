# 🚀 Sistema Inteligente de Alerta de Churn com IA & n8n

Este projeto apresenta uma solução automatizada de inteligência de negócios e CRM focada na retenção de clientes em risco de cancelamento (Churn). O sistema extrai dados transacionais, analisa o perfil financeiro do cliente através de um Agente de IA, gera estratégias de cupons personalizadas e dispara alertas estruturados via API.

## 🛠️ Arquitetura e Fluxo do Projeto

1. **Camada de Dados (SQL/PostgreSQL):** Um nó do n8n se conecta ao banco transacional (Supabase) e executa uma query avançada utilizando agrupamentos (`GROUP BY`) e filtros de exclusão (`HAVING` + `NOT IN`) para isolar clientes inativos durante o ano corrente.
2. **Camada de Inteligência (GenAI / LLM):** Os dados de cada cliente (Nome e histórico de gastos) são enviados linha por linha para um **AI Agent**. O agente atua sob regras rígidas de negócio (System Prompt):
   - Clientes com gastos ≥ R\$ 500 são classificados como **VIP** e recebem o cupom `RECONECTAR20` (20% OFF).
   - Clientes com gastos < R\$ 500 são classificados como **Comuns** e recebem o cupom `BEMVINDO10` (10% OFF).
3. **Camada de Ação (API/HTTP Request):** O texto gerado pela IA é tratado via Expressão JavaScript para extrair cirurgicamente um JSON estruturado e enviá-lo via método `POST` para um endpoint de CRM externo.

## 📦 Como Executar o Projeto Localmente

### 1. Pré-requisitos
- Docker e Docker Compose instalados na máquina.
- Uma chave de API de IA (Google Gemini ou Groq).

### 2. Configuração do Ambiente
Clone este repositório e crie um arquivo `.env` na raiz do projeto baseado no exemplo fornecido:
```bash
cp .env.example .env
```
Preencha as variáveis dentro do `.env` com a sua chave de criptografia do n8n e as chaves de API da IA.

### 3. Inicialização
Rode o comando abaixo no seu terminal para subir a instância oficial e persistente do n8n:
```bash
docker compose up -d
```
Acesse o painel visual através do endereço: `http://localhost:5678/setup`

## 📊 Estrutura do JSON Enviado para a API
```json
{
  "cliente_id": 2,
  "nome_cliente": "Bruno Costa",
  "categoria_cliente": "VIP",
  "codigo_cupom": "RECONECTAR20",
  "texto_email": "Texto curto e personalizado gerado pela IA..."
}
```