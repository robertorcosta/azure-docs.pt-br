---
title: Usar scripts de implantação em modelos | Microsoft Docs
description: Use scripts de implantação em modelos de Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: jgao
ms.openlocfilehash: 5b938e2072daec56261e529ab8a2a8b15b55d143
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872327"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Usar scripts de implantação em modelos (visualização)

Saiba como usar scripts de implantação em modelos de recursos do Azure. Com um novo tipo de recurso `Microsoft.Resources/deploymentScripts`chamado, os usuários podem executar scripts de implantação em implantações de modelo e examinar os resultados da execução. Esses scripts podem ser usados para executar etapas personalizadas, como:

- Adicionar usuários a um diretório
- executar operações do plano de dados, por exemplo, copiar BLOBs ou banco de dados de semente
- Pesquisar e validar uma chave de licença
- criar um certificado autoassinado
- criar um objeto no Azure AD
- Pesquisar blocos de endereços IP do sistema personalizado

Os benefícios do script de implantação:

- Fácil de codificar, usar e depurar. Você pode desenvolver scripts de implantação em seus ambientes de desenvolvimento favoritos. Os scripts podem ser inseridos em modelos ou em arquivos de script externos.
- Você pode especificar a linguagem de script e a plataforma. Atualmente, há suporte para scripts de implantação Azure PowerShell e CLI do Azure no ambiente Linux.
- Permitir a especificação das identidades que são usadas para executar os scripts. Atualmente, há suporte apenas para [a identidade gerenciada atribuída pelo usuário do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Permita passar argumentos de linha de comando para o script.
- Pode especificar saídas de script e passá-las de volta para a implantação.

O recurso de script de implantação só está disponível nas regiões em que a instância de contêiner do Azure está disponível.  Consulte [disponibilidade de recursos para instâncias de contêiner do Azure em regiões do Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Uma conta de armazenamento e uma instância de contêiner são necessárias para a execução do script e a solução de problemas. Você tem as opções para especificar uma conta de armazenamento existente, caso contrário, a conta de armazenamento junto com a instância de contêiner será criada automaticamente pelo serviço de script. Os dois recursos criados automaticamente geralmente são excluídos pelo serviço de script quando a execução do script de implantação entra em um estado terminal. Você será cobrado pelos recursos até que eles sejam excluídos. Para saber mais, consulte [limpar recursos de script de implantação](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma identidade gerenciada atribuída pelo usuário com a função do colaborador para o grupo de recursos de destino**. Essa identidade é usada para executar scripts de implantação. Para executar operações fora do grupo de recursos, você precisa conceder permissões adicionais. Por exemplo, atribua a identidade ao nível de assinatura se você quiser criar um novo grupo de recursos.

  > [!NOTE]
  > O serviço de script cria uma conta de armazenamento (a menos que você especifique uma conta de armazenamento existente) e uma instância de contêiner em segundo plano.  Uma identidade gerenciada atribuída pelo usuário com a função do colaborador no nível da assinatura será necessária se a assinatura não tiver registrado os provedores de recursos da conta de armazenamento do Azure (Microsoft. Storage) e da instância de contêiner do Azure (Microsoft. ContainerInstance).

  Para criar uma identidade, consulte [criar uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), ou [usando CLI do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), ou [usando Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Você precisa da ID da identidade ao implantar o modelo. O formato da identidade é:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Use a CLI ou o script do PowerShell a seguir para obter a ID fornecendo o nome do grupo de recursos e o nome da identidade.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** ou **CLI do Azure**. Para obter uma lista de versões de Azure PowerShell com suporte, consulte [aqui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list); para obter uma lista de versões de CLI do Azure com suporte, consulte [aqui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > O script de implantação usa as imagens da CLI disponíveis do registro de contêiner da Microsoft (MCR). Leva cerca de um mês para certificar uma imagem da CLI para o script de implantação. Não use as versões da CLI que foram lançadas dentro de 30 dias. Para localizar as datas de lançamento das imagens, consulte [CLI do Azure Release Notes](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Se uma versão sem suporte for usada, a mensagem de erro listará as versões com suporte.

    Você não precisa dessas versões para implantar modelos. Mas essas versões são necessárias para testar os scripts de implantação localmente. Confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Você pode usar uma imagem pré-configurada do Docker.  Consulte [Configurar ambiente de desenvolvimento](#configure-development-environment).

## <a name="sample-templates"></a>Modelos de exemplo

O JSON a seguir é um exemplo.  O esquema de modelo mais recente pode ser encontrado [aqui](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> O exemplo é para fins de demonstração.  **scriptContent** e **primaryScriptUris** não podem coexistir em um modelo.

Detalhes do valor da propriedade:

- **Identidade**: o serviço de script de implantação usa uma identidade gerenciada atribuída pelo usuário para executar os scripts. Atualmente, somente a identidade gerenciada atribuída pelo usuário tem suporte.
- **tipo**: especifique o tipo de script. Atualmente, os scripts Azure PowerShell e CLI do Azure são compatíveis. Os valores são **AzurePowerShell** e **AzureCLI**.
- **forceUpdateTag**: a alteração desse valor entre implantações de modelo força o script de implantação a ser executado novamente. Use a função newGuid () ou utcNow () que precisa ser definida como o defaultValue de um parâmetro. Para saber mais, confira [Executar script mais de uma vez](#run-script-more-than-once).
- **containerSettings**: especifique as configurações para personalizar a instância de contêiner do Azure.  **containerGroupName** é para especificar o nome do grupo de contêineres.  Se não for especificado, o nome do grupo será gerado automaticamente.
- **storageAccountSettings**: especifique as configurações para usar uma conta de armazenamento existente. Se não for especificado, uma conta de armazenamento será criada automaticamente. Consulte [usar uma conta de armazenamento existente](#use-an-existing-storage-account).
- **azPowerShellVersion**/**azCliVersion**: Especifique a versão do módulo a ser usada. Para obter uma lista de versões do PowerShell e da CLI com suporte, consulte [pré-requisitos](#prerequisites).
- **argumentos**: Especifique os valores de parâmetro. os valores são separados por espaços.
- **environmentVariables**: especifique as variáveis de ambiente para passar para o script. Para obter mais informações, consulte [desenvolver scripts de implantação](#develop-deployment-scripts).
- **scriptContent**: especifique o conteúdo do script. Para executar um script externo, use `primaryScriptUri` em vez disso. Para obter exemplos, consulte [usar script embutido](#use-inline-scripts) e [usar script externo](#use-external-scripts).
- **primaryScriptUri**: especifique uma URL acessível publicamente para o script de implantação primário com as extensões de arquivo com suporte.
- **supportingScriptUris**: especifique uma matriz de URLs acessíveis publicamente para dar suporte a arquivos que são chamados `ScriptContent` no `PrimaryScriptUri`ou no.
- **tempo limite**: especifique o tempo de execução máximo permitido do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**.
- **cleanupPreference**. Especifique a preferência de limpeza dos recursos de implantação quando a execução do script chegar em um estado de terminal. A configuração padrão é **sempre**, o que significa excluir os recursos, apesar do estado do terminal (êxito, falha, cancelado). Para saber mais, confira [Limpar recursos do script de implantação](#clean-up-deployment-script-resources).
- **retentionInterval**: especifique o intervalo para o qual o serviço retém os recursos de script de implantação após a execução do script de implantação atingir um estado de terminal. Os recursos de script de implantação serão excluídos quando essa duração expirar. A duração é baseada no [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**, que significa sete dias. Essa propriedade é usada quando cleanupPreference está configurado como *OnExpiration*. A propriedade *Onexpiretion* não está habilitada no momento. Para saber mais, confira [Limpar recursos do script de implantação](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Outros exemplos

- [criar e atribuir um certificado a um cofre de chaves](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [crie e atribua uma identidade gerenciada atribuída pelo usuário a um grupo de recursos e execute um script de implantação](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> É recomendável criar uma identidade atribuída pelo usuário e conceder permissões com antecedência. Você poderá obter erros relacionados a entrada e permissão se criar a identidade e conceder permissões no mesmo modelo em que você executa scripts de implantação. Leva algum tempo para que as permissões se tornem efetivas.

## <a name="use-inline-scripts"></a>Usar scripts embutidos

O modelo a seguir tem um recurso definido com `Microsoft.Resources/deploymentScripts` o tipo. A parte realçada é o script embutido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Como os scripts de implantação embutidos são colocados entre aspas duplas, as cadeias de caracteres dentro dos scripts de implantação precisam ser colocadas entre aspas simples. O caractere de escape do PowerShell é **&#92;**. Você também pode considerar o uso da substituição de cadeia de caracteres, como mostrado no exemplo de JSON anterior. Consulte o valor padrão do parâmetro Name.

O script usa um parâmetro e a saída do valor do parâmetro. **DeploymentScriptOutputs** é usado para armazenar saídas.  Na seção de saídas, a linha de **valor** mostra como acessar os valores armazenados. `Write-Output`é usado para fins de depuração. Para saber como acessar o arquivo de saída, consulte [depurar scripts de implantação](#debug-deployment-scripts).  Para obter as descrições de propriedade, consulte [modelos de exemplo](#sample-templates).

Para executar o script, selecione **Experimente-** o para abrir o Cloud Shell e cole o código a seguir no painel Shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

A saída se parece com isso:

![Saída do script de implantação do modelo do Resource Manager Olá, mundo](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Usar scripts externos

Além dos scripts embutidos, você também pode usar arquivos de script externos. Somente os scripts primários do PowerShell com a extensão de arquivo **ps1** têm suporte. Para scripts da CLI, os scripts principais podem ter qualquer extensão (ou sem uma extensão), desde que os scripts sejam scripts bash válidos. Para usar arquivos de script externo, `scriptContent` substitua `primaryScriptUri`por. Por exemplo: 

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Para ver um exemplo, selecione [aqui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Os arquivos de script externo devem estar acessíveis.  Para proteger os arquivos de script armazenados nas contas de armazenamento do Azure, consulte [implantar modelo de ARM privado com token SAS](./secure-template-with-sas-token.md).

Você é responsável por garantir a integridade dos scripts que são referenciados pelo script de implantação, seja **PrimaryScriptUri** ou **SupportingScriptUris**.  Referencie somente scripts nos quais você confia.

## <a name="use-supporting-scripts"></a>Usar scripts de suporte

Você pode separar as lógicas complicadas em um ou mais arquivos de script de suporte. A `supportingScriptURI` propriedade permite que você forneça uma matriz de URIs para os arquivos de script de suporte, se necessário:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Os arquivos de script de suporte podem ser chamados de scripts embutidos e arquivos de script primários. Os arquivos de script de suporte não têm restrições sobre a extensão de arquivo.

Os arquivos de suporte são copiados para azscripts/azscriptinput no tempo de execução. Use o caminho relativo para fazer referência aos arquivos de suporte de scripts embutidos e arquivos de script primários.

## <a name="work-with-outputs-from-powershell-script"></a>Trabalhar com saídas do script do PowerShell

O modelo a seguir mostra como passar valores entre dois recursos de deploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

No primeiro recurso, você define uma variável chamada **$DeploymentScriptOutputs**e a usa para armazenar os valores de saída. Para acessar o valor de saída de outro recurso dentro do modelo, use:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Trabalhar com saídas do script da CLI

Diferente do script de implantação do PowerShell, o suporte de CLI/bash não expõe uma variável comum para armazenar saídas de script, em vez disso, há uma variável de ambiente chamada **AZ_SCRIPTS_OUTPUT_PATH** que armazena o local onde o arquivo de saída de script reside. Se um script de implantação for executado de um modelo do Resource Manager, essa variável de ambiente será definida automaticamente para você pelo shell bash.

As saídas de script de implantação devem ser salvas no local de AZ_SCRIPTS_OUTPUT_PATH e as saídas devem ser um objeto de cadeia de caracteres JSON válido. O conteúdo do arquivo deve ser salvo como um par chave-valor. Por exemplo, uma matriz de cadeias de caracteres é armazenada como {"MyResult": ["foo", "bar"]}.  Armazenar apenas os resultados da matriz, por exemplo, ["foo", "bar"], é inválido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) é usado no exemplo anterior. Ele vem com as imagens de contêiner. Consulte [Configurar ambiente de desenvolvimento](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Desenvolver scripts de implantação

### <a name="handle-non-terminating-errors"></a>Tratar erros de não encerramento

Você pode controlar como o PowerShell responde a erros de não encerramento usando a variável [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) em seu script de implantação. O serviço de script não define/altera o valor.  Apesar do valor definido para $ErrorActionPreference, o script de implantação define o estado de provisionamento de recursos como *falha* quando o script encontra um erro.

### <a name="pass-secured-strings-to-deployment-script"></a>Passar cadeias de caracteres seguras para o script de implantação

Definir variáveis de ambiente (EnvironmentVariable) em suas instâncias de contêiner permite que você forneça a configuração dinâmica do aplicativo ou script executado pelo contêiner. O script de implantação manipula variáveis de ambiente não seguras e protegidas da mesma maneira que a instância de contêiner do Azure. Para obter mais informações, consulte [definir variáveis de ambiente em instâncias de contêiner](../../container-instances/container-instances-environment-variables.md#secure-values).

## <a name="debug-deployment-scripts"></a>Depurar scripts de implantação

O serviço de script cria uma [conta de armazenamento](../../storage/common/storage-account-overview.md) (a menos que você especifique uma conta de armazenamento existente) e uma instância de [contêiner](../../container-instances/container-instances-overview.md) para execução de script. Se esses recursos forem criados automaticamente pelo serviço de script, ambos os recursos terão o sufixo **azscripts** nos nomes dos recursos.

![Nomes de recursos de script de implantação de modelo do Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

O script do usuário, os resultados da execução e o arquivo stdout são armazenados nos compartilhamentos de arquivos da conta de armazenamento. Há uma pasta chamada **azscripts**. Na pasta, há mais duas pastas para a entrada e os arquivos de saída: **azscriptinput** e **azscriptoutput**.

A pasta de saída contém um **executionresult.json** e o arquivo de saída de script. Você pode ver a mensagem de erro de execução de script em **ExecutionResult. JSON**. O arquivo de saída é criado somente quando o script é executado com êxito. A pasta de entrada contém um arquivo de script do PowerShell do sistema e os arquivos de script da implantação do usuário. Você pode substituir o arquivo de script de implantação do usuário por um revisado e executar novamente o script de implantação da instância de contêiner do Azure.

Você pode obter as informações de implantação de recurso de script de implantação no nível do grupo de recursos e no nível da assinatura usando a API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

O exemplo a seguir usa [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

A saída deverá ser semelhante a:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

A saída mostra o estado de implantação e as IDs de recurso de script de implantação.

A API REST a seguir retorna o log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Ele só funciona antes de os recursos de script de implantação serem excluídos.

Para ver o recurso deploymentScripts no portal, selecione **Mostrar tipos ocultos**:

![Script de implantação de modelo do Resource Manager, Mostrar tipos ocultos, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>Usar uma conta de armazenamento existente

Uma conta de armazenamento e uma instância de contêiner são necessárias para a execução do script e a solução de problemas. Você tem as opções para especificar uma conta de armazenamento existente, caso contrário, a conta de armazenamento junto com a instância de contêiner será criada automaticamente pelo serviço de script. Os requisitos para usar uma conta de armazenamento existente:

- Os tipos de conta de armazenamento com suporte são: contabilidade de uso geral v2, uso geral v1 e contas de armazenamento de File. Somente o armazenamento de fileé compatível com o SKU Premium. Para obter mais informações, consulte [tipos de contas de armazenamento](../../storage/common/storage-account-overview.md).
- As regras de firewall da conta de armazenamento ainda não têm suporte. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](../../storage/common/storage-network-security.md).
- A identidade gerenciada atribuída pelo usuário do script de implantação deve ter permissões para gerenciar a conta de armazenamento, que inclui os compartilhamentos de arquivos de leitura, criação e exclusão.

Para especificar uma conta de armazenamento existente, adicione o JSON a seguir ao elemento de `Microsoft.Resources/deploymentScripts`propriedade de:

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: especifique o nome da conta de armazenamento.
- **storageAccountKey "**: especifique uma das chaves da conta de armazenamento. Você pode usar a [`listKeys()`](./template-functions-resource.md#listkeys) função para recuperar a chave. Por exemplo: 

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Consulte [modelos de exemplo](#sample-templates) para obter `Microsoft.Resources/deploymentScripts` uma amostra de definição completa.

Quando uma conta de armazenamento existente é usada, o serviço de script cria um compartilhamento de arquivos com um nome exclusivo. Consulte [limpar recursos de script de implantação](#clean-up-deployment-script-resources) para saber como o serviço de script limpa o compartilhamento de arquivos.

## <a name="clean-up-deployment-script-resources"></a>Limpar recursos de script de implantação

Uma conta de armazenamento e uma instância de contêiner são necessárias para a execução do script e a solução de problemas. Você tem as opções para especificar uma conta de armazenamento existente, caso contrário, uma conta de armazenamento juntamente com uma instância de contêiner será criada automaticamente pelo serviço de script. Os dois recursos criados automaticamente são excluídos pelo serviço de script quando a execução do script de implantação entra em um estado terminal. Você será cobrado pelos recursos até que eles sejam excluídos. Para obter as informações de preço, consulte [preços de instâncias de contêiner](https://azure.microsoft.com/pricing/details/container-instances/) e preços de [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

O ciclo de vida desses recursos é controlado pelas seguintes propriedades no modelo:

- **cleanupPreference**: limpar a preferência quando a execução do script chegar em um estado de terminal. Os valores com suporte são:

  - **Sempre**: exclua os recursos criados automaticamente quando a execução do script chegar em um estado de terminal. Se uma conta de armazenamento existente for usada, o serviço de script excluirá o compartilhamento de arquivos criado na conta de armazenamento. Como o recurso deploymentScripts ainda pode estar presente depois que os recursos são limpos, os serviços de script persistem os resultados da execução do script, por exemplo, stdout, saídas, valor de retorno, etc. antes de os recursos serem excluídos.
  - **OnSuccess**: exclua os recursos criados automaticamente somente quando a execução do script for bem-sucedida. Se uma conta de armazenamento existente for usada, o serviço de script removerá o compartilhamento de arquivos somente quando a execução do script for bem-sucedida. Você ainda pode acessar os recursos para localizar as informações de depuração.
  - **Onexpiretion**: exclua os recursos automaticamente somente quando a configuração **retentionInterval** estiver expirada. Se uma conta de armazenamento existente for usada, o serviço de script removerá o compartilhamento de arquivos, mas manterá a conta de armazenamento.

- **retentionInterval**: especifique o intervalo de tempo que um recurso de script será retido e depois o qual será expirado e excluído.

> [!NOTE]
> Não é recomendável usar a conta de armazenamento e a instância de contêiner que são geradas pelo serviço de script para outras finalidades. Os dois recursos podem ser removidos, dependendo do ciclo de vida do script.

## <a name="run-script-more-than-once"></a>Executar script mais de uma vez

A execução do script de implantação é uma operação idempotente. Se nenhuma das propriedades de recurso deploymentScripts (incluindo o script embutido) for alterada, o script não será executado quando você reimplantar o modelo. O serviço de script de implantação compara os nomes de recursos no modelo com os recursos existentes no mesmo grupo de recursos. Há duas opções se você quiser executar o mesmo script de implantação várias vezes:

- Altere o nome do recurso deploymentScripts. Por exemplo, use a função de modelo [UtcNow](./template-functions-date.md#utcnow) como o nome do recurso ou como parte do nome do recurso. Alterar o nome do recurso cria um novo recurso deploymentScripts. É bom manter um histórico da execução do script.

    > [!NOTE]
    > A função utcNow só pode ser usada no valor padrão para um parâmetro.

- Especifique um valor diferente na propriedade `forceUpdateTag` de modelo.  Por exemplo, use utcNow como o valor.

> [!NOTE]
> Grave os scripts de implantação idempotentes. Isso garante que, se eles forem executados novamente acidentalmente, não causarão alterações no sistema. Por exemplo, se o script de implantação for usado para criar um recurso do Azure, verifique se o recurso não existe antes de criá-lo, para que o script seja bem sucedido ou você não crie o recurso novamente.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

Você pode usar uma imagem de contêiner pré-configurada do Docker como seu ambiente de desenvolvimento de script de implantação. O procedimento a seguir mostra como configurar a imagem do Docker no Windows. Para Linux e Mac, você pode encontrar as informações na Internet.

1. Instale o [Docker desktop](https://www.docker.com/products/docker-desktop) em seu computador de desenvolvimento.
1. Abra o Docker desktop.
1. Selecione o ícone de área de trabalho do Docker em barras de tarefas e, em seguida, selecione **configurações**.
1. Selecione **unidades compartilhadas**, selecione uma unidade local que você deseja disponibilizar para seus contêineres e, em seguida, selecione **aplicar**

    ![Unidade do Docker do script de implantação do modelo do Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Insira suas credenciais do Windows no prompt.
1. Abra uma janela de terminal, um prompt de comando ou o Windows PowerShell (não use o ISE do PowerShell).
1. Efetuar pull da imagem de contêiner do script de implantação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    O exemplo usa a versão 2.7.0 do PowerShell.

    Para efetuar pull de uma imagem da CLI de um registro de contêiner da Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo usa a CLI da versão 2.0.80. O script de implantação usa as imagens de contêineres de CLI padrão encontradas [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Execute a imagem do Docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Substitua ** &lt;a letra do driver do host>** e ** &lt;o nome do diretório de host>** com uma pasta existente na unidade compartilhada.  Ele mapeia a pasta para a pasta **/Data** no contêiner. Para obter exemplos, para mapear D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-isso** significa manter a imagem de contêiner ativa.

    Um exemplo de CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selecione **compartilhá-lo** quando receber um prompt.
1. A captura de tela a seguir mostra como executar um script do PowerShell, Considerando que você tem um arquivo HelloWorld. ps1 na pasta d:\docker

    ![Gerenciador de script de implantação do modelo do Resource Manager cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois que o script for testado com êxito, você poderá usá-lo como um script de implantação.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar os scripts de implantação. Para percorrer um tutorial de script de implantação:

> [!div class="nextstepaction"]
> [Tutorial: usar scripts de implantação em modelos de Azure Resource Manager](./template-tutorial-deployment-script.md)