---
title: Configurações de Política de Grupo e MDM para ESR - Azure Active Directory
description: Configurações de gerenciamento para Roaming enterprise state
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672360"
---
# <a name="group-policy-and-mdm-settings"></a>Configurações de Política de Grupo e do MDM

Use essas configurações da política de grupo e do MDM (gerenciamento de dispositivos móveis) somente em dispositivos corporativos, já que as políticas são aplicadas ao dispositivo inteiro do usuário. A aplicação de uma política MDM para desabilitar a sincronização de configurações para um dispositivo pessoal de propriedade do usuário afetará negativamente o uso do dispositivo. Além disso, outras contas de usuário no dispositivo também serão afetadas pela política.

As empresas que quiserem gerenciar o roaming para dispositivos pessoais (não gerenciados) poderão usar o portal do Azure para habilitar ou desabilitar o roaming, em vez de usar a Política de Grupo ou o MDM.
As tabelas a seguir descrevem as configurações de política disponíveis.

> [!NOTE]
> Este artigo se aplica ao navegador baseado em HTML Microsoft Edge Legacy, lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado no Microsoft Edge Chromium, lançado em 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Configurações do MDM

As configurações da  política de MDM se aplicam ao Windows 10 e ao Windows 10 Mobile.  Há suporte do Windows Mobile 10 somente para roaming baseado em conta da Microsoft por meio da conta do OneDrive do usuário. Consulte [Dispositivos e pontos finais](enterprise-state-roaming-windows-settings-reference.md) para obter detalhes sobre quais dispositivos são suportados para sincronização baseada no Azure AD.

| Nome | Descrição |
| --- | --- |
| Permitir Conexão da Conta da Microsoft |Permite que os usuários se autentiquem usando uma conta da Microsoft no dispositivo |
| Permitir Sincronizar Configurações |Permite aos usuários mover dados de aplicativo e configurações do Windows. Desabilitar esta política desabilitará a sincronização, bem como backups em dispositivos móveis |

## <a name="group-policy-settings"></a>Configurações de política de grupo

As configurações de Política de Grupo se aplicam a dispositivos Windows 10 ingressados em um domínio do Active Directory. A tabela também inclui as configurações herdadas que apareceriam para gerenciar as configurações de sincronização, mas que não funcionam para o Enterprise State Roaming para Windows 10 e estão indicadas com "Não usar" na descrição.

Essas configurações estão localizadas em: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Nome | Descrição |
| --- | --- |
| Contas: Bloquear Contas da Microsoft |Essa configuração de política impede que os usuários adicionem novas contas da Microsoft a este computador |
| Não sincronizar |Impede que os usuários movam dados de aplicativo e configurações do Windows |
| Não sincronizar personalização |Desabilita a sincronização do grupo Temas |
| Não sincronizar configurações do navegador |Desabilita a sincronização do grupo Internet Explorer |
| Não sincronizar senhas |Desabilita a sincronização do grupo Senhas |
| Não sincronizar outras configurações do Windows |Desabilita a sincronização do grupo Outras configurações do Windows |
| Não sincronizar personalização da área de trabalho |Não use; não tem nenhum efeito |
| Não sincronizar em conexões limitadas |Desabilita o roaming em conexões limitadas, como o 3G do celular |
| Não sincronizar aplicativos |Não use; não tem nenhum efeito |
| Não sincronizar configurações do aplicativo |Desabilita o roaming de dados do aplicativo |
| Não sincronizar configurações iniciais |Não use; não tem nenhum efeito |

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral, consulte [a visão geral do State Roaming da empresa.](enterprise-state-roaming-overview.md)
