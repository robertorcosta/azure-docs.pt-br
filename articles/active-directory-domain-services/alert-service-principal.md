---
title: Resolver alertas principais de serviço sustais nos Serviços de Domínio azure AD | Microsoft Docs
description: Saiba como solucionar problemas nos alertas de configuração principal do serviço para os Serviços de Domínio do Diretório Ativo do Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257957"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Problemas conhecidos: Alertas principais de serviço sustais nos serviços de domínio do Diretório Ativo do Azure

[Os princípios de serviço](../active-directory/develop/app-objects-and-service-principals.md) são aplicativos que a plataforma Azure usa para gerenciar, atualizar e manter um domínio gerenciado pelo Azure AD DS. Se um diretor de serviço for excluído, a funcionalidade no domínio gerenciado pelo Azure AD DS será impactada.

Este artigo ajuda você a solucionar problemas e resolver alertas de configuração relacionados ao serviço.

## <a name="alert-aadds102-service-principal-not-found"></a>Alerta AADDS102: Diretor de serviço não encontrado

### <a name="alert-message"></a>Mensagem de alerta

*Um princípio de serviço necessário para que os Serviços de Domínio AD do Azure funcionem corretamente foi excluído do diretório Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, corrigir e sincronizar seu domínio gerenciado.*

Se um principal de serviço necessário for excluído, a plataforma Azure não poderá executar tarefas de gerenciamento automatizadas. O domínio gerenciado pelo Azure AD DS pode não aplicar corretamente atualizações ou fazer backups.

### <a name="check-for-missing-service-principals"></a>Verificar entidades de serviço ausentes

Para verificar qual diretor de serviço está faltando e precisa ser recriado, complete as seguintes etapas:

1. No portal Azure, selecione **O Diretório Ativo do Azure** no menu de navegação à esquerda.
1. Selecione **Aplicativos empresariais**. Escolha *Todos os aplicativos* do menu suspenso tipo de **aplicativo** e, em seguida, **selecione Aplicar**.
1. Procure por cada um dos IDs do aplicativo. Se nenhum aplicativo existente for encontrado, siga as etapas de *Resolução* para criar o principal do serviço ou recadastrar o namespace.

    | ID do aplicativo | Resolução |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recriar um principal de serviço ausente](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Reregistre o namespace microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Reregistre o namespace microsoft.AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Reregistre o namespace microsoft.AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Recriar um diretor de serviço ausente

Se o ID do aplicativo *2565bd9d-da50-47d4-8b85-4c97f669dc36* estiver faltando no diretório Azure AD, use o Azure AD PowerShell para concluir as seguintes etapas. Para obter mais informações, consulte [instalar o Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Instale o módulo Ad PowerShell do Azure e importe-o da seguinte forma:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Agora recrie o principal de serviço usando o [cmdlet New-AzureAdServicePrincipal:][New-AzureAdServicePrincipal]

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

O Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

### <a name="re-register-the-microsoft-aad-namespace"></a>Reregistre o namespace do Microsoft AAD

Se o iD do aplicativo *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a -dc74e5d09022*, ou *d87dcbc6-a371-462e-88e3-28ad15ec4e64* está faltando no seu diretório Azure AD, completar as seguintes etapas para recadastrar o provedor de recursos *Microsoft.AAD:*

1. No portal Azure, procure e selecione **Assinaturas**.
1. Escolha a assinatura associada ao seu domínio gerenciado pelo Azure AD DS.
1. Na navegação à esquerda, escolha **Provedores de recursos**.
1. Procure *microsoft.AAD*e selecione **Re-registrar**.

O Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alerta AADDS105: O aplicativo de sincronização de senha está desatualizado

### <a name="alert-message"></a>Mensagem de alerta

*O principal do serviço com o ID do aplicativo "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi excluído e depois recriado. A recreação deixa para trás permissões inconsistentes nos recursos do Azure AD Domain Services necessários para atender ao seu domínio gerenciado. A sincronização de senhas no domínio gerenciado pode ser afetada.*

O Azure AD DS sincroniza automaticamente contas de usuário e credenciais do Azure AD. Se houver um problema com o aplicativo Azure AD usado para este processo, a sincronização de credencial entre o Azure AD DS e o Azure AD falhará.

### <a name="resolution"></a>Resolução

Para recriar o aplicativo Azure AD usado para sincronização de credenciais, use o Azure AD PowerShell para concluir as seguintes etapas. Para obter mais informações, consulte [instalar o Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. Instale o módulo Ad PowerShell do Azure e importe-o da seguinte forma:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Agora exclua o aplicativo e o objeto antigos usando os seguintes cmdlets do PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Depois de excluir ambos os aplicativos, a plataforma Azure os recria automaticamente e tenta retomar a sincronização de senhas. O Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="next-steps"></a>Próximas etapas

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
