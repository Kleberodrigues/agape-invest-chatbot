# 🤝 Contribuindo para o Projeto

> **Guia completo para desenvolvedores que desejam contribuir com o chatbot Ágape Invest**

## 🎯 Visão Geral

Agradecemos seu interesse em contribuir! Este projeto demonstra a implementação de um chatbot conversacional robusto para qualificação de leads, e contribuições são bem-vindas para melhorar a funcionalidade, performance e experiência do usuário.

## 📋 Como Contribuir

### 🚀 Primeiros Passos

1. **Fork** este repositório
2. **Clone** seu fork localmente
3. **Configure** o ambiente de desenvolvimento
4. **Crie** uma branch para sua feature/fix
5. **Implemente** suas mudanças
6. **Teste** thoroughly
7. **Submita** um Pull Request

### 🏗️ Setup do Ambiente de Desenvolvimento

#### Pré-requisitos
```bash
# Verificar versões necessárias
node --version  # >= 16.0.0
npm --version   # >= 8.0.0
git --version   # >= 2.25.0
```

#### Instalação
```bash
# Clone do repositório
git clone https://github.com/Kleberodrigues/agape-invest-chatbot.git
cd agape-invest-chatbot

# Configurar ambiente
cp .env.example .env
# Edite .env com suas credenciais
```

#### Configuração do n8n Local
```bash
# Instalar n8n globalmente
npm install -g n8n

# Ou via Docker
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

## 🎨 Diretrizes de Desenvolvimento

### 📝 Padrões de Código

#### Estrutura de Arquivos
```
agape-invest-chatbot/
├── docs/                    # Documentação técnica
│   ├── API_REFERENCE.md
│   └── TECHNICAL_DECISIONS.md
├── workflows/               # Workflows do n8n
│   ├── chatbot-principal.json
│   └── workflow-alertas.json
├── scripts/                 # Scripts de utilidade
├── tests/                   # Testes automatizados
├── .github/                 # GitHub Actions
└── README.md
```

#### Naming Conventions

**Workflows:**
- Use kebab-case: `chatbot-principal`, `workflow-alertas`
- Inclua prefixo do projeto: `agape-`
- Seja descritivo: `agape-lead-qualification`

**Nodes:**
- Use PascalCase: `AI Agent`, `OpenAI Chat Model`
- Seja específico: `Redis Buffer - Message Queue`
- Inclua função: `Supabase - Create Lead`

**Variáveis:**
- Use camelCase: `phoneNumber`, `leadData`
- Prefixe contexto: `user_`, `lead_`, `system_`
- Seja explícito: `isQualifiedLead` vs. `qualified`

#### JSON Structure
```json
{
  "workflow_name": "descriptive-name",
  "version": "1.0.0",
  "description": "Brief description of workflow purpose",
  "nodes": [...],
  "connections": {...},
  "metadata": {
    "author": "contributor-name",
    "created": "2024-01-15",
    "tags": ["ai", "chatbot", "lead-qualification"]
  }
}
```

### 🧪 Testes

#### Tipos de Testes Necessários

**1. Unit Tests (Scripts/Functions)**
```javascript
// tests/utils/validation.test.js
describe('Input Validation', () => {
  test('should sanitize malicious input', () => {
    const maliciousInput = '<script>alert("hack")</script>';
    const sanitized = sanitizeInput(maliciousInput);
    expect(sanitized).not.toContain('<script>');
  });
  
  test('should validate phone format', () => {
    expect(validatePhone('5511999999999')).toBe(true);
    expect(validatePhone('invalid')).toBe(false);
  });
});
```

**2. Integration Tests (Workflows)**
```javascript
// tests/integration/chatbot-flow.test.js
describe('Chatbot Flow', () => {
  test('should complete qualification flow', async () => {
    // Simulate complete conversation
    const conversation = [
      'Olá',
      'João Silva',
      'Quero vender precatório',
      'Federal, INSS, 123.456.789-00'
    ];
    
    const result = await simulateConversation(conversation);
    expect(result.leadQualified).toBe(true);
    expect(result.dataComplete).toBe(true);
  });
});
```

**3. API Tests**
```javascript
// tests/api/webhooks.test.js
describe('Webhook Endpoints', () => {
  test('should process valid WhatsApp message', async () => {
    const payload = mockWhatsAppMessage();
    const response = await request(app)
      .post('/webhook/agape-invest')
      .send(payload)
      .expect(200);
      
    expect(response.body.processed).toBe(true);
  });
});
```

#### Executando Testes
```bash
# Todos os testes
npm test

