# HubDev Skill

This skill teaches AI agents how to query **Hub do Desenvolvedor** APIs using `curl`, with a focus on CNPJ registration data, Simples Nacional/SIMEI status, and CPF data from Brazil's Receita Federal.

## What It Does

The skill documents how to call Hub do Desenvolvedor web services at `https://ws.hubdodesenvolvedor.com.br`, always using token-based authentication through the `token` URL parameter.

It covers three main flows:

- CNPJ lookup via `WSCNPJ1`
- Simples Nacional, SIMEI, and MEI lookup via `WSSIMPLESJSON`
- CPF lookup via the CPF endpoint

## Configuration

The API calls require:

- A valid Hub do Desenvolvedor token
- Available credits
- An authorized IP in the Hub do Desenvolvedor dashboard, when applicable

Recommended environment variable example:

```bash
HUBDEV_TOKEN=your_token_here
```

## CNPJ Lookup

When the user asks for a CNPJ lookup using `WSCNPJ1`, the skill instructs the agent to run two queries for the same CNPJ:

- `WSCNPJ1`, to retrieve company registration data
- `WSSIMPLESJSON`, to retrieve Simples Nacional and SIMEI/MEI status

The agent must return only one final JSON object: the original `WSCNPJ1` response with two fields appended inside `result`:

- `simples_nacional`: `SIM` or `NÃO`
- `simples_nacional_mei`: `SIM` or `NÃO`

Example call:

```bash
curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/cnpj/?cnpj=13481309000192&token=YOUR_TOKEN" \
  --max-time 310

curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/simples/?cnpj=13481309000192&token=YOUR_TOKEN" \
  --max-time 310
```

## Simples Nacional Only Lookup

When the user asks only about Simples Nacional, SIMEI, or MEI status, the skill instructs the agent to run only `WSSIMPLESJSON` and return that query's JSON response directly.

Example:

```bash
curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/simples/?cnpj=13481309000192&token=YOUR_TOKEN" \
  --max-time 310
```

## CPF Lookup

The skill also teaches the agent how to query CPF data, including support for:

- Normal lookup, with a recommended timeout of 610 seconds
- Turbo lookup, with a recommended timeout of 35 seconds
- CPF lookup using only the CPF number and token

Example:

```bash
curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/cpf/?cpf=00539287768&token=YOUR_TOKEN" \
  --max-time 610
```

## Important Options

- `ignore_db`: forces a direct Receita Federal lookup and consumes more credits
- `last_update=2`: checks the last update date without consuming credits
- `ie=1`: includes real-time State Tax Registrations in CNPJ lookups
- `turbo`: uses turbo mode for CPF lookups

## Error Handling

The skill instructs the agent to always check the `return` field before processing the result.

Common errors include:

- `Parametro Invalido.`
- `Token Inválido ou sem saldo para a consulta.`
- `IP de origem nao identificado.`
- `Consulta não retornou`
- `Timeout.`
- `Limite Excedido`
- `Token Bloqueado.`

When a query fails, the agent must return the relevant error JSON and must not invent missing values.

## Files

- `SKILL.md` - Main instructions for AI agents
- `README.md` - Skill behavior summary

## Version

1.0.0
