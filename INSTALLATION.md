# 🚀 Guia de Instalação e Configuração

> **Instruções completas para configurar o ambiente e deployar o chatbot da Ágape Invest**

## 📋 Pré-requisitos

### Serviços Necessários

- ✅ **n8n** (Self-hosted ou Cloud)
- ✅ **OpenAI Account** com créditos disponíveis
- ✅ **Supabase Project** configurado
- ✅ **Redis Instance** (local ou cloud)
- ✅ **Evolution API** configurada
- ✅ **Domínio/VPS** para webhooks (opcional para desenvolvimento)

### Conhecimentos Técnicos

- Básico de **JSON** e **APIs REST**
- Conceitos de **webhook** e **automação**
- **SQL** básico para configuração do banco
- Noções de **deployment** e **variáveis de ambiente**

## 🛠️ Configuração dos Serviços

### 1. Setup do Supabase

#### 1.1. Criar Projeto
```bash
# Acesse https://supabase.com
# Clique em "New Project"
# Escolha organização e nome do projeto
# Aguarde a criação (2-3 minutos)
```

#### 1.2. Configurar Tabela de Leads
```sql
-- Execute no SQL Editor do Supabase
CREATE TABLE "Agape Invest" (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    telefone VARCHAR(20) UNIQUE NOT NULL,
    status TEXT DEFAULT 'novo_lead',
    tipo_precatorio VARCHAR(50),
    ente_devedor TEXT,
    numero_processo VARCHAR(100),
    cpf VARCHAR(14),
    observacoes TEXT,
    data_criacao TIMESTAMP DEFAULT NOW(),
    data_atualizacao TIMESTAMP DEFAULT NOW(),
    origem VARCHAR(50) DEFAULT 'whatsapp_bot'
);

-- Criar índices para performance
CREATE INDEX idx_telefone ON "Agape Invest"(telefone);
CREATE INDEX idx_data_criacao ON "Agape Invest"(data_criacao);
CREATE INDEX idx_status ON "Agape Invest"(status);

-- Habilitar RLS (Row Level Security)
ALTER TABLE "Agape Invest" ENABLE ROW LEVEL SECURITY;

-- Política para permitir operações (ajustar conforme necessário)
CREATE POLICY "Enable all operations for service role" ON "Agape Invest"
    FOR ALL USING (true);
```

#### 1.3. Obter Credenciais
```bash
# No Dashboard do Supabase, vá em Settings > API
# Copie:
# - Project URL
# - anon/public key
# - service_role key (para operações administrativas)
```

### 2. Setup do OpenAI

#### 2.1. Criar Conta e API Key
```bash
# Acesse https://platform.openai.com
# Crie uma conta ou faça login
# Vá em API Keys
# Clique em "Create new secret key"
# Copie e guarde a chave (só aparece uma vez!)
```

#### 2.2. Configurar Billing
```bash
# Em Billing > Payment methods
# Adicione um método de pagamento
# Configure limites de uso conforme necessário
# Recomendado: $20/mês para começar
```

### 3. Setup do Redis

#### 3.1. Opção Local (Docker)
```bash
# Instalar Docker se não tiver
docker --version

# Executar Redis
docker run -d \
  --name redis-agape \
  -p 6379:6379 \
  redis:7-alpine

# Verificar se está funcionando
docker logs redis-agape
```

#### 3.2. Opção Cloud (Redis Labs)
```bash
# Acesse https://redis.com/cloud/
# Crie uma conta gratuita
# Configure um database
# Anote: hostname, port, password
```

#### 3.3. Teste de Conectividade
```bash
# Com redis-cli
redis-cli -h localhost -p 6379 ping
# Deve retornar: PONG

# Ou via Docker
docker exec redis-agape redis-cli ping
```

### 4. Setup Evolution API

#### 4.1. Deploy da Evolution API
```bash
# Opção 1: Docker Compose
git clone https://github.com/EvolutionAPI/evolution-api.git
cd evolution-api
docker-compose up -d

# Opção 2: VPS Manual
# Seguir documentação oficial: https://docs.evolution-api.com/
```

