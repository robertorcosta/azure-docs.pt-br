---
title: Implantar Firewall do Azure usando um modelo
description: Implantar Firewall do Azure usando um modelo
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/9/2018
ms.author: victorh
ms.openlocfilehash: c0a6cda54a58e3cc03ba31e221fb57fc725dd779
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839383"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Implantar Firewall do Azure usando um modelo

O modelo [Configuração de área restrita do AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) cria um ambiente de rede de teste com um firewall. A rede tem uma rede virtual (VNet) com três sub-redes: *AzureFirewallSubnet*, *ServersSubnet*e *JumpboxSubnet*. A sub-rede *ServersSubnet* e *JumpboxSubnet* tem uma máquina virtual única de dois núcleos do Windows Server.

O firewall está na sub-rede *AzureFirewallSubnet* e tem uma coleção de regras de aplicativo com uma única regra que permite o acesso a `www.microsoft.com`.

Uma rota definida pelo usuário que aponta o tráfego de rede da sub-rede *ServersSubnet* até o firewall, em que as regras de firewall são aplicadas.

Para obter mais informações sobre o Azure Firewall, consulte [Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Use o modelo para implantar o Firewall do Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

**Para instalar e implantar o Firewall do Azure usando o modelo:**

1. Acessar o modelo no [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Leia a introdução e quando estiver pronto para implantar, selecione **Implantar no Azure**.
   
1. Entre no portal do Azure, se necessário. 

1. No portal, na página**Criar uma configuração de área restrita do AzureFirewall**, digite ou selecione os seguintes valores:
   
   - **Grupo de recursos**: selecione **criar novo**, digite um nome para o grupo de recursos e selecione **OK**. 
   - **Nome da rede virtual**: digite um nome para a nova VNet. 
   - **Nome**de usuário do administrador: digite um nome de usuário para a conta do administrador.
   - **Senha**do administrador: digite uma senha de administrador. 
   
1. Leia os termos e condições e depois selecione **Eu concordo com os termos e condições declarados acima**.
   
1. Selecione **Comprar**.
   
   Levará poucos minutos para criar os recursos. 
   
1. Explore os recursos criados com o firewall. 

Saiba mais sobre a sintaxe e as propriedades de JSON para um firewall em um modelo em [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais deles, poderá remover o grupo de recursos, o firewall e todos os recursos relacionados executando o comando do PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Para remover um grupo de recursos nomeado *MyResourceGroup*, execute: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Não remova o grupo de recursos e o firewall ainda, se você planeja prosseguir para o firewall do tutorial de monitoramento. 

## <a name="next-steps"></a>Próximas etapas

Em seguida, é possível monitorar os logs do Firewall do Azure:

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
