# 📡 API Reference e Integrações

> **Documentação completa das APIs, webhooks, endpoints e integrações utilizadas no chatbot Ágape Invest.**

## 🔗 Visão Geral das Integrações

```mermaid
graph TB
    WA[WhatsApp] --> EA[Evolution API]
    EA --> WH[n8n Webhook]
    WH --> AI[OpenAI GPT-4.1]
    WH --> SB[Supabase]
    WH --> RD[Redis]
    
    AI --> LLM[Language Model]
    SB --> DB[(Database)]
    RD --> CH[(Cache)]
    
    N8N[n8n Workflows] --> EA
    N8N --> SB
    N8N --> RD
    
    style WA fill:#25d366
    style EA fill:#ff6b6b
    style AI fill:#412991
    style SB fill:#3ecf8e
    style RD fill:#dc382d
```

## 🔄 Evolution API Integration

### Base Configuration

```javascript
const evolutionConfig = {
  baseURL: "https://your-evolution-api.com",
  apiKey: "your-api-key",
  instanceName: "AgapeInvest",
  webhookUrl: "https://your-n8n.com/webhook/agape-invest"
};
```

### 1. Instance Management

#### Create Instance
```http
POST /instance/create
Content-Type: application/json
apikey: your-api-key

{
  "instanceName": "AgapeInvest",
  "qrcode": true,
  "number": "5511999999999",
  "integration": "WHATSAPP-BAILEYS"
}
```

**Response:**
```json
{
  "instance": {
    "instanceName": "AgapeInvest",
    "status": "created",
    "serverUrl": "https://evolution-api.com",
    "apikey": "your-api-key"
  }
}
```

#### Connect WhatsApp
```http
GET /instance/connect/AgapeInvest
apikey: your-api-key
```

**Response:**
```json
{
  "qrcode": {
    "base64": "data:image/png;base64,iVBORw0KGgoAAAANSU...",
    "code": "1@abc123def456..."
  }
}
```

#### Instance Status
```http
GET /instance/connectionState/AgapeInvest
apikey: your-api-key
```

**Response:**
```json
{
  "instance": {
    "instanceName": "AgapeInvest",
    "state": "open"
  }
}
```

### 2. Message Operations

#### Send Text Message
```http
POST /message/sendText/AgapeInvest
Content-Type: application/json
apikey: your-api-key

{
  "number": "5511999999999",
  "text": "Olá! Como posso ajudar você hoje?"
}
```

**Response:**
```json
{
  "message": {
    "key": {
      "remoteJid": "5511999999999@s.whatsapp.net",
      "fromMe": true,
      "id": "3EB0C767D82B2B4B5C"
    },
    "status": "PENDING"
  }
}
```

#### Send Media Message
```http
POST /message/sendMedia/AgapeInvest
Content-Type: application/json
apikey: your-api-key

{
  "number": "5511999999999",
  "mediatype": "image",
  "media": "https://example.com/image.jpg",
  "caption": "Imagem explicativa"
}
```

### 3. Webhook Configuration

#### Set Webhook
```http
POST /webhook/set/AgapeInvest
Content-Type: application/json
apikey: your-api-key

{
  "url": "https://your-n8n.com/webhook/agape-invest",
  "events": [
    "MESSAGES_UPSERT",
    "CONNECTION_UPDATE"
  ],
  "webhook_by_events": true
}
```

#### Webhook Payload Structure

**Message Received:**
```json
{
  "event": "messages.upsert",
  "instance": "AgapeInvest",
  "data": {
    "key": {
      "remoteJid": "5511999999999@s.whatsapp.net",
      "fromMe": false,
      "id": "3EB0C767D82B2B4B5C"
    },
    "pushName": "João Silva",
    "message": {
      "conversation": "Olá, gostaria de informações sobre precatórios"
    },
    "messageTimestamp": 1641234567
  },
  "date_time": "2024-01-15T10:30:00Z"
}
```

## 🤖 OpenAI API Integration

### Configuration
```javascript
const openaiConfig = {
  apiKey: "sk-your-openai-api-key",
  model: "gpt-4.1-mini-2025-04-14",
  temperature: 0.7,
  maxTokens: 1000
};
```

### 1. Chat Completions

#### Request Structure
```http
POST https://api.openai.com/v1/chat/completions
Content-Type: application/json
Authorization: Bearer sk-your-api-key

{
  "model": "gpt-4.1-mini-2025-04-14",
  "messages": [
    {
      "role": "system",
      "content": "Você é o Téo, atendente da Ágape Invest..."
    },
    {
      "role": "user", 
      "content": "Gostaria de vender meu precatório"
    }
  ],
  "temperature": 0.7,
  "max_tokens": 1000,
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "register_lead",
        "description": "Register qualified lead in database",
        "parameters": {
          "type": "object",
          "properties": {
            "nome": {"type": "string"},
            "telefone": {"type": "string"}
          }
        }
      }
    }
  ]
}
```

