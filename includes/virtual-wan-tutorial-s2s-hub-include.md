---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488978"
---
1. Localize o WAN Virtual que você criou. Na página WAN virtual, na seção **Conectividade**, selecione **Hubs**.
2. Na página Hubs, selecione **+Novo Hub** para abrir a página **Criar Hub Virtual**.

    ![Noções básicas](./media/virtual-wan-tutorial-hub-include/basics.png "Noções básicas")
3. Na página **Criar Hub Virtual**, na guia **Básico**, preencha os seguintes campos:

    **Detalhes do projeto**

   * Região (anteriormente conhecida como Local)
   * NOME
   * Espaço de endereço privado do hub. O espaço de endereço mínimo é /24 para criar um hub, e isso significa que qualquer intervalo entre /25 e /32 produzirá um erro durante a criação.
4. Selecione **Avançar: Site a site**.

    ![Site a site](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Site a site")

5. Na guia **Site a site**, preencha os seguintes campos:

   * Selecione **Sim** para criar uma VPN site a site.
   * No momento, o campo Número do sistema autônomo não pode ser editado no hub virtual.
   * Selecione o valor de **Unidades de escala de gateway** no menu suspenso. A unidade de escala permite escolher a taxa de transferência agregada do gateway de VPN que está sendo criado no hub virtual ao qual conectar os sites. Se você escolher que 1 unidade de escala = 500 Mbps, isso significará que duas instâncias de redundância serão criadas, cada uma com uma taxa de transferência máxima de 500 Mbps. Por exemplo, se você tiver cinco branches, cada um com 10 Mbps, precisará de um total de 50 Mbps na cabeceira de rede. O planejamento da capacidade total do gateway de VPN do Azure deve ser realizado depois de avaliar a capacidade necessária para dar suporte ao número de branches para o Hub.
6. Selecione **Revisar + Criar** para validar.
7. Selecione **Criar** para criar o hub. Depois de 30 minutos, **Atualize** para exibir o hub na página **Hubs**. Selecione **Ir para o recurso** para navegar até o recurso.