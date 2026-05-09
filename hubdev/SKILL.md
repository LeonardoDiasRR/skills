
---
name: hubdev
description: "Query Hub do Desenvolvedor APIs (hubdodesenvolvedor.com.br) via curl. Use whenever the user needs to look up a Brazilian CNPJ, Simples Nacional status, or CPF at the Receita Federal (Brazilian tax authority). Covers: CNPJ registration data (company name, partners, CNAE, status), Simples Nacional / SIMEI opt-in verification, and CPF individual taxpayer data. All calls use curl with a token-based auth and return JSON."
tags:
  - cnpj
  - cpf
  - simples-nacional
  - receita-federal
  - hubdodesenvolvedor
  - curl
  - brazil
---

# Hub do Desenvolvedor API Queries via cURL

## Overview

This skill covers how to call **Hub do Desenvolvedor** (hubdodesenvolvedor.com.br) APIs using `curl`. All APIs return JSON and require a valid **authentication token**, plus an authorized IP registered in the user's dashboard.

**Base URL:** `https://ws.hubdodesenvolvedor.com.br`

**Authentication:** `token` query-string parameter (e.g. `&token=YOUR_TOKEN`).

**Credits:** Each successful query consumes credits. Queries that go directly to the Receita Federal (`ignore_db`) consume more credits.

---

## 1. CNPJ JSON WebService — WSCNPJ1

### Description

Fetches CNPJ registration data from the Receita Federal. The lookup first hits the internal database (continuously updated). If the CNPJ is not found there, the query is forwarded to the Receita Federal.

When the user asks for a CNPJ lookup using `WSCNPJ1`, always perform two queries for the same CNPJ: `WSCNPJ1` and `WSSIMPLESJSON`. Return only one JSON object to the user: the original `WSCNPJ1` response with the Simples Nacional information appended inside `result`.

If the user asks only about Simples Nacional, SIMEI, or MEI status, use only `WSSIMPLESJSON` and return its JSON response directly.

Append these fields to `result`:
- `simples_nacional`: `"SIM"` when `WSSIMPLESJSON.result.situacao_simples_nacional` indicates the company is opted into Simples Nacional; otherwise `"NÃO"`.
- `simples_nacional_mei`: `"SIM"` when `WSSIMPLESJSON.result.situacao_simei` indicates the company is opted into SIMEI/MEI; otherwise `"NÃO"`.

In the enriched `WSCNPJ1` flow, do not return the raw `WSSIMPLESJSON` payload separately. If either query fails, return the relevant error JSON instead of inventing missing values.

- `ignore_db`: forces a direct query to the Receita Federal (costs 2 credits instead of 1).
- `last_update=2`: checks whether the CNPJ exists in the database and when it was last updated (no credits consumed).
- `ie=1`: includes State Tax Registrations (Inscrições Estaduais) in real time (costs 2 extra credits).
- **Timeout:** 300 seconds.

**Credit consumption:**
- Database lookup: 1 credit
- Receita Federal lookup: 2 credits

### Endpoint

```
http://ws.hubdodesenvolvedor.com.br/v2/cnpj/?cnpj={CNPJ}&token={YOUR_TOKEN}
```

### Parameters

| Parameter     | Required | Description                                              |
|---------------|----------|----------------------------------------------------------|
| `cnpj`        | Yes      | CNPJ to query (digits only)                              |
| `token`       | Yes      | Your authentication token                                |
| `ignore_db`   | No       | If present, queries the Receita Federal directly         |
| `last_update` | No       | Set to `2` to check the last update date without cost    |
| `ie`          | No       | Set to `1` to include real-time State Tax Registrations  |

### curl example

```bash
curl -X GET \
  "http://ws.hubdodesenvolvedor.com.br/v2/cnpj/?cnpj=13481309000192&token=YOUR_TOKEN" \
  --max-time 310

curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/simples/?cnpj=13481309000192&token=YOUR_TOKEN" \
  --max-time 310
```

### Response example (JSON)

