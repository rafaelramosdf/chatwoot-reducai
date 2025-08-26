# Quick Start Guide - Chatwoot Reducai

## Pré-requisitos
- Docker e Docker Compose instalados
- Git instalado
- Pelo menos 4GB de RAM disponível

## Início Rápido

### 1. Clone e configure o ambiente
```bash
git clone https://github.com/sua-organizacao/chatwoot-reducai.git
cd chatwoot-reducai
```

### 2. Configure as variáveis de ambiente
```bash
# Copie o arquivo de exemplo
cp .env.example .env

# Gere uma SECRET_KEY_BASE segura
docker run --rm ruby:3.2 ruby -e "require 'securerandom'; puts SecureRandom.hex(64)"

# Edite o arquivo .env e substitua 'replace_with_lengthy_secure_hex' pela chave gerada
```

### 3. Construa e inicie os containers
```bash
# Construa as imagens
docker-compose build

# Inicie todos os serviços
docker-compose up -d

# Acompanhe os logs (opcional)
docker-compose logs -f
```

### 4. Aguarde a inicialização
Aguarde alguns minutos para que todos os serviços sejam inicializados completamente.

### 5. Acesse a aplicação
- **Chatwoot**: http://localhost:3000
- **MailHog** (emails): http://localhost:8025

### 6. Login padrão
- **Email**: john@acme.inc
- **Senha**: Password1!

## Comandos úteis

```bash
# Ver status dos containers
docker-compose ps

# Parar todos os serviços
docker-compose down

# Reconstruir e reiniciar
docker-compose down && docker-compose build && docker-compose up -d

# Ver logs de um serviço específico
docker-compose logs -f rails

# Executar comandos no container Rails
docker-compose exec rails bash

# Resetar o banco de dados
docker-compose exec rails bundle exec rails db:reset
```

## Solução de problemas

### Container não inicia
```bash
# Verifique os logs do container com problema
docker-compose logs nome_do_container

# Verifique se todas as portas estão livres
netstat -tulpn | grep -E ':(3000|3036|5432|6379|8025|1025)'
```

### Erro de banco de dados
```bash
# Recriar o banco
docker-compose exec rails bundle exec rails db:drop db:create db:migrate db:seed
```

### Performance lenta
```bash
# Verifique o uso de recursos
docker stats

# Aumente a memória disponível para Docker se necessário
```

## Documentação completa
Para informações detalhadas sobre desenvolvimento, veja o arquivo `DEVELOPMENT.md`.
