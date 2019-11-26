---
title: Criar uma conta de armazenamento do Azure Data Lake Storage Gen2 | Microsoft Docs
description: Aprenda rapidamente criar uma nova conta de armazenamento com acesso ao Data Lake Storage Gen2 usando o portal do Azure, o Azure PowerShell ou a CLI do Azure.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 57350bd00a33c36b5aef3a0ccd3034b4db3d2c55
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227962"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Criar uma conta de armazenamento do Azure Data Lake Storage Gen2

O Azure Data Lake Storage Gen2 [dá suporte a um namespace hierárquico](data-lake-storage-introduction.md) que fornece um contêiner baseado em diretórios nativo sob medida para funcionar com o HDFS (Sistema de Arquivos Distribuído Hadoop). O acesso aos dados do Armazenamento do Data Lake Gen2 pelo HDFS está disponível por meio do [driver ABFS](data-lake-storage-abfs-driver.md).

Este artigo demonstra como criar uma conta usando o portal do Azure, Azure PowerShell ou por meio do CLI do Azure.

## <a name="prerequisites"></a>pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

|           | Pré-requisito |
|-----------|--------------|
|Portal     | nenhum         |
|PowerShell | Este artigo requer o módulo do PowerShell AZ. Storage versão **0,7** ou posterior. Para localizar a versão atual, execute o comando `Get-Module -ListAvailable Az.Storage`. Se, após a execução desse comando, nenhum resultado for exibido ou se uma versão inferior a **0.7** for exibida, você precisará atualizar o módulo do PowerShell. Confira a seção [Atualizar seu módulo PowerShell](#upgrade-your-powershell-module) deste guia.
|CLI        | Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir: <ul><li>Você pode executar comandos da CLI de dentro do portal do Azure, no Azure Cloud Shell </li><li>Você pode instalar a CLI e executar comandos da CLI localmente</li></ul>|

Ao trabalhar na linha de comando, você tem a opção de executar o Azure Cloud Shell ou instalar a CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Clique no botão **Cloud Shell** no menu no canto superior direito do portal do Azure:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas neste artigo:

[![Captura de tela mostrando a janela do Cloud Shell no portal](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Este artigo requer que você esteja executando o CLI do Azure versão 2.0.38 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Criar uma conta de armazenamento com o Azure Data Lake Storage Gen2 habilitado

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do armazenamento do Azure que podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. Os dados em sua conta de armazenamento do Azure são duráveis e altamente disponíveis, seguros e amplamente escalonáveis.

> [!NOTE]
> Você deve criar novas contas de armazenamento com o tipo **StorageV2 (uso geral V2)** para tirar proveito dos recursos de Armazenamento do Data Lake Gen2.  

Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de Armazenamento do Azure](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Criar uma conta usando o portal do Azure

Entre no [Portal do Azure](https://portal.azure.com).

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Para criar uma conta de armazenamento de uso geral v2 no portal do Azure, siga estas etapas:

> [!NOTE]
> O namespace hierárquico está disponível atualmente em todas as regiões públicas.

1. Escolha a assinatura na qual você deseja criar a conta de armazenamento.
2. Na portal do Azure, escolha o botão **criar um recurso** e, em seguida, escolha **conta de armazenamento**.
3. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para o novo grupo de recursos.
   
   Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente.

4. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
5. Escolha um local.
6. Verifique se **StorageV2 (uso geral v2)** aparece como selecionado na lista suspensa **tipo de conta** .
7. Opcionalmente, altere os valores em cada um desses campos: **desempenho**, **replicação**, **camada de acesso**. Para saber mais sobre essas opções, consulte [introdução ao armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. Escolha a guia **avançado** .
10. Na seção **Data Lake Storage Gen2**, defina **Namespace hierárquico** como **Habilitado**.
11. Clique em **Revisar + Criar** para criar a conta de armazenamento.

Sua conta de armazenamento é criada por meio do portal.

### <a name="clean-up-resources"></a>Limpar recursos

Para remover um grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Grupo de Recursos** para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão **Mais** ( **...** ) do lado direito da lista.
3. Selecione **Excluir grupo de recursos** e confirme.

## <a name="create-an-account-using-powershell"></a>Criar uma conta usando o PowerShell

Primeiro, instale a última versão do módulo [PowerShellGet](/powershell/scripting/gallery/installing-psget) do PowerShell.

Em seguida, atualize seu módulo PowerShell, entre em sua assinatura do Azure, crie um grupo de recursos e, em seguida, crie uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualizar seu módulo do powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para interagir com o Data Lake Storage Gen2 usando o PowerShell, será necessário instalar o módulo Az.Storage versão **0.7** ou posterior.

Comece abrindo uma sessão do PowerShell com permissões elevadas.

Instalar o módulo Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua Assinatura do Azure

Use o comando `Login-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, use o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup): 

> [!NOTE]
> O namespace hierárquico está disponível atualmente em todas as regiões públicas.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento de uso geral v2

Para criar uma conta de armazenamento de uso geral v2 do PowerShell com armazenamento com redundância local (LRS), use o comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount):

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Criar uma conta usando a CLI do Azure

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Se desejar entrar na instalação local da CLI, execute o comando de logon:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Adicionar a extensão da CLI para o Azure Data Lake Gen 2

Para interagir com o Data Lake Storage Gen2 usando a CLI, será necessário adicionar uma extensão ao seu shell.

Para fazer isso, insira o seguinte comando usando o Cloud Shell ou um shell local: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com a CLI do Azure, use o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > O namespace hierárquico está disponível atualmente em todas as regiões públicas.

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento de uso geral v2

Para criar uma conta de armazenamento de uso geral v2 a partir da CLI do Azure com armazenamento com redundância local, use o comando [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma conta de armazenamento com recursos de Data Lake Storage Gen2. Para saber como carregar e baixar blobs para e da sua conta de armazenamento, confira o tópico a seguir.

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
