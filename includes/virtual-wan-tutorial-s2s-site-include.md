---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488921"
---
1. Na página de portal da sua WAN virtual, na seção **Conectividade**, selecione **Sites VPN** para abrir a página Sites VPN.
2. Na página **Sites VPN**, clique em **+Criar site**.

   ![Noções básicas](./media/virtual-wan-tutorial-site-include/basics.png "Noções básicas")
3. Na página **Criar Site VPN**, na guia **Básico**, preencha os seguintes campos:

    * **Região** - anteriormente conhecida como Local. Esse é o local em que você deseja criar o recurso de site.
    * **Nome**: o nome que você deseja dar ao site local.
    * **Fornecedor do dispositivo** - o nome do fornecedor do dispositivo VPN (por exemplo: Citrix, Cisco, Barracuda). Isso pode ajudar a equipe do Azure a entender melhor seu ambiente para possível e futuramente adicionar otimizações ou para ajudá-lo a solucionar problemas.
    * **Border Gateway Protocol** - quando habilitado, significa que todas as conexões do site estarão habilitadas para BGP. Eventualmente, você configurará as informações de BGP para cada link do site VPN na seção Links. Configurar o BGP em uma WAN Virtual é equivalente a configurar o BGP em um gateway de rede virtual VPN do Azure. Seu endereço de par no nível de protocolo BGP local não deve ser o mesmo que o endereço IP público do VPN para dispositivo ou o espaço de endereço da VNet do site VPN. Use um endereço IP diferente no dispositivo VPN para o IP de par no nível de protocolo BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. No entanto, ele não pode ser um endereço APIPA (169.254.x.x). Especifique esse endereço no site VPN correspondente que representa o local. Para saber os pré-requisitos do BGP, confira [Sobre o BGP com o Gateway de VPN do Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Você sempre pode editar uma conexão VPN para atualizar seus parâmetros BGP (o IP de emparelhamento no link e o número do sistema autônomo) quando a configuração de BGP do site VPN estiver habilitada.
    * **Espaço de endereço privado** - o espaço do endereço IP localizado no local. O tráfego destinado a esse espaço de endereço é roteado para o site local. Isso é necessário quando o BGP não está habilitado para o site.
    * **Hubs** - o hub ao qual você deseja que o Site se conecte. Um site só pode ser conectado a hubs que têm um Gateway de VPN. Se você não vir o hub, primeiro crie um gateway de VPN no hub.
4. Selecione **Links** para adicionar informações sobre os links físicos no branch. Se você tiver um dispositivo CPE de parceiro de WAN virtual, verifique com o parceiro para ver se essas informações são trocadas com o Azure como parte do upload de informações do branch configurado em seus sistemas.

   ![links](./media/virtual-wan-tutorial-site-include/links.png "Links")

    * **Nome do link** - um nome que você deseja fornecer para o link físico no site VPN. Exemplo: mylink1.
    * **Nome do provedor** - o nome do link físico no site VPN. Exemplo: ATT, Verizon.
    * **Velocidade** - esta é a velocidade do dispositivo VPN na localização do branch. Exemplo: 50, que significa 50 Mbps na velocidade do dispositivo VPN no branch.
    * **Endereço IP** - o endereço IP público do dispositivo local que usa este link. Opcionalmente, você pode fornecer o endereço IP privado do dispositivo VPN local por trás do ExpressRoute.
5. Você pode usar a caixa de seleção para excluir ou adicionar links. Há suporte a quatro links por site VPN. Por exemplo, se você tiver quatro ISPs (provedor de serviços de Internet) no local do branch, poderá criar quatro links, um para cada ISP, e fornecer as informações para cada link.
6. Depois de terminar de preencher os campos, selecione **Revisão + Criar** para verificar e criar o site.
7. Exiba o status na página dos sites VPN. O site passará para **Conexão necessária** porque ainda não foi conectado ao Hub.