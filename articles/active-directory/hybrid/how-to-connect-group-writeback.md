---
title: 'Azure AD Connect: Write-back de grupo'
description: Este artigo descreve o Write-back de grupo no Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c8fb54261f51d74f02b7b79c27f7a2043426686
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385136"
---
# <a name="azure-ad-connect-group-writeback"></a>Write-back de grupo de Azure AD Connect

Write-back de grupos permite que os clientes aproveitem os grupos de nuvem para suas necessidades híbridas. Se usar o recurso Grupos do Office 365 , você poderá ter esses grupos representados no Active Directory local. Essa opção **só** estará disponível se você tiver o Exchange presente em seu Active Directory local.

## <a name="pre-requisites"></a>Pré-requisitos
Os pré-requisitos a seguir devem ser atendidos para habilitar o Write-back do grupo.
- Azure Active Directory Premium licenças para seu locatário.
- Uma implantação híbrida configurada entre sua organização do Exchange local e o Office 365 e verificada se ela está funcionando corretamente.
- Instalou uma versão com suporte do Exchange local
- Logon único configurado usando o Azure Active Directory Connect 

## <a name="enable-group-writeback"></a>Habilitar write-back de grupo
Para habilitar o Write-back de grupo, use as seguintes etapas:

1. Abra o assistente de Azure AD Connect, selecione **Configurar** e clique em **Avançar**.
2. Selecione **Personalizar opções de sincronização** e clique em **Avançar**.
3. Na página **conectar ao Azure ad** , insira suas credenciais. Clique em **Próximo**.
4. Na página **recursos opcionais** , verifique se as opções configuradas anteriormente ainda estão selecionadas.
5. Selecione **grupo write-back** e clique em **Avançar**.
6. Na **página write-back**, selecione uma UO (unidade organizacional) Active Directory para armazenar objetos que são sincronizados do Office 365 para sua organização local e, em seguida, clique em **Avançar**.
7. Na página **pronto** para configurar, clique em **Configurar**.
8. Quando o assistente for concluído, clique em **sair** na página configuração concluída.
9. Abra o Windows PowerShell como administrador no servidor de Azure Active Directory Connect e execute os comandos a seguir.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

Para obter informações adicionais sobre como configurar os grupos do Office 365, confira [configurar grupos de Microsoft 365 com o Exchange híbrido local](https://docs.microsoft.com/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Desabilitando write-back de grupo
Para desabilitar o Write-back de grupo, use as seguintes etapas: 


1. Inicie o assistente de Azure Active Directory Connect e navegue até a página tarefas adicionais. Selecione a tarefa **Personalizar opções de sincronização** e clique em **Avançar**.
2. Na página **recursos opcionais** , desmarque o Write-back do grupo.  Você receberá um aviso informando que os grupos serão excluídos.  Clique em **Sim**.
   >[!IMPORTANT]
   > A desabilitação do write-back do grupo fará com que todos os grupos que foram criados anteriormente por esse recurso sejam excluídos do Active Directory local no próximo ciclo de sincronização. 

   ![Desmarcar caixa](media/how-to-connect-group-writeback/group2.png)
  
3. Clique em **Próximo**.
4. Clique em **Configurar**.

 >[!NOTE]
 > A desabilitação do write-back do grupo definirá os sinalizadores de importação completa e sincronização completa como ' verdadeiro ' no conector de Azure Active Directory, fazendo com que as alterações de regra se propaguem no próximo ciclo de sincronização, excluindo os grupos que foram gravados anteriormente no Active Directory.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
