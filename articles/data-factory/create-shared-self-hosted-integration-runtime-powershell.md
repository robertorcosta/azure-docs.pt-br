---
title: Criar um runtime de integração auto-hospedada compartilhado com o PowerShell
description: Aprenda como criar um runtime de integração auto-hospedada compartilhado no Azure Data Factory, para que vários data factories possam acessar o runtime de integração.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 8422d6978c21744696e3d37c34fdd867b014a19e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84655689"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Criar um runtime de integração auto-hospedada compartilhado no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este guia mostra como criar um runtime de integração auto-hospedada compartilhado no Azure Data Factory. Em seguida, você pode usar o runtime de integração auto-hospedada compartilhado em outro data factory.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Criar um IR auto-hospedado compartilhado usando a interface do usuário do Azure Data Factory

Para criar um IR auto-hospedado compartilhado usando a interface do usuário do Azure Data Factory, siga estas etapas:

1. No IR auto-hospedado a ser compartilhado, selecione **conceder permissão a outro data Factory** e, na página "configuração do tempo de execução de integração", selecione o data Factory no qual você deseja criar o ir vinculado.
      
    ![Botão para conceder permissão na guia Compartilhamento](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Observe e copie o "ID de recurso" acima do IR hospedado para ser compartilhado.
         
3. No data factory para o qual as permissões foram concedidas, crie um novo IR auto-hospedado (vinculado) e insira a ID do recurso.
      
    ![Botão para criar um tempo de execução de integração auto-hospedado](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Botão para criar um runtime de integração auto-hospedada vinculado](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Caixas de nome e ID do recurso](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Criar um IR auto-hospedado compartilhado usando o Azure PowerShell

Para criar um IR auto-hospedado compartilhado usando o Azure PowerShell, siga estas etapas: 
1. Criar um data factory. 
1. Criar um runtime de integração auto-hospedada.
1. Compartilhe o runtime de integração auto-hospedada com outros data factories.
1. Crie um runtime de integração vinculado.
1. Revogue o compartilhamento.

### <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

- **Azure PowerShell**. Siga as instruções em [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). Você usa o PowerShell para executar um script para criar um runtime de integração auto-hospedada que pode ser compartilhado com outros data factories. 

> [!NOTE]  
> Para obter uma lista de regiões do Azure nas quais o Data Factory está atualmente disponível, selecione as regiões que lhe interessam em [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Criar uma data factory

1. Inicie o ISE (ambiente de script integrado) do Windows PowerShell.

1. Crie variáveis. Copie e cole o seguinte script. Substitua as variáveis, como **SubscriptionName** e **ResourceGroupName**, com valores reais: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Entre e selecione uma assinatura. Adicione o código a seguir ao script para entrar e selecionar sua assinatura do Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Crie um grupo de recursos e um Data Factory.

    > [!NOTE]  
    > Esta etapa é opcional. Se você já tiver um data factory, ignore esta etapa. 

    Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o comando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` na localização WestEurope: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Execute o comando a seguir para criar um data factory: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Criar um Integration Runtime auto-hospedado

> [!NOTE]  
> Esta etapa é opcional. Se você já possui o runtime de integração auto-hospedada que deseja compartilhar com outros data factories, pule esta etapa.

Execute o comando a seguir para criar um runtime de integração auto-hospedada:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Obter a chave de autenticação do runtime de integração e registrar um nó

Execute o comando a seguir para obter a chave de autenticação para o runtime de integração auto-hospedada:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

A resposta contém a chave de autenticação para esse runtime de integração auto-hospedada. Use essa chave ao registrar o nó do runtime de integração.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Instale e registre o runtime de integração auto-hospedada

1. Baixe o instalador do runtime de integração auto-hospedada do [Integration runtime do Azure Data Factory](https://aka.ms/dmg).

2. Execute o instalador para instalar a integração auto-hospedada em um computador local.

3. Registre a nova integração auto-hospedada com a chave de autenticação que você recuperou em uma etapa anterior.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Compartilhar o runtime de integração auto-hospedada com outro data factory

#### <a name="create-another-data-factory"></a>Criar outro data factory

> [!NOTE]  
> Esta etapa é opcional. Se você já possui o data factory com o qual deseja compartilhar, pule esta etapa.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Conceder permissão

Conceda permissão para o data factory que precisa acessar o runtime de integração auto-hospedada que você criou e registrou.

> [!IMPORTANT]  
> Não ignore esta etapa!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Criar um runtime de integração auto-hospedada vinculado

Execute o comando a seguir para criar um runtime de integração auto-hospedada vinculado:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Agora você pode usar esse runtime de integração vinculado em qualquer serviço vinculado. O runtime de integração vinculado usa o runtime de integração compartilhada para executar atividades.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revogar o compartilhamento do runtime de integração de um data factory

Para revogar o acesso de um data factory a partir do runtime de integração compartilhada, execute o seguinte comando:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Para remover o runtime de integração vinculado existente, execute o seguinte comando no runtime de integração compartilhada:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Próximas etapas

- Revise os [conceitos de runtime de integração no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Saiba como [criar um runtime de integração auto-hospedada no portal do Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
