---
title: Integração do Application Gateway com os pontos finais do serviço - Azure App Service | Microsoft Docs
description: Descreve como o Application Gateway se integra ao Azure App Service protegido com pontos finais de serviço.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980056"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integração do Application Gateway com pontos finais de serviço
Existem três variações do App Service que requerem configuração ligeiramente diferente da integração com o Azure Application Gateway. As variações incluem serviço de aplicativo regular - também conhecido como multi-inquilino, Ambiente de Serviço de Aplicativo (ILB) de Balanceador de Carga Interna (ILB) e ASE externo. Este artigo irá percorrer como configurá-lo com o App Service (multi-inquilino) e discutir considerações sobre o ILB e o ASE externo.

## <a name="integration-with-app-service-multi-tenant"></a>Integração com o App Service (multilocatário)
O App Service (multi-inquilino) tem uma internet pública voltada para o ponto final. Usando [pontos finais de serviço,](../../virtual-network/virtual-network-service-endpoints-overview.md) você pode permitir o tráfego apenas a partir de uma sub-rede específica dentro de uma Rede Virtual Azure e bloquear todo o resto. No cenário seguinte, usaremos essa funcionalidade para garantir que uma instância do App Service só possa receber tráfego de uma instância específica do Application Gateway.

![Integração do Application Gateway com o App Service](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Há duas partes para esta configuração além de criar o App Service e o Application Gateway. A primeira parte é habilitar pontos finais de serviço na sub-rede da Rede Virtual onde o Gateway de aplicativo é implantado. Os pontos finais do serviço garantirão que todo o tráfego de rede que sai da sub-rede em direção ao Serviço do Aplicativo será marcado com o ID de sub-rede específico. A segunda parte é definir uma restrição de acesso do aplicativo web específico para garantir que somente o tráfego marcado com essa ID de sub-rede específica seja permitido. Você pode configurá-lo usando diferentes ferramentas, dependendo da preferência.

## <a name="using-azure-portal"></a>Usando o Portal do Azure
Com o portal Azure, você segue quatro passos para provisionar e configurar a configuração. Se você tiver recursos existentes, você pode pular os primeiros passos.
1. Crie um Serviço de Aplicativo usando uma das Quickstarts na documentação do App Service, por exemplo [.Net Core Quickstart](../../app-service/app-service-web-get-started-dotnet.md)
2. Crie um Gateway de aplicativo usando o [portal Quickstart,](../../application-gateway/quick-create-portal.md)mas pule a seção Adicionar alvos backend.
3. Configure [o Serviço de Aplicativo como um backend no Gateway de aplicativo,](../../application-gateway/configure-web-app-portal.md)mas pule a seção Restringir acesso.
4. Finalmente crie a [restrição de acesso usando pontos finais de serviço](../../app-service/app-service-ip-restrictions.md#service-endpoints).

Agora você pode acessar o App Service através do Application Gateway, mas se você tentar acessar o Serviço de Aplicativo diretamente, você deve receber um erro HTTP 403 indicando que o site está parado.

![Integração do Application Gateway com o App Service](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Usar o modelo do Azure Resource Manager
O [modelo de implantação do Gerenciador de recursos][template-app-gateway-app-service-complete] irá provisionar um cenário completo. O cenário consiste em uma instância do App Service bloqueada com pontos finais de serviço e restrição de acesso para receber apenas tráfego do Application Gateway. O modelo inclui muitos Smart Defaults e postfixes exclusivos adicionados aos nomes de recursos para que ele seja simples. Para substituí-los, você terá que clonar o repo ou baixar o modelo e editá-lo. 

Para aplicar o modelo, você pode usar o botão Implantar para Azure encontrado na descrição do modelo, ou você pode usar o PowerShell/CLI apropriado.

## <a name="using-azure-command-line-interface"></a>Usando a interface de linha de comando do Azure
A [amostra Azure CLI](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) irá provisionar um Serviço de Aplicativo bloqueado com pontos finais de serviço e restrição de acesso para receber apenas tráfego do Application Gateway. Se você só precisar isolar o tráfego para um Serviço de Aplicativo existente a partir de um Gateway de Aplicativo existente, o seguinte comando é suficiente.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Na configuração padrão, o comando garantirá tanto a configuração do ponto final de serviço na sub-rede quanto a restrição de acesso no Serviço do Aplicativo.

## <a name="considerations-for-ilb-ase"></a>Considerações para ILB ASE
O ILB ASE não está exposto à internet e o tráfego entre a instância e um Gateway de aplicativo já está isolado na Rede Virtual. O [guia de como fazer configura](../environment/integrate-with-application-gateway.md) um ILB ASE e o integra com um Gateway de aplicativo usando o portal Azure. 

Se você quiser garantir que apenas o tráfego da sub-rede do Application Gateway esteja chegando ao ASE, você pode configurar um GRUPO de segurança de rede (NSG) que afeta todos os aplicativos da Web no ASE. Para o NSG, você pode especificar a faixa IP da sub-rede e, opcionalmente, as portas (80/443). Certifique-se de que não anulará as [regras de NSG necessárias](../environment/network-info.md#network-security-groups) para que a ASE funcione corretamente.

Para isolar o tráfego em um aplicativo web individual, você precisará usar restrições de acesso baseadas em IP, pois os pontos finais de serviço não funcionarão para a ASE. O endereço IP deve ser o IP privado da instância do Application Gateway.

## <a name="considerations-for-external-ase"></a>Considerações para ASE externa
O ASE externo tem um balanceador de carga voltado para o público, como o Serviço de Aplicativos multi-inquilino. Os pontos finais de serviço não funcionam para o ASE, e é por isso que você terá que usar restrições de acesso baseadas em IP usando o IP público da instância do Application Gateway. Para criar uma ASE externa usando o portal Azure, você pode seguir este [Quickstart](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Modelo do Azure Resource Manager para cenário completo"

## <a name="considerations-for-kuduscm-site"></a>Considerações para o site kudu/scm
O site scm, também conhecido como kudu, é um site de admin, que existe para todos os aplicativos web. Não é possível reverter proxy do site scm e você provavelmente também quer bloqueá-lo em endereços IP individuais ou em uma sub-rede específica.

Se você quiser usar as mesmas restrições de acesso do site principal, você pode herdar as configurações usando o seguinte comando.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Se você quiser definir restrições de acesso individuais para o site scm, você pode adicionar restrições de acesso usando o sinalizador --scm-site como mostrado abaixo.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Ambiente de Serviço do Aplicativo, consulte a [documentação do Ambiente de Serviço do Aplicativo](https://docs.microsoft.com/azure/app-service/environment).

Para proteger ainda mais seu aplicativo web, informações sobre o Web Application Firewall no Application Gateway podem ser encontradas na documentação do [Firewall do Aplicativo Web do Azure](../../web-application-firewall/ag/ag-overview.md).