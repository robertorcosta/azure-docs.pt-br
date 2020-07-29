---
title: Solucionar problemas de conexão de serviço área de trabalho virtual do Windows (clássico)-Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de locatário da área de trabalho virtual do Windows (clássico).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 213f7843e4896f8c7c9234e2c94433afbed1d565
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292514"
---
# <a name="windows-virtual-desktop-classic-service-connections"></a>Conexões de serviço da área de trabalho virtual (clássica) do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à área de trabalho virtual do Windows (clássico), que não dá suporte a Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver tentando gerenciar Azure Resource Manager objetos da área de trabalho virtual do Windows, consulte [Este artigo](../troubleshoot-service-connection.md).

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
