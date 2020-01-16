---
title: 'Início Rápido: Criar um balanceador de carga Standard – modelo do Azure'
titleSuffix: Azure Load Balancer
description: Este início rápido mostra como criar um Standard Load Balancer usando o modelo do Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 28c4ca59b20fcdd8ab0249392dc0f13cbabf1a97
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75970627"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Início Rápido: Criar um Standard Load Balancer para balancear carga de VMs usando o modelo do Azure Resource Manager

O balanceamento de carga fornece um nível mais alto de disponibilidade e escala com a distribuição das solicitações recebidas entre várias VMs (máquinas virtuais). Este início rápido mostra como implantar um modelo do Azure Resource Manager que cria um Standard Load Balancer para balancear a carga de VMs. Usar o modelo do Resource Manager leva menos etapas em comparação com outros métodos de implantação.

O [modelo do Resource Manager](../azure-resource-manager/templates/overview.md) é um arquivo JSON (JavaScript Object Notation) que define a infraestrutura e a configuração do projeto. O modelo usa a sintaxe declarativa, que permite declarar o que você pretende implantar sem precisar gravar a sequência de comandos de programação para criá-lo. Caso deseje saber mais sobre como desenvolver modelos do Resource Manager, confira a [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelos](/azure/templates/microsoft.network/loadbalancers).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-standard-load-balancer"></a>Criar um Standard Load Balancer

O Standard Load Balancer oferece suporte a apenas um endereço IP público padrão. Ao criar um Standard Load Balancer, você também precisa criar um novo endereço IP público padrão configurado como o front-end do Standard Load Balancer.

O modelo usado neste início rápido é um [Modelo de início rápido](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

Vários recursos do Azure foram definidos no modelo:

- **Microsoft.Network/loadBalancers**
- **Microsoft.Network/publicIPAddresses**: para o balanceador de carga.
- **Microsoft.Network/networkSecurityGroups**
- **Microsoft.Network/virtualNetworks**
- **Microsoft.Compute/virutalMachines** (três deles)
- **Microsoft.Network/publicIPAddresses** (três deles): para cada uma das três máquinas virtuais.
- **Microsoft.Network/networkInterfaces** (três deles)
- **Microsoft.Compute/virtualMachine/extensions** (três deles): use para configurar o IIS e as páginas da Web

Para encontrar mais modelos relacionados ao Azure Load Balancer, confira [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Clique em **Experimentar** no bloco de código a seguir para abrir o Azure Cloud Shell e então acompanhe as instruções para entrar no Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Aguarde até ver o prompt do console.

1. Selecione **Copiar** no bloco de códigos anterior para copiar o script do PowerShell.

1. Clique com o botão direito do mouse no painel do console do shell e selecione **Colar**.

1. Insira os valores.

   A implantação do modelo cria três zonas de disponibilidade. As zonas de disponibilidade têm suporte apenas em [determinadas regiões](../availability-zones/az-overview.md). Use uma das regiões com suporte. Se você não tiver certeza, insira **centralus**.

   O nome do grupo de recursos é o nome do projeto com o acréscimo de **rg**. Você precisará do nome do grupo de recursos na seção a seguir.

A implantação do modelo leva cerca de 10 minutos. Quando tiver concluído, a saída deverá ser semelhante a:

![Saída de implantação do PowerShell do modelo do Resource Manager do Azure Standard Load Balancer](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

O Azure PowerShell é usado para implantar o modelo. Além do Azure PowerShell, você também pode usar o portal do Azure, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

1. Entre no [portal do Azure](https://portal.azure.com).

1. Escolha **Grupos de recursos** no painel esquerdo.

1. Selecione o grupo de recursos criado na seção anterior. O nome do grupo de recursos padrão é o nome do projeto com o acréscimo de **rg**.

1. Escolha o balanceador de carga. O nome padrão é o nome do projeto com o acréscimo de **-lb**.

1. Copie apenas a parte do endereço IP do endereço IP público e cole-a na barra de endereço do seu navegador.

   ![IP público do modelo do Resource Manager do Azure Standard Load Balancer](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    O navegador exibe a página padrão do servidor da Web dos Serviços de Informações da Internet (IIS).

   ![Servidor Web do IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Para ver o balanceador de carga distribuir o tráfego entre todas as três VMs, você poderá forçar a atualização de seu navegador da Web no computador cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados quando você não precisar mais deles. Para isso, acesse o portal do Azure, selecione o grupo de recursos que contém o balanceador de carga e, em seguida, selecione **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Standard Load Balancer, anexou VMs a ele, configurou a regra de tráfego do balanceador de carga, fez uma investigação de integridade e testou o balanceador de carga.

Para saber mais, continue com os tutoriais do Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
