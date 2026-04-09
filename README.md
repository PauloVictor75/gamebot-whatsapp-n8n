# 🎮 RetroGame Store Bot — Atendimento Inteligente via WhatsApp

> Agente de atendimento automatizado para loja de jogos, integrado ao WhatsApp, com suporte a **mensagens de texto**, **áudio** e **imagem**, alimentado por IA e conectado a uma planilha de estoque em tempo real.

---

## 📸 Preview

### Fluxo no n8n
![Workflow](./assets/workflow.png)

### Conversa real no WhatsApp
![WhatsApp](./assets/whatsapp-preview.png)

---

## 🚀 Funcionalidades

- 💬 **Atendimento por texto** — responde perguntas sobre jogos, preços e disponibilidade
- 🎙️ **Atendimento por áudio** — transcreve mensagens de voz e processa como texto
- 🖼️ **Atendimento por imagem** — analisa imagens enviadas pelo cliente
- 📊 **Catálogo dinâmico** — consulta planilha Google Sheets com estoque atualizado
- 🛒 **Registro de pedidos** — anota pedidos automaticamente na planilha
- 🔄 **Atualização de estoque** — decrementa quantidade vendida após cada pedido
- 🧠 **Memória de conversa** — mantém contexto da conversa via Redis
- 🤖 **IA conversacional** — respostas naturais e personalizadas com OpenAI

---

## 🏗️ Arquitetura do Workflow

```
WhatsApp (Webhook)
        │
        ▼
   [Dados] → [Switch por tipo de mensagem]
        │
        ├─── Texto ──────────────────────────────────────────┐
        │                                                    │
        ├─── Áudio → get-media-base64 → Convert to File     │
        │            → Transcribe (OpenAI Whisper)           │
        │            → Mensagem de Áudio ───────────────────▶│
        │                                                    │
        └─── Imagem → get-media-base64 → Convert to File    │
                     → Analyze Image (GPT-4 Vision)         │
                     → Edit Fields ────────────────────────▶│
                                                            ▼
                                                     [AI Agent]
                                                    /    |    \
                                             OpenAI  Redis  Tools
                                                     Memory
                                                      │
                                          ┌───────────┼───────────┐
                                          ▼           ▼           ▼
                                   Lista Jogos   Lista Jogo  Atualiza
                                   (read sheet) (read sheet)  Estoque
                                                              │
                                                         Anota Pedido
                                                         (append sheet)
                                                              │
                                                              ▼
                                                       Enviar Texto
                                                       (WhatsApp)
```

---

## 🛠️ Tecnologias Utilizadas

| Ferramenta | Uso |
|---|---|
| **n8n** | Orquestração do workflow |
| **WhatsApp API** | Canal de atendimento |
| **OpenAI GPT-4** | Agente conversacional e análise de imagem |
| **OpenAI Whisper** | Transcrição de áudios |
| **Google Sheets** | Catálogo de jogos e registro de pedidos |
| **Redis** | Memória de contexto da conversa |

---

## 📋 Planilhas Google Sheets

### Aba `Produtos` — Catálogo de Jogos

| Campo | Descrição |
|---|---|
| ID | Identificador único |
| Nome | Nome do jogo |
| Gênero do Jogo | Categoria (RPG, FPS, Esporte...) |
| Plataforma | Nintendo, PlayStation, Multi-plataforma... |
| Preço do Jogo | Valor em R$ |
| Quantidade Vendida | Atualizado a cada venda |
| Imagem | Link do Google Drive |

### Aba `Pedido` — Registro de Vendas

| Campo | Descrição |
|---|---|
| Nome | Nome do cliente |
| Jogo | Jogo solicitado |
| Valor | Preço unitário |
| Quantidade | Qtd comprada |
| Valor Total | Calculado automaticamente |
| Data | Timestamp do pedido |

---

## ⚙️ Como Configurar

### Pré-requisitos

- [n8n](https://n8n.io/) instalado (self-hosted ou cloud)
- Conta na [OpenAI](https://platform.openai.com/)
- Google Sheets configurado com as abas `Produtos` e `Pedido`
- WhatsApp Business API (ex: Evolution API, Baileys, Twilio)
- Redis (local ou serviço como Upstash)

### Passo a Passo

1. **Clone o repositório**
   ```bash
   git clone https://github.com/seu-usuario/retrogame-store-bot.git
   cd retrogame-store-bot
   ```

2. **Importe o workflow no n8n**
   - Acesse seu n8n → `Workflows` → `Import`
   - Selecione o arquivo `workflow.json`

3. **Configure as credenciais no n8n**
   - `OpenAI API Key`
   - `Google Sheets OAuth2`
   - `WhatsApp / Evolution API`
   - `Redis` (host, porta, senha)

4. **Aponte o Webhook do WhatsApp**
   - Copie a URL do nó `Webhook` no n8n
   - Configure no painel do seu provedor WhatsApp como URL de recebimento

5. **Configure a planilha**
   - Preencha a aba `Produtos` com seu catálogo
   - Ajuste os IDs das planilhas nos nós `Lista Todos os Jogos`, `Lista Jogo`, `Atualiza quantidade venda` e `Anota pedido`

6. **Ative o workflow** e faça um teste enviando uma mensagem no WhatsApp!

---

## 💬 Exemplo de Conversa

```
Cliente: [envia áudio de 2 segundos]

Bot: Bom dia! Tudo ótimo por aqui, e com você? Pronto pra dar uma 
     viajada no tempo com nossos jogos e consoles retrô? Me conta, 
     como posso ajudar você hoje?

Cliente: Vcs tem jogo fifa ??

Bot: Parece que no momento não temos jogos FIFA disponíveis no nosso 
     estoque. Mas se quiser, posso te ajudar a encontrar outros jogos 
     clássicos. Quer dar uma olhada?

Cliente: Quero dois supermario

Bot: Que escolha massa! Dois Super Mario Bros. pra garantir muita 
     diversão e nostalgia. Cada um tá saindo por R$60,00, então dois 
     vão ficar R$120,00 no total.
     Me diz seu nome pra eu anotar seu pedido, por favor!

Cliente: Paulo

Bot: Pedido anotado, Paulo! Dois Super Mario Bros. por R$120,00. 
     Vai ser uma viagem incrível na nostalgia com esse clássico!
```

---

## 📁 Estrutura do Repositório

```
retrogame-store-bot/
├── workflow.json          # Workflow exportado do n8n
├── README.md
└── assets/
    ├── workflow.png        # Print do workflow no n8n
    └── whatsapp-preview.png
```

---

## 🤝 Contribuindo

Contribuições são bem-vindas! Abra uma issue ou envie um pull request.

---

## 📄 Licença

MIT License — sinta-se livre para usar e adaptar.

---

> Desenvolvido com ❤️ usando n8n + OpenAI + WhatsApp
