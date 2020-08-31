---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 517acc5137d70c722d8defade1e218a3b2e78f86
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052513"
---
É possível criar uma VNet com um modelo de implantação do Resource Manager e com o portal do Azure seguindo estas etapas. Para obter mais informações sobre redes virtuais, confira [Visão geral da rede virtual](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Ao usar uma rede virtual como parte de uma arquitetura entre locais, é necessário coordenar com o administrador de rede local para que ele consiga um intervalo de endereços IP que possa ser usado especificamente para essa rede virtual. Se houver um intervalo de endereços duplicado nos dois lados da conexão de VPN, o tráfego será roteado inesperadamente. Além disso, se você quiser conectar essa rede virtual a outra rede virtual, o espaço de endereço não poderá se sobrepor com a outra rede virtual. Planeje sua configuração de rede de forma adequada.
>
>

1. Entre no [portal do Azure](https://portal.azure.com).
1. Em **Pesquisar recursos, serviço e documentos (G+/)** , digite *rede virtual*.

   ![Localizar a página de recursos de Rede Virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Localizar a página de recursos de rede virtual")
1. Selecione **Rede Virtual** nos resultados do **Marketplace**.

   ![Selecione a rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Localizar a página de recursos de rede virtual")
1. Na página **Rede Virtual**, selecione **Criar**.

   ![página de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Selecione Criar")
1. Depois que você selecionar, **Criar**, a página **Criar rede virtual** será aberta.
1. Na guia **Básico**, configure os **Detalhes do projeto** e as definições de VNet dos **Detalhes da instância**.

   ![Guia Básico](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Guia Básico") Quando você preencher os campos, verá a marca de seleção verde quando os caracteres inseridos no campo forem validados. Alguns valores são preenchidos automaticamente, que podem ser substituídos por seus próprios valores:

   - **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando o menu suspenso.
   - **Grupo de recursos**: Selecione um grupo de recursos ou clique em **Criar** para criar um. Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Name**: insira o nome de sua rede virtual.
   - **Região**: selecione a localização de sua VNet. A localização determina onde ficarão os recursos que você implanta nessa rede virtual.

1. Na guia **endereços IP**, configure os valores. Os valores mostrados nos exemplos a seguir servem para fins de demonstração. Ajuste esses valores de acordo com as configurações que você precisa.

   ![Guia de endereços IP](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Guia de endereços IP")  
   - **Espaço de endereço IPv4**: por padrão, um espaço de endereço é criado automaticamente. Você pode clicar no espaço de endereço para ajustá-lo a fim de refletir seus valores. Também é possível adicionar outros espaços de endereço.
   - **Sub-rede**: se você usar o espaço de endereço padrão, uma sub-rede padrão será criada automaticamente. Se você alterar o espaço de endereço, precisará adicionar uma sub-rede. Selecione **+ Adicionar sub-rede** para abrir a janela **Adicionar sub-rede**. Defina as seguintes configurações e selecione **Adicionar** para adicionar os valores:
      - **Nome da sub-rede**: neste exemplo, nomeamos a sub-rede "FrontEnd".
      - **Intervalo de endereços da sub-rede**: o intervalo de endereços para esta sub-rede.

1. Na guia **Segurança**, neste momento, deixe os valores padrão:

   - **Proteção contra DDoS**: Basic
   - **Firewall**: Desabilitado
1. Selecione **Examinar + criar** para validar as configurações de rede virtual.
1. Depois que as configurações forem validadas, selecione **Criar**.