#### Response Structure
```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "model": "gpt-4.1-mini-2025-04-14",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Perfeito! Para prosseguir, preciso de algumas informações sobre seu precatório...",
        "tool_calls": [
          {
            "id": "call_abc123",
            "type": "function",
            "function": {
              "name": "register_lead",
              "arguments": "{\"nome\":\"João Silva\",\"telefone\":\"5511999999999\"}"
            }
          }
        ]
      }
    }
  ],
  "usage": {
    "prompt_tokens": 156,
    "completion_tokens": 89,
    "total_tokens": 245
  }
}
```

### 2. Function Calling

#### Tools Available

**Think Tool:**
```json
{
  "type": "function",
  "function": {
    "name": "think",
    "description": "Use when you need to reason about the conversation",
    "parameters": {
      "type": "object",
      "properties": {
        "thought": {"type": "string"}
      }
    }
  }
}
```

**Register Lead Tool:**
```json
{
  "type": "function", 
  "function": {
    "name": "register_lead",
    "description": "Register qualified lead in Supabase",
    "parameters": {
      "type": "object",
      "properties": {
        "nome": {"type": "string"},
        "telefone": {"type": "string"},
        "tipo_precatorio": {"type": "string"},
        "ente_devedor": {"type": "string"}
      }
    }
  }
}
```

**Alert Team Tool:**
```json
{
  "type": "function",
  "function": {
    "name": "alert_team",
    "description": "Send alert to commercial team",
    "parameters": {
      "type": "object", 
      "properties": {
        "nome": {"type": "string"},
        "telefone": {"type": "string"}
      }
    }
  }
}
```

## 🗄️ Supabase API Integration

### Configuration
```javascript
const supabaseConfig = {
  url: "https://your-project.supabase.co",
  anonKey: "your-anon-key",
  serviceRoleKey: "your-service-role-key"
};
```

### 1. Table Schema

```sql
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
```

### 2. API Operations

#### Create Lead
```http
POST https://your-project.supabase.co/rest/v1/Agape%20Invest
Content-Type: application/json
apikey: your-service-role-key
Authorization: Bearer your-service-role-key

{
  "nome": "João Silva",
  "telefone": "5511999999999", 
  "status": "novo_lead",
  "tipo_precatorio": "Federal",
  "ente_devedor": "INSS",
  "numero_processo": "1234567-89.2023.4.03.6104"
}
```

**Response:**
```json
[
  {
    "id": 123,
    "nome": "João Silva",
    "telefone": "5511999999999",
    "status": "novo_lead", 
    "data_criacao": "2024-01-15T10:30:00.000Z"
  }
]
```

#### Get Lead by Phone
```http
GET https://your-project.supabase.co/rest/v1/Agape%20Invest?telefone=eq.5511999999999
apikey: your-service-role-key
Authorization: Bearer your-service-role-key
```

#### Update Lead Status
```http
PATCH https://your-project.supabase.co/rest/v1/Agape%20Invest?id=eq.123
Content-Type: application/json
apikey: your-service-role-key
Authorization: Bearer your-service-role-key

{
  "status": "qualificado",
  "data_atualizacao": "2024-01-15T10:35:00.000Z"
}
```

### 3. Real-time Subscriptions

```javascript
const subscription = supabase
  .channel('leads')
  .on('postgres_changes', 
    { 
      event: 'INSERT', 
      schema: 'public', 
      table: 'Agape Invest' 
    },
    (payload) => {
      console.log('Novo lead:', payload.new);
      notifyTeam(payload.new);
    }
  )
  .subscribe();
```

## 🔴 Redis API Integration

### Configuration
```javascript
const redisConfig = {
  host: "localhost",
  port: 6379,
  password: "your-redis-password",
  db: 0
};
```

### 1. Message Buffer Operations

#### Push Message to Buffer
```javascript
// LPUSH command
await redis.lpush(`${phoneNumber}_buffer`, messageText);
// Sets TTL of 1 hour
await redis.expire(`${phoneNumber}_buffer`, 3600);
```

#### Get Messages from Buffer
```javascript
// LRANGE command - get all messages
const messages = await redis.lrange(`${phoneNumber}_buffer`, 0, -1);
// Returns: ["message3", "message2", "message1"] (LIFO order)
```

#### Clear Buffer
```javascript
// DEL command
await redis.del(`${phoneNumber}_buffer`);
```

### 2. Session Management

#### Set User State
```javascript
await redis.setex(
  `user:${phoneNumber}:state`, 
  1800, // 30 minutes TTL
  'coletando_dados'
);
```

#### Get User State  
```javascript
const state = await redis.get(`user:${phoneNumber}:state`);
// Returns: 'coletando_dados' or null if expired
```

### 3. Rate Limiting

