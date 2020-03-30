---
title: Exporte sua configuração de provisionamento e reverta para um estado bom conhecido para recuperação de desastres.| Microsoft Docs
description: Saiba como exportar sua configuração de provisionamento e reverter para um estado bom conhecido para recuperação de desastres.
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
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051305"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Exporte sua configuração de provisionamento e reverta para um estado bom conhecido

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exportar e importar sua configuração de provisionamento a partir do portal Azure

### <a name="how-can-i-export-my-provisioning-configuration"></a>Como posso exportar minha configuração de provisionamento?
Para exportar sua configuração:
1. No [portal do Azure](https://portal.azure.com/), no painel de navegação esquerdo, selecione **Azure Active Directory**.
2. No **painel do Azure Active Directory,** selecione **aplicativos Corporativos** e escolha seu aplicativo.
3. No painel de navegação à esquerda, selecione **provisionamento**. Na página de configuração de provisionamento, clique em **mapeamentos de atributos,** depois **mostre opções avançadas**e, finalmente, **revise seu esquema**. Isso vai levá-lo ao editor do esquema. 
5. Clique em baixar na barra de comando no topo da página para baixar seu esquema.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Recuperação de desastres - reverter para um estado conhecido bom
Exportar e salvar sua configuração permite que você reverta para uma versão anterior de sua configuração. Recomendamos exportar sua configuração de provisionamento e guardá-la para uso posterior sempre que você fizer uma alteração em seus mapeamentos de atributos ou filtros de escopo. Tudo o que você precisa fazer é abrir o arquivo JSON que você baixou nas etapas acima, copiar todo o conteúdo do arquivo JSON, substituir todo o conteúdo da carga json no editor do esquema e, em seguida, salvar. Se houver um ciclo de provisionamento ativo, ele será concluído e o próximo ciclo usará o esquema atualizado. O próximo ciclo também será um ciclo inicial, que reavalia cada usuário e grupo com base na nova configuração. Considere o seguinte ao reverter para uma configuração anterior:
* Os usuários serão avaliados novamente para determinar se devem estar no escopo. Se os filtros de escopo tiverem sido alterados, um usuário não estiver mais no escopo, ele será desativado. Embora este seja o comportamento desejado na maioria dos casos, há momentos em que você pode querer evitar isso e pode usar a funcionalidade [de exclusão de escopo.](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) 
* Alterar sua configuração de provisionamento reinicia o serviço e aciona um [ciclo inicial](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental).


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Exportar e importar sua configuração de provisionamento usando a API do Microsoft Graph
Você pode usar a API do Microsoft Graph e o Microsoft Graph Explorer para exportar mapeamentos e esquemas de atributos do Provisionamento do Usuário para um arquivo JSON e importá-lo de volta para o Azure AD. Você também pode usar as etapas capturadas aqui para criar um backup da sua configuração de provisionamento. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recupere seu ID principal do serviço de serviço de provisionamento (ID do objeto)

1. Inicie o [portal Azure](https://portal.azure.com)e navegue até a seção Propriedades do seu aplicativo de provisionamento. Por exemplo, se você quiser exportar seu *dia de trabalho para o mapeamento do aplicativo de provisionamento do usuário AD* navegue para a seção Propriedades desse aplicativo. 
1. Na seção Propriedades do seu aplicativo de provisionamento, copie o valor GUID associado ao campo *ID de Objeto*. Esse valor também é chamado **de ServicePrincipalId** do seu App e será usado nas operações do Microsoft Graph Explorer.

   ![ID da Entidade de Serviço de Aplicativo do Workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Faça login no Microsoft Graph Explorer

1. Abrir o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão “Entrar com a Microsoft” e entre usando as credenciais de administrador do aplicativo ou de administrador global do Azure AD.

    ![Login do Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Após entrar com êxito, você verá os detalhes da conta de usuário no painel esquerdo.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passo 3: Recuperar o ID do Trabalho de Provisionamento do Aplicativo de Provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] pelo **ServicePrincipalId** extraído da [Etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Você obterá uma resposta, conforme mostrado abaixo. Copie o “atributo de id” presente na resposta. Esse valor é o **ProvisioningJobId** e será usado para recuperar os metadados de esquema subjacentes.

   [![ID do trabalho de provisionamento](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Passo 4: Baixe o esquema de provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nas etapas anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON da resposta e salve-o em um arquivo para criar um backup do esquema.

### <a name="step-5-import-the-provisioning-schema"></a>Passo 5: Importar o esquema de provisionamento

> [!CAUTION]
> Execute esta etapa somente se você precisar modificar o esquema de configuração que não pode ser alterado usando o portal do Azure ou se precisar restaurar a configuração de um arquivo de backup anterior com um esquema válido e funcional.

No Microsoft Graph Explorer, configure a seguinte consulta PUT, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nas etapas anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na guia “Corpo da solicitação”, copie o conteúdo do arquivo de esquema JSON.

   [![Corpo de solicitação](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Na guia “Cabeçalhos de solicitação”, adicione o atributo de cabeçalho de Content-Type com o valor “application/json”

   [![Cabeçalhos de solicitação](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Clique no botão “Executar consulta” para importar o novo esquema.
