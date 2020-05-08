---
title: Visão geral da solução de problemas de área de trabalho virtual do Windows – Azure
description: Uma visão geral para solucionar problemas durante a configuração de um ambiente de locatário de área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 196c4f692b425d2fc6592888ba89f2fa6dafd3de
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612496"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Visão geral da solução de problemas, comentários e suporte

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma visão geral dos problemas que você pode encontrar ao configurar um ambiente de locatário de área de trabalho virtual do Windows e fornece maneiras de resolver os problemas.

## <a name="report-issues-during-public-preview"></a>Relatar problemas durante a visualização pública

Para relatar problemas ou sugerir recursos durante a visualização pública da versão 2020 do Spring, visite a [comunidade técnica de área de trabalho virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Você pode usar a comunidade de tecnologia para discutir as práticas recomendadas ou sugerir e votar em novos recursos. Ao relatar um problema relacionado à versão de visualização pública, certifique-se de rotular o tipo de problema como **atualização 2020 do Spring (versão prévia)**.

Quando você fizer uma postagem solicitando ajuda ou propor um novo recurso, certifique-se de descrever seu tópico com o máximo de detalhes possível. Informações detalhadas podem ajudar outros usuários a responder sua pergunta ou entender o recurso para o qual você está propondo um voto.

## <a name="escalation-tracks"></a>Faixas de escalonamento

Antes de fazer qualquer outra coisa, certifique-se de verificar a [página de status](https://status.azure.com/status) do Azure e a [integridade do serviço do Azure](https://azure.microsoft.com/features/service-health/) para verificar se o serviço do Azure está sendo executado corretamente.

Use a tabela a seguir para identificar e resolver os problemas que você pode encontrar ao configurar um ambiente de locatário usando o Área de Trabalho Remota Client. Depois de configurar o locatário, você pode usar nosso novo [serviço de diagnóstico](diagnostics-role-service.md) para identificar problemas em cenários comuns.

| **Problema**                                                            | **Solução sugerida**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Acessando modelos do Marketplace no portal do Azure       | Os modelos de área de trabalho virtual do Windows do Azure Marketplace estão disponíveis gratuitamente.|
| Configurações de rede virtual (VNET) do pool de hosts da sessão e de rota expressa               | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione o serviço apropriado (na categoria rede). |
| Criação de VM (máquina virtual) do pool de hosts da sessão quando Azure Resource Manager modelos fornecidos com a área de trabalho virtual do Windows não estão sendo usados | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/)e selecione **máquina virtual executando o Windows** para o serviço. <br> <br> Para problemas com os modelos de Azure Resource Manager que são fornecidos com a área de trabalho virtual do Windows, consulte a seção criando locatário da área de trabalho virtual do Windows do [ambiente e criação do pool de hosts](troubleshoot-set-up-issues.md). |
| Gerenciando o ambiente de host de sessão de área de trabalho virtual do Windows no portal do Azure    | [Abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Para problemas de gerenciamento ao usar o Serviços de Área de Trabalho Remota/Windows Virtual Desktop PowerShell, consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) ou [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço, selecione **configuração e gerenciamento** para o tipo de problema e, em seguida, selecione **problemas ao configurar o locatário usando o PowerShell** para o subtipo de problema. |
| Gerenciando a configuração da área de trabalho virtual do Windows vinculada a pools de hosts e grupos de aplicativos      | Consulte [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)ou [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione o tipo de problema apropriado.|
| Implantando e gerenciando contêineres de perfil FSLogix | Consulte [Guia de solução de problemas para produtos FSLogix](/fslogix/fslogix-trouble-shooting-ht/) e, se isso não resolver o problema, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço, selecione **FSLogix** para o tipo de problema e, em seguida, selecione o subtipo de problema apropriado. |
| Problemas de clientes de área de trabalho remota em iniciar                                                 | Consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md) e, se isso não resolver o problema, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione **área de trabalho remota clientes** para o tipo de problema.  <br> <br> Se for um problema de rede, os usuários precisarão entrar em contato com o administrador da rede. |
| Conectado, mas sem feed                                                                 | Solucionar problemas usando o [usuário se conecta, mas nada é exibido (sem feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) seção de [conexões do serviço de área de trabalho virtual do Windows](troubleshoot-service-connection.md). <br> <br> Se os usuários tiverem sido atribuídos a um grupo de aplicativos, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/), selecione **área de trabalho virtual do Windows** para o serviço e, em seguida, selecione **área de trabalho remota clientes** para o tipo de problema. |
| Problemas de descoberta de feed devido à rede                                            | Os usuários precisam entrar em contato com o administrador da rede. |
| Conectando clientes                                                                    | Consulte [conexões do serviço de área de trabalho virtual do Windows](troubleshoot-service-connection.md) e, se isso não resolver o problema, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md). |
| Capacidade de resposta de aplicativos remotos ou área de trabalho                                      | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |
| Mensagens de licenciamento ou erros                                                          | Se os problemas estiverem vinculados a um aplicativo ou produto específico, entre em contato com a equipe responsável pelo produto. |
| Problemas com métodos de autenticação de terceiros | Verifique se o provedor de terceiros dá suporte a cenários de área de trabalho virtual do Windows e aborde-os em relação a problemas conhecidos. |
| Problemas ao usar o Log Analytics para área de trabalho virtual do Windows | Para problemas com o esquema de diagnóstico, [abra uma solicitação de suporte do Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Para consultas, visualização ou outros problemas no Log Analytics, selecione o tipo de problema apropriado em Log Analytics. |

## <a name="next-steps"></a>Próximas etapas

- Para solucionar problemas durante a criação de um pool de hosts em um ambiente de área de trabalho virtual do Windows, consulte [ambiente e criação de pool de hosts](troubleshoot-set-up-issues.md).
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md)
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
- Para percorrer um tutorial de solução de problemas, consulte [tutorial: solucionar problemas de implantações de modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber mais sobre as ações para determinar erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
