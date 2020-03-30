---
title: Ambiente de desktop virtual do Windows - Azure
description: Os elementos básicos de um ambiente de desktop virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127909"
---
# <a name="windows-virtual-desktop-environment"></a>Ambiente da Área de Trabalho Virtual do Windows

O Windows Virtual Desktop é um serviço que dá aos usuários acesso fácil e seguro aos seus desktops virtualizados e RemoteApps. Este tópico lhe dirá um pouco mais sobre a estrutura geral do ambiente de desktop virtual do Windows.

## <a name="tenants"></a>Locatários

O inquilino do Windows Virtual Desktop é a interface principal para gerenciar seu ambiente de Área de Trabalho Virtual do Windows. Cada inquilino do Windows Virtual Desktop deve estar associado ao Azure Active Directory contendo os usuários que farão login no ambiente. A partir do inquilino do Windows Virtual Desktop, você pode começar a criar pools de host para executar as cargas de trabalho de seus usuários.

## <a name="host-pools"></a>Piscinas de hospedagem

Um pool de hosts é uma coleção de máquinas virtuais do Azure que se registram no Windows Virtual Desktop como hosts de sessão quando você executa o agente de desktop virtual do Windows. Todas as máquinas virtuais host de sessão em um pool de host devem ser originadas da mesma imagem para uma experiência de usuário consistente.

Uma piscina de host pode ser um dos dois tipos:

- Pessoal, onde cada host de sessão é atribuído a usuários individuais.
- Pooled, onde os hosts de sessão podem aceitar conexões de qualquer usuário autorizado a um grupo de aplicativos dentro do pool de host.

Você pode definir propriedades adicionais no pool de host para alterar seu comportamento de balanceamento de carga, quantas sessões cada host de sessão pode ter e o que o usuário pode fazer com os hosts de sessão no pool de hostes enquanto estiver conectado às sessões do Windows Virtual Desktop. Você controla os recursos publicados aos usuários através de grupos de aplicativos.

## <a name="app-groups"></a>Grupos de aplicativos

Um grupo de aplicativos é um agrupamento lógico de aplicativos instalados em hosts de sessão no pool de host. Um grupo de aplicativos pode ser um dos dois tipos:

- RemoteApp, onde os usuários acessam os RemoteApps que você seleciona e publica individualmente para o grupo de aplicativos
- Desktop, onde os usuários acessam a área de trabalho completa

Por padrão, um grupo de aplicativos de desktop (chamado "Desktop Application Group") é criado automaticamente sempre que você cria um pool de hosts. Você pode remover este grupo de aplicativos a qualquer momento. No entanto, você não pode criar outro grupo de aplicativos de desktop no pool de host enquanto um grupo de aplicativos de desktop existe. Para publicar RemoteApps, você deve criar um grupo de aplicativos do RemoteApp. Você pode criar vários grupos de aplicativos do RemoteApp para acomodar diferentes cenários de trabalhadores. Diferentes grupos de aplicativos do RemoteApp também podem conter aplicativos remotos sobrepostos.

Para publicar recursos aos usuários, você deve atribuí-los a grupos de aplicativos. Ao atribuir usuários a grupos de aplicativos, considere as seguintes coisas:

- Um usuário não pode ser atribuído a um grupo de aplicativos de desktop e a um grupo de aplicativos do RemoteApp no mesmo pool de host.
- Um usuário pode ser atribuído a vários grupos de aplicativos dentro do mesmo pool de host, e seu feed será um acúmulo de ambos os grupos de aplicativos.

## <a name="tenant-groups"></a>Grupos de inquilinos

No Windows Virtual Desktop, o inquilino do Windows Virtual Desktop é onde a maior parte da configuração e configuração acontece. O inquilino do Windows Virtual Desktop contém os pools de host, grupos de aplicativos e atribuições de usuários de grupos de aplicativos. No entanto, pode haver certas situações em que você precisa gerenciar vários inquilinos do Windows Virtual Desktop ao mesmo tempo, especialmente se você é um Provedor de Serviços em Nuvem (CSP) ou um parceiro de hospedagem. Nessas situações, você pode usar um grupo de inquilinos personalizados do Windows Virtual Desktop para colocar cada um dos inquilinos do Windows Virtual Desktop dos clientes e gerenciar centralmente o acesso. No entanto, se você estiver apenas gerenciando um único inquilino do Windows Virtual Desktop, o conceito de grupo de inquilinos não se aplica e você pode continuar a operar e gerenciar seu inquilino que existe no grupo de inquilinos padrão.

## <a name="end-users"></a>Usuários finais

Depois de atribuir os usuários aos seus grupos de aplicativos, eles podem se conectar a uma implantação do Windows Virtual Desktop com qualquer um dos clientes do Windows Virtual Desktop.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre acesso delegado e como atribuir funções aos usuários no [Acesso Delegado no Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Para saber como configurar seu inquilino do Windows Virtual Desktop, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Para saber como se conectar ao Windows Virtual Desktop, consulte um dos seguintes artigos:

- [Conectar-se do Windows 10 ou Windows 7](connect-windows-7-and-10.md)
- [Conectar-se de um navegador da Web](connect-web.md)
