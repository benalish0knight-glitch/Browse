# Browse


<div align="center">
<img src="./img/browse.jpg" alt="Ancestral Knowledge" width="50%" height="50%">
</div>

## Passo a passo para Modelo Externo

1. Groq - Mais Recomendado! ⚡

Como conseguir:

1) Acesse: https://console.groq.com

2) Crie conta (pode usar Google/GitHub)

3) Vá em: API Keys (menu lateral esquerdo)

4) Clique: "Create API Key"

5) Copie a chave (começa com gsk_...)

```bash
# Cole no .env
GROQ_API_KEY=gsk_sua_chave_aqui
```

```yml
# No docker-compose.yml, descomente:
- OPENAI_API_BASE_URL=https://api.groq.com/openai/v1
- OPENAI_API_KEY=${GROQ_API_KEY:-}
```

## Passo a passo para Modelo Interno

```yml
Passo 1: Edite o docker-compose.yml
yaml# 1. COMENTE a seção de API externa:
# - OPENAI_API_BASE_URL=https://api.openai.com/v1
# - OPENAI_API_KEY=${OPENAI_API_KEY:-}

# 2. DESCOMENTE a seção Ollama:
- OLLAMA_BASE_URL=http://ollama:11434
- ENABLE_OLLAMA_API=true

# 3. DESCOMENTE o depends_on:
depends_on:
  - ollama

# 4. DESCOMENTE todo o serviço ollama:
ollama:
  image: ollama/ollama:latest
  # ... resto da configuração

# 5. DESCOMENTE o volume ollama no final:
volumes:
  open-webui:
    driver: local
  ollama:
    driver: local
```
```bash
# Modelo recomendado (2GB, rápido e bom)
docker exec -it ollama ollama pull llama3.2:3b

# Liste os modelos
docker exec -it ollama ollama list

# Deve mostrar algo como:
# NAME              ID              SIZE    MODIFIED
# llama3.2:3b       abc123def       2.0 GB  2 minutes ago
```


## Início

```bash
# 1. Inicie
docker-compose up -d

# 3. Acesse
http://localhost:3000

# 4. Verificar log
docker-compose logs open-webui | grep -i error


# 5. Desligar
docker compose down
```

### Teste de recebimento RAG

1) Passo 1: Upload do Arquivo de Teste

- teste_rag_api.txt
```txt
A onça de jacarei é roxa.
```

```
# 1. Upload do Arquivo
# Substitua 'YOUR_API_KEY' pelo seu token real.
# O arquivo 'teste_rag_api.txt' deve estar no diretório atual.
```

```bash
curl -X POST "http://localhost:3000/api/v1/files/" \
-H "Authorization: Bearer sk-a4d8fd1bd7fa4c458c8e9899df5de14c" \
-F "file=@teste_rag_api.txt"
```

```json
{
    "id": "75e0d796-2ea7-46c5-a5db-1454c0f0d2b1",
    "user_id": "fb50ad93-a979-4382-b9a0-dd627fa64408",
    "hash": null,
    "filename": "teste_rag_api.txt",
    "data": {
        "status": "pending"
    },
    "meta": {
        "name": "teste_rag_api.txt",
        "content_type": "text/plain",
        "size": 28,
        "data": {}
    },
    "created_at": 1762911632,
    "updated_at": 1762911632,
    "status": true,
    "path": "/app/backend/data/uploads/75e0d796-2ea7-46c5-a5db-1454c0f0d2b1_teste_rag_api.txt",
    "access_control": null
}
```
2) Passo 2: Chat com RAG (Usando o file_id Obtido )

```
# 2. Chat com RAG
# Substitua 'YOUR_API_KEY' pelo seu token real.
# Substitua 'SEU_FILE_ID_AQUI' pelo ID do arquivo obtido no Passo 1.
# Substitua 'gpt-4-turbo' pelo nome do modelo que você está usando.
```

```bash
curl -X POST "http://localhost:3000/api/chat/completions" \ 
-H "Authorization: Bearer sk-a4d8fd1bd7fa4c458c8e9899df5de14c" \
-H "Content-Type: application/json" \
      -d '{
              "model": "groq/compound",
              "messages": [
                  {
                      "role": "user",
                      "content": "Qual é a cor da onça de jacarei?"
                  }
              ],
              "files": [
                  {
                      "type": "file",
                      "id": "75e0d796-2ea7-46c5-a5db-1454c0f0d2b1"
                  }
              ]
          }'
```




