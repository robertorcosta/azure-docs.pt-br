---
title: Use scripts de implantação em modelos | Microsoft Docs
description: usar scripts de implantação em modelos do Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: jgao
ms.openlocfilehash: 3ef1c3d3fe0fd1ecad95e027b06ce14fd70d4d3f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437870"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Use scripts de implantação em modelos (Visualização)

Saiba como usar scripts de implantação em modelos de recursos do Azure. Com um novo `Microsoft.Resources/deploymentScripts`tipo de recurso chamado, os usuários podem executar scripts de implantação em implantações de modelos e revisar resultados de execução. Esses scripts podem ser usados para executar etapas personalizadas, tais como:

- adicionar usuários a um diretório
- criar um registro de aplicativo
- realizar operações de plano de dados, por exemplo, copiar blobs ou banco de dados de sementes
- olhar para cima e validar uma chave de licença
- criar um certificado autoassinado
- criar um objeto no Azure AD
- procurar blocos de endereço IP do sistema personalizado

Os benefícios do script de implantação:

- Fácil de codificar, usar e depurar. Você pode desenvolver scripts de implantação em seus ambientes de desenvolvimento favoritos. Os scripts podem ser incorporados em modelos ou em arquivos de script externos.
- Você pode especificar o idioma e a plataforma do script. Atualmente, os scripts de implantação do Azure PowerShell e do Azure CLI no ambiente Linux são suportados.
- Permita especificar as identidades usadas para executar os scripts. Atualmente, apenas [a identidade gerenciada atribuída pelo usuário do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) é suportada.
- Permitir a passagem de argumentos de linha de comando para o script.
- Pode especificar saídas de script e passá-las de volta para a implantação.

