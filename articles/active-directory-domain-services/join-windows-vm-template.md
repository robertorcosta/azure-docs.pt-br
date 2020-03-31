---
title: Use um modelo para juntar uma VM do Windows ao Azure AD DS | Microsoft Docs
description: Aprenda a usar os modelos do Azure Resource Manager para juntar uma VM do Windows Server nova ou existente a um domínio gerenciado do Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: iainfou
ms.openlocfilehash: c9b25fe7bc47e05972aebb194e9d94c1ea6dd247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298727"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Junte uma máquina virtual do Windows Server a um domínio gerenciado do Azure Active Directory Domain Services usando um modelo de Gerenciador de recursos

Para automatizar a implantação e configuração de VMs (Azure, máquinas virtuais do Azure), você pode usar um modelo de Gerenciador de recursos. Esses modelos permitem que você crie implantações consistentes cada vez. As extensões também podem ser incluídas nos modelos para configurar automaticamente uma VM como parte da implantação. Uma extensão útil une VMs a um domínio, que pode ser usado com domínios gerenciados do Azure Active Directory Domain Services (Azure AD DS).

Este artigo mostra como criar e juntar uma VM do Windows Server a um domínio gerenciado pelo Azure AD DS usando modelos do Gerenciador de recursos. Você também aprende como juntar uma VM do Windows Server existente a um domínio Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que faz parte do domínio gerenciado pelo Azure AD DS.

## <a name="azure-resource-manager-template-overview"></a>Visão geral do modelo do Azure Resource Manager

Os modelos do Gerenciador de recursos permitem definir a infra-estrutura do Azure em código. Os recursos necessários, conexões de rede ou configuração de VMs podem ser definidos em um modelo. Esses modelos criam implantações consistentes e reprodutíveis cada vez, e podem ser versões à medida que você faz alterações. Para obter mais informações, consulte [a visão geral dos modelos do Azure Resource Manager][template-overview].

Cada recurso é definido em um modelo usando A notação de objeto JavaScript (JSON). O exemplo json a seguir usa o tipo de recurso *Microsoft.Compute/virtualMachines/extensions* para instalar a extensão de adesão ao domínio Active Directory. Os parâmetros são usados que você especifica no momento da implantação. Quando a extensão é implantada, a VM é juntada ao domínio gerenciado do Azure AD DS especificado.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Esta extensão vm pode ser implantada mesmo se você não criar uma VM no mesmo modelo. Os exemplos deste artigo mostram as duas abordagens a seguir:

* [Crie uma VM do Windows Server e junte-se a um domínio gerenciado](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Unir uma VM do Windows Server existente em um domínio gerenciado](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Crie uma VM do Windows Server e junte-se a um domínio gerenciado

Se você precisar de uma VM do Windows Server, você pode criar e configurar um usando um modelo de Gerenciador de recursos. Quando a VM é implantada, uma extensão é instalada para se juntar à VM a um domínio gerenciado pelo Azure AD DS. Se você já tiver uma VM que deseja aderir a um domínio gerenciado pelo Azure AD DS, pule para [Juntar uma VM do Windows Server existente para um domínio gerenciado](#join-an-existing-windows-server-vm-to-a-managed-domain).

Para criar uma VM do Windows Server, junte-a a um domínio gerenciado pelo Azure AD DS, complete as seguintes etapas:

1. Navegue até o [modelo quickstart](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Selecione a opção **para implantar no Azure**.
1. Na página **de implantação Personalizada,** digite as seguintes informações para criar e junte uma VM do Windows Server ao domínio gerenciado pelo Azure AD DS:

    | Configuração                   | Valor |
    |---------------------------|-------|
    | Subscription              | Escolha a mesma assinatura do Azure em que você habilitou o Azure AD Domain Services. |
    | Resource group            | Escolha o grupo de recursos para sua VM. |
    | Location                  | Selecione a localização de sua VM. |
    | Nome VNET existente        | O nome da rede virtual existente para conectar a VM, como *myVnet*. |
    | Nome da sub-rede existente      | O nome da sub-rede virtual existente, como *Cargas de Trabalho*. |
    | Prefixo de etiqueta DNS          | Digite um nome DNS para usar na VM, como *myvm*. |
    | Tamanho da VM                   | Especifique um tamanho de VM, como *Standard_DS2_v2*. |
    | Domínio para participar            | O Azure AD DS gerenciou o nome DNS de domínio, como *aaddscontoso.com*. |
    | Nome de usuário de domínio           | A conta de usuário no domínio gerenciado do Azure AD DS que deve ser `contosoadmin@aaddscontoso.com`usado para juntar a VM ao domínio gerenciado, tais como . Essa conta deve fazer parte do domínio gerenciado pelo Azure AD DS. |
    | Senha de domínio           | A senha da conta de usuário especificada na configuração anterior. |
    | Caminho ou opcional          | O OU personalizado para adicionar o VM. Se você não especificar um valor para este parâmetro, a VM será adicionada ao OU *computadores AAD DC* padrão. |
    | Nome de usuário do VM Admin         | Especifique uma conta de administrador local para criar na VM. |
    | VM Admin Password         | Especifique uma senha de administrador local para a VM. Crie uma senha de administrador local forte para proteger contra ataques de força bruta de senha. |

1. Revise os termos e condições e, em seguida, verifique a caixa para **que eu concorde com os termos e condições indicados acima**. Quando estiver pronto, **selecione Comprar** para criar e juntar a VM ao domínio gerenciado pelo Azure AD DS.

> [!WARNING]
> **Trate as senhas com cuidado.**
> O arquivo parâmetro de modelo solicita a senha de uma conta de usuário que faz parte do domínio gerenciado pelo Azure AD DS. Não digite manualmente valores neste arquivo e deixe-o acessível em compartilhamentos de arquivos ou outros locais compartilhados.

Leva alguns minutos para a implantação ser concluída com sucesso. Quando concluído, o Windows VM é criado e unido ao domínio gerenciado pelo Azure AD DS. O VM pode ser gerenciado ou conectado usando contas de domínio.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Unir uma VM do Windows Server existente em um domínio gerenciado

Se você tiver uma VM existente ou um grupo de VMs, que deseja aderir a um domínio gerenciado pelo Azure AD DS, você poderá usar um modelo de Gerenciador de recursos para simplesmente implantar a extensão VM.

Para juntar uma VM do Windows Server existente a um domínio gerenciado pelo Azure AD DS, complete as seguintes etapas:

1. Navegue até o [modelo quickstart](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Selecione a opção **para implantar no Azure**.
1. Na página **de implantação Personalizada,** digite as seguintes informações para se juntar à VM ao domínio gerenciado pelo Azure AD DS:

    | Configuração                   | Valor |
    |---------------------------|-------|
    | Subscription              | Escolha a mesma assinatura do Azure em que você habilitou o Azure AD Domain Services. |
    | Resource group            | Escolha o grupo de recursos com sua VM existente. |
    | Location                  | Selecione a localização da VM existente. |
    | Lista vm                   | Digite a lista separada por comma das VM(s) existentes para se juntar ao domínio gerenciado pelo Azure AD DS, como *myVM1,myVM2*. |
    | Domínio Juntar nome de usuário     | A conta de usuário no domínio gerenciado do Azure AD DS que deve ser `contosoadmin@aaddscontoso.com`usado para juntar a VM ao domínio gerenciado, tais como . Essa conta deve fazer parte do domínio gerenciado pelo Azure AD DS. |
    | Domínio Juntar senha de usuário | A senha da conta de usuário especificada na configuração anterior. |
    | Caminho ou opcional          | O OU personalizado para adicionar o VM. Se você não especificar um valor para este parâmetro, a VM será adicionada ao OU *computadores AAD DC* padrão. |

1. Revise os termos e condições e, em seguida, verifique a caixa para **que eu concorde com os termos e condições indicados acima**. Quando estiver pronto, selecione **Comprar** para juntar a VM ao domínio gerenciado pelo Azure AD DS.

> [!WARNING]
> **Trate as senhas com cuidado.**
> O arquivo parâmetro de modelo solicita a senha de uma conta de usuário que faz parte do domínio gerenciado pelo Azure AD DS. Não digite manualmente valores neste arquivo e deixe-o acessível em compartilhamentos de arquivos ou outros locais compartilhados.

Leva alguns momentos para a implantação ser concluída com sucesso. Quando concluídas, as VMs do Windows especificadas são unidas ao domínio gerenciado pelo Azure AD DS e podem ser gerenciadas ou assinadas usando contas de domínio.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você usou o portal Azure para configurar e implantar recursos usando modelos. Você também pode implantar recursos com modelos do Gerenciador de Recursos usando [o Azure PowerShell][deploy-powershell] ou o [Azure CLI][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