#### 4.2. Criar Instância do WhatsApp
```bash
# POST para criar instância
curl -X POST https://your-evolution-api.com/instance/create \
  -H "Content-Type: application/json" \
  -H "apikey: YOUR-API-KEY" \
  -d '{
    "instanceName": "AgapeInvest",
    "qrcode": true
  }'
```

#### 4.3. Conectar WhatsApp
```bash
# GET QR Code para conectar
curl -X GET https://your-evolution-api.com/instance/connect/AgapeInvest \
  -H "apikey: YOUR-API-KEY"

# Escaneie o QR Code com WhatsApp Business
# Aguarde confirmação de conexão
```

### 5. Setup do n8n

#### 5.1. Instalação (Docker)
```bash
# Criar diretório para dados
mkdir n8n-data

# Executar n8n
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v $(pwd)/n8n-data:/home/node/.n8n \
  -e WEBHOOK_TUNNEL_URL="https://your-domain.com/" \
  -e GENERIC_TIMEZONE="America/Sao_Paulo" \
  n8nio/n8n
```

#### 5.2. Configurar Credenciais

**OpenAI:**
```json
{
  "name": "OpenAI account",
  "type": "openAiApi",
  "data": {
    "apiKey": "sk-your-openai-api-key"
  }
}
```

**Supabase:**
```json
{
  "name": "Agape Invest",
  "type": "supabaseApi", 
  "data": {
    "host": "https://your-project.supabase.co",
    "serviceRole": "your-service-role-key"
  }
}
```

**Evolution API:**
```json
{
  "name": "Evolution account",
  "type": "evolutionApi",
  "data": {
    "baseURL": "https://your-evolution-api.com",
    "apiKey": "your-evolution-api-key"
  }
}
```

**Redis:**
```json
{
  "name": "Redis account",
  "type": "redis",
  "data": {
    "host": "localhost",
    "port": 6379,
    "database": 0
  }
}
```

## 📥 Import dos Workflows

### 1. Workflow Principal (Chatbot)

#### 1.1. Criar Novo Workflow
```bash
# No n8n, clique em "Add workflow"
# Cole o JSON do workflow principal
# Salve como "Agape Invest - Chatbot Principal"
```

#### 1.2. Configurar Webhook
```bash
# No nó Webhook, copie a URL gerada
# Exemplo: https://your-n8n.com/webhook/agape-invest
# Configure esta URL na Evolution API
```

#### 1.3. Testar Conexões
```bash
# Clique em "Test workflow"
# Verifique se todos os nós estão conectados
# Teste cada credencial individualmente
```

### 2. Workflow de Alertas

#### 2.1. Import e Configuração
```bash
# Importe o JSON do workflow de alertas
# Salve como "Agape-alerta"
# Configure o número da equipe comercial
```

#### 2.2. Teste de Integração
```bash
# Execute o workflow principal
# Verifique se o alerta é disparado
# Confirme recebimento no WhatsApp da equipe
```

## 🔧 Configuração Avançada

### 1. SSL/HTTPS (Obrigatório para Produção)

#### 1.1. Certificado SSL
```bash
# Opção 1: Let's Encrypt (gratuito)
sudo certbot --nginx -d your-domain.com

# Opção 2: Cloudflare (recomendado)
# Configure proxy através do Cloudflare
# Habilite SSL/TLS encryption mode: Full
```

#### 1.2. Configurar Nginx
```nginx
# /etc/nginx/sites-available/n8n
server {
    listen 80;
    server_name your-domain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your-domain.com;
    
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    
    location / {
        proxy_pass http://localhost:5678;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 2. Monitoramento e Logs

#### 2.1. Configurar Logs Estruturados
```javascript
// No n8n, adicionar variáveis de ambiente
LOG_LEVEL=info
LOG_OUTPUT=file
LOG_FILE=/var/log/n8n/n8n.log
```

#### 2.2. Health Check Endpoint
```bash
# Criar workflow de health check
# GET /webhook/health
# Retorna status de todos os serviços
```

### 3. Backup e Recuperação

#### 3.1. Backup do Supabase
```bash
# Configurar backup automático diário
# Dashboard > Settings > Database > Backups
# Habilitar: "Enable automatic backups"
```

#### 3.2. Backup dos Workflows
```bash
# Export workflows regularmente
# Via API ou interface do n8n
# Armazenar em repositório Git
```

## 🔒 Segurança e Compliance

### 1. Configurações de Segurança

#### 1.1. Firewall
```bash
# Permitir apenas portas necessárias
sudo ufw allow 80
sudo ufw allow 443
sudo ufw allow 22 # SSH
sudo ufw enable

