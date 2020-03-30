---
title: Visão geral da solução de problemas do Windows Virtual Desktop - Azure
description: Uma visão geral para solucionar problemas ao configurar um ambiente de inquilino do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127411"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Visão geral da solução de problemas, comentários e suporte

Este artigo fornece uma visão geral dos problemas que você pode encontrar ao configurar um ambiente de inquilino do Windows Virtual Desktop e fornece maneiras de resolver os problemas.

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="escalation-tracks"></a>Faixas de escalada

Use a tabela a seguir para identificar e resolver problemas que você pode encontrar ao configurar um ambiente de inquilino usando o cliente Remote Desktop. Uma vez configurado o seu inquilino, você pode usar nosso novo [serviço de Diagnóstico](diagnostics-role-service.md) para identificar problemas para cenários comuns.

>[!NOTE]
> Temos um fórum da Comunidade Tecnológica que você pode visitar para discutir seus problemas com a equipe de produtos e membros ativos da comunidade. Visite a [Comunidade De Desktop Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para iniciar uma discussão.

| **Problema**                                                            | **Solução sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Criando um inquilino de desktop virtual do Windows                                                    | Se houver uma paralisação do Azure, [abra uma solicitação de suporte do Azure;](https://azure.microsoft.com/support/create-ticket/) caso [contrário, abra uma solicitação de suporte do Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **o Windows Virtual Desktop** para o serviço, selecione **Deployment** para o tipo de problema e selecione **Problemas criando um inquilino do Windows Virtual Desktop** para o subtipo de problemas.|
| Acessando modelos de Marketplace no portal Azure       | Se houver uma paralisação do Azure, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Os modelos de Desktop Virtual do Azure Marketplace Windows estão disponíveis gratuitamente.|
| Acessando os modelos do Azure Resource Manager do GitHub                                  | Consulte a [seção Criar a sessão de desktop virtual do Windows, a](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) criação de VMs de [inquilino e pool de host](troubleshoot-set-up-issues.md). Se o problema ainda não estiver resolvido, entre em contato com a equipe de suporte do [GitHub](https://github.com/contact). <br> <br> Se o erro ocorrer depois de acessar o modelo no GitHub, entre em contato com [o Azure Support](https://azure.microsoft.com/support/create-ticket/).|
| Pool de sessões Azure Virtual Network (VNET) e Express Route configurações               | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione o serviço apropriado (na categoria Rede). |
| Criação do Pool de Bancos de Sessões Virtual Machine (VM) quando os modelos do Azure Resource Manager fornecidos com o Windows Virtual Desktop não estão sendo usados | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione **A Máquina Virtual executando** o Windows para o serviço. <br> <br> Para obter problemas com os modelos do Azure Resource Manager fornecidos com o Windows Virtual Desktop, consulte Criando a seção de inquilino do Windows Virtual Desktop de [inquilino e criação de pool de host](troubleshoot-set-up-issues.md). |
| Gerenciamento do ambiente de host de sessão virtual do Windows a partir do portal Azure    | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Para problemas de gerenciamento ao usar o Remote Desktop Services/Windows Virtual Desktop PowerShell, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou abra uma solicitação de suporte ao [Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **o Windows Virtual Desktop** para o serviço, selecione **Configuração e gerenciamento** para o tipo de problema e selecione **Problemas configurando o inquilino usando** o PowerShell para o subtipo de problemas. |
| Gerenciamento da configuração do Windows Virtual Desktop vinculado a pools de host e grupos de aplicativos (grupos de aplicativos)      | Consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)ou abra uma solicitação de suporte do [Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **o Windows Virtual Desktop** para o serviço e selecione o tipo de problema apropriado.|
| Implantação e gerenciamento de contêineres de perfil FSLogix | Consulte [o guia de solução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e, se isso não resolver o problema, Abra uma solicitação de suporte ao [Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **o Windows Virtual Desktop** para o serviço, selecione **FSLogix** para o tipo de problema e selecione o subtipo de problema apropriado. |
| Falha dos clientes de desktop remotos no início                                                 | Consulte [Solucionar problemas do cliente da Área de Trabalho Remota](troubleshoot-client.md) e, se isso não resolver o problema, Abra uma solicitação de suporte ao [Azure,](https://azure.microsoft.com/support/create-ticket/)selecione **o Windows Virtual Desktop** para o serviço e selecione **clientes de Área de Trabalho Remota** para o tipo de problema.  <br> <br> Se for um problema de rede, seus usuários precisam entrar em contato com o administrador da rede. |
| Conectado, mas sem ração                                                                 | Solucionar problemas usando o [Usuário se conecta, mas nada é exibido (sem feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) seção de [conexões](troubleshoot-service-connection.md)de serviço do Windows Virtual Desktop . <br> <br> Se seus usuários tiverem sido atribuídos a um grupo de aplicativos, [abra uma solicitação de suporte ao Azure,](https://azure.microsoft.com/support/create-ticket/)selecione o Windows Virtual **Desktop** para o serviço e selecione **Clientes de Desktop Remoto** para o tipo de problema. |
| Problemas de descoberta de feed devido à rede                                            | Seus usuários precisam entrar em contato com o administrador da rede. |
| Conectando clientes                                                                    | Consulte [as conexões de serviço do Windows Virtual Desktop](troubleshoot-service-connection.md) e, se isso não resolver o seu problema, consulte a [configuração da máquina virtual do host session](troubleshoot-vm-configuration.md). |
| Receptividade de aplicativos remotos ou desktop                                      | Se os problemas estiverem vinculados a uma aplicação ou produto específico, entre em contato com a equipe responsável por esse produto. |
| Mensagens de licenciamento ou erros                                                          | Se os problemas estiverem vinculados a uma aplicação ou produto específico, entre em contato com a equipe responsável por esse produto. |
| Problemas ao usar ferramentas do Windows Virtual Desktop no GitHub (modelos do Azure Resource Manager, ferramenta de diagnóstico, ferramenta de gerenciamento) | Consulte [os modelos do Gerenciador de recursos do Azure para serviços de desktop remotos](https://github.com/Azure/RDS-Templates/blob/master/README.md) para relatar problemas. |

## <a name="next-steps"></a>Próximas etapas

- Para solucionar problemas ao criar um inquilino e um pool de host em um ambiente de Desktop Virtual do Windows, consulte [Criação de inquilino e pool de host](troubleshoot-set-up-issues.md).
- Para solucionar problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte [Configuração da máquina virtual do host session](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de clientes do Windows Virtual Desktop, consulte [conexões de serviço do Windows Virtual Desktop](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes de desktop remoto, consulte [Solucionar problemas do cliente de desktop remoto](troubleshoot-client.md)
- Para solucionar problemas ao usar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o [ambiente Windows Virtual Desktop](environment-setup.md).
- Para passar por um tutorial de solução de problemas, consulte Tutorial: Implantação de [modelo do Gerenciador de recursos de solução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre ações para determinar erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
