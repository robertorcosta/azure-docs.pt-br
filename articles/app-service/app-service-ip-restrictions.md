---
title: Restrições de acesso ao Azure App Service
description: Saiba como proteger seu aplicativo no Azure App Service especificando restrições de acesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 298555da2056bc4c16d4d7b16615604f9798b91b
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639277"
---
# <a name="azure-app-service-access-restrictions"></a>Restrições de acesso ao Azure App Service

As restrições de acesso permitem definir uma lista de permitir/negar ordenada prioritária que controla o acesso da rede ao seu aplicativo. A lista pode incluir endereços IP ou sub-redes da Rede Virtual Azure. Quando há uma ou mais entradas, há então um "negar tudo" implícito que existe no final da lista.

O recurso de restrições de acesso funciona com todas as cargas de trabalho hospedadas do App Service, incluindo; aplicativos web, aplicativos de API, aplicativos Linux, aplicativos de contêiner Linux e Funções.

Quando uma solicitação é feita ao seu aplicativo, o endereço FROM é avaliado de acordo com as regras de endereço IP em sua lista de restrições de acesso. Se o endereço DE ESTIVER em uma sub-rede configurada com pontos finais de serviço para microsoft.Web, a sub-rede de origem será comparada com as regras da rede virtual em sua lista de restrições de acesso. Se o endereço não é permitido o acesso com base nas regras na lista, o serviço responderá com um código de status [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403).

O recurso de restrições de acesso é implementado nas funções front-end do App Service, que são a montante dos hosts do trabalhador onde seu código é executado. Portanto, as restrições de acesso são efetivamente ACLs de rede.

A capacidade de restringir o acesso ao seu aplicativo web a partir de uma Rede Virtual Azure (VNet) é chamada [de pontos finais de serviço][serviceendpoints]. Os pontos finais de serviço permitem restringir o acesso a um serviço de vários inquilinos a partir de sub-redes selecionadas. Ele deve ser habilitado tanto no lado da rede quanto no serviço com o que está sendo habilitado. Não funciona para restringir o tráfego a aplicativos hospedados em um ambiente de serviço de aplicativo. Se você estiver em um ambiente de serviço de aplicativo, você pode controlar o acesso ao seu aplicativo com regras de endereço IP.

