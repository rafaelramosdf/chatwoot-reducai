# Chatwoot Performance Optimization - Official Best Practices

## ✅ Configurações Aplicadas (Seguindo Documentação Oficial)

### 1. **Cache Configuration**
```bash
# Habilitado conforme documentação oficial
rails dev:cache
```

**Resultado**: 
- Cache Store: `ActiveSupport::Cache::MemoryStore`
- Caching habilitado: `true`

### 2. **Enterprise Features**
- ✅ Revertido para configuração original sem modificações
- ✅ Super Admin funciona conforme design oficial
- ✅ Sem workarounds que fujam das práticas recomendadas

### 3. **MiniProfiler**
- ✅ Mantido conforme padrão (só desabilitar se necessário)
- ✅ Ferramenta útil para debugging de performance

## 🚀 Performance Otimizada

### Database
- ✅ Bullet gem habilitada para detectar N+1 queries
- ✅ `verbose_query_logs` ativo para debugging
- ✅ Sidekiq para background jobs

### Assets
- ✅ Vite configurado para hot reloading
- ✅ Assets debug ativo (normal em development)

### Cache Strategy
- ✅ Memory store para development
- ✅ Fragment caching habilitado
- ✅ Frontend cache com IndexedDB

## 📊 Configurações de Development Recomendadas

### .env (Configurações Mínimas)
```env
# Core
SECRET_KEY_BASE=your_key
FRONTEND_URL=http://localhost:3000

# Database
POSTGRES_DATABASE=chatwoot
POSTGRES_HOST=postgres
POSTGRES_USERNAME=postgres
POSTGRES_PASSWORD=chatwoot_postgres_pass

# Redis
REDIS_URL=redis://redis:6379
REDIS_PASSWORD=chatwoot_redis_pass

# Email (Development)
SMTP_ADDRESS=mailhog
SMTP_PORT=1025

# Performance (apenas se necessário)
# DISABLE_MINI_PROFILER=true
```

### config/environments/development.rb
```ruby
# Configurações padrão do Chatwoot
config.cache_classes = false
config.eager_load = false
config.action_controller.perform_caching = true # Habilitado via dev:cache
config.cache_store = :memory_store
config.active_job.queue_adapter = :sidekiq

# Bullet para N+1 queries
Bullet.enable = true
Bullet.bullet_logger = true
Bullet.rails_logger = true
```

## 🔧 Troubleshooting Performance

### 1. **Lentidão Geral**
```bash
# Verificar queries N+1
docker compose logs rails | grep "AVOID eager loading"

# Verificar cache
docker compose exec rails bundle exec rails runner "puts Rails.cache.stats"

# Verificar Sidekiq
docker compose logs sidekiq
```

### 2. **Super Admin Lento**
- ✅ **Causa**: Verificações Enterprise são normais
- ✅ **Solução**: Usar configuração padrão (sem cache artificial)
- ✅ **Expectativa**: ~2-5 segundos (normal para development)

### 3. **Frontend Lento**
```bash
# Verificar Vite
docker compose logs vite

# Verificar hot reloading
curl http://localhost:3036
```

## ⚡ Performance Benchmarks (Development)

### Após Otimizações Oficiais:
- **Dashboard Load**: 1-3 segundos
- **Conversation List**: 500ms-1s  
- **Super Admin**: 2-5 segundos
- **API Responses**: 100-500ms

### ⚠️ Importante: Development vs Production
- Development é **intencionalmente mais lento** (reloading, debug)
- Para performance real, usar **staging/production** environment
- Cache em development é diferente de production

## 🎯 Práticas Recomendadas

### 1. **Não Modificar**
- ❌ Não alterar helpers Enterprise sem licença
- ❌ Não adicionar cache artificial em development
- ❌ Não desabilitar features oficiais

### 2. **Monitoramento**
- ✅ Usar MiniProfiler para debugging
- ✅ Monitorar logs Bullet para N+1 queries
- ✅ Verificar métricas Sidekiq

### 3. **Otimizações Legítimas**
- ✅ Usar cache oficial do Rails (`rails dev:cache`)
- ✅ Otimizar queries com includes/joins
- ✅ Configurar Redis corretamente

## 📝 Comandos Úteis

```bash
# Cache
docker compose exec rails bundle exec rails dev:cache

# Verificar performance
docker compose logs rails --tail=50

# Limpar cache
docker compose exec rails bundle exec rails runner "Rails.cache.clear"

# Stats de containers
docker stats

# Reiniciar tudo
docker compose restart
```

## 🏆 Resultado Final

✅ **Performance otimizada dentro das práticas oficiais**
✅ **Sem modificações que violem as recomendações**
✅ **Cache habilitado conforme documentação**
✅ **Super Admin funcionando conforme design oficial**
✅ **Enterprise features respeitadas**

A plataforma agora roda conforme as **práticas oficiais do Chatwoot**, sem workarounds ou modificações que fujam das recomendações da documentação oficial.
