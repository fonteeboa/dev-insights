# 📊🔍 OpenSearch Dashboards: Otimizando Consultas de Dados Massivos (Big Data) com Busca Assíncrona

Trabalhar com logs, telemetria ou grandes volumes de dados no OpenSearch pode resultar em consultas lentas e pesadas. Este guia cobre **tudo o que um Tech Lead precisa saber** sobre otimização de buscas usando a **API de Busca Assíncrona** no **OpenSearch Dashboards**.

---

## 🧩 Arquitetura e Conceitos

O OpenSearch é composto por:

* **Cluster** → grupo de nós que armazenam e processam dados.
* **Shards** → fragmentos de índices distribuídos.
* **Dashboards** → interface de visualização e Dev Tools REST.
* **Plugins** → extensões como *Security*, *Reports* e *Asynchronous Search*.

A busca assíncrona (`_plugins/_asynchronous_search`) executa consultas **em segundo plano**, permitindo acompanhar o progresso, cancelar ou recuperar resultados sem bloquear o cliente.

**Fluxo simplificado:**

```
[Dashboards / API]
   ↓
POST /_plugins/_asynchronous_search
   ↓
→ O cluster processa a consulta em segundo plano
   ↓
← Retorna o ID
   ↓
GET /_plugins/_asynchronous_search/{id}
DELETE /_plugins/_asynchronous_search/{id}
```

---

## ⚙️ Exemplo Completo de Requisição

```json
POST /_plugins/_asynchronous_search?index=logs*,events*&keep_on_completion=true&wait_for_completion_timeout=2s
{
  "size": 5000,
  "track_total_hits": false,
  "_source": ["timestamp", "source_ip", "event_type"],
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "@timestamp": { "gte": "now-30d" }
          }
        }
      ],
      "must": [
        {
          "query_string": {
            "query": "*malware*",
            "fields": ["message", "event_type"],
            "analyze_wildcard": true,
            "allow_leading_wildcard": true
          }
        }
      ]
    }
  }
}
```

### ✅ Resposta Esperada (simplificada)

```json
{
  "id": "F3B7E5A6-24C3-11EF-AEA3-12AB34CD56EF",
  "is_partial": false,
  "is_running": true,
  "response": {
    "took": 134,
    "timed_out": false,
    "hits": { "total": 2381, "hits": [] }
  }
}
```

---

## 🧠 Busca Síncrona vs Assíncrona

| Tipo       | Endpoint                              | Bloqueia Cliente | Ideal para                              |
| ---------- | ------------------------------------- | ---------------- | --------------------------------------- |
| Síncrona   | `POST /_search`                       | ✅ Sim            | Consultas rápidas (<5s)                 |
| Assíncrona | `POST /_plugins/_asynchronous_search` | ❌ Não            | Big Data, relatórios, múltiplos índices |

> A busca assíncrona retorna imediatamente um **ID**, e o processamento continua no servidor.

---

## 📈 Parâmetros Importantes

| Parâmetro                     | Descrição                                      | Exemplo                    | Observações                       |
| ----------------------------- | ---------------------------------------------- | -------------------------- | --------------------------------- |
| `index`                       | Índices alvo                                   | `logs*, events*`           | Use curingas com cuidado          |
| `keep_on_completion`          | Mantém o resultado após a conclusão            | `true`                     | Necessário para consultar depois  |
| `wait_for_completion_timeout` | Tempo de espera inicial antes de retornar o ID | `2s`                       | Mantém o cliente responsivo       |
| `keep_alive`                  | Tempo de retenção do resultado                 | `1d`, `7d`                 | Útil para relatórios agendados    |
| `track_total_hits`            | Conta todos os documentos                      | `false`                    | Desative em Big Data              |
| `_source`                     | Campos retornados                              | `["timestamp", "message"]` | Evite `*` para melhor performance |

---

## 🔍 Estrutura da Query

**Exemplo otimizado:**

```json
{
  "bool": {
    "filter": [
      { "range": { "@timestamp": { "gte": "now-7d" } } }
    ],
    "must": [
      { "match": { "event_type": "unauthorized_access" } }
    ]
  }
}
```

**Retorno esperado:**

```json
{
  "hits": {
    "total": 542,
    "max_score": 1.0,
    "hits": [
      {
        "_index": "logs-2025.11",
        "_id": "Dfgh12345",
        "_source": {
          "timestamp": "2025-11-03T22:14:25Z",
          "event_type": "unauthorized_access",
          "source_ip": "192.168.3.24"
        }
      }
    ]
  }
}
```

---

## ⚡ Dicas de Performance

* Prefira **`filter`** para campos exatos e datas (cacheável).
* Evite curingas iniciais (`*malware*` → lento).
* Reduza `size` (1k–5k) e use **scroll** ou **search_after**.
* Ajuste `refresh_interval` para índices estáticos.
* Monitore o heap com `_nodes/stats/jvm`.
* Desative `track_total_hits` para buscas não analíticas.

---

## 🧮 Monitoramento e Acompanhamento

### Ver progresso

```bash
GET /_plugins/_asynchronous_search/F3B7E5A6-24C3-11EF-AEA3-12AB34CD56EF
```

**Retorno esperado:**

```json
{
  "id": "F3B7E5A6-24C3-11EF-AEA3-12AB34CD56EF",
  "is_running": false,
  "is_partial": false,
  "response": {
    "took": 3210,
    "hits": { "total": 2381, "hits": [...] }
  }
}
```

### Ver estatísticas globais

```bash
GET /_plugins/_asynchronous_search/stats
```

**Retorno esperado:**

```json
{
  "total": {
    "submitted": 102,
    "completed": 95,
    "running": 7,
    "failed": 0
  }
}
```

### Cancelar ou limpar buscas antigas

```bash
DELETE /_plugins/_asynchronous_search/F3B7E5A6-24C3-11EF-AEA3-12AB34CD56EF
```

---

## 🔒 Segurança e Controle de Acesso

* Requer permissão: `cluster:admin/opensearch/asynchronous_search/*`
* Resultados são armazenados em índices internos (`.opendistro-asynchronous-search*`).
* Sempre habilite **TLS** e **autenticação** (BasicAuth, JWT ou SAML).
* Use **DLS/FLS** para restringir dados sensíveis.
* Realize **limpeza periódica** de buscas antigas.

---

## 📚 Referências

* [Documentação do OpenSearch – Asynchronous Search](https://docs.opensearch.org/docs/latest/search-plugins/async/index/)
* [Boas Práticas AWS OpenSearch](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/bp.html)
* [Guia Opster – Async Search](https://opster.com/guides/opensearch/opensearch-how-tos/opensearch-async-search/)

