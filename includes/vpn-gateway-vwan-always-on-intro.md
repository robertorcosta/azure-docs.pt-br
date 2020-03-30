---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116960"
---
Um novo recurso do cliente VPN do Windows 10, Always On, é a capacidade de manter uma conexão VPN. Com o Always On, o perfil de VPN ativo pode se conectar automaticamente e permanecer conectado com base em gatilhos, como login do usuário, alteração do estado de rede ou tela ativa do dispositivo.

Você pode usar gateways com o Windows 10 Always On para estabelecer túneis de usuário persistentes e túneis de dispositivos para o Azure. Este artigo ajuda você a configurar um túnel de usuário Always On VPN.

As conexões SEMPRE Em VPN incluem qualquer um dos dois tipos de túneis:

* **Túnel do dispositivo**: Conecta-se a servidores VPN especificados antes que os usuários entrem no dispositivo. Os cenários de conectividade pré-login e o gerenciamento de dispositivos usam um túnel de dispositivo.

* **Túnel do usuário**: Conecta-se somente após o login dos usuários no dispositivo. Usando túneis de usuário, você pode acessar recursos da organização através de servidores VPN.

Túneis de dispositivos e túneis de usuário operam independentemente de seus perfis VPN. Eles podem ser conectados ao mesmo tempo, e podem usar diferentes métodos de autenticação e outras configurações de VPN, conforme apropriado.
