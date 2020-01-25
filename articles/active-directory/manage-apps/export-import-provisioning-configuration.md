---
title: Exportar ou importar sua configuração de provisionamento usando o API do Graph | Microsoft Docs
description: Saiba como exportar e importar a configuração de provisionamento usando o API do Graph.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609031bfad23a14a954a09a447e363e89a9d29d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711756"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Exportar ou importar sua configuração de provisionamento usando API do Graph

Você pode usar Microsoft Graph API e o explorador do Graph para exportar os mapeamentos e o esquema de atributo de provisionamento de usuário para um arquivo JSON e importá-los de volta para o Azure AD. Você também pode usar as etapas capturadas aqui para criar um backup de sua configuração de provisionamento. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Etapa 1: recuperar a ID da entidade de serviço do aplicativo de provisionamento (ID do objeto)

1. Inicie o [portal do Azure](https://portal.azure.com)e navegue até a seção Propriedades do seu aplicativo de provisionamento. Por exemplo, se você quiser exportar seu *WORKDAY para* o mapeamento de aplicativo de provisionamento de usuário do AD, navegue até a seção de propriedades desse aplicativo. 
1. Na seção Propriedades do seu aplicativo de provisionamento, copie o valor GUID associado ao campo *ID de Objeto*. Esse valor também é chamado de **ServicePrincipalId** do seu aplicativo e ele será usado em operações do Graph Explorer.

   ![ID da Entidade de Serviço de Aplicativo do Workday](media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Etapa 2: entrar no Microsoft Graph Explorer

1. Abrir o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão “Entrar com a Microsoft” e entre usando as credenciais de administrador do aplicativo ou de administrador global do Azure AD.

    ![Entrar no Graph](media/export-import-provisioning-mappings/wd_export_02.png)

1. Após entrar com êxito, você verá os detalhes da conta de usuário no painel esquerdo.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Etapa 3: recuperar a ID do trabalho de provisionamento do aplicativo de provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] pelo **ServicePrincipalId** extraído da [Etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Você obterá uma resposta, conforme mostrado abaixo. Copie o “atributo de id” presente na resposta. Esse valor é o **ProvisioningJobId** e será usado para recuperar os metadados de esquema subjacentes.

   [ID do trabalho de provisionamento do ![](media/export-import-provisioning-mappings/wd_export_03.png)](media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Etapa 4: baixar o esquema de provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nas etapas anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON da resposta e salve-o em um arquivo para criar um backup do esquema.

## <a name="step-5-import-the-provisioning-schema"></a>Etapa 5: importar o esquema de provisionamento

> [!CAUTION]
> Execute esta etapa somente se você precisar modificar o esquema de configuração que não pode ser alterado usando o portal do Azure ou se precisar restaurar a configuração de um arquivo de backup anterior com um esquema válido e funcional.

No Microsoft Graph Explorer, configure a seguinte consulta PUT, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nas etapas anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na guia “Corpo da solicitação”, copie o conteúdo do arquivo de esquema JSON.

   [![Corpo da solicitação](media/export-import-provisioning-mappings/wd_export_04.png)](media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

Na guia “Cabeçalhos de solicitação”, adicione o atributo de cabeçalho de Content-Type com o valor “application/json”

   [![Cabeçalhos de solicitação](media/export-import-provisioning-mappings/wd_export_05.png)](media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Clique no botão “Executar consulta” para importar o novo esquema.
