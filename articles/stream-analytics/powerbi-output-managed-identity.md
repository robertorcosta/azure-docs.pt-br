---
title: Use a identidade gerenciada para autenticar seu trabalho do Azure Stream Analytics para a saída de POWER BI
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho do Azure Stream Analytics para a saída do Power BI.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 8a7dfd7c690d79d8430f7c33a25b38949dbd06c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086333"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Use a identidade gerenciada para autenticar seu trabalho do Azure Stream Analytics para power BI

[A autenticação gerenciada de identidade](../active-directory/managed-identities-azure-resources/overview.md) para saída do Power BI dá aos trabalhos do Stream Analytics acesso direto a um espaço de trabalho dentro de sua conta power bi. Esse recurso permite que as implantações de trabalhos do Stream Analytics sejam totalmente automatizadas, uma vez que não é mais necessário que um usuário faça login interativo no Power BI através do portal Azure. Além disso, trabalhos de longa duração que escrevem no Power BI são agora mais bem suportados, já que você não precisará reautorizar periodicamente o trabalho.

Este artigo mostra como ativar a identidade gerenciada para a saída de Power BI de um trabalho do Stream Analytics através do portal Azure e através de uma implantação do Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes são necessários para usar este recurso:

- Uma conta power bi com uma [licença Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).

- Um espaço de trabalho atualizado dentro de sua conta power BI. Consulte o anúncio deste recurso [do Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) para obter mais detalhes.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Crie um trabalho de Stream Analytics usando o portal Azure

1. Crie um novo trabalho no Stream Analytics ou abra um trabalho existente no portal Azure. Na barra de menu situada no lado esquerdo da tela, selecione **'Identidade gerenciada'** localizada em **Configure**. Certifique-se de que "Use identidade gerenciada atribuída ao sistema" esteja selecionada e, em seguida, selecione o botão **Salvar** na parte inferior da tela.

   ![Configurar a identidade gerenciada do Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Antes de configurar a saída, dê ao trabalho do Stream Analytics acesso ao seu espaço de trabalho power bi seguindo as instruções no [Acesso ao trabalho Doe o Fluxo Analytics à sua](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) seção de espaço de trabalho power bi deste artigo.

3. Navegue até a seção **Saídas** do trabalho do Stream Analytic, selecione **+ Adicione**e escolha **Power BI**. Em seguida, selecione o botão **Autorizar** e faça login com sua conta power BI.

   ![Autorizar com a conta power bi](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Uma vez autorizado, uma lista de isto será preenchida com todos os espaços de trabalho a que você tem acesso. Selecione o espaço de trabalho que você autorizou na etapa anterior. Em seguida, **selecione Identidade Gerenciada** como o "modo de autenticação". Por fim, selecione o botão **Salvar.**

   ![Configurar a saída de POWER BI com identidade gerenciada](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Implantação do Azure Resource Manager

O Azure Resource Manager permite automatizar totalmente a implantação do seu trabalho no Stream Analytics. Você pode implantar modelos do Gerenciador de recursos usando o Azure PowerShell ou o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Os exemplos abaixo usam o Azure CLI.


1. Você pode criar um recurso **Microsoft.StreamAnalytics/streamingjobs** com uma identidade gerenciada, incluindo a seguinte propriedade na seção de recursos do modelo do Gerenciador de recursos:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Essa propriedade diz ao Azure Resource Manager para criar e gerenciar a identidade para o seu trabalho no Stream Analytics. Abaixo está um exemplo do modelo do Gerenciador de recursos que implanta um trabalho de Stream Analytics com a habilitação Managed Identity e um dissipador de saída do Power BI que usa identidade gerenciada:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Implante o trabalho acima para o grupo de recursos **ExampleGroup** usando o comando CLI abaixo do Azure:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Depois que o trabalho for criado, use o Azure Resource Manager para recuperar a definição completa do trabalho.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    O comando acima retornará uma resposta como a abaixo:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Se você planeja usar a API REST do Power BI para adicionar o trabalho do Stream Analytics ao seu espaço de trabalho power bi, anote o "principalId" retornado.

3. Agora que o trabalho foi criado, continue a [dar ao trabalho Give the Stream Analytics acesso à sua](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) seção de espaço de trabalho power bi deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Dê ao trabalho do Stream Analytics acesso ao seu espaço de trabalho power bi

Agora que o trabalho do Stream Analytics foi criado, ele pode ter acesso a um espaço de trabalho do Power BI.

### <a name="use-the-power-bi-ui"></a>Use a ui power bi

   > [!Note]
   > Para adicionar o trabalho do Stream Analytics ao seu espaço de trabalho power bi usando a ui, você também precisa habilitar o acesso principal do serviço nas **configurações** do Desenvolvedor no portal de admin power bi. Consulte [Comece com um diretor de serviço](https://docs.microsoft.com/power-bi/developer/embed-service-principal#get-started-with-a-service-principal) para obter mais detalhes.

1. Navegue até as configurações de acesso do espaço de trabalho. Veja este artigo para mais detalhes: [Dê acesso ao seu espaço de trabalho](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Digite o nome do seu trabalho de Stream Analytics na caixa de texto e selecione **Contribuinte** como o nível de acesso.

3. Selecione **Adicionar** e fechar o painel.

   ![Adicionar o trabalho de Stream Analytics ao espaço de trabalho do Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Use os cmdlets PowerShell do Power BI

1. Instale os `MicrosoftPowerBIMgmt` cmdlets PowerShell do Power BI.

   > [!Important]
   > Certifique-se de que está usando a versão 1.0.821 ou posterior dos cmdlets.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Faça login no Power BI.

```powershell
Login-PowerBI
```

3. Adicione seu trabalho de Stream Analytics como colaborador do espaço de trabalho.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Use a API Power BI REST

O trabalho do Stream Analytics também pode ser adicionado como um Contribuinte para o espaço de trabalho usando diretamente a API REST "Add Group User". A documentação completa desta API pode ser encontrada aqui: [Grupos - Adicionar Usuário de Grupo](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Solicitação de amostra**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Corpo da solicitação
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Limitações
Abaixo estão as limitações deste recurso:

- Os espaços de trabalho clássicos do Power BI não são suportados.

- Contas do Azure sem diretório ativo do Azure.

- O acesso de vários inquilinos não é suportado. O diretor do Serviço criado para um determinado trabalho do Stream Analytics deve residir no mesmo inquilino do Azure Active Directory no qual o trabalho foi criado e não pode ser usado com um recurso que reside em um inquilino diferente do Azure Active Directory.

- [A identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/overview.md) não é suportada. Isso significa que você não pode inserir seu próprio diretor de serviço para ser usado pelo trabalho do Stream Analytics. O principal de serviço deve ser gerado pelo Azure Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Integração do painel de controle do Power BI com o Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Entender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)
