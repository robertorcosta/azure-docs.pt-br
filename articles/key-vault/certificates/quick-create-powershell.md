---
title: 'Início Rápido: Definir e exibir certificados do Azure Key Vault – Azure PowerShell'
description: Início Rápido que mostra como definir e recuperar um certificado do Azure Key Vault usando o Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ff618024c269a010eddf08128e827ade7921c94
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940592"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Início Rápido: Definir e recuperar um certificado do Azure Key Vault usando o Azure PowerShell

Neste início rápido, você criará um cofre de chaves no Azure Key Vault com o Azure PowerShell. O Azure Key Vault é um serviço de nuvem que funciona como um repositório de segredos seguro. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Para saber mais sobre o Key Vault, reveja a [Visão geral](../general/overview.md). O Azure PowerShell é usado para criar e gerenciar recursos do Azure usando comandos ou scripts. Depois de concluir isso, você armazenará um certificado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Digite `$PSVersionTable.PSVersion` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzAccount` para criar uma conexão com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Agora, você criará um Key Vault. Ao realizar esta etapa, você precisará de algumas informações:

Embora usemos "Contoso KeyVault2" como o nome do nosso Key Vault em todo este início rápido, você precisará usar um nome exclusivo.

- **Nome do cofre** Contoso-Vault2.
- **Nome do grupo de recursos** ContosoResourceGroup.
- **Local:** Leste dos EUA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

A saída desse cmdlet mostra as propriedades do cofre de chaves criado recentemente. Anote as duas propriedades listadas abaixo:

* **Nome do cofre**: no exemplo, é **Contoso-Vault2**. Você usará esse nome para outros cmdlets do Cofre da Chave.
* **URI do cofre**: neste exemplo, é https://Contoso-Vault2.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Após a criação do cofre, sua conta do Azure é a única conta que pode fazer algo nesse novo cofre.

## <a name="add-a-certificate-to-key-vault"></a>Adicionar um certificado ao Key Vault

Para adicionar um certificado ao cofre, basta executar algumas etapas adicionais. Esse certificado pode ser usado por um aplicativo. 

Digite os comandos abaixo para criar um certificado autoassinado com uma política chamada **ExampleCertificate**:

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal
Add-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Agora você pode referenciar esse certificado que foi adicionado ao Azure Key Vault usando o respectivo URI. Use **'https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate '** para obter a versão atual. 

Para ver o certificado armazenado anteriormente:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate"
```

Agora, você criou um Key Vault, armazenou um certificado e o recuperou.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando o grupo de recursos e todos os recursos relacionados não forem mais necessários, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para removê-los. Você pode excluir os recursos da seguinte maneira:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Confira a referência dos [cmdlets do Key Vault do Azure PowerShell](/powershell/module/az.keyvault/)
- Examine as [Melhores práticas do Azure Key Vault](../general/best-practices.md)
