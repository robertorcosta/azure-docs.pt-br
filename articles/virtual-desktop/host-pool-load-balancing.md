---
title: Balanceamento de carga do pool de hosts da área de trabalho virtual do Windows-Azure
description: Saiba mais sobre métodos de balanceamento de carga do pool de hosts para um ambiente de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 72a7c275cfc30a8ebaae5325ce003f5f274ce190
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069976"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga de pool de host

>[!IMPORTANT]
>Este conteúdo se aplica à atualização da Spring 2020 com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows na versão 2019, sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).
>
> A atualização 2020 da Área de Trabalho Virtual do Windows está em versão prévia pública no momento. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A área de trabalho virtual do Windows dá suporte a dois métodos de balanceamento de carga. Cada método determina qual host de sessão hospedará a sessão de um usuário quando se conectar a um recurso em um pool de hosts.

Os seguintes métodos de balanceamento de carga estão disponíveis na área de trabalho virtual do Windows:

- O balanceamento de carga em primeiro lugar permite que você distribua uniformemente as sessões de usuário nos hosts de sessão em um pool de hosts.
- O balanceamento de carga antes da profundidade permite saturar um host de sessão com sessões de usuário em um pool de hosts. Depois que a primeira sessão atingir seu limite de limite de sessão, o balanceador de carga direcionará todas as novas conexões de usuário para o próximo host de sessão no pool de hosts até atingir seu limite e assim por diante.

Cada pool de hosts só pode configurar um tipo de balanceamento de carga específico para ele. No entanto, ambos os métodos de balanceamento de carga compartilham os seguintes comportamentos, independentemente do pool de hosts em que estão:

- Se um usuário já tiver uma sessão no pool de hosts e estiver se reconectando a essa sessão, o balanceador de carga irá redirecioná-las com êxito para o host de sessão com a sessão existente. Esse comportamento se aplica mesmo se a propriedade AllowNewConnections do host da sessão estiver definida como false.
- Se um usuário ainda não tiver uma sessão no pool de hosts, o balanceador de carga não considerará os hosts de sessão cuja propriedade AllowNewConnections está definida como false durante o balanceamento de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de balanceamento de carga da primeira amplitude

O método de balanceamento de carga da primeira amplitude permite distribuir conexões de usuário para otimizar esse cenário. Esse método é ideal para organizações que desejam fornecer a melhor experiência para os usuários que se conectam ao ambiente de área de trabalho virtual em pool.

O método amplitude-First consulta primeiro os hosts de sessão que permitem novas conexões. O método seleciona o host da sessão com o menor número de sessões. Se houver um vínculo, o método selecionará o primeiro host de sessão na consulta.

## <a name="depth-first-load-balancing-method"></a>Método de balanceamento de carga da primeira profundidade

O método de balanceamento de carga de profundidade primeiro permite saturar um host de sessão por vez para otimizar esse cenário. Esse método é ideal para organizações econômicas que desejam um controle mais granular sobre o número de máquinas virtuais alocadas para um pool de hosts.

O método depth-first primeiro consulta os hosts de sessão que permitem novas conexões e que não passaram por seu limite máximo de sessão. O método seleciona o host da sessão com o número mais alto de sessões. Se houver um vínculo, o método selecionará o primeiro host de sessão na consulta.