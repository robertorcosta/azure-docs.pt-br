---
title: Tutorial – Criar e modificar um circuito com o ExpressRoute
description: Neste tutorial, saiba como criar, provisionar, verificar, atualizar, excluir e desprovisionar um circuito do ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: duau
ms.openlocfilehash: e0ba14cd5db47c12435b2de35d0753b402c947ea
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566255"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Tutorial: Criar e modificar um circuito do ExpressRoute

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI do Azure](howto-circuit-cli.md)
> * [Modelo do Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-circuit-classic.md)
>

Este tutorial mostra como criar um circuito do ExpressRoute usando o portal do Azure e o modelo de implantação do Azure Resource Manager. Você também pode verificar o status, atualizar, excluir ou desprovisionar um circuito.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um circuito do ExpressRoute
> * Obter o status atual de um circuito
> * Modificar um circuito
> * Desprovisionar e excluir um circuito

## <a name="before-you-begin"></a>Antes de começar

* Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Verifique se você tem acesso ao [portal do Azure](https://portal.azure.com).
* Verifique se você tem permissões para criar novos recursos de rede. Se você não tiver as permissões corretas, entre em contato com o administrador da conta.
* Você pode [exibir um vídeo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de começar para entender melhor as etapas.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Criar e provisionar um circuito do ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Entre no Portal do Azure

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Criar um novo circuito do ExpressRoute

> [!IMPORTANT]
> O circuito do ExpressRoute será cobrado a partir do momento em que uma chave de serviço for emitida. Execute esta operação quando o provedor de conectividade estiver pronto para provisionar o circuito.

Você pode criar um circuito do ExpressRoute selecionando a opção de criar um novo recurso. 

1. No menu do portal do Azure, selecione **Criar um recurso**. Clique em **Rede** > **ExpressRoute**, conforme mostrado na seguinte imagem:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Criar um circuito do ExpressRoute":::

2. Após clicar em **ExpressRoute**, você verá a página **Criar circuito do ExpressRoute**. Forneça o **Grupo de Recursos**, a **Região** e o **Nome** do circuito. Em seguida, clique em **Próximo: Configuração >** .

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Configurar o grupo de recursos e a região":::

3. Ao preencher os valores nessa página, especifique a camada de SKU (Local, Standard ou Premium) e o modelos de cobrança de medição de dados (Limitado ou Ilimitado) corretos.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Configurar o circuito":::
    
    * O **Tipo de Porta** determina se você está se conectando a um provedor de serviços ou diretamente à rede global da Microsoft em uma localização de emparelhamento.
    * **Criar ou importar do clássico** determina se um circuito está sendo criado ou se você está migrando um circuito clássico para o ARM.
    * O **Provedor** é o provedor de serviços de Internet de quem você solicitará o serviço.
    * **Local de Emparelhamento** é o local físico em que você está realizando o emparelhamento com a Microsoft.

    > [!IMPORTANT]
    > O Local de Emparelhamento indica o [local físico](expressroute-locations.md) em que você está realizando o emparelhamento com a Microsoft. Isso **não** tem vínculo à propriedade "Local", que se refere à posição geográfica na qual o Provedor de Recursos de Rede do Azure está localizado. Embora eles não estejam relacionados, é uma boa prática escolher um provedor de recursos de rede geograficamente próximo do Local de Emparelhamento do circuito.

    * **SKU** determina se um complemento Local, Standard ou Premium do ExpressRoute está habilitado. Você pode especificar **Local** para obter o SKU local, **Standard** para obter o SKU Standard ou **Premium** para obter o complemento premium. Observe que você pode alterar o SKU para habilitar o complemento premium.
    > [!IMPORTANT]
    > Não é possível alterar o SKU de **Standard/Premium** para **Local**.
    
    * O **Modelo de cobrança** determina o tipo de cobrança. Você pode especificar **Limitado** para um plano de dados limitado e **Ilimitado** para um plano de dados ilimitado. Observe que você pode alterar o tipo de cobrança de **Limitada** para **Ilimitada**.

    > [!IMPORTANT]
    > Não é possível alterar o tipo de **Ilimitada** para **Limitada**.

    * **Permitir operação clássica** permitirá que redes virtuais clássicas sejam vinculadas ao circuito.

### <a name="3-view-the-circuits-and-properties"></a>3. Exibir os circuitos e as propriedades

**Exibir todos os circuitos**

Você pode ver todos os circuitos que criou selecionando **Todos os serviços > Rede > ExpressRoute** no menu à esquerda.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Menu de circuito do ExpressRoute":::

Todos os circuitos do ExpressRoute criados na assinatura serão mostrados aqui.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Lista de circuitos do ExpressRoute":::

**Exibir as propriedades**

Selecione o circuito para exibir as propriedades dele. Na página **Visão geral** do circuito, a chave de serviço é exibida no campo de chave de serviço. Confira a chave de serviço do circuito e forneça-a para o provedor de serviços para concluir o processo de provisionamento. A chave de serviço é específica para o circuito.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Exibir propriedades":::

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Enviar a chave de serviço ao seu provedor de conectividade para obter provisionamento

Nessa página, **Status do provedor** fornece informações sobre o estado de provisionamento atual no lado do provedor de serviço. **Status de circuito** fornece o estado no lado da Microsoft. Para saber mais sobre estados de provisionamento do circuito, confira o artigo [Fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando você cria um novo circuito do ExpressRoute, ele está no seguinte estado:

Status do provedor: **Não provisionado**<BR>
Status do circuito: **Enabled**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Iniciar o processo de provisionamento":::

O circuito assumirá o seguinte o estado quando o provedor de conectividade estiver habilitando-o para você:

Status do provedor: **Provisionamento**<BR>
Status do circuito: **Enabled**

Para que você consiga usar um circuito do ExpressRoute, ele deverá estar no seguinte estado:

Status do provedor: **Provisionado**<BR>
Status do circuito: **Enabled**

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Verifique periodicamente o status e o estado da chave do circuito

Você pode exibir as propriedades do circuito de seu interesse selecionando-o. Verifique o **Status do provedor** e se ele mudou para **Provisionado** antes de continuar.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Status do circuito e do provedor":::

### <a name="6-create-your-routing-configuration"></a>6. Criar sua configuração de roteamento

Para obter instruções passo a passo, confira o artigo [Configuração do roteamento de circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) para criar e modificar os emparelhamentos de circuito.

> [!IMPORTANT]
> Estas instruções aplicam-se apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de camada 3 (normalmente um IP VPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Vincular uma rede virtual a um circuito de ExpressRoute

Em seguida, vincule uma rede virtual a seu circuito do ExpressRoute. Use o artigo [Vincular redes virtuais a circuitos do ExpressRoute](expressroute-howto-linkvnet-arm.md) ao trabalhar com o modelo de implantação do Gerenciador de Recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Obtendo o status de um circuito do ExpressRoute

Você pode exibir o status de um circuito selecionando-o e mostrando a página Visão geral.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Modificar um circuito do ExpressRoute

Você pode modificar certas propriedades de um circuito do ExpressRoute sem afetar a conectividade. É possível modificar a largura de banda, o SKU, o modelo de cobrança e permitir operações clássicas na página **Configurações**. Para saber mais sobre os limites e as limitações, consulte as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

É possível executar as seguintes tarefas sem tempo de inatividade:

* Habilitar ou desabilitar um complemento Premium do ExpressRoute para seu circuito do ExpressRoute.

> [!IMPORTANT]
  > Não há suporte para alterar o SKU de **Standard/Premium** para **Local**.

* Aumentar a largura de banda do circuito do ExpressRoute, desde que haja capacidade disponível na porta.

  > [!IMPORTANT]
  > Não há suporte para o downgrade da largura de banda de um circuito.

* Alterar o plano de medição de *Dados Limitados* para *Dados Ilimitados*.

  > [!IMPORTANT]
  > Não há suporte para a alteração do plano de medição de **Dados Ilimitados** para **Dados Limitados**.

* Você pode habilitar e desabilitar *Permitir Operações Clássicas*.
  > [!IMPORTANT]
  > Talvez seja necessário recriar o circuito de ExpressRoute se não houver capacidade adequada na porta existente. Você não pode atualizar o circuito não se houver capacidade adicional disponível nesse local.
  >
  > Embora seja possível atualizar a largura de banda sem problemas, não é possível reduzir a largura de banda de um circuito ExpressRoute sem interrupção. O downgrade da largura de banda exige o desprovisionamento do circuito do ExpressRoute e um reprovisionamento de um novo circuito do ExpressRoute.
  >
  > Poderá ocorrer uma falha ao desabilitar a operação do complemento Premium se você estiver usando recursos que ultrapassam o que é permitido para o circuito standard.

Para modificar um circuito do ExpressRoute, clique em **Configuração**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Modificar o circuito":::

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Desprovisionamento e exclusão de um circuito do ExpressRoute

Se o estado de provisionamento do provedor de serviço de circuito de ExpressRoute for **Provisionando** ou **Provisionado**, você deverá trabalhar com seu provedor de serviços para que ele desprovisione o circuito. Continuaremos a reservar recursos e a cobrar de você até que o provedor de serviços complete o desprovisionamento do circuito e nos notifique.

> [!NOTE]
>* Você precisa desvincular *todas as redes virtuais* do circuito do ExpressRoute antes do desprovisionamento. Se essa operação falhar, verifique se há redes virtuais vinculadas ao circuito.
>* Se o provedor de serviços tiver desprovisionado o circuito (o estado de provisionamento do provedor de serviços estiver definido como **Não provisionado**), exclua o circuito. Isso interrompe a cobrança pelo circuito.

Você pode excluir seu circuito do ExpressRoute selecionando o ícone **Excluir**. 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Excluir circuito":::

## <a name="next-steps"></a>Próximas etapas

Depois de criar o circuito, continue para as próximas etapas:

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
