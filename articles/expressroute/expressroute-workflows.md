---
title: 'Azure ExpressRoute: fluxos de trabalho de configuração de circuito'
description: Esta página mostra os fluxos de trabalho para configurar o circuito do ExpressRoute e os emparelhamentos
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: 58914709838c72246678ce92005de5ac18695a1f
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86204172"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Fluxos de trabalho do ExpressRoute para provisionamento e estados do circuito
Esta página fornece uma orientação de alto nível pelos fluxos de trabalho de provisionamento do serviço e de configuração do roteamento.

![fluxo de trabalho](./media/expressroute-workflows/expressroute-circuit-workflow.png)

A figura a seguir e as etapas correspondentes descrevem as tarefas para provisionar um circuito de ExpressRoute de ponta a ponta. 

1. Use o PowerShell para configurar um circuito do ExpressRoute. Siga as instruções no artigo [Criar circuitos do ExpressRoute](expressroute-howto-circuit-classic.md) para obter mais detalhes.
2. Solicite conectividade do provedor de serviço. Esse processo varia. Entre em contato com o provedor de conectividade para obter mais detalhes sobre a solicitação de conectividade.
3. Confira se o circuito foi provisionado com sucesso verificando o estado de provisionamento do circuito do ExpressRoute por meio do PowerShell. 
4. Configure os domínios de roteamento. Se seu provedor de conectividade gerencia a configuração de camada 3, ele configurará o roteamento para o circuito. Se seu provedor de conectividade oferece apenas serviços de camada 2, você deve configurar o roteamento de acordo com as diretrizes descritas nas páginas de [configuração de roteamento](expressroute-howto-routing-classic.md) e [requisitos de roteamento](expressroute-routing.md) .
   
   * Habilitar emparelhamento privado do Azure - Habilite esse emparelhamento para conectar VMs/serviços de nuvem implantados em redes virtuais.

   * Habilitar o emparelhamento da Microsoft – habilite isso para acessar o Microsoft serviços online, como o Office 365. Todos os serviços de PaaS do Azure são acessíveis por meio do emparelhamento da Microsoft.
     
     > [!IMPORTANT]
     > Use um proxy/borda diferente da usada para a Internet para se conectar à Microsoft. Usar a mesma borda para o ExpressRoute e para a Internet causará o roteamento assimétrico e falhas de conectividade em sua rede.
     > 
     > 
     
     ![roteamento de fluxos de trabalho](./media/expressroute-workflows/routing-workflow.png)
5. Vinculando redes virtuais aos circuitos do ExpressRoute - Você pode vincular redes virtuais ao circuito do ExpressRoute. Siga as instruções [para vincular redes virtuais](expressroute-howto-linkvnet-arm.md) ao seu circuito. Essas redes virtuais podem estar na mesma assinatura do Azure que o circuito do ExpressRoute ou podem estar em uma assinatura diferente.

## <a name="expressroute-circuit-provisioning-states"></a>Estados de provisionamento de circuito do ExpressRoute
Cada circuito de ExpressRoute tem dois estados:

* Estado de provisionamento do provedor de serviço
* Status

O status representa o estado de provisionamento da Microsoft. Essa propriedade é definida como Habilitada quando você cria um circuito de ExpressRoute

O estado de provisionamento do provedor de conectividade representa o estado no lado do provedor de conectividade. Ele pode ser *Não Provisionado*, *Provisionando* ou *Provisionado*. O circuito do ExpressRoute deve estar em um estado provisionado na ordem de configuração de emparelhamento.

### <a name="possible-states-of-an-expressroute-circuit"></a>Possíveis estados de um circuito do ExpressRoute
Esta seção lista os possíveis estados de um circuito do ExpressRoute.

**No momento da criação**

O circuito do ExpressRoute relatará os seguintes Estados na criação de recursos.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Quando o provedor de conectividade estiver no processo de provisionamento do circuito**

O circuito do ExpressRoute relatará os seguintes Estados enquanto o provedor de conectividade estiver trabalhando para provisionar o circuito.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Quando o provedor de conectividade tiver concluído o processo de provisionamento**

O circuito do ExpressRoute relatará os seguintes Estados assim que o provedor de conectividade tiver provisionado com êxito o circuito.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Quando o provedor de conectividade estiver desprovisionando o circuito**

Se o circuito do ExpressRoute precisar ser desprovisionado, o circuito relatará os Estados a seguir quando o provedor de serviços tiver concluído o processo de desprovisionamento.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Você pode optar por habilitá-lo novamente se for necessário, ou executar cmdlets do PowerShell para excluir o circuito.  

> [!IMPORTANT]
> Um circuito não pode ser excluído quando o ServiceProviderProvisioningState é provisionado ou provisionado. O provedor de conectividade precisa desprovisionar o circuito antes que ele possa ser excluído. A Microsoft continuará a cobrar o circuito até que o recurso de circuito do ExpressRoute seja excluído no Azure.
> 

## <a name="routing-session-configuration-state"></a>Estado de configuração da sessão de roteamento
O estado de provisionamento do BGP relata se a sessão BGP foi habilitada no Microsoft Edge. O estado deve ser habilitado para usar o emparelhamento privado ou da Microsoft.

É importante verificar o estado da sessão BGP, especialmente para o emparelhamento da Microsoft. Além do estado de provisionamento BGP, há outro estado chamado *estado de prefixos públicos anunciados*. O estado de prefixos públicos anunciados deve estar no estado *configurado* , tanto para a sessão BGP quanto para que o roteamento funcione de ponta a ponta. 

Se o estado de prefixo público anunciado for definido como *validação necessária* , a sessão BGP não estará habilitada, pois os prefixos anunciados não corresponderam ao número AS em qualquer um dos registros do roteamento. 

> [!IMPORTANT]
> Se o estado de prefixos públicos anunciados estiver no estado de *validação manual* , você precisará abrir um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e fornecer evidências de que você possui os endereços IP anunciados junto com o número do sistema autônomo associado.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Configurar sua conexão do ExpressRoute.
  
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

