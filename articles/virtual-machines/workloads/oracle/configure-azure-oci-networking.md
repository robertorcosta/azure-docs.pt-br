---
title: Conecte o Azure ExpressRoute com a Oracle Cloud Infrastructure | Microsoft Docs
description: Conecte o Azure ExpressRoute com o OCI Infrastructure (OCI) FastConnect para permitir soluções de aplicativos Oracle em nuvem cruzada
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: d85c0fc8986adfa00559eab1c49a79daacdeb33f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263175"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Configure uma interconexão direta entre o Azure e o Oracle Cloud Infrastructure  

Para criar uma [experiência integrada em várias nuvens,](oracle-oci-overview.md)a Microsoft e a Oracle oferecem interconexão direta entre o Azure e o Oracle Cloud Infrastructure (OCI) através [do ExpressRoute](../../../expressroute/expressroute-introduction.md) e [do FastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Através da interconexão ExpressRoute e FastConnect, os clientes podem experimentar baixa latência, alta produtividade, conectividade direta privada entre as duas nuvens.

> [!IMPORTANT]
> A Oracle certificará esses aplicativos para serem executados no Azure ao usar a solução de interconexão Azure/Oracle Cloud até maio de 2020.
> * Suíte E-Business
> * JD Edwards EnterpriseOne
> * Peoplesoft
> * Aplicativos oracle de varejo
> * Oracle Hyperion Gestão Financeira

A imagem a seguir mostra uma visão geral de alto nível da interconexão:

![Conexão de rede entre nuvens](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Pré-requisitos

* Para estabelecer conectividade entre o Azure e o OCI, você deve ter uma assinatura ativa do Azure e uma locação ativa do OCI.

* A conectividade só é possível quando um local de peering do Azure ExpressRoute estiver próximo ou no mesmo local de peering do OCI FastConnect. Consulte [Disponibilidade da região](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Configure conectividade direta entre ExpressRoute e FastConnect

1. Crie um circuito ExpressRoute padrão em sua assinatura do Azure em um grupo de recursos. 
    * Ao criar o ExpressRoute, escolha **o Oracle Cloud FastConnect** como o provedor de serviços. Para criar um circuito ExpressRoute, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Um circuito Azure ExpressRoute oferece opções de largura de banda granular, enquanto o FastConnect suporta 1, 2, 5 ou 10 Gbps. Portanto, recomenda-se escolher uma dessas opções de largura de banda correspondentes no ExpressRoute.

    ![Criar circuito ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Anote a **tecla**ExpressRoute Service . Você precisa fornecer a chave enquanto configura o circuito FastConnect.

    ![Chave do Serviço ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Você será cobrado pelas taxas do ExpressRoute assim que o circuito ExpressRoute for provisionado (mesmo que o Status do **Provedor** não esteja **provisionado**).

1. Esqueie dois espaços privados de endereço IP de /30 cada um que não se sobrepõem à sua rede virtual Azure ou ao espaço ip de rede de nuvem virtual OCI. Nos referiremos ao primeiro espaço de endereço IP como espaço de endereço primário e ao segundo espaço de endereço IP como espaço de endereço secundário. Anote os endereços que você precisa ao configurar o circuito FastConnect.
1. Crie um Gateway de Roteamento Dinâmico (DRG). Você precisará disso ao criar seu circuito FastConnect. Para obter mais informações, consulte a documentação [do Dynamic Routing Gateway.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Crie um circuito FastConnect sob o seu inquilino Oracle. Para obter mais informações, consulte a [documentação oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * Na configuração FastConnect, selecione **Microsoft Azure: ExpressRoute** como provedor.
    * Selecione o Gateway de roteamento dinâmico que você provisionou na etapa anterior.
    * Selecione a largura de banda a ser provisionada. Para um desempenho ideal, a largura de banda deve corresponder à largura de banda selecionada ao criar o circuito ExpressRoute.
    * Na **tecla de serviço do provedor,** cole a chave de serviço ExpressRoute.
    * Use o primeiro espaço de endereço IP privado /30 esculpido em uma etapa anterior para o **endereço IP BGP primário** e o segundo espaço de endereço IP privado /30 para o endereço IP **BGP secundário.**
        * Atribua o primeiro endereço utilizável das duas faixas para o endereço IP Oracle BGP (Principal e Secundário) e o segundo endereço para o endereço IP BGP do cliente (a partir de uma perspectiva FastConnect). O primeiro endereço IP utilizável é o segundo endereço IP no espaço de endereço /30 (o primeiro endereço IP é reservado pela Microsoft).
    * Clique em **Criar**.
1. Complete a vinculação do FastConnect à rede de nuvem virtual sob o seu inquilino Oracle via Dynamic Routing Gateway, usando a Tabela de Rotas.
1. Navegue até o Azure e certifique-se de que o status do **provedor** para o circuito ExpressRoute tenha sido alterado para **Provisionado** e que um peering do tipo **Azure private** tenha sido provisionado. Este é um pré-requisito para as seguintes etapas.

    ![Status do provedor ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Clique no **peering privado do Azure.** Você verá que os detalhes de peering foram configurados automaticamente com base nas informações inseridas ao configurar o circuito FastConnect.

    ![Configurações de peering privado](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Conecte a rede virtual ao ExpressRoute

1. Crie uma rede virtual e um gateway de rede virtual, se você ainda não tiver. Para obter detalhes, consulte o [guia passo a passo](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Configure a conexão entre o gateway de rede virtual e o circuito ExpressRoute executando o [script Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) ou executando o comando PowerShell para [configurar o ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

Depois de concluir a configuração da rede, você pode verificar a validade de sua configuração clicando em **Obter Registros ARP** e **Obter tabela de rota** sob a lâmina de peering Private ExpressRoute private no portal Azure.

## <a name="automation"></a>Automação

A Microsoft criou scripts Terraform para permitir a implantação automatizada da interconexão de rede. Os scripts Terraform precisam autenticar com o Azure antes da execução, porque exigem permissões adequadas na assinatura do Azure. A autenticação pode ser realizada usando [um diretor de serviço do Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) ou usando o Cli do Azure. Para obter mais informações, consulte a [documentação terraforme](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Os scripts Terraform e documentação relacionada para implantar o inter-connect podem ser encontrados neste [repositório do GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Monitoramento

Instalando agentes em ambas as nuvens, você pode aproveitar o [NPM (Azure Network Performance Monitor)](../../../expressroute/how-to-npm.md) para monitorar o desempenho da rede de ponta a ponta. O NPM ajuda você a identificar facilmente os problemas da rede e ajuda a eliminá-los.

## <a name="delete-the-interconnect-link"></a>Exclua o link de interconexão

Para excluir a interconexão, as seguintes etapas devem ser seguidas, na ordem específica dada. Caso contrário, o circuito ExpressRoute "falhou".

1. Exclua a conexão ExpressRoute. Exclua a conexão clicando no ícone **Excluir** na página para sua conexão. Para obter mais informações, consulte a [documentação do ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Exclua o Oracle FastConnect do Oracle Cloud Console.
1. Uma vez que o circuito Oracle FastConnect tenha sido excluído, você pode excluir o circuito Azure ExpressRoute.

Neste ponto, o processo de exclusão e desprovisionamento está completo.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a conexão entre o OCI e o Azure, consulte a [documentação oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Use [scripts Terraform](https://aka.ms/azureociinterconnecttf) para implantar infra-estrutura para aplicativos Oracle direcionados ao Azure e configure a interconexão de rede. 
