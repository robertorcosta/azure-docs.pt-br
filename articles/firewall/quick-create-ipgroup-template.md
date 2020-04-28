---
title: 'Início Rápido: Criar um Firewall do Azure e Grupos de IP – modelo do Resource Manager'
description: Saiba como usar um modelo do Resource Manager para criar um Firewall do Azure e Grupos de IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 53e5bf4f770ce986af2f3572bd6c1ef4cd9e3c2b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605241"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Início Rápido: Criar um Firewall do Azure e Grupos de IP – modelo do Resource Manager

Neste início rápido, você usará um modelo do Resource Manager para implantar um Firewall do Azure com os Grupos de IP de exemplo usados em uma regra de rede e em uma regra de aplicativo.

Um Grupo de IP é um recurso de nível superior que permite que você defina e agrupe endereços IP, intervalos e sub-redes em um só objeto. Isso é útil para o gerenciamento de endereços IP em regras do Firewall do Azure. Você pode inserir manualmente os endereços IP ou importá-los de um arquivo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-and-ip-groups"></a>Criar um Firewall do Azure e Grupos de IP

Este modelo cria um Firewall do Azure e Grupos de IP, juntamente com os recursos necessários para dar suporte ao Firewall do Azure.

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo do Resource Manager no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria um Firewall do Azure, a infraestrutura de rede e duas máquinas virtuais.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. No portal, na página **Criar um Firewall do Azure com IpGroups**, digite ou selecione os seguintes valores:
   - Assinatura: selecione entre as assinaturas existentes 
   - Grupo de recursos:  selecione um dos grupos de recursos existentes ou selecione **Criar** e selecione **OK**.
   - Localização: Selecione um local
   - Nome da Rede Virtual: digite um nome para a nova VNet (rede virtual) 
   - Nome do Grupo de IP 1: digite o nome do Grupo de IP 1 
   - Nome do Grupo de IP 2: digite o nome do Grupo de IP 2 
   - Nome de usuário do administrador: digite o nome de usuário para a conta de usuário administrador 
   - Autenticação: selecione sshPublicKey ou a senha 
   - Senha do Administrador: digite uma senha de administrador ou chave

3. Selecione **Concordo com os termos e condições declarados acima** e selecione **Comprar**. A implantação pode levar 10 minutos ou mais para ser concluída.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

No portal do Azure, examine os recursos implantados, principalmente as regras de firewall que usam Grupos de IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Grupos de IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Regras de rede.":::

Para saber mais sobre a sintaxe e as propriedades de JSON para um firewall em um modelo, confira [referência de modelo Microsoft.Network/azureFirewalls](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o firewall, exclua o grupo de recursos. Isso remove o firewall e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure](tutorial-hybrid-portal.md)