---
title: Solucionar problemas de conexão de serviço área de trabalho virtual do Windows Outono 2019-Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de locatário da Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad5e740a2874d398f3947fe755024bedc897ec73
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085785"
---
# <a name="troubleshoot-windows-virtual-desktop-service-connections"></a>Solucionar problemas de conexões do Windows Virtual Desktop Service

>[!IMPORTANT]
>Esse conteúdo se aplica à versão Outono 2019 que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager introduzidos na atualização Spring 2020, confira [este artigo](../troubleshoot-service-connection.md).

Use este artigo para resolver problemas com as conexões de cliente da Área de Trabalho Virtual do Windows.

## <a name="provide-feedback"></a>Fornecer comentários

Forneça comentários e troque informações sobre o Serviço da Área de Trabalho Virtual do Windows com a equipe do produto e outros membros ativos da comunidade na [Tech Community da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O usuário se conecta, mas nada é exibido (sem feed)

Um usuário consegue iniciar clientes da Área de Trabalho Remota e se autenticar, mas não vê nenhum ícone no feed de descoberta da Web.

Confirme se o usuário que está informando os problemas foi atribuído a grupos de aplicativos por meio desta linha de comando:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Confirme se o usuário está fazendo logon com as credenciais corretas.

Se o cliente Web estiver sendo usado, confirme se não há problemas de credenciais em cache.

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview-2019.md).
- Veja como solucionar problemas ao criar um pool de locatários e de hosts em um ambiente da Área de Trabalho Virtual do Windows em [Criação de pool de locatários e de hosts](troubleshoot-set-up-issues-2019.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration-2019.md).
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell-2019.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