# Testes específicos
npm test -- --grep "validation"

# Coverage report
npm run test:coverage
```

### 📊 Métricas de Qualidade

#### Code Quality Checks
```bash
# ESLint
npx eslint workflows/ scripts/

# Prettier (formatação)
npx prettier --check "**/*.{json,md,js}"

# JSON validation
node scripts/validate-workflows.js
```

#### Performance Benchmarks
```javascript
// tests/performance/response-time.test.js
describe('Performance', () => {
  test('response time should be under 3s', async () => {
    const startTime = Date.now();
    await processMessage('test message');
    const duration = Date.now() - startTime;
    
    expect(duration).toBeLessThan(3000);
  });
});
```

## 🐛 Reportando Bugs

### 🔍 Antes de Reportar

1. **Verifique** se já existe issue similar
2. **Teste** em ambiente limpo
3. **Colete** logs e evidências
4. **Reproduza** o problema consistentemente

### 📝 Template de Bug Report

```markdown
## 🐛 Bug Description
Brief, clear description of the issue.

## 🔄 Steps to Reproduce
1. Configure workflow with...
2. Send message "..."
3. Observe behavior...

## ✅ Expected Behavior
What should happen instead.

## 📱 Environment
- n8n version: X.X.X
- OpenAI model: gpt-4.1-mini
- Browser: Chrome 120
- OS: Ubuntu 20.04

## 📋 Additional Context
- Error logs
- Screenshots
- Related workflows
```

## ✨ Solicitando Features

### 💡 Template de Feature Request

```markdown
## 🚀 Feature Request
Clear title describing the feature.

## 📝 Description
Detailed explanation of the proposed feature.

## 🎯 Use Case
Why is this feature needed? What problem does it solve?

## 💭 Proposed Solution
How should this feature work?

## 🔄 Alternatives Considered
What other approaches did you consider?

## 📊 Impact
- Performance implications
- Breaking changes
- Migration requirements
```

## 🔧 Tipos de Contribuições

### 🎯 Prioridades Atuais

**Alta Prioridade:**
- 🔒 Melhorias de segurança
- ⚡ Otimizações de performance
- 🐛 Correção de bugs críticos
- 📱 Melhorias de UX

**Média Prioridade:**
- 🤖 Novos tipos de agentes IA
- 📊 Dashboards e analytics
- 🔌 Novas integrações
- 📚 Melhorias na documentação

**Baixa Prioridade:**
- 🎨 Melhorias visuais
- 🧹 Refactoring de código
- ⚙️ Configurações avançadas

### 🛠️ Áreas de Contribuição

#### 1. **Workflow Development**
- Novos fluxos conversacionais
- Otimização de fluxos existentes
- Tratamento de edge cases
- Integração com novas APIs

#### 2. **AI/ML Improvements**
- Prompt engineering
- Model fine-tuning
- Intent classification
- Sentiment analysis

#### 3. **Infrastructure**
- Performance optimization
- Scalability improvements
- Monitoring & alerting
- Security enhancements

#### 4. **Testing**
- Automated test suites
- Load testing
- Security testing
- User acceptance tests

#### 5. **Documentation**
- Technical documentation
- User guides
- API documentation
- Video tutorials

## 📐 Guidelines Técnicos

### 🎨 UI/UX Principles

**Conversational Design:**
- Mantenha tom consistente com persona "Téo"
- Use linguagem natural e acolhedora
- Divida mensagens longas em chunks
- Inclua confirmações e feedbacks

**Error Handling:**
- Sempre forneça fallbacks gracioosos
- Mensagens de erro user-friendly
- Retry logic automático
- Logging detalhado para debugging

### ⚡ Performance Guidelines

**Response Time:**
- Webhook processing: < 100ms
- AI response generation: < 3s
- Database operations: < 200ms
- Message delivery: < 500ms

**Resource Usage:**
- Memory: < 100MB por sessão ativa
- CPU: < 30% average usage
- Network: Minimize API calls redundantes
- Storage: Cleanup automático de dados antigos

### 🔒 Security Requirements

**Input Validation:**
```javascript
// Sempre validar e sanitizar inputs
const sanitizeInput = (input) => {
  return input
    .replace(/[<>]/g, '')
    .replace(/javascript:/gi, '')
    .substring(0, 1000)
    .trim();
};
```

**Data Protection:**
```javascript
// Criptografar dados sensíveis
const encryptSensitiveData = (data) => {
  // Implementação de criptografia
};
```

**Rate Limiting:**
```javascript
// Implementar rate limiting
const rateLimiter = {
  maxRequests: 10,
  windowMs: 60000, // 1 minute
  skipSuccessfulRequests: true
};
```

## 🔄 Processo de Pull Request

### 📝 PR Checklist

**Antes de submeter:**
- [ ] Código testado localmente
- [ ] Testes passando
- [ ] Documentação atualizada
- [ ] Sem breaking changes (ou documentado)
- [ ] Code review próprio realizado
- [ ] Commits limpos e organizados

### 📋 PR Template

```markdown
## 📝 Description
Brief description of changes made.

