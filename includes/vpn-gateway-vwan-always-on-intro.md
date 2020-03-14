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
ms.openlocfilehash: 2783c828f96eeb3539e2266eaf1d6d590a6af4c4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370944"
---
Um novo recurso do cliente de VPN do Windows 10, Always On, é a capacidade de manter uma conexão VPN. Com o Always On, o perfil de VPN ativo pode se conectar automaticamente e permanecer conectado com base em gatilhos, como entrada do usuário, alteração de estado da rede ou tela do dispositivo ativa.

Você pode usar gateways de rede virtual do Azure com o Windows 10 Always On para estabelecer túneis de usuário persistentes e túneis de dispositivo para o Azure. Este artigo ajuda você a configurar um túnel de usuário de VPN Always On.

Always On conexões VPN incluem um dos dois tipos de túneis:

* **Túnel de dispositivo**: conecta-se a servidores VPN especificados antes que os usuários entrem no dispositivo. Os cenários de conectividade de pré-logon e o gerenciamento de dispositivos usam um túnel de dispositivo.

* **Túnel do usuário**: conecta-se somente depois que os usuários entram no dispositivo. Usando túneis de usuário, você pode acessar os recursos da organização por meio de servidores VPN.

Os túneis de dispositivo e os túneis de usuário operam independentemente de seus perfis de VPN. Eles podem ser conectados ao mesmo tempo e podem usar diferentes métodos de autenticação e outras definições de configuração de VPN, conforme apropriado.
