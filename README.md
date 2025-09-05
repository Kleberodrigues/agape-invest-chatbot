# 🤖 Ágape Invest - Chatbot de Qualificação de Leads

> **Sistema de conversação inteligente para qualificação automática de leads interessados em antecipação de precatórios, desenvolvido com n8n, OpenAI e integrado ao WhatsApp.**

[![n8n](https://img.shields.io/badge/n8n-Latest-ff6d5a?style=flat-square&logo=n8n)](https://n8n.io/)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4.1-412991?style=flat-square&logo=openai)](https://openai.com/)
[![Redis](https://img.shields.io/badge/Redis-7.0-dc382d?style=flat-square&logo=redis)](https://redis.io/)
[![Supabase](https://img.shields.io/badge/Supabase-Latest-3ecf8e?style=flat-square&logo=supabase)](https://supabase.com/)

## 📋 Visão Geral

Este projeto implementa um chatbot conversacional avançado que opera via WhatsApp para qualificar leads interessados em **antecipação de precatórios**. O sistema utiliza inteligência artificial para conduzir conversas naturais, coletando informações essenciais e registrando automaticamente leads qualificados no CRM.

### 🎯 Objetivos do Projeto
- **Automatizar** a qualificação inicial de leads
- **Reduzir** o tempo de resposta para clientes potenciais
- **Aumentar** a eficiência da equipe de vendas
- **Padronizar** o processo de coleta de informações

### 💼 Contexto de Negócio
A **Ágape Invest** é uma empresa especializada na compra de precatórios (títulos de dívida pública), oferecendo antecipação de valores para pessoas físicas e jurídicas que possuem esses títulos.

## ✨ Funcionalidades Principais

### 🔄 Fluxo de Conversação Inteligente
- **Saudação personalizada** com identificação automática do usuário
- **Qualificação de intenção** (venda vs. informações)
- **Coleta estruturada** de dados do precatório:
  - Tipo (Federal, Estadual, Municipal)
  - Ente devedor
  - Número do processo ou CPF

### 🧠 Inteligência Artificial
- **Persona conversacional** como "Téo", atendente da empresa
- **Contextualização** com data e informações da empresa
- **Memória de conversação** para manter contexto entre mensagens
- **Validação inteligente** de dados coletados

### 📊 Gestão de Dados
- **Registro automático** de leads no Supabase
- **Sistema de alertas** para equipe comercial
- **Controle de duplicatas** e histórico de conversas
- **Status tracking** do lead no pipeline

### 🚀 Performance e Confiabilidade
- **Sistema de buffer** Redis para controle de fluxo de mensagens
- **Processamento em lote** para otimização de recursos
- **Tratamento de erros** com mensagens apropriadas
- **Prevenção de loops** e mensagens duplicadas

## 🏗️ Arquitetura Técnica

### 📐 Visão Geral da Arquitetura

```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐    ┌──────────────┐
│   WhatsApp  │───▶│ Evolution API │───▶│   n8n       │───▶│   OpenAI     │
│   Cliente   │    │   Webhook     │    │  Workflows  │    │   GPT-4.1    │
└─────────────┘    └──────────────┘    └─────────────┘    └──────────────┘
                                              │
                                              ▼
                   ┌─────────────┐    ┌──────────────┐    ┌──────────────┐
                   │   Supabase  │◀───│    Redis     │───▶│  Validation  │
                   │  Database   │    │    Buffer    │    │   & Logic    │
                   └─────────────┘    └──────────────┘    └──────────────┘
```

### 🛠️ Stack Tecnológico

| Tecnologia | Função | Versão |
|------------|--------|---------|
| **n8n** | Orquestração de workflows | Latest |
| **OpenAI GPT-4.1 Mini** | Processamento de linguagem natural | 4.1-mini-2025 |
| **Evolution API** | Integração WhatsApp | Latest |
| **Supabase** | Banco de dados e CRM | Latest |
| **Redis** | Cache e controle de fluxo | 7.0+ |

### 📦 Componentes do Sistema

#### 1. **Workflow Principal - Chatbot**
- **Entrada**: Webhook da Evolution API
- **Processamento**: AI Agent com OpenAI
- **Saída**: Mensagens estruturadas para WhatsApp

#### 2. **Workflow de Alertas**
- **Função**: Notificar equipe sobre novos leads
- **Trigger**: Chamada do workflow principal
- **Destino**: Número da equipe comercial

#### 3. **Sistema de Memória**
- **Buffer Window Memory** para contexto de conversação
- **Session management** por telefone do cliente
- **Persistência** entre interações

## 🚀 Instalação e Configuração

### Pré-requisitos

- **n8n** instalado e configurado
- **Conta OpenAI** com acesso à API
- **Instância Evolution API** configurada
- **Projeto Supabase** com tabela de leads
- **Servidor Redis** ativo

### Variáveis de Ambiente

```env
# OpenAI Configuration
OPENAI_API_KEY=sk-your-openai-api-key

# Evolution API Configuration
EVOLUTION_API_URL=https://your-evolution-api-url
EVOLUTION_API_KEY=your-evolution-api-key

# Supabase Configuration
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_ANON_KEY=your-supabase-anon-key

# Redis Configuration
REDIS_URL=redis://localhost:6379
```

### Configuração da Tabela Supabase

```sql
CREATE TABLE "Agape Invest" (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(255),
    telefone VARCHAR(20),
    status TEXT,
    data_hora TIMESTAMP DEFAULT NOW()
);
```

## 📱 Como Usar

### Para Clientes
1. **Inicie** uma conversa no WhatsApp
2. **Siga** as instruções do chatbot "Téo"
3. **Forneça** as informações solicitadas sobre seu precatório
4. **Aguarde** o contato da equipe especializada

### Para Administradores
1. **Monitore** leads no painel Supabase
2. **Acompanhe** alertas no WhatsApp da equipe
3. **Analise** métricas de conversão
4. **Ajuste** prompts conforme necessário

## 📊 Métricas e Monitoramento

### KPIs Principais
- **Taxa de conversão** de visitantes para leads qualificados
- **Tempo médio** de qualificação por lead
- **Precisão** na coleta de dados
- **Satisfação** do cliente com o atendimento

### Logs e Debugging
- **Redis logs** para controle de fluxo
- **n8n execution logs** para debugging
- **Supabase logs** para auditoria de dados
- **OpenAI usage metrics** para controle de custos

## 🔒 Segurança e Compliance

### Proteção de Dados
- **Criptografia** de dados em trânsito
- **Validação** de entrada para prevenir ataques
- **Controle de acesso** baseado em credenciais
- **LGPD compliance** no tratamento de dados pessoais

### Controles Implementados
- **Rate limiting** para prevenir spam
- **Validação** de números de telefone
- **Sanitização** de inputs
- **Logs de auditoria** completos

## 🎯 Diferenciais Técnicos

### 🧩 Arquitetura Modular
- **Separação clara** de responsabilidades
- **Workflows independentes** e reutilizáveis
- **Integrações desacopladas** via APIs

### ⚡ Performance Otimizada
- **Processamento assíncrono** de mensagens
- **Cache inteligente** com Redis
- **Batching** para reduzir latência

### 🤖 IA Contextual
- **Persona bem definida** para conversas naturais
- **Fluxo estruturado** mas flexível
- **Validação inteligente** de dados

### 📈 Escalabilidade
- **Arquitetura cloud-native**
- **Auto-scaling** de componentes
- **Monitoramento proativo**

## 🚀 Próximos Passos

### Melhorias Planejadas
- [ ] **Dashboard analytics** em tempo real
- [ ] **Integração CRM** mais robusta
- [ ] **Multi-channel** (Telegram, Instagram)
- [ ] **A/B testing** de prompts
- [ ] **Machine Learning** para scoring de leads

### Otimizações Técnicas
- [ ] **Webhook resilience** com retry logic
- [ ] **Database optimization** com índices
- [ ] **API caching** strategy
- [ ] **Monitoring alerts** automatizados

## 👨‍💻 Sobre o Desenvolvedor

Este projeto foi desenvolvido como uma solução completa de automação conversacional, demonstrando competências em:

- **Integração de APIs** complexas
- **Arquitetura de sistemas** distribuídos
- **Inteligência Artificial** aplicada
- **Gestão de estado** e persistência
- **Performance optimization**
- **Segurança e compliance**

## 📞 Contato e Suporte

Para dúvidas técnicas ou propostas de colaboração, entre em contato através do GitHub.

---

**Desenvolvido com ❤️ para automatizar e escalar operações de negócio através da tecnologia.**