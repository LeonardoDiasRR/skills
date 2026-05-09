# Leonardo Dias - Skills Repository

This repository contains custom skills created by **Leonardo Dias** for AI agents and automation systems.

## 📦 Skills

### 1. [telegram-send-attachment](./telegram-send-attachment/)
A skill designed to help AI agents (like OpenClaw, Hermes, and similar) correctly send file attachments in Telegram chats using the Telegram Bot API.

**Key Features:**
- File-type agnostic (supports documents, images, videos, audio)
- Uses curl for universal compatibility
- Prevents common mistake of sending file content as text instead of attachments
- Comprehensive examples and troubleshooting guide

**Version:** 1.2.0

### 2. [sei](./sei/)
Uma skill para automatizar interações com o Sistema Eletrônico de Informações (SEI), usado por instituições públicas brasileiras.

**Principais Funcionalidades:**
- Autenticação automática e detecção de sessão
- Pesquisa de processos e documentos por número
- Filtro de processos atribuídos ao usuário
- Geração e download de arquivos ZIP de processos
- Suporte multi-instituição (qualquer órgão que use SEI)
- Tratamento de erros e validação de estados

**Versão:** 1.0.0

### 3. [groq-whisper-transcription](./groq-whisper-transcription/)
A skill that enables AI agents (like OpenClaw and Hermes) to understand and respond to audio/voice messages naturally — as if they had received a plain text message. Uses the Groq API with Whisper models via `curl`.

**Key Features:**
- Supports Telegram and WhatsApp audio formats (`.ogg`, `.oga`, `.opus`, `.m4a`, `.mp3`, `.wav`)
- Uses `curl` only — no Python SDK required
- Agent responds to the message content naturally, never exposing the transcription
- Optional `ffmpeg` conversion for unsupported formats

**Version:** 1.0.0

### 4. [hubdev](./hubdev/)
Uma skill para consultar APIs do Hub do Desenvolvedor via `curl`, incluindo dados cadastrais de CNPJ, enquadramento no Simples Nacional/SIMEI e consultas de CPF na Receita Federal.

**Principais Funcionalidades:**
- Consulta de CNPJ via `WSCNPJ1` com retorno JSON enriquecido com dados do Simples Nacional
- Consulta específica de Simples Nacional, SIMEI e MEI via `WSSIMPLESJSON`
- Consulta de CPF com suporte a modo normal e turbo
- Uso de autenticação por token e exemplos completos com `curl`
- Tratamento de erros comuns e recomendações de timeout

**Versão:** 1.0.0

### 5. [viacep](./viacep/)
A skill that teaches AI agents how to query Brazilian CEP address data through the free ViaCEP web service using `curl`.

**Key Features:**
- Query CEP data through `https://viacep.com.br/ws/{CEP}/json/`
- Normalize formatted CEPs such as `01001-000` to 8 digits
- Validate CEP format before calling the API
- Handle not-found responses with `{ "erro": true }`
- Return address fields such as street, neighborhood, city, state, IBGE code, DDD, and SIAFI

**Version:** 1.0.0

---

## 🎯 Purpose

These skills are designed to enhance AI agent capabilities by providing:
- Clear, actionable instructions
- Real-world examples
- Common pitfalls and solutions
- Universal tool recommendations (curl, standard APIs)

## 📝 Contributing

This is a personal skills repository. Each skill includes:
- `SKILL.md` - Main skill documentation for AI agents
- `README.md` - Comprehensive usage guide

## 📄 License

These skills are provided as-is for educational and practical use.

---

**Author:** Leonardo Dias  
**Last Updated:** May 2026