```json
{
    "sources": [
        {
            "source": {
                "type": "file",
                "id": "75e0d796-2ea7-46c5-a5db-1454c0f0d2b1"
            },
            "document": [
                "A onça de jacarei é roxa."
            ],
            "metadata": [
                {
                    "embedding_config": "{'engine': '', 'model': 'sentence-transformers/all-MiniLM-L6-v2'}",
                    "created_by": "fb50ad93-a979-4382-b9a0-dd627fa64408",
                    "name": "teste_rag_api.txt",
                    "start_index": 0,
                    "hash": "8531730fcb225201b32bbe3292c44df8983471dc0ddc6e492d2ce9b5cf60bce2",
                    "source": "teste_rag_api.txt",
                    "file_id": "75e0d796-2ea7-46c5-a5db-1454c0f0d2b1"
                }
            ],
            "distances": [
                0.8749670386314392
            ]
        }
    ],
    "id": "stub",
    "object": "chat.completion",
    "created": 1762910871,
    "model": "groq/compound",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": "A onça de Jacareí é roxa【1】.",
                "reasoning": "<Think>\n\n</Think>"
            },
            "logprobs": null,
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "queue_time": 0.300248,
        "prompt_tokens": 944,
        "prompt_time": 0.034124,
        "completion_tokens": 92,
        "completion_time": 0.218688,
        "total_tokens": 1036,
        "total_time": 0.252812
    },
    "usage_breakdown": {
        "models": [
            {
                "model": "meta-llama/llama-4-scout-17b-16e-instruct",
                "usage": {
                    "queue_time": 0.150823613,
                    "prompt_tokens": 465,
                    "prompt_time": 0.012598853,
                    "completion_tokens": 15,
                    "completion_time": 0.03405118,
                    "total_tokens": 480,
                    "total_time": 0.046650033
                }
            },
            {
                "model": "openai/gpt-oss-120b",
                "usage": {
                    "queue_time": 0.149424135,
                    "prompt_tokens": 479,
                    "prompt_time": 0.02152503,
                    "completion_tokens": 77,
                    "completion_time": 0.184636877,
                    "total_tokens": 556,
                    "total_time": 0.206161907
                }
            }
        ]
    },
    "x_groq": {
        "id": "stub"
    },
    "service_tier": "on_demand"
}
```

```bash
curl -X POST "http://localhost:3000/api/chat/completions" \
     -H "Authorization: Bearer sk-a4d8fd1bd7fa4c458c8e9899df5de14c" \
     -H "Content-Type: application/json" \
     -d '{
           "model": "groq/compound",
           "messages": [
             {"role": "user", "content": "Qual é o código de ativação para o modo de desenvolvedor?"}
           ],
           "files": [
             {"type": "file", "id": "75e0d796-2ea7-46c5-a5db-1454c0f0d2b1"}
           ]
         }'

```

```json
{
    "sources": [
        {
            "source": {
                "type": "file",
                "id": "75e0d796-2ea7-46c5-a5db-1454c0f0d2b1"
            },
            "document": [
                "A onça de jacarei é roxa."
            ],
            "metadata": [
                {
                    "source": "teste_rag_api.txt",
                    "created_by": "fb50ad93-a979-4382-b9a0-dd627fa64408",
                    "name": "teste_rag_api.txt",
                    "start_index": 0,
                    "embedding_config": "{'engine': '', 'model': 'sentence-transformers/all-MiniLM-L6-v2'}",
                    "file_id": "75e0d796-2ea7-46c5-a5db-1454c0f0d2b1",
                    "hash": "8531730fcb225201b32bbe3292c44df8983471dc0ddc6e492d2ce9b5cf60bce2"
                }
            ],
            "distances": [
                0.6451036334037781
            ]
        }
    ],
    "id": "stub",
    "object": "chat.completion",
    "created": 1762912120,
    "model": "groq/compound",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": "Não sei qual é o código de ativação para o modo de desenvolvedor. O contexto fornecido não contém nenhuma informação sobre esse assunto (ele apenas menciona a cor de uma onça). Se você puder fornecer mais detalhes ou outra fonte, tentarei ajudar.",
                "reasoning": "Não sei o código de ativação para o modo de desenvolvedor. A informação fornecida não menciona esse tópico, apenas fala sobre a cor da onça de jacaré, que é roxa [1]. Se você puder fornecer mais contexto ou detalhes, ficarei feliz em tentar ajudar."
            },
            "logprobs": null,
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "queue_time": 0.452865,
        "prompt_tokens": 2364,
        "prompt_time": 0.094986,
        "completion_tokens": 296,
        "completion_time": 0.631897,
        "total_tokens": 2660,
        "total_time": 0.726883
    },
    "usage_breakdown": {
        "models": [
            {
                "model": "meta-llama/llama-4-scout-17b-16e-instruct",
                "usage": {
                    "queue_time": 0.152253691,
                    "prompt_tokens": 471,
                    "prompt_time": 0.017585097,
                    "completion_tokens": 20,
                    "completion_time": 0.04533036,
                    "total_tokens": 491,
                    "total_time": 0.062915457
                }
            },
            {
                "model": "meta-llama/llama-4-scout-17b-16e-instruct",
                "usage": {
                    "queue_time": 0.151116411,
                    "prompt_tokens": 874,
                    "prompt_time": 0.031092068,
                    "completion_tokens": 61,
                    "completion_time": 0.13819574,
                    "total_tokens": 935,
                    "total_time": 0.169287808
                }
            },
            {
                "model": "openai/gpt-oss-120b",
                "usage": {
                    "queue_time": 0.149494948,
                    "prompt_tokens": 1019,
                    "prompt_time": 0.046308831,
                    "completion_tokens": 215,
                    "completion_time": 0.448370755,
                    "total_tokens": 1234,
                    "total_time": 0.494679586
                }
            }
        ]
    },
    "x_groq": {
        "id": "stub"
    },
    "service_tier": "on_demand"
}
```
