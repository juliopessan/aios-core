# Automação de Provisionamento de Projetos no Azure DevOps

Este documento detalha a implementação de um fluxo automatizado utilizando **Microsoft Power Automate** para a criação padronizada de projetos no **Azure DevOps**. Esta solução visa reduzir o erro humano, garantir a aplicação de processos de governança e acelerar o onboarding de novas iniciativas.

## 1. Visão Geral

A automação consiste em um fluxo (Flow) que captura uma solicitação (via formulário ou integração), valida as informações e utiliza a **REST API do Azure DevOps** para provisionar o projeto com configurações pré-definidas (Process Template, Repositórios, Permissões).

### Benefícios
* **Padronização:** Todos os projetos seguem o mesmo modelo de processo (Scrum, Agile, etc).
* **Agilidade:** Provisionamento imediato após aprovação.
* **Governança:** Controle centralizado de quem pode solicitar a criação de projetos.

---

## 2. Arquitetura da Solução

O fluxo segue o padrão abaixo:

1. **Gatilho (Trigger):** Entrada de dados via Microsoft Forms, SharePoint List ou requisição HTTP.
2. **Camada de Aprovação (Opcional):** Fluxo de aprovação nativo do Power Automate para gestores.
3. **Processamento:** Extração e sanitização dos nomes (remover espaços, caracteres especiais).
4. **Chamada de API:** Requisição `POST` para o endpoint de projetos do Azure DevOps.
5. **Callback/Monitoramento:** Verificação do status da operação assíncrona.
6. **Notificação:** Envio de e-mail ou mensagem no Teams com o link do novo projeto.

---

## 3. Pré-requisitos e Autenticação

Para que o Power Automate se comunique com o Azure DevOps, recomendamos o uso de um **Personal Access Token (PAT)** ou uma **Service Principal**.

### Criando um PAT (Recomendado para PoC/Interno)
1. No Azure DevOps, acesse **User Settings** > **Personal Access Tokens**.
2. Clique em **New Token**.
3. Selecione o escopo **Project and Team (Read & Write)**.
4. Copie o token (ele não será exibido novamente).

### Autenticação no Power Automate
No componente **HTTP**, utilize a autenticação `Basic`.
* **Username:** Pode ser deixado em branco ou qualquer valor.
* **Password:** O seu **PAT**.

---

## 4. Passo a Passo da Implementação Técnica

### Componente HTTP (Ação Principal)
Configure a ação HTTP no Power Automate com os seguintes parâmetros:

* **Método:** `POST`
* **URI:** `https://dev.azure.com/{sua_organizacao}/_apis/projects?api-version=7.1-preview.4`
* **Headers:**
    * `Content-Type`: `application/json`

### Payload JSON (Content Body)
O corpo da requisição deve seguir este formato:

```json
{
  "name": "NomeDoMeuNovoProjeto",
  "description": "Descrição detalhada do propósito do projeto.",
  "capabilities": {
    "versioncontrol": {
      "sourceControlType": "Git"
    },
    "processTemplate": {
      "templateTypeId": "adcc4245-60b1-4054-8d14-31c3615f182b" 
    }
  }
}
```
> **Nota:** O `templateTypeId` varia conforme a organização. O ID acima é o padrão para o template **Scrum**.

---

## 5. Manutenção e Troubleshooting

### Tratamento de Resposta Assíncrona
A criação de projetos no Azure DevOps é uma operação de longa duração. A API retornará um **HTTP 202 (Accepted)** com um `id` de operação.
* Implemente um loop `Do Until` no Power Automate para consultar o status dessa operação (`/_apis/operations/{operationId}`) até que o status seja `succeeded`.

### Erros Comuns
* **409 Conflict:** O nome do projeto já existe.
* **401 Unauthorized:** O PAT expirou ou não possui as permissões necessárias.
* **400 Bad Request:** Payload JSON mal formatado ou ID de template de processo inválido.

---
*Documentação gerada por Orion (@aios-master) - Fevereiro 2026*
