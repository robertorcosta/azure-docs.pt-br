---
title: Pular a exclusão de usuários fora do escopo | Microsoft Docs
description: Saiba como substituir o comportamento padrão de desprovisionamento fora dos usuários do escopo.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522442"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Pular a exclusão de contas de usuário que saem do escopo

Por padrão, o mecanismo de provisionamento Azure AD exclui ou desativa usuários que saem do escopo. No entanto, para certos cenários como Workday to AD User Inbound Provisioning esse comportamento pode não ser o esperado e você pode querer substituir esse comportamento padrão.  

Este guia descreve como usar a API do Microsoft Graph e o explorador de API do Microsoft Graph para definir o sinalizador ***SkipOutOfScopeDeletions*** que controla o processamento de contas que saem do escopo. 
* Se ***SkipOutOfScopeDeletions*** for definido como 0 (falso), então as contas que sairem do escopo serão desativadas no destino
* Se ***SkipOutOfScopeDeletions*** estiver definido como 1 (verdadeiro), então as contas que sairem do escopo não serão desativadas no destino Este sinalizador é definido no nível do *Aplicativo de Provisionamento* e pode ser configurado usando a API do gráfico. 

Como essa configuração é amplamente utilizada com o aplicativo de *provisionamento do usuário Workday to Active Directory,* as etapas abaixo incluem capturas de tela do aplicativo Workday. No entanto, isso também pode ser usado com **todos os outros aplicativos,** tais como (ServiceNow, Salesforce, Dropbox, etc.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recupere seu ID principal do serviço de serviço de provisionamento (ID do objeto)

1. Inicie o [portal Azure](https://portal.azure.com)e navegue até a seção Propriedades do seu aplicativo de provisionamento. Por exemplo, se você quiser exportar seu dia de trabalho para o mapeamento do aplicativo de *provisionamento de usuários ad* navegue para a seção Propriedades desse aplicativo. 
1. Na seção Propriedades do seu aplicativo de provisionamento, copie o valor GUID associado ao campo *ID de Objeto*. Esse valor também é chamado de **ServicePrincipalId** do seu aplicativo e ele será usado em operações do Graph Explorer.

   ![ID da Entidade de Serviço de Aplicativo do Workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Faça login no Microsoft Graph Explorer

1. Abrir o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão “Entrar com a Microsoft” e entre usando as credenciais de administrador do aplicativo ou de administrador global do Azure AD.

    ![Entrar no Graph](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Após entrar com êxito, você verá os detalhes da conta de usuário no painel esquerdo.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Passo 3: Obtenha credenciais de aplicativos existentes e detalhes de conectividade

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] pelo **ServicePrincipalId** extraído da [Etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![OBTER consulta de emprego](./media/skip-out-of-scope-deletions/skip-03.png)

Copie a resposta em um arquivo de texto. Ele se parecerá com o texto JSON mostrado abaixo, com valores destacados em amarelo específicos para sua implantação. Adicione as linhas destacadas em verde até o final e atualize a senha de conexão workday destacada em azul. 

   ![OBTER resposta de trabalho](./media/skip-out-of-scope-deletions/skip-04.png)

Aqui está o bloco JSON para adicionar ao mapeamento. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Passo 4: Atualize o ponto final dos segredos com o sinalizador SkipOutOfScopeDeletions

No Graph Explorer, execute o comando abaixo para atualizar o ponto final dos segredos com o sinalizador ***SkipOutOfScopeDeletions.*** 

Na URL abaixo substitua [servicePrincipalId] pelo **ServicePrincipalId** extraído da [Etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Copie o texto atualizado da Etapa 3 para o "Corpo de Solicitação" e defina o cabeçalho "Tipo de conteúdo" como "aplicativo/json" em "Cabeçalhos de solicitação". 

   ![Solicitação PUT](./media/skip-out-of-scope-deletions/skip-05.png)

Clique em "Run Query". 

Você deve obter a saída como "Sucesso – Código de Status 204". 

   ![Resposta PUT](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Passo 5: Verifique se os usuários fora do escopo não são desativados

Você pode testar esses resultados de bandeira no comportamento esperado atualizando suas regras de escopo para pular um usuário específico. No exemplo abaixo, estamos excluindo o empregado com ID 21173 (que estava no escopo anterior) adicionando uma nova regra de escopo: 

   ![Exemplo de escopo](./media/skip-out-of-scope-deletions/skip-07.png)

No próximo ciclo de provisionamento, o serviço de provisionamento Azure AD identificará que o usuário 21173 saiu do escopo e se a propriedade SkipOutOfScopeDeletions estiver ativada, então a regra de sincronização para esse usuário exibirá uma mensagem como mostrado abaixo: 

   ![Exemplo de escopo](./media/skip-out-of-scope-deletions/skip-08.png)