![fluxo de restrições de acesso](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Adicionando e editando regras de restrição de acesso no portal ##

Para adicionar uma regra de restrição de acesso ao seu aplicativo, use o menu para abrir**restrições de acesso à** **rede**>e clique em **Configurar restrições de acesso**

![Opções de rede do Serviço de Aplicativo do Azure](media/app-service-ip-restrictions/access-restrictions.png)  

A partir da ui restrições de acesso, você pode rever a lista de regras de restrição de acesso definidas para o seu aplicativo.

![restrições de acesso à lista](media/app-service-ip-restrictions/access-restrictions-browse.png)

A lista mostrará todas as restrições atuais que estão em seu aplicativo. Se você tiver uma restrição de VNet em seu aplicativo, a tabela mostrará se os pontos finais do serviço estão ativados para microsoft.Web. Quando não houver restrições definidas em seu aplicativo, seu aplicativo estará acessível de qualquer lugar.  

## <a name="adding-ip-address-rules"></a>Adicionando regras de endereço IP

Você pode clicar em **[+] Adicionar regra** para adicionar uma nova regra de restrição de acesso. Depois de adicionar uma regra, entrará em vigor imediatamente. As regras são impostas em ordem de prioridade a partir do número mais baixo e para cima. Há uma negação implícita tudo o que está em vigor depois que você adicionar até mesmo uma única regra.

Ao criar uma regra, você deve selecionar permitir/negar e também o tipo de regra. Você também é obrigado a fornecer o valor prioritário e o que você está restringindo o acesso.  Você pode adicionar opcionalmente um nome e uma descrição à regra.  

![adicionar uma regra de restrição de acesso IP](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Para definir uma regra baseada em endereço IP, selecione um tipo de IPv4 ou IPv6. Notação de endereço IP deve ser especificada na notação de CIDR de endereços IPv4 e IPv6. Para especificar um endereço exato, você pode usar algo como 1.2.3.4/32 onde os quatro primeiros octetos representam o endereço IP e /32 é a máscara. A notação de CIDR de IPv4 para todos os endereços é 0.0.0.0/0. Para saber mais sobre a notação CIDR, leia [Roteamento entre domínios sem classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

## <a name="service-endpoints"></a>Pontos de extremidade de serviço

Os pontos finais do serviço permitem restringir o acesso às sub-redes de rede virtuais selecionadas do Azure. Para restringir o acesso a uma sub-rede específica, crie uma regra de restrição com um tipo de Rede Virtual. Você pode escolher a assinatura, a VNet e a sub-rede com a sua indémidade que deseja permitir ou negar acesso. Se os pontos finais de serviço ainda não estiverem ativados com o Microsoft.Web para a sub-rede selecionada, ele será automaticamente ativado para você, a menos que você verifique a caixa pedindo para não fazer isso. A situação em que você gostaria de habilitá-lo no aplicativo, mas não na sub-rede, está em grande parte relacionada se você tem as permissões para habilitar pontos finais de serviço na sub-rede ou não. Se você precisar de alguém para habilitar pontos finais de serviço na sub-rede, você pode verificar a caixa e ter seu aplicativo configurado para pontos finais de serviço, antecipando-o de ser ativado mais tarde na sub-rede. 

![adicionar uma regra de restrição de acesso VNet](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Os pontos finais do serviço não podem ser usados para restringir o acesso a aplicativos executados em um ambiente de serviço de aplicativo. Quando seu aplicativo está em um ambiente de serviço de aplicativo, você pode controlar o acesso ao seu aplicativo com regras de acesso IP. 

Com pontos finais de serviço, você pode configurar seu aplicativo com gateways de aplicativos ou outros dispositivos WAF. Você também pode configurar aplicativos de vários níveis com backends seguros. Para obter mais detalhes sobre algumas das possibilidades, leia recursos de rede e [integração](networking/app-gateway-with-service-endpoints.md)de Serviço de [Aplicativo](networking-features.md) e Gateway de Aplicativo com pontos finais de serviço .

## <a name="managing-access-restriction-rules"></a>Gerenciamento de regras de restrição de acesso

Você pode clicar em qualquer linha para editar uma regra de restrição de acesso existente. As edições são eficazes imediatamente, incluindo as alterações na ordem de prioridade.

![editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Quando você edita uma regra, você não pode alterar o tipo entre uma regra de endereço IP e uma regra de Rede Virtual. 

![editar uma regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Para excluir uma regra, clique em **...** em sua regra e, em seguida, clique **em Remover**.

![excluir regra de restrição de acesso](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Bloqueando um único endereço IP ##

Ao adicionar sua primeira regra de restrição de IP, o serviço adicionará uma regra explícita **negar todas as** regras com uma prioridade de 2147483647. Na prática, a regra **Negar explícita será** a última regra executada e bloqueará o acesso a qualquer endereço IP que não seja explicitamente permitido usando uma regra De **permitir.**

Para o cenário em que os usuários desejam bloquear explicitamente um único endereço IP ou bloco de endereço IP, mas permitir todo o acesso ao resto, é necessário adicionar uma regra explícita **de Permitir tudo.**

![bloquear endereço ip único](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>Site do SCM 

Além de poder controlar o acesso ao seu aplicativo, você também pode restringir o acesso ao site scm usado pelo seu aplicativo. O site scm é o ponto final de implantação da Web e também o console Kudu. Você pode atribuir separadamente restrições de acesso ao site scm do aplicativo ou usar o mesmo conjunto tanto para o aplicativo quanto para o site scm. Quando você verifica a caixa para ter as mesmas restrições que seu aplicativo, tudo é apagado. Se você desmarcar a caixa, as configurações que você teve anteriormente no site scm serão aplicadas. 

![restrições de acesso à lista](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Manipulação programática de regras de restrição de acesso ##

[O Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) e [o Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) têm suporte para editar restrições de acesso. Exemplo de adição de uma restrição de acesso usando o Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Exemplo de adição de uma restrição de acesso usando o Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Os valores também podem ser definidos manualmente com uma operação [Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT na configuração do aplicativo no Gerenciador de recursos ou usando um modelo do Azure Resource Manager. Por exemplo, você pode usar resources.azure.com e editar o bloco ipSecurityRestrictions para adicionar o JSON necessário.

O local para obter essas informações no Gerenciador de Recursos é:

management.azure.com/subscriptions/**subscription ID**/resourceGroups/**resource groups**/providers/Microsoft.Web/sites/**web app name**/config/web?api-version=2018-02-01

A sintaxe JSON para o exemplo anterior é:
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-functions-access-restrictions"></a>Restrições de acesso às funções do Azure

As restrições de acesso também estão disponíveis para aplicativos de função com a mesma funcionalidade que os planos do App Service. A ativação das restrições de acesso desativará o editor de código do portal para quaisquer IPs proibidos.

## <a name="next-steps"></a>Próximas etapas
[Restrições de acesso para funções do Azure](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integração do Application Gateway com pontos finais de serviço](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