## 🎯 Type of Change
- [ ] Bug fix (non-breaking change)
- [ ] New feature (non-breaking change)
- [ ] Breaking change
- [ ] Documentation update

## 🧪 Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## 📊 Performance Impact
- [ ] No performance degradation
- [ ] Performance improvements documented
- [ ] Resource usage analyzed

## 🔒 Security Impact
- [ ] Security review completed
- [ ] Input validation added/updated
- [ ] No sensitive data exposed

## 📚 Documentation
- [ ] Code comments added
- [ ] Documentation updated
- [ ] API documentation updated (if applicable)
```

### 🔍 Review Process

**Automated Checks:**
1. GitHub Actions pipeline
2. Code quality analysis
3. Security scanning
4. Test execution

**Manual Review:**
1. Code architecture review
2. Business logic validation
3. Performance assessment
4. Security evaluation

### ✅ Merge Criteria

**Aprovação requerida quando:**
- ✅ Todos os checks automáticos passando
- ✅ Code review aprovado por maintainer
- ✅ Documentação adequada
- ✅ Testes cobrindo mudanças

## 👥 Comunidade

### 💬 Comunicação

- **GitHub Issues**: Bugs e feature requests
- **GitHub Discussions**: Perguntas gerais e ideias
- **Pull Requests**: Code review e discussões técnicas

### 🏆 Reconhecimento

Contribuidores serão reconhecidos:
- 📋 Lista de contributors no README
- 🏅 Badges por tipo de contribuição
- 📢 Menção em releases notes
- 🎖️ Hall of Fame para grandes contribuições

### 📜 Code of Conduct

Este projeto adere a um código de conduta baseado em:
- ✅ Respeito mútuo
- ✅ Colaboração construtiva
- ✅ Foco em melhorias técnicas
- ✅ Ambiente inclusivo e acolhedor

## 🎓 Recursos de Aprendizado

### 📚 Documentação Recomendada

- [n8n Documentation](https://docs.n8n.io/)
- [OpenAI API Reference](https://platform.openai.com/docs/api-reference)
- [Supabase Documentation](https://supabase.com/docs)
- [Evolution API Docs](https://evolution-api.com/docs)

### 🛠️ Ferramentas Úteis

- **n8n Desktop**: Para desenvolvimento local
- **Postman**: Para testar APIs
- **Redis CLI**: Para debug de cache
- **Supabase Studio**: Para gerenciar banco de dados

### 💡 Dicas para Contribuidores

1. **Comece pequeno**: Issues marcadas como "good first issue"
2. **Entenda o fluxo**: Teste o chatbot antes de modificar
3. **Documente mudanças**: Outros devs precisam entender
4. **Teste edge cases**: Conversas fora do padrão
5. **Performance first**: Sempre considere impacto na performance

---

**Obrigado por contribuir! 🚀 Sua ajuda faz a diferença para tornar este projeto ainda melhor.**