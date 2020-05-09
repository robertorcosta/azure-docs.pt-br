---
title: 'Início Rápido: Criar um Firewall do Azure com o Zonas de Disponibilidade – modelo do Resource Manager'
description: Implantar Firewall do Azure usando um modelo. A rede virtual tem uma VNet com três sub-redes. Duas máquinas virtuais do Windows Server são implantadas; uma jump box e um servidor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 17ab693033b61c25ba2f5b5bd588ef52caf8c046
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597698"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Início Rápido: Implantar o Firewall do Azure com o Zonas de Disponibilidade – modelo do Resource Manager

Neste guia de início rápido, você usa um modelo do Resource Manager para implantar um Firewall do Azure em três Zonas de Disponibilidade. 

O modelo cria um ambiente de rede de teste com um firewall. A rede tem uma rede virtual (VNet) com três sub-redes: *AzureFirewallSubnet*, *ServersSubnet* e *JumpboxSubnet*. A sub-rede *ServersSubnet* e *JumpboxSubnet* tem uma máquina virtual única de dois núcleos do Windows Server.

O firewall está na sub-rede *AzureFirewallSubnet* e tem uma coleção de regra de aplicativo com uma regra única que permite acesso a `www.microsoft.com`.

Uma rota definida pelo usuário que aponta o tráfego de rede da sub-rede *ServersSubnet* até o firewall, em que as regras de firewall são aplicadas.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Para obter mais informações sobre o Azure Firewall, consulte [Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-with-availability-zones"></a>Criar um Firewall do Azure com Zonas de Disponibilidade

Este modelo cria um Firewall do Azure com Zonas de Disponibilidade, juntamente com os recursos necessários para dar suporte ao Firewall do Azure.

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-with-zones-sandbox/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

Vários recursos do Azure são definidos no modelo:

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

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. No portal, na página **Criar uma configuração de área restrita do Firewall do Azure com Zonas**, digite ou selecione os seguintes valores:
   - **Grupo de recursos**: Selecione **Criar novo**, digite um nome para o grupo de recursos e selecione **OK**. 
   - **Nome da Rede Virtual**: Digite um nome para a nova VNet. 
   - **Nome de Usuário do Administrador**: Digite um nome de usuário para a conta de usuário administrador.
   - **Senha do Administrador**: Digite uma senha de administrador. 

3. Leia os termos e condições e, em seguida, selecione **Eu concordo com os termos e condições declarados acima** e então selecione **Comprar**. A implantação pode levar 10 minutos ou mais para ser concluída.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Explore os recursos criados com o firewall.

Saiba mais sobre a sintaxe e as propriedades de JSON para um firewall em um modelo em [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisa mais delas, você pode remover o grupo de recursos, o firewall e a todos os recursos relacionados, executando o comando `Remove-AzResourceGroup` do PowerShell. Para remover um grupo de recursos nomeado *MyResourceGroup*, execute: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Não remova o grupo de recursos e o firewall se planejar prosseguir para o firewall do tutorial de monitoramento. 

## <a name="next-steps"></a>Próximas etapas

Em seguida, é possível monitorar os logs do Firewall do Azure:

[Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
