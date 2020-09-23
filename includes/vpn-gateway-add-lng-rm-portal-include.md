---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025193"
---
1. No portal, clique em **+Criar um recurso**.
2. Na caixa de pesquisa da página, insira **Gateway de rede local**, depois pressione **Enter** para pesquisar. Isso retornará uma lista de resultados. Clique em **Gateway de rede local**, depois clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="Criar o gateway de rede local":::

3. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

   - **Nome:** especifique um nome para seu objeto de gateway de rede local.
   - **Ponto de extremidade:** Selecione o tipo de ponto de extremidade para o dispositivo VPN local- **endereço IP** ou **FQDN (nome de domínio totalmente qualificado)**.
      - **Endereço IP**: se você tiver um endereço IP público estático alocado do seu provedor de serviços de Internet para seu dispositivo VPN, selecione a opção endereço IP e preencha o endereço IP, conforme mostrado no exemplo. Esse é o endereço IP público do dispositivo VPN ao qual você deseja que o gateway de VPN do Azure se conecte. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados no exemplo, mas precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
      - **FQDN**: se você tiver um endereço IP público dinâmico que possa ser alterado após determinado período de tempo, geralmente determinado pelo seu provedor de serviços de Internet, você poderá usar um nome DNS constante com um serviço DNS dinâmico para apontar para o endereço IP público atual do seu dispositivo VPN. O gateway de VPN do Azure resolverá o FQDN para determinar o endereço IP público ao qual se conectar. Uma captura de tela abaixo mostra um exemplo de como usar o FQDN em vez do endereço IP.
   - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. *Se deseja se conectar ao site local, use seus próprios valores aqui, e não os valores mostrados no exemplo*.
   - **Configurar as definições de BGP ASN**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
   - **Assinatura:** Verifique se a assinatura correta está sendo exibida.
   - **Grupo de recursos:** Selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
   - **Local:** Selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

    Esta é a mesma página, mas com o FQDN realçado:
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="Criar o gateway de rede local-FQDN":::
   
   > [!NOTE]
   >
   > * A VPN do Azure dá suporte a apenas um endereço IPv4 para cada FQDN. Se o nome de domínio for resolvido para vários endereços IP, o gateway de VPN do Azure usará o primeiro endereço IP retornado pelos servidores DNS. Para eliminar a incerteza, é recomendável que seu FQDN sempre resolva um único endereço IPv4. Não há suporte para IPv6.
   > * O gateway de VPN do Azure mantém um cache DNS atualizado a cada 5 minutos. O gateway tenta resolver os FQDNs somente para túneis desconectados. A redefinição do gateway também irá disparar a resolução do FQDN.
   >

4. Quando terminar de especificar os valores, selecione o botão **criar** para criar o gateway.