```json
{
  "status": "true",
  "return": "OK",
  "consumed": 1,
  "result": {
    "numero_de_inscricao": "13481309000192",
    "tipo": "MATRIZ",
    "abertura": "06/04/2011",
    "nome": "RN COMERCIO VAREJISTA S.A",
    "fantasia": "RICARDO ELETRO",
    "atividade_principal": {
      "text": "Comércio varejista especializado de eletrodomésticos e equipamentos de áudio e vídeo",
      "code": "47.53-9-00"
    },
    "natureza_juridica": "205-4 - Sociedade Anônima Fechada",
    "logradouro": "PC BARAO DO RIO BRANCO 43",
    "numero": "43 A",
    "cep": "45.000-903",
    "municipio": "VITORIA DA CONQUISTA",
    "uf": "BA",
    "situacao": "ATIVA",
    "capital_social": "257292670.00",
    "simples_nacional": "SIM",
    "simples_nacional_mei": "NÃO"
  }
}
```

### Response fields

| Field                    | Description                                          |
|--------------------------|------------------------------------------------------|
| `return`                 | `OK` = success, `NOK` = error                        |
| `message`                | Error message when `return` is `NOK`                 |
| `nome`                   | Legal company name (Razão Social)                    |
| `fantasia`               | Trade name (Nome Fantasia)                           |
| `situacao`               | Registration status (e.g. `ATIVA`)                   |
| `atividade_principal`    | Primary CNAE activity                                |
| `atividades_secundarias` | List of secondary CNAE activities                    |
| `quadro_socios`          | List of partners/shareholders                        |
| `simples_nacional`       | `SIM` or `NÃO`, derived from `WSSIMPLESJSON`          |
| `simples_nacional_mei`   | `SIM` or `NÃO`, derived from `WSSIMPLESJSON` SIMEI    |

---

## 2. Simples Nacional JSON WebService — WSSIMPLESJSON

### Description

Checks a CNPJ's opt-in status for Simples Nacional and SIMEI. The lookup first hits the internal database. If the CNPJ is not found, the query is forwarded to the Receita Federal.

> ⚠️ **Notice:** This query type is being phased out and will be replaced. Contact support for details on the new alternative.

- `ignore_db`: forces a direct query to the Receita Federal (costs 2 credits instead of 1).
- `last_update=2`: checks the last update date in the database (no credits consumed).

**Credit consumption:**
- Database lookup: 1 credit
- Receita Federal lookup: 2 credits

### Endpoint

```
https://ws.hubdodesenvolvedor.com.br/v2/simples/?cnpj={CNPJ}&token={YOUR_TOKEN}
```

### Parameters

| Parameter     | Required | Description                                              |
|---------------|----------|----------------------------------------------------------|
| `cnpj`        | Yes      | CNPJ to query (digits only)                              |
| `token`       | Yes      | Your authentication token                                |
| `ignore_db`   | No       | If present, queries the Receita Federal directly         |
| `last_update` | No       | Set to `2` to check the last update date without cost    |

### curl example

```bash
curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/simples/?cnpj=13481309000192&token=YOUR_TOKEN"
```

### Response example (JSON)

```json
{
  "status": true,
  "return": "OK",
  "consumed": 1,
  "result": {
    "cnpj": "13481309000192",
    "nome_empresarial": "RN COMERCIO VAREJISTA S.A",
    "situacao_simples_nacional": "NÃO optante pelo Simples Nacional",
    "situacao_simei": "NÃO optante pelo SIMEI",
    "opcoes_pelo_simples_nacional_periodos_anteriores": "Não Existem",
    "opcoes_pelo_simei_periodos_anteriores": "Não Existem",
    "agendamentos_simples_nacional": "Não Existem",
    "eventos_futuros_simples_nacional": "Não Existem",
    "eventos_futuros_simei": "Não Existem"
  }
}
```

### Response fields

| Field                                              | Description                                              |
|----------------------------------------------------|----------------------------------------------------------|
| `return`                                           | `OK` = success, `NOK` = error                            |
| `situacao_simples_nacional`                        | Whether the CNPJ is enrolled in Simples Nacional         |
| `situacao_simei`                                   | Whether the CNPJ is enrolled in SIMEI                    |
| `opcoes_pelo_simples_nacional_periodos_anteriores` | Historical Simples Nacional opt-ins from prior periods   |
| `agendamentos_simples_nacional`                    | Scheduled future events in Simples Nacional              |

---

