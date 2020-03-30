---
title: Criar e associar políticas de ponto de extremidade de serviço – portal do Azure
titlesuffix: Azure Virtual Network
description: Neste artigo, saiba como configurar e associar políticas de ponto de extremidade de serviço usando o portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651119"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Criar, alterar ou excluir a política de ponto de extremidade de serviço usando o portal do Azure

As políticas de ponto de extremidade de serviço permitem que você filtre o tráfego de rede virtual para recursos específicos do Azure em pontos de extremidade de serviço. Se você não estiver familiarizado com as políticas de ponto de extremidade de serviço, confira [visão geral das políticas de ponto de extremidade de serviço](virtual-network-service-endpoint-policies-overview.md) para saber mais.

 Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma política de ponto de extremidade de serviço
> * Criar uma definição da política de ponto de extremidade de serviço
> * Criar uma rede virtual com uma sub-rede
> * Associar uma política de ponto de extremidade de serviço a uma sub-rede

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Criar uma política de ponto de extremidade de serviço

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. No painel de pesquisa, digite "service endpoint policy" e selecione **'Política de ponto final de serviço'** e selecione **Criar**.

![Criar política de ponto final de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Insira, ou selecione, as seguintes informações em **Básico** 

   - Assinatura : Selecione sua assinatura para política
   - Grupo de recursos : Selecione **Criar novo** e digite *o myResourceGroup*
   - Nome: myEndpointPolicy
   - Localização : Central EUA
 
   ![Criar o básico da política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Selecione **+ Adicione** em **Recursos** e insira ou selecione as seguintes informações em Adicionar um painel **de recursos**

   - Serviço : Somente **o Microsoft.Storage** está disponível com políticas de ponto final de serviço
   - Escopo : Selecione uma fora da **Conta Única,** **Todas as contas em assinatura** e todas as contas no grupo de **recursos**
   - Assinatura: selecione sua assinatura para a conta de armazenamento. A política e as contas de armazenamento podem estar em diferentes assinaturas.
   - Grupo de recursos: selecione seu grupo de recursos. Obrigatório, se o escopo estiver definido como "Todas as contas no grupo de recursos" ou "Conta individual".  
   - Recurso : Selecione seu recurso de armazenamento do Azure o grupo de recursos ou assinatura selecionado
   - Clique em **Adicionar** botão na parte inferior para terminar de adicionar o recurso

   ![Definição de política de ponto final de serviço - recurso](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Adicione mais recursos repetindo as etapas acima, conforme necessário

5. Opcional: insira ou selecione as seguintes informações em **Tags**:
   
   - Chave: selecione sua chave para a política. Por exemplo: Depto     
   - Valor: insira um par de valor para a chave. Por exemplo: Finanças

6. Selecione **Revisão + Criar**. Valide as informações e clique em **Criar**. Para fazer mais edições, clique em **Anterior**. 

   ![Criar validações definitivas da política de ponto de extremidade de serviço](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Exibir políticas de ponto de extremidade 

1. Na caixa *Todos os serviços* no portal, comece digitando *políticas de ponto de extremidade de serviço*. Selecione **Políticas de ponto final de serviço**.
2. Em **Assinaturas**, selecione sua assinatura e grupo de recursos, conforme mostrado na imagem a seguir

   ![Mostrar política](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Selecione a política e clique em **Definições de política** para exibir ou adicionar mais definições de política.

   ![Mostrar definições de política](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Selecione **Sub-redes associadas** para exibir as sub-redes às quais a política está associada. Se ainda não houver nenhuma sub-rede associada, siga as instruções no próximo passo.

   ![Sub-redes associadas](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Associar uma política a uma sub-rede

>[!WARNING] 
> Certifique-se de que todos os recursos acessados da sub-rede sejam adicionados à definição de política antes de associar a política à sub-rede dada. Uma vez que a diretiva esteja associada, somente o acesso aos recursos *listados de permissão* será permitido sobre os pontos finais do serviço. 
>
> Certifique-se também de que não existam serviços Gerenciados do Azure na sub-rede que está sendo associada à política de ponto final do serviço

- Antes de associar uma política a uma sub-rede, você tem que criar uma rede virtual e uma sub-rede. Consulte o artigo [Criar uma Rede Virtual](./quick-create-portal.md) para obter ajuda com isso.

- Uma vez que a rede virtual e a sub-rede estejam configuradas, você precisa configurar os pontos finais do serviço de rede virtual para o armazenamento Azure. Na lâmina Rede Virtual, selecione **pontos finais do Serviço**e, no painel seguinte, selecione **Microsoft.Storage** e em **Subnets** selecione a VNet ou sub-rede desejada

- Agora, você pode optar por selecionar a diretiva ponto final de serviço a partir do drop-down no painel acima, se você já tiver criado políticas de ponto final de serviço antes de configurar o ponto final de serviço para a sub-rede, conforme mostrado abaixo

    ![Associar sub-rede ao criar o ponto final do serviço](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- OU se você estiver associando as políticas de ponto final de serviço depois que os pontos finais de serviço já estiverem configurados, você pode optar por associar a sub-rede dentro da lâmina de diretiva de ponto final de serviço navegando para o painel **Subnets associados,** conforme mostrado abaixo

    ![Sub-rede associada via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>O acesso aos recursos de armazenamento do Azure em todas as regiões será restrito de acordo com a Política de Ponto final de serviço a partir desta sub-rede.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou uma política de ponto de extremidade de serviço e a associou a uma sub-rede. Para saber mais sobre políticas de ponto de extremidade de serviço, confira [visão geral de políticas de ponto de extremidade de serviço.](virtual-network-service-endpoint-policies-overview.md)