> [!IMPORTANT]
> Dois recursos de script de implantação, uma conta de armazenamento e uma instância de contêiner são criados no mesmo grupo de recursos para execução de script e solução de problemas. Esses recursos geralmente são excluídos pelo serviço de script quando a execução do script de implantação fica em um estado terminal. Você será cobrado pelos recursos até que eles sejam excluídos. Para saber mais, consulte [recursos de script de implantação de limpeza](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma identidade gerenciada atribuída pelo usuário com a função do contribuinte para o grupo de recursos-destino**. Essa identidade é usada para executar scripts de implantação. Para realizar operações fora do grupo de recursos, você precisa conceder permissões adicionais. Por exemplo, atribua a identidade ao nível de assinatura se quiser criar um novo grupo de recursos.

  > [!NOTE]
  > O mecanismo de script de implantação cria uma conta de armazenamento e uma instância de contêiner em segundo plano.  Uma identidade gerenciada atribuída pelo usuário com a função do contribuinte no nível de assinatura é necessária se a assinatura não tiver registrado os provedores de recursos da conta de armazenamento Azure (Microsoft.Storage) e do Azure (Microsoft.ContainerInstance).

  Para criar uma identidade, consulte [Criar uma identidade gerenciada atribuída pelo usuário usando o portal Azure,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)ou [usando o Azure CLI,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)ou [usando o Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Você precisa da ID da identidade ao implantar o modelo. O formato da identidade é:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Use o seguinte script CLI ou PowerShell para obter o ID fornecendo o nome do grupo de recursos e o nome da identidade.

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

- **Azure PowerShell** ou **Azure CLI**. Para obter uma lista de versões suportadas do Azure PowerShell, consulte [aqui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list); para obter uma lista de versões cli suportadas do Azure, veja [aqui](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > O script de implantação usa as imagens CLI disponíveis do Microsoft Container Registry(MCR). Leva cerca de um mês para certificar uma imagem CLI para script de implantação. Não use as versões CLI que foram lançadas dentro de 30 dias. Para encontrar as datas de lançamento das imagens, consulte [as notas de versão do Azure CLI](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Se uma versão não suportada for usada, a mensagem de erro lista as versões suportadas.

    Você não precisa dessas versões para implantar modelos. Mas essas versões são necessárias para testar scripts de implantação localmente. Confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Você pode usar uma imagem Docker pré-configurada.  Consulte [Configurar ambiente de desenvolvimento](#configure-development-environment).

## <a name="sample-templates"></a>Modelos de exemplo

O seguinte json é um exemplo.  O último esquema de modelo pode ser encontrado [aqui](/azure/templates/microsoft.resources/deploymentscripts).

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
> O exemplo é para fins de demonstração.  **scriptConteúdo** e **scriptScriptUris não** podem coexistir em um modelo.

Detalhes do valor da propriedade:

- **Identidade**: O serviço de script de implantação usa uma identidade gerenciada atribuída pelo usuário para executar os scripts. Atualmente, apenas a identidade gerenciada atribuída pelo usuário é suportada.
- **tipo**: Especifique o tipo de script. Atualmente, os scripts Azure PowerShell e Azure CLI são suportados. Os valores são **AzurePowerShell** e **AzureCLI**.
- **forceUpdateTag**: Alterar esse valor entre implantações de modelo força o script de implantação a ser reexecutado. Use a função newGuid() ou utcNow() que precisa ser definida como padrãoValor de um parâmetro. Para saber mais, confira [Executar script mais de uma vez](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: Especifique a versão do módulo a ser usada. Para obter uma lista de versões powershell e CLI suportadas, consulte [Pré-requisitos](#prerequisites).
- **argumentos**: Especifique os valores dos parâmetros. os valores são separados por espaços.
- **ambienteVariáveis**: Especifique as variáveis de ambiente para passar para o script. Para obter mais informações, consulte [Desenvolver scripts de implantação](#develop-deployment-scripts).
- **scriptContent**: Especifique o conteúdo do script. Para executar um script `primaryScriptUri` externo, use em vez disso. Por exemplo, consulte [Usar script inline](#use-inline-scripts) e [Usar script externo](#use-external-scripts).
- **primaryScriptUri**: Especifique uma Url acessível ao público para o script de implantação principal com extensões de arquivo suportadas.
- **suporteScriptUris**: Especifique uma matriz de Urls acessíveis ao público para suportar arquivos que são chamados em ambos `ScriptContent` ou `PrimaryScriptUri`.
- **tempo limite**: Especifique o tempo máximo de execução do script especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D**.
- **limpezaPreferência**. Especifique a preferência de limpar os recursos de implantação quando a execução do script chegar em um estado de terminal. A configuração padrão é **Always**, o que significa excluir os recursos apesar do estado terminal (Bem sucedido, falhou, cancelou). Para saber mais, confira [Limpar recursos do script de implantação](#clean-up-deployment-script-resources).
- **retentionInterval**: Especifique o intervalo para o qual o serviço retém os recursos do script de implantação após a execução do script de implantação atingir um estado de terminal. Os recursos do script de implantação serão excluídos quando essa duração expirar. A duração é baseada no [padrão ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). O valor padrão é **P1D,** o que significa sete dias. Essa propriedade é usada quando cleanupPreference está configurado como *OnExpiration*. A propriedade *OnExpiration* não está habilitada no momento. Para saber mais, confira [Limpar recursos do script de implantação](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Outros exemplos

- [criar e atribuir um certificado a um cofre de chaves](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [criar e atribuir uma identidade gerenciada atribuída pelo usuário a um grupo de recursos e executar um script de implantação](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Recomenda-se criar uma identidade atribuída pelo usuário e conceder permissões com antecedência. Você pode obter erros relacionados ao login e à permissão se criar a identidade e conceder permissões no mesmo modelo onde executa scripts de implantação. Leva algum tempo até que as permissões se tornem eficazes.

## <a name="use-inline-scripts"></a>Use scripts inline

O modelo a seguir tem `Microsoft.Resources/deploymentScripts` um recurso definido com o tipo. A parte destacada é o roteiro inline.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Como os scripts de implantação embutidos são colocados entre aspas duplas, as cadeias de caracteres dentro dos scripts de implantação precisam ser colocadas entre aspas simples. O caractere de escape do PowerShell é **&#92;**. Você também pode considerar o uso de substituição de string como é mostrado na amostra JSON anterior. Veja o valor padrão do parâmetro nome.

O script pega um parâmetro e produz o valor do parâmetro. **DeploymentScriptOutputs** é usado para armazenar saídas.  Na seção saídas, a linha **de valor** mostra como acessar os valores armazenados. `Write-Output`é usado para fins de depuração. Para saber como acessar o arquivo de saída, consulte [Depurar scripts de implantação](#debug-deployment-scripts).  Para obter as descrições da propriedade, consulte [Modelos de amostra](#sample-templates).

Para executar o script, **selecione Tente** abrir o Cloud Shell e cole o seguinte código no painel shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

A saída se parece com isso:

![Desenvolvimento de modelo de gerenciamento de recursos script hello world output](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Use scripts externos

Além de scripts inline, você também pode usar arquivos de script externos. Apenas scripts powershell primários com a extensão de arquivo **ps1** são suportados. Para scripts CLI, os scripts principais podem ter quaisquer extensões (ou sem uma extensão), desde que os scripts sejam scripts de bash válidos. Para usar arquivos de `scriptContent` `primaryScriptUri`script externos, substitua por . Por exemplo: 

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Para ver um exemplo, selecione [aqui](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Os arquivos de script externos devem estar acessíveis.  Para proteger os arquivos de script armazenados em contas de armazenamento do Azure, consulte [Tutorial: Secure artifacts in Azure Resource Manager implantações de modelos](./template-tutorial-secure-artifacts.md).

## <a name="use-supporting-scripts"></a>Use scripts de suporte

Você pode separar lógicas complicadas em um ou mais arquivos de script de suporte. A `supportingScriptURI` propriedade permite que você forneça uma matriz de URIs aos arquivos de script de suporte, se necessário:

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

Arquivos de script de suporte podem ser chamados tanto de scripts inline quanto de arquivos de script primários. Os arquivos de script de suporte não têm restrições na extensão do arquivo.

Os arquivos de suporte são copiados para azscripts/azscriptinput no tempo de execução. Use caminho relativo para referenciar os arquivos de suporte de scripts inline e arquivos de script primários.

## <a name="work-with-outputs-from-powershell-script"></a>Trabalhe com saídas do script PowerShell

O modelo a seguir mostra como passar valores entre dois recursos do DeploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

No primeiro recurso, você define uma variável chamada **$DeploymentScriptOutputs**e a usa para armazenar os valores de saída. Para acessar o valor de saída de outro recurso dentro do modelo, use:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Trabalhe com saídas do script CLI

Diferente do script de implantação do PowerShell, o suporte cli/bash não expõe uma variável comum para armazenar saídas de script, em vez disso, há uma variável de ambiente chamada **AZ_SCRIPTS_OUTPUT_PATH** que armazena o local onde o arquivo de saídas de script reside. Se um script de implantação for executado a partir de um modelo de Gerenciador de recursos, essa variável de ambiente será definida automaticamente para você pelo shell Bash.

As saídas de script de implantação devem ser salvas no local AZ_SCRIPTS_OUTPUT_PATH e as saídas devem ser um objeto de seqüência JSON válido. O conteúdo do arquivo deve ser salvo como um par de valor-chave. Por exemplo, uma matriz de strings é armazenada como { "MyResult": [ "foo", "bar"] }.  Armazenar apenas os resultados da matriz, por exemplo [ "foo", "bar" ], é inválido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) é usado na amostra anterior. Vem com as imagens do contêiner. Consulte [Configurar ambiente de desenvolvimento](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Desenvolver scripts de implantação

### <a name="handle-non-terminating-errors"></a>Lidar com erros não terminativos

Você pode controlar como o PowerShell responde a erros não terminais usando a variável [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) em seu script de implantação. O mecanismo de script de implantação não define/altera o valor.  Apesar do valor definido para $ErrorActionPreference, o script de implantação define o estado de provisionamento de recursos *como Falha* quando o script encontra um erro.

### <a name="pass-secured-strings-to-deployment-script"></a>Passe strings seguras para o script de implantação

A definição de variáveis de ambiente (EnvironmentVariable) em suas instâncias de contêiner permite que você forneça configuração dinâmica do aplicativo ou script executado pelo contêiner. O script de implantação lida com variáveis de ambiente não protegidas e protegidas da mesma forma que a Instância de Contêiner do Azure. Para obter mais informações, consulte [Definir variáveis de ambiente em instâncias de contêiner](../../container-instances/container-instances-environment-variables.md#secure-values).

## <a name="debug-deployment-scripts"></a>Scripts de implantação de depuração

O serviço de script cria uma [conta de armazenamento](../../storage/common/storage-account-overview.md) e uma instância de [contêiner](../../container-instances/container-instances-overview.md) para execução de script. Ambos os recursos têm o sufixo **azscripts** nos nomes dos recursos.

![Nomes de recursos de implantação de script do Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

O script do usuário, os resultados da execução e o arquivo stdout são armazenados nos arquivos compartilhada conta de armazenamento. Há uma pasta chamada **azscripts.** Na pasta, há mais duas pastas para a entrada e os arquivos de saída: **azscriptinput** e **azscriptoutput**.

A pasta de saída contém um **executionresult.json** e o arquivo de saída de script. Você pode ver a mensagem de erro de execução do script em **executionresult.json**. O arquivo de saída é criado somente quando o script é executado com sucesso. A pasta de entrada contém um arquivo de script do PowerShell do sistema e os arquivos de script da implantação do usuário. Você pode substituir o arquivo de script de implantação do usuário por um revisado e executar o script de implantação a partir da instância de contêiner Dozure.

Você pode obter as informações de implantação de recursos de script de implantação no nível do grupo de recursos e no nível de assinatura usando a API REST:

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

A saída mostra o estado de implantação e os IDs de recurso de script de implantação.

A API REST a seguir retorna o registro:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Ele só funciona antes que os recursos do script de implantação sejam excluídos.

Para ver o recurso deploymentScripts no portal, selecione **Mostrar tipos ocultos:**

![Script de implantação de modelo do Gerenciador de recursos, mostrar tipos ocultos, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Limpar os recursos do script de implantação

O script de implantação cria uma conta de armazenamento e uma instância de contêiner que são usadas para executar scripts de implantação e armazenar informações de depuração. Esses dois recursos são criados no mesmo grupo de recursos que os recursos provisionados e serão excluídos pelo serviço de script quando o script expirar. Você pode controlar o ciclo de vida desses recursos.  Até que eles sejam excluídos, você é cobrado por ambos os recursos. Para obter as informações de preço, consulte [os preços das instâncias de contêiner](https://azure.microsoft.com/pricing/details/container-instances/) e os preços do Armazenamento [Azure](https://azure.microsoft.com/pricing/details/storage/).

O ciclo de vida desses recursos é controlado pelas seguintes propriedades no modelo:

- **cleanupPreference**: Limpe a preferência quando a execução do script chegar em um estado terminal.  Os valores com suporte são:

  - **Sempre**: Exclua os recursos assim que a execução do script chegar em um estado terminal. Uma vez que o recurso deploymentScripts ainda pode estar presente depois que os recursos são limpos, o script do sistema copiaria os resultados da execução do script, por exemplo, stdout, saídas, valor de retorno, etc. para DB antes que os recursos sejam excluídos.
  - **OnSuccess**: Exclua os recursos somente quando a execução do script for bem sucedida. Você ainda pode acessar os recursos para encontrar as informações de depuração.
  - **OnExpir**: Exclua os recursos somente quando a configuração **retentionInterval** for expirada. Esta propriedade está atualmente desativada.

- **retentionInterval**: Especifique o intervalo de tempo que um recurso de script será retido e depois será expirado e excluído.

> [!NOTE]
> Não é recomendável usar os recursos do script de implantação para outros fins.

## <a name="run-script-more-than-once"></a>Executar script mais de uma vez

A execução do script de implantação é uma operação idempotente. Se nenhuma das propriedades de recurso deploymentScripts (incluindo o script inline) for alterada, o script não será executado quando você reimplantar o modelo. O serviço de script de implantação compara os nomes de recursos no modelo com os recursos existentes no mesmo grupo de recursos. Existem duas opções se você quiser executar o mesmo script de implantação várias vezes:

- Alterar o nome do recurso deploymentScripts. Por exemplo, use a função de modelo [utcNow](./template-functions-string.md#utcnow) como o nome do recurso ou como parte do nome do recurso. Alterar o nome do recurso cria um novo recurso deploymentScripts. É bom para manter uma história de execução de roteiro.

    > [!NOTE]
    > A função utcNow só pode ser usada no valor padrão de um parâmetro.

- Especifique um `forceUpdateTag` valor diferente na propriedade do modelo.  Por exemplo, use utcNow como valor.

> [!NOTE]
> Escreva os scripts de implantação que são impotentes. Isso garante que, se eles forem executados novamente acidentalmente, não causará alterações no sistema. Por exemplo, se o script de implantação for usado para criar um recurso do Azure, verifique se o recurso não existe antes de criá-lo, então o script terá sucesso ou você não criará o recurso novamente.

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

Você pode usar uma imagem de contêiner docker pré-configurada como seu ambiente de desenvolvimento de script de implantação. O procedimento a seguir mostra como configurar a imagem do docker no Windows. Para Linux e Mac, você pode encontrar as informações na Internet.

1. Instale [o Docker Desktop](https://www.docker.com/products/docker-desktop) no seu computador de desenvolvimento.
1. Abra o Docker Desktop.
1. Selecione o ícone do Docker Desktop nas barras de tarefas e, em seguida, selecione **Configurações**.
1. Selecione **Unidades compartilhadas,** selecione uma unidade local que você deseja estar disponível em seus contêineres e, em seguida, selecione **Aplicar**

    ![Unidade de implantação de script de implantação do gerenciador de recursos](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Digite suas credenciais de janelas no prompt.
1. Abra uma janela de terminal, seja o Prompt de Comando ou o Windows PowerShell (Não use o PowerShell ISE).
1. Puxe a imagem do contêiner de script de implantação para o computador local:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    O exemplo usa a versão PowerShell 2.7.0.

    Para puxar uma imagem CLI de um Microsoft Container Registry (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Este exemplo usa a versão CLI 2.0.80. O script de implantação usa as imagens padrão de contêineres CLI encontradas [aqui](https://hub.docker.com/_/microsoft-azure-cli).

1. Execute a imagem do docker localmente.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Substitua ** &lt;o>de>** de driver host e ** &lt;o nome do diretório do host>** por uma pasta existente na unidade compartilhada.  Ele mapeia a pasta para a pasta **/data** no contêiner. Para exemplos, para mapear D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-** significa manter a imagem do recipiente viva.

    Um exemplo da CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Selecione **Compartilhá-lo** quando receber um prompt.
1. A captura de tela a seguir mostra como executar um script PowerShell, dado que você tem um arquivo helloworld.ps1 na pasta d:\docker.

    ![Gerenciamento de recursos de implantação de script de implantação do docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Depois que o script for testado com sucesso, você pode usá-lo como um script de implantação.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar scripts de implantação. Para passar por um tutorial de script de implantação:

> [!div class="nextstepaction"]
> [Tutorial: Use scripts de implantação em modelos do Azure Resource Manager](./template-tutorial-deployment-script.md)