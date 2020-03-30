---
title: Visão geral de redirecionamento do Gateway de Aplicativo do Azure
description: Saiba mais sobre o recurso de redirecionamento no Azure Application Gateway para redirecionar o tráfego recebido em um ouvinte para outro ouvinte ou para um site externo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129875"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral do redirecionamento do Gateway de Aplicativo

Você pode usar o gateway de aplicativo para redirecionar o tráfego.  Contém um mecanismo de redirecionamento genérico, que permite o redirecionamento do tráfego recebido em um ouvinte para outro ouvinte ou para um site externo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho.

Um cenário comum de redirecionamento para muitos aplicativos web é suportar o redirecionamento https automático para HTTPS para garantir que toda a comunicação entre aplicativo e seus usuários ocorra por um caminho criptografado. No passado, os clientes usaram técnicas como a criação de um pool de back-end dedicado cujo único propósito é redirecionar as solicitações recebidas de HTTP para HTTPS. Com o suporte de redirecionamento no Application Gateway, você pode conseguir isso simplesmente adicionando uma nova configuração de redirecionamento a uma regra de roteamento e especificando outro ouvinte com protocolo HTTPS como ouvinte de destino.

Os seguintes tipos de redirecionamento são suportados:

- 301 Redirecionamento Permanente
- 302 Encontrado
- 303 Ver Outro
- 307 Redirecionamento Temporário

O suporte a redirecionamento do Gateway de Aplicativo oferece os seguintes recursos:

-  **Redirecionamento Global**

   Redirecionamento global de um ouvinte para outro ouvinte no gateway. Isso habilita o redirecionamento de HTTP para HTTPS em um site.
- **Redirecionamento baseado em caminho**

   Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas em uma área específica do site, por exemplo, uma área de carrinho de compras é denotada por /cart/*.
- **Redirecionamento para um site externo**

![redirecionamento](./media/redirect-overview/redirect.png)

Com essa alteração, os clientes precisam criar um novo objeto de configuração de redirecionamento, que especifica o ouvinte de destino ou o site externo para o qual o redirecionamento é desejado. O elemento de configuração também dá suporte a opções para habilitar o acréscimo da cadeia de consulta e do caminho de URI à URL redirecionada. Você também pode escolher o tipo de redirecionamento. Depois de criada, essa configuração de redirecionamento é anexada ao ouvinte de origem por meio de uma nova regra. Ao usar uma regra básica, a configuração de redirecionamento é associada a um ouvinte de origem e é um redirecionamento global. Quando uma regra de caminho é usada, a configuração de redirecionamento é definida no mapa de caminho de URL. Então ela se aplica somente a área de caminho específico de um site.

### <a name="next-steps"></a>Próximas etapas

[Como configurar o redirecionamento de URL em um gateway de aplicativo](tutorial-url-redirect-powershell.md)