#### Increment Rate Counter
```javascript
const key = `rate:${phoneNumber}`;
const current = await redis.incr(key);

if (current === 1) {
  await redis.expire(key, 60); // 1 minute window
}

if (current > 10) {
  throw new Error('Rate limit exceeded');
}
```

## 🔗 n8n Webhook Endpoints

### 1. Main Chatbot Webhook

**Endpoint:** `POST /webhook/agape-invest`

**Request Headers:**
```http
Content-Type: application/json
User-Agent: Evolution-API/1.0
```

**Request Body:**
```json
{
  "event": "messages.upsert",
  "instance": "AgapeInvest", 
  "data": {
    "key": {
      "remoteJid": "5511999999999@s.whatsapp.net",
      "fromMe": false,
      "id": "message_id"
    },
    "pushName": "João Silva",
    "message": {
      "conversation": "Gostaria de vender meu precatório"
    },
    "messageTimestamp": 1641234567
  },
  "date_time": "2024-01-15T10:30:00Z"
}
```

**Response:**
```json
{
  "status": "received",
  "processed": true,
  "execution_id": "exec_abc123"
}
```

### 2. Health Check Endpoint

**Endpoint:** `GET /webhook/health`

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "services": {
    "openai": "operational",
    "supabase": "operational", 
    "redis": "operational",
    "evolution_api": "operational"
  },
  "metrics": {
    "uptime": "99.7%",
    "avg_response_time": "1.8s",
    "total_conversations": 1247,
    "active_sessions": 12
  }
}
```

## 🔧 Error Handling

### 1. Common Error Responses

#### Rate Limit Exceeded
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests from this phone number",
    "retry_after": 60,
    "phone": "5511999999999"
  }
}
```

#### OpenAI API Error
```json
{
  "error": {
    "code": "OPENAI_API_ERROR",
    "message": "Insufficient quota", 
    "fallback_used": true,
    "execution_id": "exec_abc123"
  }
}
```

#### Database Connection Error
```json
{
  "error": {
    "code": "DATABASE_ERROR",
    "message": "Connection timeout to Supabase",
    "retry_scheduled": true,
    "execution_id": "exec_abc123"
  }
}
```

### 2. Retry Logic

```javascript
const retryConfig = {
  maxRetries: 3,
  backoffStrategy: "exponential",
  baseDelay: 1000, // 1 second
  maxDelay: 10000  // 10 seconds
};

async function retryOperation(operation, config = retryConfig) {
  for (let attempt = 1; attempt <= config.maxRetries; attempt++) {
    try {
      return await operation();
    } catch (error) {
      if (attempt === config.maxRetries) throw error;
      
      const delay = Math.min(
        config.baseDelay * Math.pow(2, attempt - 1),
        config.maxDelay
      );
      
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}
```

## 📊 API Monitoring

### 1. Key Metrics

```javascript
const apiMetrics = {
  webhook_calls: {
    total: 15420,
    success: 15210,
    errors: 210,
    success_rate: "98.6%"
  },
  openai_calls: {
    total: 8934,
    avg_tokens: 245,
    avg_latency: "1.8s",
    cost: "$26.80"
  },
  supabase_operations: {
    reads: 2430,
    writes: 1245,
    avg_latency: "180ms"
  },
  redis_operations: {
    gets: 18650,
    sets: 12340,
    avg_latency: "12ms"
  }
};
```

### 2. Alerting Thresholds

```yaml
alerts:
  webhook_error_rate:
    threshold: "> 5%"
    action: "notify_admin"
  
  openai_latency:
    threshold: "> 5s"
    action: "switch_to_fallback"
  
  database_errors:
    threshold: "> 3 consecutive"
    action: "activate_circuit_breaker"
  
  redis_memory:
    threshold: "> 80%"
    action: "cleanup_old_sessions"
```

## 🔒 Security Considerations

### 1. Authentication

```javascript
// Validate Evolution API webhook
const validateWebhook = (request) => {
  const signature = request.headers['x-evolution-signature'];
  const payload = JSON.stringify(request.body);
  const expectedSignature = crypto
    .createHmac('sha256', process.env.EVOLUTION_WEBHOOK_SECRET)
    .update(payload)
    .digest('hex');
  
  return signature === expectedSignature;
};
```

### 2. Input Sanitization

```javascript
const sanitizeInput = (input) => {
  return input
    .replace(/[<>]/g, '') // Remove HTML
    .replace(/javascript:/gi, '') // Remove JS
    .replace(/data:/gi, '') // Remove data URLs
    .substring(0, 1000) // Limit length
    .trim();
};
```

### 3. Data Encryption

```javascript
// Encrypt sensitive data before storing
const encrypt = (text) => {
  const cipher = crypto.createCipher('aes-256-ctr', process.env.ENCRYPTION_KEY);
  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  return encrypted;
};
```

---

Esta documentação de API fornece todas as informações necessárias para integrar, manter e expandir o sistema, demonstrando proficiência técnica em múltiplas tecnologias e APIs.