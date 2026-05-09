# ViaCEP Skill

This skill teaches AI agents how to query Brazilian CEP address data using the free ViaCEP web service.

## What It Does

The skill provides instructions for looking up a CEP through:

```text
https://viacep.com.br/ws/{CEP}/json/
```

Agents learn to normalize formatted CEPs, validate the required 8-digit format, call the API with `curl`, and handle both successful and not-found responses.

## Key Features

- Query Brazilian postal codes without authentication
- Use the JSON endpoint `viacep.com.br/ws/01001000/json/`
- Normalize CEPs such as `01001-000` to `01001000`
- Validate invalid CEP formats before calling the API
- Handle ViaCEP's `{ "erro": true }` response for non-existent CEPs
- Return address fields such as street, neighborhood, city, state, IBGE code, DDD, and SIAFI

## Example

```bash
curl -X GET \
  "https://viacep.com.br/ws/01001000/json/" \
  --max-time 30
```

Example response:

```json
{
  "cep": "01001-000",
  "logradouro": "Praca da Se",
  "complemento": "lado impar",
  "bairro": "Se",
  "localidade": "Sao Paulo",
  "uf": "SP",
  "ibge": "3550308",
  "ddd": "11",
  "siafi": "7107"
}
```

## Files

- `SKILL.md` - Main instructions for AI agents
- `README.md` - Overview and usage summary

## Version

1.0.0
