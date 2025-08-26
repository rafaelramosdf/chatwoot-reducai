# White Label Customization Guide - Legal Methods

## ✅ Modificações Permitidas (MIT License)

### 1. Branding e Visual
- **Logo**: Substitua arquivos em `app/assets/images/`
- **Cores**: Modifique `tailwind.config.js` e arquivos CSS
- **Nome do Produto**: Altere strings de interface
- **Favicon**: Substitua `public/favicon.ico`

### 2. Configurações de Produto
```ruby
# config/application.rb
config.application_name = "Sua Marca Chat"
config.product_name = "Sua Marca"
```

### 3. Personalização de Email Templates
- Modifique templates em `app/views/devise_mailer/`
- Personalize `app/views/conversation_mailer/`

### 4. Customização de Dashboard
- Modifique componentes Vue em `app/javascript/dashboard/`
- Altere layouts em `app/javascript/dashboard/layouts/`

### 5. Configurações de Domínio
```env
# .env
FRONTEND_URL=https://sua-marca.com
MAILER_SENDER_EMAIL="Sua Marca <noreply@sua-marca.com>"
```

## ❌ Modificações NÃO Permitidas

### 1. Funcionalidades Enterprise
- Não remover verificações de licença
- Não ativar features pagas sem licença
- Não modificar código em `enterprise/`

### 2. Limites e Restrições
- Manter limites de agentes/inboxes (se aplicável)
- Não burlar sistema de billing

## 🔧 Implementação Segura

### 1. Criar Tema Personalizado
```javascript
// app/javascript/dashboard/assets/scss/variables.scss
$primary-color: #your-brand-color;
$secondary-color: #your-secondary-color;
```

### 2. Override de Componentes
```javascript
// app/javascript/dashboard/components/widgets/Branding.vue
// Criar componente personalizado
```

### 3. Configuração de Multi-tenant
```ruby
# config/initializers/custom_branding.rb
class CustomBranding
  def self.logo_url
    ENV['CUSTOM_LOGO_URL'] || '/assets/logo.svg'
  end
  
  def self.product_name
    ENV['CUSTOM_PRODUCT_NAME'] || 'Chatwoot'
  end
end
```

## 📋 Checklist de White Label Legal

- [ ] Modificar apenas arquivos fora de `enterprise/`
- [ ] Personalizar logo e cores
- [ ] Configurar domínio próprio
- [ ] Customizar emails
- [ ] Alterar textos de interface
- [ ] Configurar SMTP próprio
- [ ] Manter créditos quando obrigatório
- [ ] Documentar modificações

## ⚠️ Considerações Importantes

1. **Performance**: Modificações excessivas podem impactar performance
2. **Atualizações**: Facilitar merge de atualizações futuras
3. **Suporte**: Manter compatibilidade com versões oficiais
4. **Legal**: Sempre respeitar licenças MIT e Enterprise

## 🔄 Upgrade Path

Para funcionalidades Enterprise legais:
1. Considere licença oficial Chatwoot
2. Desenvolva funcionalidades próprias compatíveis
3. Use sistema de plugins/extensões
4. Contribua com comunidade open source
