---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/09/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 56f79bf38b627f80d73b59dbbfbb73dddd809458
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525138"
---
Em um navegador, acesse o portal do Azure e entre com sua conta do Azure.

1. Navegue até a página da WAN virtual. No portal, clique em **+Criar um recurso**. Digite **WAN Virtual** na caixa de pesquisa e clique em Enter.
1. Selecione **WAN Virtual** nos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Criar WAN.
1. Na página **Criar WAN**, na guia **Básico**, preencha os seguintes campos:

   :::image type="content" source="./media/virtual-wan-create-vwan-include/basics.png" alt-text="Noções básicas":::

   * **Assinatura**: selecione a assinatura que você quer usar.
   * **Grupo de recursos**: crie um novo ou use um existente.
   * **Localização do grupo de recursos**: escolha uma localização de recursos na lista suspensa. Uma WAN é um recurso global e não pode residir em uma região específica. No entanto, você deve selecionar uma região a fim de gerenciar e localizar o recurso de WAN criado mais facilmente.
   * **Nome**: digite o nome que você quer dar à sua WAN.
   * **Tipo**: Básico ou Standard. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes somente de conectividade VPN site a site.
1. Quando terminar de preencher os campos, selecione **Examinar + Criar**.
1. Depois que a validação for aprovada, selecione **Criar** para criar a WAN Virtual.
