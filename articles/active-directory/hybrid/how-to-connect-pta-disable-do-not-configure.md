---
title: Desabilite o PTA ao usar o Azure AD Connect "Não configure" | Microsoft Docs
description: Este artigo descreve como desativar o PTA com o recurso Azure AD Connect "não configurar".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726794"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Desativar pta ao usar o Azure AD Connect "Não configurar"

Se você estiver usando a Autenticação pass-through com o Azure AD Connect e você tiver ele definido como "Não configure", você pode desativá-lo. A desativação do PTA pode ser feita usando os seguintes cmdlets. 

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos são necessários:
- Qualquer máquina de janelas que tenha o agente PTA instalado. 
- O agente deve estar na versão 1.5.1742.0 ou posterior. 
- Uma conta de administrador global do Azure para executar os cmdlets do PowerShell para desativar o PTA.

>[!NOTE]
> Se o seu agente for mais velho, então ele pode não ter os cmdlets necessários para concluir esta operação. Você pode obter um novo agente do Azure Portal para instalá-lo em qualquer máquina de janelas e fornecer credenciais de administrador. (A instalação do agente não afeta o status PTA na nuvem)

> [!IMPORTANT]
> Se você estiver usando a nuvem do Azure Government, então você terá que passar no parâmetro ENVIRONMENTNAME com o seguinte valor. 
>
>| Nome do ambiente | Nuvem |
>| - | - |
>| AzureUSGovernment | Gov dos EUA|


## <a name="to-disable-pta"></a>Para desativar o PTA
A partir de uma sessão powerShell, use o seguinte para desativar pta:
1. PS C:\Arquivos do programa\Microsoft Azure AD Connect Authentication Agent>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` ou `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` ou `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Se você não tem acesso a um agente

Se você não tiver uma máquina de agente, você pode usar o comando seguinte para instalar um agente.

1. Baixe o mais recente Auth Agent de portal.azure.com.
2. Instale o `.\AADConnectAuthAgentSetup.exe` recurso: ou`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Próximas etapas

- [Entrada do usuário com autenticação de passagem do Azure Active Directory](how-to-connect-pta.md)