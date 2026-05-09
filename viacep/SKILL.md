---
name: viacep
description: "Query ViaCEP (viacep.com.br) via curl to look up Brazilian postal codes (CEP). Use whenever the user needs address data from a CEP, including street, neighborhood, city, state, IBGE code, DDD, and SIAFI. Returns JSON and requires no authentication token."
tags:
  - cep
  - viacep
  - brazil
  - postal-code
  - address
  - curl
---

# ViaCEP Postal Code Queries via cURL

## Overview

This skill teaches agents how to query **ViaCEP** (`viacep.com.br`) to retrieve Brazilian address data from a CEP (Brazilian postal code). ViaCEP is a free web service and does not require authentication.

**Base URL:** `https://viacep.com.br`

**Authentication:** none.

**Default response format:** JSON.

## CEP Lookup

### Description

Use this endpoint when the user provides a CEP and asks for address information. The CEP must contain exactly 8 digits. If the user provides a formatted CEP such as `01001-000`, remove all non-digit characters before calling the API.

### Endpoint

```text
https://viacep.com.br/ws/{CEP}/json/
```

### Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `CEP` | Yes | Brazilian postal code with exactly 8 digits, digits only |

### curl Example

```bash
curl -X GET \
  "https://viacep.com.br/ws/01001000/json/" \
  --max-time 30
```

### Successful Response Example

```json
{
  "cep": "01001-000",
  "logradouro": "Praca da Se",
  "complemento": "lado impar",
  "unidade": "",
  "bairro": "Se",
  "localidade": "Sao Paulo",
  "uf": "SP",
  "estado": "Sao Paulo",
  "regiao": "Sudeste",
  "ibge": "3550308",
  "gia": "1004",
  "ddd": "11",
  "siafi": "7107"
}
```

### Response Fields

| Field | Description |
|-------|-------------|
| `cep` | Formatted postal code |
| `logradouro` | Street, avenue, square, or public place name |
| `complemento` | Address complement returned by ViaCEP, if any |
| `unidade` | Unit information, when available |
| `bairro` | Neighborhood |
| `localidade` | City |
| `uf` | Brazilian state abbreviation |
| `estado` | Brazilian state name |
| `regiao` | Brazilian region |
| `ibge` | IBGE city code |
| `gia` | GIA/ICMS code, mainly for Sao Paulo state |
| `ddd` | Telephone area code |
| `siafi` | SIAFI municipality code |

## Error Handling

### Invalid CEP Format

If the CEP does not contain exactly 8 digits, do not call the API. Ask the user for a valid CEP or return a clear validation error.

ViaCEP returns HTTP `400 Bad Request` for invalid formats such as:
- `950100100` with 9 digits
- `95010A10` with letters
- `95010 10` with spaces in the raw value

### Existing Format but Not Found

If the CEP has a valid 8-digit format but does not exist in ViaCEP's database, the API returns:

```json
{
  "erro": true
}
```

When this happens, tell the user that the CEP was not found. Do not invent address data.

## Agent Workflow

1. Extract the CEP from the user's message.
2. Normalize it by removing non-digit characters.
3. Validate that the normalized CEP contains exactly 8 digits.
4. Query `https://viacep.com.br/ws/{CEP}/json/` using `curl`.
5. If the response contains `"erro": true`, report that the CEP was not found.
6. Otherwise, return the JSON response or summarize the address fields requested by the user.

## Best Practices

- Always normalize formatted CEPs before querying, for example `01001-000` becomes `01001000`.
- Always validate the CEP length before calling ViaCEP.
- Use `--max-time 30` to avoid hanging requests.
- Prefer JSON output unless the user explicitly asks for XML.
- Do not use ViaCEP for massive local database validation; ViaCEP may block abusive usage.
