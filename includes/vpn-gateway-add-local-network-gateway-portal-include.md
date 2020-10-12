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
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025370"
---
1. No menu do [portal do Azure](https://portal.azure.com), selecione **Criar um recurso**.

   ![Recurso](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. No campo **Pesquisar o marketplace**, digite **Gateway de rede local** e, em seguida, pressione **Enter** para pesquisar. Isso retornará uma lista de resultados. Clique em **Gateway de rede local**, depois clique no botão **Criar** para abrir a página **Criar gateway de rede local**.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="Criar um endereço IP de gateway de rede local":::

3. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

   - **Nome:** especifique um nome para seu objeto de gateway de rede local.
   - **Ponto de extremidade:** Selecione o tipo de ponto de extremidade para o dispositivo VPN local – **endereço IP** ou **FQDN (Nome de Domínio Totalmente Qualificado)** .
      - **Endereço IP**: Se você tiver um endereço IP público estático alocado do seu provedor de serviços de Internet para seu dispositivo VPN, selecione a opção de endereço IP e preencha o endereço IP, conforme mostrado no exemplo. Este é o endereço IP público do dispositivo VPN ao qual você deseja que o gateway de VPN do Azure se conecte. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados no exemplo, mas precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
      - **FQDN**: se você tiver um endereço IP público dinâmico que possa ser alterado após determinado período de tempo, geralmente determinado pelo seu provedor de serviços de Internet, você poderá usar um nome DNS constante com um serviço DNS dinâmico para apontar para seu endereço IP público atual do seu dispositivo VPN. O gateway de VPN do Azure resolverá o FQDN para determinar o endereço IP público ao qual se conectar. Uma captura de tela abaixo mostra um exemplo de como usar o FQDN em vez do endereço IP.
   - **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. *Se deseja se conectar ao site local, use seus próprios valores aqui, e não os valores mostrados no exemplo*.
   - **Configurar as definições de BGP ASN**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
   - **Assinatura:** Verifique se a assinatura correta está sendo exibida.
   - **Grupo de recursos:** Selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
   - **Localização:** a localização é a mesma que a **Região** em outras configurações. Selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

   Essa é a mesma página, mas com o FQDN realçado:

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="Criar um endereço IP de gateway de rede local":::

   > [!NOTE]
   >
   > * A VPN do Azure dá suporte a apenas um endereço IPv4 para cada FQDN. Se o nome de domínio for resolvido para vários endereços IP, o Gateway de VPN do Azure usará o primeiro endereço IP retornado pelos servidores DNS. Para eliminar a incerteza, recomendamos que seu FQDN sempre resolva para um endereço IPv4. Não há suporte para IPv6.
   > * O Gateway de VPN do Azure mantém um cache DNS atualizado a cada 5 minutos. O gateway tenta resolver FQDNs somente para túneis desconectados. A redefinição do gateway também vai disparar a resolução do FQDN.
   >

4. Quando você terminar de especificar os valores, selecione o botão **Criar** na parte inferior da página para criar o gateway de rede local.
