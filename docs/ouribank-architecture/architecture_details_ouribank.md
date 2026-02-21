# Arquitetura do Quadro Ouribank (Azure)

Este documento detalha os componentes da solução de arquitetura web de alta segurança e disponibilidade desenvolvida para o portal/quadro do Ouribank.

## 1. Segurança e Perímetro (Borda)
*   **Azure Front Door (AFD):** Ponto de entrada global (CDN). Atua com **Web Application Firewall (WAF)** para mitigar ataques comuns da OWASP (SQL Injection, XSS) e fornece proteção antinegação de serviço (DDoS) nativa. O Front Door encaminhará o tráfego exclusivamente para o frontend via Private Link.
*   **Microsoft Entra ID:** Serviço de identidade corporativa ("Identity Provider"). Controla todos os logins dos usuários via protocolos modernos (OIDC/SAML). Será configurado com **Conditional Access** para forçar MFA (Multi-Factor Authentication) na autenticação.

## 2. Camada de Apresentação (Frontend)
*   **Azure App Service:** Hospeda o "Quadro" (Web App). Isolado do tráfego público direto através de restrições na rede e integração de VNet. Somente IPs e serviços autorizados (Front Door via Private Endpoint) conseguem trafegar pacotes para este servidor web. **Nota Arquitetural:** Utilizaremos *Deployment Slots* para garantir que novos releases do quadro ocorram com *Zero-Downtime* (Blue/Green Deployment).

## 3. Integração e Processamento (APIs Serverless)
*   **Azure API Management (APIM):** O "porteiro" das APIs. Recebe todas as requisições de backend do Frontend. Ele aplica políticas limitadoras de taxa (Rate Limit/Throttling), valida tokens JWT em milissegundos, faz mock de rotas, e roteia para os nós corretos isolando serviços vitais de clientes.
*   **Azure Functions:** Computação sem servidor para processar a lógica de negócio do "Quadro" (ex: "Criar novo card", "Gerar relatórios de fechamento"). **Nota Arquitetural (Performance):** Será provisionado utilizando o **Premium Plan** para eliminar os tempos de resposta ruins ocasionados por *Cold Starts*, garantindo o SLA do mercado financeiro e permitindo integração nativa de saída em VNet (VNet Integration).

## 4. Camada de Persistência (Banco de Dados e Cofre)
*   **Azure SQL Database:** Banco de dados relacional PaaS da Microsoft. Ideal para dados transacionais ou cruciais exigidos pelo Bacen, que requerem consistência ACID impecável (contas, transações, status do quadro). **Nota Arquitetural (Segurança):** Será implementado o **Row-Level Security (RLS)** para adicionar uma camada forte de isolamento e garantir que os usuários apenas acessem as linhas do seu domínio de tenant lógico/setor.
*   **Azure Cosmos DB:** Banco NoSQL distribuído globalmente, com latência na casa de milissegundos. Ótimo para dados não estruturados gerados em altíssimo volume no painel (logs de auditoria avançada, estado dinâmico da tela do quadro de um usuário, documentos complexos anexados). A estruturação exigirá atenção inicial meticulosa na definição da sua **Partition Key** (Chave de Particionamento) para evitar sobrecargas de queries (*cross-partition queries*).
*   **Azure Key Vault:** Único repositório seguro para chaves criptográficas (Bring Your Own Key), strings de conexão de bancos, e certificados TSL/SSL. Nem mesmo as *Functions* sabem a senha do banco; os serviços adquirem o Token temporário do serviço para extrair os "segredos" em memória via *Managed Identity*.

## 5. Hub de Observabilidade (Governança Central)
*   **Azure Monitor (App Insights + Log Analytics):** Monitoramento distribuído inserido nativamente em todo o pipeline. Qualquer erro HTTP 500 gerado pela Function App e enviado para o frontend ou queries lentas no Azure SQL aparecerão visualmente como "Application Maps". Essencial para as equipes do NOC e SOC monitorarem o tempo de vida do portal 24/7.
