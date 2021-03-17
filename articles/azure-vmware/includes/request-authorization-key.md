---
title: Solicitar chave de autorização para ExpressRoute
description: Etapas para a solicitação da chave de autorização para ExpressRoute.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491819"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. No portal do Azure, na seção **Conectividade**, na guia **ExpressRoute**, selecione **+ Solicitar uma chave de autorização**. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Captura de tela mostrando como solicitar uma chave de autorização do ExpressRoute." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Forneça um nome para a chave e selecione **Criar**. 
      
   Pode levar cerca de 30 segundos para a criação da chave. Depois de criada, a nova chave aparece na lista de chaves de autorização para a nuvem privada.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Captura de tela mostrando a chave de autorização do ExpressRoute de Alcance Global.":::
  
1. Anote a chave de autorização e a ID do ExpressRoute. Você as usará para concluir o emparelhamento.  

   > [!NOTE]
   > A chave de autorização desaparece depois de algum tempo, portanto, copie-a assim que ela for exibida.