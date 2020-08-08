---
title: Solucionar problemas de conexão de serviço da Área de Trabalho Virtual do Windows – Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de locatário da Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c6d0360a4fe957f43e38fd892cef6b4ab0a2325a
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009368"
---
# <a name="windows-virtual-desktop-service-connections"></a>Conexões de serviço da Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Use este artigo para resolver problemas com as conexões de cliente da Área de Trabalho Virtual do Windows.

## <a name="provide-feedback"></a>Fornecer comentários

Forneça comentários e troque informações sobre o Serviço da Área de Trabalho Virtual do Windows com a equipe do produto e outros membros ativos da comunidade na [Tech Community da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>O usuário se conecta, mas nada é exibido (sem feed)

Um usuário consegue iniciar clientes da Área de Trabalho Remota e se autenticar, mas não vê nenhum ícone no feed de descoberta da Web.

1. Confirme se o usuário que está informando os problemas foi atribuído a grupos de aplicativos por meio desta linha de comando:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Confirme se o usuário está entrando com as credenciais corretas.

3. Se o cliente Web estiver sendo usado, confirme se não há problemas de credenciais em cache.

4. Se o usuário fizer parte de um grupo de usuários Azure Active Directory (AD), verifique se o grupo de usuários é um grupo de segurança em vez de um grupo de distribuição. A área de trabalho virtual do Windows não dá suporte a grupos de distribuição do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao criar um ambiente de Área de Trabalho Virtual do Windows e pool de host em um ambiente da Área de Trabalho Virtual do Windows, consulte [Criação de ambiente e pool de host](troubleshoot-set-up-issues.md).
- Veja como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration.md).
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