## 3. CPF JSON WebService

### Description

Fetches individual taxpayer (CPF) registration data from the Receita Federal. The lookup first hits the internal database. If not found, the query is forwarded to the Receita Federal.

- `ignore_db`: forces a direct query to the Receita Federal.
- `last_update=2`: checks the last update date in the database (no credits consumed).
- `turbo`: official Receita Federal lookup with response in up to 30 seconds (costs 25 credits).

**Credit consumption:**
- Database lookup: 1 credit
- Receita Federal lookup (normal): 5 credits
- Receita Federal lookup (turbo): 25 credits

**Timeout:**
- Normal: 600 seconds
- Turbo: 30 seconds

### Endpoint

```
https://ws.hubdodesenvolvedor.com.br/v2/cpf/?cpf={CPF}&token={YOUR_TOKEN}
```

### Parameters

| Parameter     | Required      | Description                                               |
|---------------|---------------|-----------------------------------------------------------|
| `cpf`         | Yes           | CPF to query (digits only)                                |
| `token`       | Yes           | Your authentication token                                 |
| `ignore_db`   | No            | If present, queries the Receita Federal directly          |
| `turbo`       | No            | Turbo lookup (30 s, 25 credits)                           |
| `last_update` | No            | Set to `2` to check the last update date without cost     |

### curl example

```bash
curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/cpf/?cpf=00539287768&token=YOUR_TOKEN" \
  --max-time 610
```

### curl example (turbo mode)

```bash
curl -X GET \
  "https://ws.hubdodesenvolvedor.com.br/v2/cpf/?cpf=00539287768&token=YOUR_TOKEN&turbo" \
  --max-time 35
```

### Response example (JSON)

```json
{
  "status": true,
  "return": "OK",
  "consumed": 1,
  "result": {
    "numero_de_cpf": "005.392.877-68",
    "nome_da_pf": "FULANO DE TAL",
    "data_nascimento": "26/08/1939",
    "situacao_cadastral": "REGULAR",
    "data_inscricao": "anterior a 10/11/1990",
    "digito_verificador": "00",
    "comprovante_emitido": "CE0E.8687.3D2E.E534",
    "comprovante_emitido_data": "09:02:38 às 27/01/2017"
  }
}
```

### Response fields

| Field                 | Description                                             |
|-----------------------|---------------------------------------------------------|
| `return`              | `OK` = success, `NOK` = error                           |
| `message`             | Error message when `return` is `NOK`                    |
| `numero_de_cpf`       | Formatted CPF number                                    |
| `nome_da_pf`          | Full name of the individual                             |
| `data_nascimento`     | Date of birth                                           |
| `situacao_cadastral`  | Registration status (e.g. `REGULAR`, `CANCELADA`)       |
| `data_inscricao`      | CPF enrollment date                                     |
| `digito_verificador`  | Check digit                                             |
| `comprovante_emitido` | Proof-of-query certificate code                         |

---

## Common Error Handling

All services return `"return": "NOK"` on error, with a descriptive message in the `message` field:

| Message                                        | Cause                                                          |
|------------------------------------------------|----------------------------------------------------------------|
| `Parametro Invalido.`                          | Document provided in an invalid format                         |
| `Token Inválido ou sem saldo para a consulta.` | Invalid token or insufficient credit balance                   |
| `IP de origem nao identificado.`               | Originating IP not registered in the HubDev dashboard          |
| `Consulta não retornou`                        | Failed to connect to the data source — retry                   |
| `Timeout.`                                     | Receita Federal did not respond within the time limit          |
| `Limite Excedido`                              | Too many requests from the same IP in a short period           |
| `Token Bloqueado.`                             | Internal token error — contact support                         |

---

## Best Practices

- **Always check the `return` field**: verify it is `OK` before processing the `result` payload.
- **Set the curl timeout** appropriately: use `--max-time 310` for CNPJ, `--max-time 610` for normal CPF, and `--max-time 35` for turbo CPF.
- **Authorize your IP**: ensure the server's IP is listed under *IPs Permitidos* in the HubDev dashboard, or leave the field blank to allow all IPs.
- **Save credits**: use `last_update=2` to check whether a document exists and when it was last updated before performing a full (paid) query.
