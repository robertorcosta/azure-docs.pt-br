---
title: 'Início Rápido: Direcionar o tráfego da Web usando um modelo do Resource Manager'
titleSuffix: Azure Application Gateway
description: Saiba como usar um modelo do Resource Manager para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0612371605f6b216eadeef49d9adb1497ba31591
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80148225"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – modelo do Resource Manager

Neste início rápido, você usará um modelo do Resource Manager para criar um Gateway de Aplicativo do Azure. Em seguida, você testará o gateway de aplicativo para verificar se ele funciona corretamente.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Também é possível concluir este início rápido usando o [portal do Azure](quick-create-portal.md), o [Azure PowerShell](quick-create-powershell.md) ou a [CLI do Azure](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Para simplificar, este modelo cria uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um site no gateway de aplicativo, uma regra de roteamento de solicitação básica e duas máquinas virtuais usadas no pool de back-end.

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

Vários recursos do Azure são definidos no modelo:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : um para o gateway de aplicativo e dois para as máquinas virtuais.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): duas máquinas virtuais
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): dois para as máquinas virtuais
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions): para configurar o IIS e as páginas da Web


### <a name="deploy-the-template"></a>Implantar o modelo

Implantar o modelo do Resource Manager no Azure:

1. Selecione **Implantar no Azure** para entrar no Azure e abrir o modelo. O modelo cria um gateway de aplicativo, a infraestrutura de rede e duas máquinas virtuais no pool de back-end que executa o IIS.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Selecione ou crie seu grupo de recursos, digite o nome de usuário e a senha do administrador da máquina virtual.
3. Selecione **Concordo com os termos e condições declarados acima** e selecione **Comprar**. A implantação pode levar 20 minutos ou mais para ser concluída.

## <a name="validate-the-deployment"></a>Validar a implantação

Embora o IIS não seja necessário para criar o gateway de aplicativo, ele é instalado para verificar se o Azure criou o gateway de aplicativo com êxito. Use o IIS para testar o gateway de aplicativo:

1. Localize o endereço IP público do gateway de aplicativo na página **Visão geral**. ![Registre o endereço IP público do gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) ou selecione **Todos os recursos**, insira *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecione-o nos resultados da pesquisa. O Azure exibe o endereço IP público na página **Visão geral**.
2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador para procurar esse endereço IP.
3. Verifique a resposta. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e é capaz de conectar-se com êxito ao back-end.

   ![Teste o gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Atualize o navegador várias vezes e você deverá ver conexões com myVM1 e myVM2.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, exclua o grupo de recursos. Isso remove o gateway de aplicativo e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
