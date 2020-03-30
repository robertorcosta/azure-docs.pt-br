---
title: Exija transferência segura para garantir conexões seguras
titleSuffix: Azure Storage
description: Saiba como exigir transferência segura para solicitações ao Azure Storage. Quando você exige transferência segura para uma conta de armazenamento, quaisquer solicitações originárias de uma conexão insegura são rejeitadas.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 3b2d78bd929e23d49a57f337022f6678114bb5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457435"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Exija transferência segura para garantir conexões seguras

Você pode configurar sua conta de armazenamento para aceitar solicitações de conexões seguras apenas definindo a propriedade **de transferência Segura necessária** para a conta de armazenamento. Quando você exige transferência segura, quaisquer solicitações originárias de uma conexão insegura são rejeitadas. A Microsoft recomenda que você sempre exija transferência segura para todas as suas contas de armazenamento.

Quando a transferência segura for necessária, uma chamada para uma operação de API Azure Storage REST deve ser feita via HTTPS. Qualquer solicitação feita sobre HTTP é rejeitada.

A conexão a um compartilhamento de arquivos do Azure sobre SMB sem criptografia falha quando a transferência segura é necessária para a conta de armazenamento. Exemplos de conexões inseguras incluem aquelas feitas sobre SMB 2.1, SMB 3.0 sem criptografia ou algumas versões do cliente Linux SMB.

Por padrão, a propriedade **De transferência Segura é** ativada quando você cria uma conta de armazenamento no portal Azure. No entanto, ele é desativado quando você cria uma conta de armazenamento com SDK.

> [!NOTE]
> Como o armazenamento do Azure não dá suporte a HTTPS para nomes de domínio personalizado, essa opção não será aplicada ao usar um nome de domínio personalizado. Não há suporte para contas de armazenamento clássicas.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Exigir transferência segura no portal Azure

Você pode ativar a propriedade **de transferência segura necessária** ao criar uma conta de armazenamento no portal [Azure](https://portal.azure.com). Você também pode habilitá-la para uma conta de armazenamento existente.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Requer transferência segura de uma conta de armazenamento nova

1. Abra o painel **Criar conta de armazenamento** no portal do Azure.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

   ![Criar uma folha da conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Requer transferência segura de uma conta de armazenamento existente

1. Selecionar uma conta de armazenamento existente no portal do Azure.
1. No painel do menu de conta de armazenamento, em **DEFINIÇÕES**, selecione **Configuração**.
1. Em **Transferência segura obrigatória**, selecione **habilitado**.

   ![Painel do menu da conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Requerem transferência segura do código

Para exigir transferência segura programáticamente, defina a propriedade _HttpsTrafficOnly_ na conta de armazenamento. Você pode definir essa propriedade usando a API do provedor de recursos de armazenamento REST, bibliotecas de clientes ou ferramentas:

* [REST API](/rest/api/storagerp/storageaccounts)
* [Powershell](/powershell/module/az.storage/set-azstorageaccount)
* [Cli](/cli/azure/storage/account)
* [Nodejs](https://www.npmjs.com/package/azure-arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [SDK do Python](https://pypi.org/project/azure-mgmt-storage)
* [SDK do Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Requerem transferência segura com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O exemplo requer o módulo Az do Azure PowerShell, versão 0.7 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

Execute `Connect-AzAccount` para criar uma conexão com o Azure.

 Use a linha de comando a seguir para verificar a configuração:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Use a linha de comando a seguir para habilitar a configuração:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Exigir transferência segura com o Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Use o seguinte comando para verificar a configuração:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Use o seguinte comando para ativar a configuração:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Próximas etapas

[Recomendações de segurança para armazenamento Blob](../blobs/security-recommendations.md)