# Bloquear acesso direto ao n8n
sudo ufw deny 5678
```

#### 1.2. Rate Limiting
```nginx
# Adicionar ao Nginx
limit_req_zone $binary_remote_addr zone=api:10m rate=10r/m;

location /webhook/ {
    limit_req zone=api burst=5;
    proxy_pass http://localhost:5678;
}
```

### 2. LGPD Compliance

#### 2.1. Política de Retenção
```sql
-- Criar job para limpeza automática
CREATE OR REPLACE FUNCTION cleanup_old_leads() 
RETURNS void AS $$
BEGIN
    DELETE FROM "Agape Invest" 
    WHERE data_criacao < NOW() - INTERVAL '2 years';
END;
$$ LANGUAGE plpgsql;

-- Agendar execução mensal
SELECT cron.schedule('cleanup-leads', '0 0 1 * *', 'SELECT cleanup_old_leads();');
```

#### 2.2. Consentimento
```javascript
// Adicionar ao prompt do chatbot
const consentimentoLGPD = `
Ao continuar, você consente com:
- Coleta de dados para qualificação
- Uso exclusivo para contato comercial  
- Política de privacidade: link.com/privacy
`;
```

## 🧪 Testes e Validação

### 1. Testes Funcionais

#### 1.1. Teste de Fluxo Completo
```bash
# Envie uma mensagem de teste
# Verifique cada etapa do fluxo
# Confirme registro no Supabase
# Verifique alerta para equipe
```

#### 1.2. Teste de Cenários Edge
```bash
# Mensagens muito longas
# Caracteres especiais
# Mensagens fora do horário
# Volume alto de mensagens
```

### 2. Monitoramento

#### 2.1. Métricas Essenciais
```bash
# Response time médio
# Taxa de sucesso de workflows
# Uso de recursos (CPU, RAM)
# Custos da OpenAI
# Volume de mensagens
```

#### 2.2. Alertas Críticos
```bash
# Falha na conexão com OpenAI
# Erro no banco de dados
# Evolution API desconectada
# Alto volume de erros
```

## 🚀 Deploy em Produção

### 1. Checklist Pré-Deploy

- [ ] SSL configurado e testado
- [ ] Backups automáticos habilitados
- [ ] Monitoramento configurado
- [ ] Rate limiting implementado
- [ ] Logs estruturados
- [ ] Credenciais seguras
- [ ] Testes E2E aprovados

### 2. Processo de Deploy

#### 2.1. Deploy Gradual
```bash
# 1. Deploy em ambiente de staging
# 2. Testes de aceitação
# 3. Deploy em horário de baixo tráfego
# 4. Monitoramento intensivo por 24h
# 5. Rollback plan ready
```

#### 2.2. Post-Deploy
```bash
# Verificar todos os health checks
# Monitorar logs por anomalias
# Testar fluxo completo
# Confirmar integrações
```

## 🔧 Troubleshooting

### Problemas Comuns

#### OpenAI API Errors
```bash
# Verificar créditos disponíveis
# Validar API key
# Checar rate limits
# Monitorar status da API
```

#### Evolution API Desconectada
```bash
# Verificar conexão do WhatsApp
# Reiniciar instância se necessário
# Validar webhook URL
# Checar logs da Evolution API
```

#### Redis Connection Issues
```bash
# Verificar se está rodando
docker ps | grep redis

# Testar conectividade
redis-cli ping

# Reiniciar se necessário
docker restart redis-agape
```

---

## 🎯 Próximos Passos

Após a instalação completa:

1. **Configure monitoramento** proativo
2. **Implemente backup** strategy
3. **Otimize performance** conforme uso
4. **Scale** conforme crescimento
5. **Mantenha** documentação atualizada

Para suporte técnico, consulte a documentação específica de cada serviço ou abra uma issue no repositório.