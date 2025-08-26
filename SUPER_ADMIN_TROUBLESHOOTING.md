# Super Admin Performance Troubleshooting

## 🐛 Problemas Comuns e Soluções

### 1. **Lentidão no Painel Super Admin (31+ segundos)**

#### Sintomas:
- Carregamento muito lento de páginas como `/super_admin/users`
- Timeout de renderização do layout
- Console do browser com erros

#### Soluções Implementadas:

1. **Cache para Features Helper**:
```ruby
# app/helpers/super_admin/features_helper.rb
@available_features ||= Rails.cache.fetch('super_admin_features', expires_in: 1.hour)
```

2. **Cache para ChatwootHub**:
```ruby
# lib/chatwoot_hub.rb
Rails.cache.fetch('chatwoot_pricing_plan', expires_in: 30.minutes)
```

3. **MiniProfiler Desabilitado**:
```env
# .env
DISABLE_MINI_PROFILER=true
```

### 2. **Erros JavaScript no Console**

#### Sintomas:
- `NameError: uninitialized constant Rack::File`
- `No route matches [GET] "/packs/js/sdk.js"`
- `No route matches [GET] "/.well-known/appspecific/com.chrome.devtools.json"`

#### Soluções:

1. **MiniProfiler**: Desabilitado via `.env`
2. **Assets Missing**: Rebuildar assets se necessário
3. **DevTools**: Erro normal do Chrome DevTools

### 3. **Problemas de Enterprise Features**

#### Sintomas:
- Features Enterprise aparecendo como enabled/disabled incorretamente
- Carregamento lento devido verificações de licença

#### Soluções:

1. **Optimized features.yml**:
```yaml
enabled: <%= Rails.env.development? ? false : (ChatwootHub.pricing_plan != 'community') rescue false %>
```

2. **Safe fallbacks** nos helpers

## 🚀 Verificações de Performance

### 1. **Teste o Super Admin**
```bash
# Acesse: http://localhost:3000/super_admin
# Login com credenciais de super admin
# Verifique tempo de carregamento < 5 segundos
```

### 2. **Monitore Logs**
```bash
docker compose logs rails --tail=50 | grep "super_admin"
```

### 3. **Verifique Cache**
```bash
# No Rails console
docker compose exec rails bundle exec rails console
> Rails.cache.stats
> Rails.cache.read('super_admin_features')
```

## ⚡ Otimizações Adicionais

### 1. **Database Queries**
- Queries N+1 reduzidas com includes
- Cache de configurações InstallationConfig

### 2. **Asset Loading**
- Vite otimizado para development
- MiniProfiler desabilitado

### 3. **Error Handling**
- Fallbacks seguros para features Enterprise
- Logs de erro melhorados

## 🔧 Configurações de Desenvolvimento

### Variáveis de Ambiente (.env):
```env
# Performance
DISABLE_MINI_PROFILER=true
RAILS_LOG_LEVEL=info

# Cache
RAILS_CACHE_STORE=memory_store

# Super Admin
SUPER_ADMIN_EMAIL=admin@localhost.dev
SUPER_ADMIN_PASSWORD=password123
```

### Configurações de Cache:
```ruby
# config/environments/development.rb
config.cache_store = :memory_store
config.action_controller.perform_caching = true
```

## 📊 Métricas de Performance

### Antes das Otimizações:
- Super Admin Users: 31,267ms
- Features loading: 16,818ms
- Settings menu: múltiplas queries

### Depois das Otimizações:
- Super Admin Users: < 5,000ms (esperado)
- Features loading: < 100ms (cached)
- Settings menu: single cached query

## 🛠️ Comandos Úteis

```bash
# Limpar cache Rails
docker compose exec rails bundle exec rails dev:cache

# Restart para aplicar mudanças
docker compose restart rails

# Verificar status do cache
docker compose exec rails bundle exec rails runner "puts Rails.cache.stats"

# Monitor performance
docker compose logs rails -f | grep "Completed"
```

## ⚠️ Notas Importantes

1. **Development vs Production**: Algumas otimizações são específicas para development
2. **Enterprise Features**: Mantidas compatíveis mas com fallbacks seguros
3. **Cache**: Cache em memória pode ser perdido ao reiniciar containers
4. **Logs**: Monitore logs para identificar novos gargalos
