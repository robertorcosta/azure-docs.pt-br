---
title: Balanceamento de carga do pool de host do Windows Virtual Desktop - Azure
description: Métodos de balanceamento de carga do pool de host para um ambiente de desktop virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127864"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga de pool de host

O Windows Virtual Desktop suporta dois métodos de balanceamento de carga. Cada método determina qual host de sessão hospedará a sessão de um usuário quando ele se conectar a um recurso em um pool de host.

Os seguintes métodos de balanceamento de carga estão disponíveis no Windows Virtual Desktop:

- O balanceamento de carga de largura-primeira permite que você distribua uniformemente as sessões dos usuários através dos hosts de sessão em um pool de host.
- O balanceamento de carga em profundidade permite saturar um host de sessão com sessões de usuário em um pool de host. Uma vez que a primeira sessão atinja seu limite limite de sessão, o balanceador de carga direciona quaisquer novas conexões de usuário para o próximo host de sessão no pool de host até que atinja seu limite, e assim por diante.

Cada pool de host só pode configurar um tipo de balanceamento de carga específico para ele. No entanto, ambos os métodos de balanceamento de carga compartilham os seguintes comportamentos, não importa em qual pool de host eles estejam:

- Se um usuário já tiver uma sessão no pool de host e estiver se reconectando a essa sessão, o balanceador de carga irá redirecioná-lo com sucesso para o host de sessão com sua sessão existente. Esse comportamento se aplica mesmo se a propriedade AllowNewConnections do host de sessão estiver definida como Falsa.
- Se um usuário ainda não tiver uma sessão no pool de hostes, o balanceador de carga não considerará hosts de sessão cuja propriedade AllowNewConnections está definida como False durante o balanceamento de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de balanceamento de carga de amplitude-primeira

O método de balanceamento de carga de amplitude permite que você distribua conexões de usuário para otimizar para este cenário. Este método é ideal para organizações que desejam fornecer a melhor experiência para os usuários que se conectam ao seu ambiente de desktop virtual agrupado.

O primeiro método de amplitude consulta os hosts de sessão que permitem novas conexões. Em seguida, o método seleciona o host de sessão com o menor número de sessões. Se houver empate, o método selecionará o primeiro host de sessão na consulta.

## <a name="depth-first-load-balancing-method"></a>Método de balanceamento de carga em profundidade

O método de balanceamento de carga de profundidade permite saturar um host de sessão por vez para otimizar para este cenário. Este método é ideal para organizações conscientes de custos que querem um controle mais granular sobre o número de máquinas virtuais que eles alocaram para um pool de host.

O primeiro método de profundidade consulta os hosts de sessão que permitem novas conexões e não ultrapassaram o limite máximo de sessão. Em seguida, o método seleciona o host de sessão com maior número de sessões. Se houver um empate, o método selecionará o primeiro host de sessão na consulta.