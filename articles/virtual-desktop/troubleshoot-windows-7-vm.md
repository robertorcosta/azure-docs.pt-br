---
title: Máquinas virtuais do Windows 7 Windows Virtual Desktop - Azure
description: Como resolver problemas para máquinas virtuais (VMs) do Windows 7 em um ambiente de Desktop Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127380"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Solucionar problemas de máquinas virtuais do Windows 7 na Área de Trabalho Virtual do Windows

Use este artigo para solucionar problemas que você está tendo ao configurar as Máquinas virtuais de hospedagem de desktop virtual do Windows (VMs).

## <a name="known-issues"></a>Problemas conhecidos

O Windows 7 no Windows Virtual Desktops não suporta os seguintes recursos:

- Aplicativos virtualizados (RemoteApps)
- Redirecionamento do fuso horário
- Dimensionamento automático de DPI

O Windows Virtual Desktop só pode virtualizar desktops completos para o Windows 7.

Embora o dimensionamento automático de DPI não seja suportado, você pode alterar manualmente a resolução em sua máquina virtual clicando com o botão direito do mouse no ícone no cliente da Área de Trabalho Remota e selecionando **Resolução**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Erro: Não é possível acessar o grupo de usuários de desktop remoto

Se o Windows Virtual Desktop não conseguir encontrar você ou as credenciais de seus usuários no grupo de usuários de desktop remoto, você poderá ver uma das seguintes mensagens de erro:

- "Este usuário não é um membro do grupo de usuários de desktop remoto"
- "Você deve receber permissões para fazer login através de serviços de desktop remotos"

Para corrigir esse erro, adicione o usuário ao grupo de usuário de desktop remoto:

1. Abra o portal do Azure.
2. Selecione a máquina virtual em que você viu a mensagem de erro.
3. Selecione **Executar um comando**.
4. Execute o seguinte `<username>` comando com substituído pelo nome do usuário que deseja adicionar:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```