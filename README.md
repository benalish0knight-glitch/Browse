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





