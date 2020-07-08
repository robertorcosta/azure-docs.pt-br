---
title: Visão geral de redirecionamento do Gateway de Aplicativo do Azure
description: Saiba mais sobre o recurso de redirecionamento no Aplicativo Azure gateway para redirecionar o tráfego recebido em um ouvinte para outro ouvinte ou para um site externo.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 8c2bac1bee5a2301a29589f15d72d0f69b4a05b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253844"
---
# <a name="application-gateway-redirect-overview"></a>Visão geral do redirecionamento do Gateway de Aplicativo

Você pode usar o gateway de aplicativo para redirecionar o tráfego.  Contém um mecanismo de redirecionamento genérico, que permite o redirecionamento do tráfego recebido em um ouvinte para outro ouvinte ou para um site externo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho.

Um cenário de redirecionamento comum para muitos aplicativos Web é dar suporte ao redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicação entre o aplicativo e seus usuários ocorra em um caminho criptografado. No passado, os clientes usaram técnicas como a criação de um pool de back-end dedicado cujo único propósito é redirecionar as solicitações recebidas de HTTP para HTTPS. Com o suporte ao redirecionamento no gateway de aplicativo, você pode fazer isso simplesmente adicionando uma nova configuração de redirecionamento a uma regra de roteamento e especificando outro ouvinte com o protocolo HTTPS como o ouvinte de destino.

Há suporte para os seguintes tipos de redirecionamento:

- redirecionamento permanente 301
- 302 Encontrado
- 303 ver outros
- redirecionamento temporário 307

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
