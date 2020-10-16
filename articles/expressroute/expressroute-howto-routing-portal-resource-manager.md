---
title: 'Tutorial: Configurar o emparelhamento para o circuito do ExpressRoute – portal do Azure'
description: Este tutorial mostra como criar e provisionar o emparelhamento da Microsoft e privado do ExpressRoute usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: cad098ed2dedc7abba57394ef1e26b9b7c87cd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855464"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Tutorial: Criar e modificar o emparelhamento de um circuito do ExpressRoute usando o portal do Azure

Este tutorial mostra como criar e gerenciar a configuração de roteamento para um circuito do ExpressRoute do Azure Resource Manager usando o portal do Azure. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com seu circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Emparelhamento público](about-public-peering.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Você pode configurar o emparelhamento privado e o emparelhamento da Microsoft para um circuito do ExpressRoute (o emparelhamento público do Azure está preterido para novos circuitos). Os emparelhamentos poderão ser configurados em qualquer ordem que você escolher. No entanto, você deve concluir a configuração de um emparelhamento por vez. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, consulte [Domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o emparelhamento público, confira [Emparelhamento público do ExpressRoute](about-public-peering.md).

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> - Configurar, atualizar e excluir o emparelhamento da Microsoft para um circuito
> - Configurar, atualizar e excluir o emparelhamento privado do Azure para um circuito

## <a name="prerequisites"></a>Pré-requisitos

* Verifique se você analisou as seguintes páginas antes de começar a configuração:
    * [Pré-requisitos](expressroute-prerequisites.md) 
    * [Requisitos de roteamento](expressroute-routing.md)
    * [Fluxos de trabalho](expressroute-workflows.md)
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade. Para configurar emparelhamentos, o circuito do ExpressRoute precisa estar em um estado habilitado e provisionado. 
* Se planejar usar um hash de chave compartilhada/MD5, use a chave em ambos os lados do túnel. O limite é 25 caracteres alfanuméricos, no máximo. Não há suporte para caracteres especiais. 

Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você. 

> [!IMPORTANT]
> Atualmente, não anunciamos emparelhamentos configurados pelos provedores de serviço por meio do portal de gerenciamento de serviço. Estamos trabalhando para habilitar esse recurso em breve. Verifique com seu provedor de serviços antes de configurar os emparelhamentos via protocolo BGP.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [Configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. Configure o circuito do ExpressRoute. Confira o **Status do provedor** para verificar se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar.

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento da Microsoft. Não será necessário seguir as instruções listadas nas próximas seções. No entanto, se o seu provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com as etapas abaixo.

   **Circuito – Status provedor: Não provisionado**

   [![Captura de tela que mostra a página Visão geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como "Não provisionado".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Circuito – Status provedor: Provisionado**

   [![Captura de tela que mostra a página Visão geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como "Provisionado".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Configurar o emparelhamento da Microsoft para o circuito. Verifique se você tem as informações a seguir antes de continuar.

   * Uma sub-rede /30 para o link principal. O bloco de endereços precisa ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o respectivo roteador.
   * Uma sub-rede /30 para o link secundário. O bloco de endereços precisa ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o respectivo roteador.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: forneça uma lista com todos os prefixos que planeja anunciar na sessão do BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
   * **Opcional –** ASN de cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.
   * Nome do registro de roteamento: você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados.
   * **Opcional –** Um hash MD5 se você optar por usar um.
3. Você pode selecionar o emparelhamento que deseja configurar, conforme mostrado no exemplo a seguir. Selecione a linha de emparelhamento da Microsoft.

   [![Selecionar a linha de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Selecionar a linha de emparelhamento da Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure o emparelhamento da Microsoft. **Salve** a configuração depois que você tiver especificado todos os parâmetros. A seguinte imagem mostra um exemplo de configuração:

   ![Configurar o emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft verifica se os "Prefixos públicos publicados" e o "ASN de ponto" (ou "cliente ASN de cliente") especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver recebendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá a mensagem "Requer validação". 
>
> Se você vir a mensagem "Requer validação", colete os documentos que mostram que os prefixos públicos foram atribuídos à organização pela entidade que está listada como proprietária dos prefixos no registro de roteamento e envie esses documentos para validação manual, abrindo um tíquete de suporte. 
>

   Se o circuito for para um estado "Validação necessária", você precisará abrir um tíquete de suporte para mostrar a prova de propriedade dos prefixos à nossa equipe de suporte. Você pode abrir um tíquete de suporte diretamente no portal, conforme mostrado no exemplo a seguir:

   ![Validação necessária – tíquete de suporte](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Depois que a configuração for aceita com êxito, você verá algo semelhante à seguinte imagem:

   ![Status de emparelhamento: configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Status de emparelhamento: Configurado")

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode ver as propriedades do emparelhamento da Microsoft selecionando a linha para o emparelhamento.

[![Ver as propriedades de emparelhamento da Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Exibir propriedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Atualizar a configuração de emparelhamento da Microsoft

Você pode selecionar a linha para o emparelhamento que deseja modificar e, depois, modificar as propriedades de emparelhamento e salvar suas modificações.

![Selecionar linha de emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. Configure o circuito do ExpressRoute. Verifique se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar. 

   Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento privado do Azure. Não será necessário seguir as instruções listadas nas próximas seções. No entanto, se o seu provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com as etapas a seguir.

   **Circuito – Status provedor: Não provisionado**

   [![Captura de tela que mostra a página Visão geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como "Não provisionado".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuito – Status provedor: Provisionado**

   [![Captura de tela que mostra a página Visão geral do Circuito de Demonstração do ExpressRoute com uma caixa vermelha realçando o Status do provedor definido como "Provisionado".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de prosseguir para as próximas etapas:

   * Uma sub-rede /30 para o link principal. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o respectivo roteador.
   * Uma sub-rede /30 para o link secundário. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais. Nessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o respectivo roteador.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento, exceto pelos números de 65515 a 65520, inclusive.
   * Você precisa anunciar as rotas do seu roteador do Edge local no Azure por meio de BGP ao configurar o emparelhamento privado.
   * **Opcional –** Um hash MD5 se você optar por usar um.
3. Selecione a linha de emparelhamento privado do Azure, conforme mostrado no seguinte exemplo:

   [![Selecionar a linha de emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Selecionar a linha de emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure o emparelhamento privado. **Salve** a configuração depois que você tiver especificado todos os parâmetros.

   ![configurar o emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Depois que a configuração é aceita com êxito, você vê algo semelhante ao exemplo a seguir:

   ![emparelhamento privado salvo](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Para exibir detalhes sobre o emparelhamento privado do Azure

Você pode exibir as propriedades de emparelhamento privado do Azure selecionando o emparelhamento.

[![Ver propriedades do emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Ver propriedades do emparelhamento privado")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Atualizar a configuração de emparelhamento privado do Azure

Você pode selecionar a linha para emparelhamento e modificar as propriedades de emparelhamento. Após a atualização, salve as alterações.

![atualizar emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento clicando no ícone Excluir, conforme mostrado na seguinte imagem:

![Excluir emparelhamento](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento selecionando o ícone Excluir, conforme mostrado na imagem a seguir:

> [!WARNING]
> Você deve garantir que todas as conexões de Alcance Global do ExpressRoute e redes virtuais são removidas antes de executar este exemplo. 
> 
> 

![excluir emparelhamento privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="next-steps"></a>Próximas etapas

Após configurar o emparelhamento privado do Azure, você poderá criar um gateway do ExpressRoute para vincular uma rede virtual ao circuito. 

> [!div class="nextstepaction"]
> [Configurar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
