---
title: 'Azure ExpressRoute: configurar o emparelhamento'
description: Este artigo documenta as etapas para criar e provisionar o emparelhamento privado e o Microsoft ExpressRoute. Este artigo também demonstra como verificar o status, atualizar ou excluir emparelhamentos de um circuito.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/13/2019
ms.author: duau
ms.openlocfilehash: ccbd9645ac7d331c06e528298b3a45a184c6cc49
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652220"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Criar e modificar o emparelhamento de um circuito de ExpressRoute

Este artigo ajuda você a criar e gerenciar a configuração de roteamento para um circuito de ExpressRoute Azure Resource Manager (ARM), usando o portal do Azure. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com seu circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Emparelhamento público](about-public-peering.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Você pode configurar emparelhamento privado e emparelhamento da Microsoft para um circuito do ExpressRoute (o emparelhamento público do Azure é preterido para novos circuitos). Os emparelhamentos podem ser configurados em qualquer ordem escolhida. No entanto, você deve concluir a configuração de um emparelhamento por vez. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, consulte [Domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o emparelhamento público, consulte [emparelhamento público do ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

* Verifique se você leu a página de [pré-requisitos](expressroute-prerequisites.md), a página de [requisitos do roteamento](expressroute-routing.md) e a página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade. Para configurar o emparelhamento, o circuito do ExpressRoute deve estar em um estado provisionado e habilitado. 
* Se você planeja usar um hash de chave compartilhada/MD5, certifique-se de usá-lo em ambos os lados do túnel e limitar o número de caracteres alfanuméricos a um máximo de 25. Não há suporte para caracteres especiais. 

Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você. 

> [!IMPORTANT]
> Atualmente, não anunciamos emparelhamentos configurados pelos provedores de serviço por meio do portal de gerenciamento de serviço. Estamos trabalhando para habilitar esse recurso em breve. Verifique com seu provedor de serviços antes de configurar os emparelhamentos via protocolo BGP.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [Configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. Configure o circuito do ExpressRoute. Verifique o **status do provedor** para garantir que o circuito seja totalmente provisionado pelo provedor de conectividade antes de continuar.

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento da Microsoft. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se o provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, prossiga com estas etapas.

   **Status do provedor de circuito: não provisionado**

   [![Captura de tela que mostra a página de visão geral do circuito de demonstração do ExpressRoute com uma caixa vermelha realçando o status do provedor definido como "não provisionado".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Status do provedor de circuito: provisionado**

   [![Captura de tela que mostra a página de visão geral do circuito de demonstração do ExpressRoute com uma caixa vermelha realçando o status do provedor definido como "provisionado".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Configurar o emparelhamento da Microsoft para o circuito. Você precisa ter as seguintes informações antes de continuar:

   * Uma sub-rede /30 para o link principal. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma sub-rede /30 para o link secundário. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: forneça uma lista com todos os prefixos que você pretende anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
   * **Opcional –** ASN de cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.
   * Nome do registro de roteamento: você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados.
   * **Opcional –** Um hash MD5 se você optar por usar um.
3. Você pode selecionar o emparelhamento que deseja configurar, conforme mostrado no exemplo a seguir. Selecione a linha de emparelhamento da Microsoft.

   [![Selecionar a linha de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selecionar a linha de emparelhamento da Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure o emparelhamento da Microsoft. **Salve** a configuração depois de especificar todos os parâmetros. A imagem a seguir mostra uma configuração de exemplo:

   ![Configurar o emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft verifica se os "Prefixos públicos publicados" e o "ASN de ponto" (ou "cliente ASN de cliente") especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver recebendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá a mensagem "Requer validação". 
>
> Se você vir a mensagem "Requer validação", colete os documentos que mostram que os prefixos públicos são atribuídos à sua organização pela entidade que está listada como o proprietário dos prefixos no registro de roteamento e envie esses documentos para validação manual, abrindo um tíquete de suporte, conforme mostrado abaixo. 
>

   Se o circuito chegar a um estado de "validação necessária", você deverá abrir um tíquete de suporte para mostrar a prova de propriedade dos prefixos para nossa equipe de suporte. Você pode abrir um tíquete de suporte diretamente no portal, conforme mostrado no exemplo a seguir:

   ![Validação necessária-tíquete de suporte](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Depois que a configuração for aceita com êxito, você verá algo semelhante à imagem a seguir:

   ![Status de emparelhamento: configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Status de emparelhamento: configurado")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode exibir as propriedades do emparelhamento da Microsoft selecionando a linha para o emparelhamento.

[![Exibir Propriedades de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Exibir propriedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Atualizar a configuração de emparelhamento da Microsoft

Você pode selecionar a linha para o emparelhamento que deseja modificar e, em seguida, modificar as propriedades de emparelhamento e salvar suas modificações.

![Selecionar linha de emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento clicando no ícone excluir, conforme mostrado na imagem a seguir:

![Excluir emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. Configure o circuito do ExpressRoute. Verifique se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar. 

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento privado do Azure. Nesse caso, você não precisará seguir as instruções listadas nas próximas seções. No entanto, se seu provedor de conectividade não gerenciar o roteamento para você, depois de criar o circuito, prossiga com as próximas etapas.

   **Status do provedor de circuito: não provisionado**

   [![Captura de tela mostrando a página de visão geral do circuito de demonstração do ExpressRoute com uma caixa vermelha realçando o status do provedor que está definido como "não provisionado".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Status do provedor de circuito: provisionado**

   [![Captura de tela mostrando a página de visão geral do circuito de demonstração do ExpressRoute com uma caixa vermelha realçando o status do provedor que está definido como "provisionado".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de continuar com as próximas etapas:

   * Uma sub-rede /30 para o link principal. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma sub-rede /30 para o link secundário. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento, exceto pelos números de 65515 a 65520, inclusive.
   * Você deve anunciar as rotas do seu roteador de borda local para o Azure por meio do BGP ao configurar o emparelhamento privado.
   * **Opcional –** Um hash MD5 se você optar por usar um.
3. Selecione a linha de emparelhamento privado do Azure, conforme mostrado no exemplo a seguir:

   [![Selecionar a linha de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selecionar a linha de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure o emparelhamento privado. **Salve** a configuração depois de especificar todos os parâmetros.

   ![configurar o emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Depois que a configuração é aceita com êxito, você vê algo semelhante ao exemplo a seguir:

   ![emparelhamento privado salvo](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Para exibir detalhes sobre o emparelhamento privado do Azure

Você pode exibir as propriedades de emparelhamento privado do Azure selecionando o emparelhamento.

[![Exibir Propriedades de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Exibir Propriedades de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Atualizar a configuração de emparelhamento privado do Azure

Você pode selecionar a linha para emparelhamento e modificar as propriedades de emparelhamento. Após a atualização, salve as alterações.

![atualizar emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento selecionando o ícone Excluir, conforme mostrado na imagem a seguir:

> [!WARNING]
> Você deve garantir que todas as conexões de Alcance Global do ExpressRoute e redes virtuais são removidas antes de executar este exemplo. 
> 
> 

![excluir emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Próximas etapas

Próxima etapa, [vincular uma VNet a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para saber mais sobre fluxos de trabalho do ExpressRoute, confira [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre o emparelhamento de circuito, veja [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
* Para saber mais sobre redes virtuais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).
