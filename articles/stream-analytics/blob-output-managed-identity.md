---
title: Autenticar saída blob com Managed Identity Azure Stream Analytics
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho do Azure Stream Analytics na saída de armazenamento do Azure Blob.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129980"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Use a identidade gerenciada para autenticar seu trabalho do Azure Stream Analytics na saída de armazenamento do Azure Blob

[A autenticação gerenciada de identidade](../active-directory/managed-identities-azure-resources/overview.md) para saída para o armazenamento Azure Blob dá aos trabalhos do Stream Analytics acesso direto a uma conta de armazenamento em vez de usar uma seqüência de conexões. Além de melhorar a segurança, esse recurso também permite gravar dados em uma conta de armazenamento em uma Rede Virtual (VNET) dentro do Azure.

Este artigo mostra como ativar a identidade gerenciada para a saída do Blob de um trabalho do Stream Analytics através do portal Azure e através de uma implantação do Azure Resource Manager.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Crie o trabalho de Stream Analytics usando o portal Azure

1. Crie um novo trabalho no Stream Analytics ou abra um trabalho existente no portal Azure. Na barra de menu situada no lado esquerdo da tela, selecione **'Identidade gerenciada'** localizada em **Configure**. Certifique-se de que "Use identidade gerenciada atribuída ao sistema" esteja selecionada e clique no botão **Salvar** na parte inferior da tela.

   ![Configurar a identidade gerenciada do Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Na janela de propriedades de saída do dissipador de saída do Azure Blob, selecione o modo de autenticação para baixo e escolha **'Identidade gerenciada ''** Para obter informações sobre as outras propriedades de saída, consulte [Entender saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md). Quando terminar, clique em **Salvar**.

   ![Configurar a saída de armazenamento Azure Blob](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Agora que o trabalho foi criado, consulte o [acesso ao trabalho Give the Stream Analytics à sua](#give-the-stream-analytics-job-access-to-your-storage-account) seção de conta de armazenamento deste artigo.

## <a name="azure-resource-manager-deployment"></a>Implantação do Azure Resource Manager

O uso do Azure Resource Manager permite automatizar totalmente a implantação do seu trabalho no Stream Analytics. Você pode implantar modelos do Gerenciador de recursos usando o Azure PowerShell ou o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Os exemplos abaixo usam o Azure CLI.


1. Você pode criar um recurso **Microsoft.StreamAnalytics/streamingjobs** com uma identidade gerenciada, incluindo a seguinte propriedade na seção de recursos do modelo do Gerenciador de recursos:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Essa propriedade diz ao Azure Resource Manager para criar e gerenciar a identidade para o seu trabalho no Stream Analytics. Abaixo está um exemplo do modelo do Gerenciador de recursos que implanta um trabalho de Stream Analytics com habilitação De identidade gerenciada e um dissipador de saída Blob que usa identidade gerenciada:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    O trabalho acima pode ser implantado no grupo de recursos **ExampleGroup** usando o comando CLI abaixo do Azure:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Depois que o trabalho é criado, você pode usar o Azure Resource Manager para recuperar a definição completa do trabalho.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    O comando acima retornará uma resposta como a abaixo:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Tome nota do **principalId** da definição do trabalho, que identifica a Identidade Gerenciada do seu trabalho no Azure Active Directory e será usado na próxima etapa para conceder ao trabalho do Stream Analytics acesso à conta de armazenamento.

3. Agora que o trabalho foi criado, consulte o [acesso ao trabalho Give the Stream Analytics à sua](#give-the-stream-analytics-job-access-to-your-storage-account) seção de conta de armazenamento deste artigo.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Dê ao trabalho do Stream Analytics acesso à sua conta de armazenamento

Existem dois níveis de acesso que você pode optar por dar o seu trabalho no Stream Analytics:

1. **Acesso ao nível do contêiner:** esta opção dá ao trabalho acesso a um contêiner específico existente.
2. **Acesso ao nível da conta:** essa opção dá ao trabalho acesso geral à conta de armazenamento, incluindo a capacidade de criar novos contêineres.

A menos que você precise do trabalho para criar contêineres em seu nome, você deve escolher **o acesso ao nível de Container,** uma vez que esta opção concederá ao trabalho o nível mínimo de acesso necessário. Ambas as opções são explicadas abaixo para o portal Azure e a linha de comando.

### <a name="grant-access-via-the-azure-portal"></a>Conceder acesso através do portal Azure

#### <a name="container-level-access"></a>Acesso ao nível do contêiner

1. Navegue até o painel de configuração do contêiner dentro de sua conta de armazenamento.

2. Selecione **Control de acesso (IAM)** no lado esquerdo.

3. Na seção "Adicionar uma atribuição de função", clique **em Adicionar**.

4. No painel de atribuição de papéis:

    1. Defina a **função como** "Storage Blob Data Contributor"
    2. **Certifique-se de que** o acesso ao access toda-down está definido como "Usuário, grupo ou principal de serviço do Azure AD".
    3. Digite o nome do seu trabalho de Stream Analytics no campo de pesquisa.
    4. Selecione seu trabalho de Stream Analytics e clique **em Salvar**.

   ![Conceder acesso a contêineres](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Acesso ao nível da conta

1. Navegue até sua conta de armazenamento.

2. Selecione **Control de acesso (IAM)** no lado esquerdo.

3. Na seção "Adicionar uma atribuição de função", clique **em Adicionar**.

4. No painel de atribuição de papéis:

    1. Defina a **função como** "Storage Blob Data Contributor"
    2. **Certifique-se de que** o acesso ao access toda-down está definido como "Usuário, grupo ou principal de serviço do Azure AD".
    3. Digite o nome do seu trabalho de Stream Analytics no campo de pesquisa.
    4. Selecione seu trabalho de Stream Analytics e clique **em Salvar**.

   ![Conceder acesso à conta](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Conceder acesso através da linha de comando

#### <a name="container-level-access"></a>Acesso ao nível do contêiner

Para dar acesso a um contêiner específico, execute o seguinte comando usando o Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Acesso ao nível da conta

Para dar acesso a toda a conta, execute o seguinte comando usando o Cli do Azure:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Habilitar o acesso VNET

Ao configurar os **Firewalls e redes virtuais**da sua conta de armazenamento, você pode permitir opcionalmente o tráfego de rede de outros serviços confiáveis da Microsoft. Quando o Stream Analytics autentica usando a Identidade Gerenciada, ele fornece a prova de que a solicitação é originária de um serviço confiável. Abaixo estão as instruções para ativar esta exceção de acesso VNET.

1.  Navegue até o painel "Firewalls e redes virtuais" dentro do painel de configuração da conta de armazenamento.
2.  Certifique-se de que a opção "Permitir que serviços confiáveis da Microsoft acessem essa conta de armazenamento" esteja ativada.
3.  Se você o habilitou, clique **em Salvar**.

   ![Habilitar o acesso VNET](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Limitações
Abaixo estão as limitações atuais deste recurso:

1. Contas clássicas do Azure Storage.

2. Contas do Azure sem diretório ativo do Azure.

3. O acesso de vários inquilinos não é suportado. O diretor do Serviço criado para um determinado trabalho do Stream Analytics deve residir no mesmo inquilino do Azure Active Directory no qual o trabalho foi criado e não pode ser usado com um recurso que reside em um inquilino diferente do Azure Active Directory.

4. [A identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/overview.md) não é suportada. Isso significa que o usuário não pode inserir seu próprio principal de serviço para ser usado pelo seu trabalho de Stream Analytics. O principal de serviço deve ser gerado pelo Azure Stream Analytics.

## <a name="next-steps"></a>Próximas etapas

* [Entender as saídas do Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Particionamento de saída de blob personalizado do Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
