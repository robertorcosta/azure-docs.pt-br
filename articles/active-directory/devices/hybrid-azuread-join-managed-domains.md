---
title: Configurar o ingresso no Azure Active Directory híbrido para domínios gerenciados | Microsoft Docs
description: Saiba como configurar o ingresso no Azure Active Directory híbrido para domínios gerenciados.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082496"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: Configurar ingresso híbrido do Azure Active Directory para domínios gerenciados

Neste tutorial, você aprenderá a configurar a junção do Azure Active Directory híbrido (Azure AD) para Active Directory dispositivos ingressados no domínio. Esse método dá suporte a um ambiente gerenciado que inclui o Active Directory local e o Azure AD.

Como um usuário na sua organização, um dispositivo é uma identidade importante que você quer proteger. É possível usar uma identidade do dispositivo para proteger seus recursos a qualquer momento e de qualquer local. Você pode atingir essa meta Gerenciando identidades de dispositivo no Azure AD. Use um dos métodos a seguir:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Este artigo se concentra na junção híbrida do Azure AD.

Colocar os dispositivos no Azure AD maximiza a produtividade do usuário por meio de SSO (logon único) em recursos locais e na nuvem. Você pode proteger o acesso aos recursos locais e na nuvem com o [Acesso Condicional](../active-directory-conditional-access-azure-portal.md) ao mesmo tempo.

Você pode implantar um ambiente gerenciado usando a [sincronização de hash de senha (PHS)](../hybrid/whatis-phs.md) ou a [autenticação de passagem (PTA)](../hybrid/how-to-connect-pta.md) com [logon único contínuo](../hybrid/how-to-connect-sso.md). Esses cenários não exigem que você configure um servidor de federação para autenticação.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar ingresso no Azure AD híbrido
> * Habilitar dispositivos de nível inferior do Windows
> * Verificar dispositivos ingressados
> * Solução de problemas

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

- O [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou posterior)
- As credenciais de um administrador global para o locatário do Azure AD
- Credenciais de administrador corporativo para cada uma das florestas

Familiarize-se com estes artigos:

- [O que é uma identidade do dispositivo?](overview.md)
- [Como planejar sua implementação de junção de Azure Active Directory híbrida](hybrid-azuread-join-plan.md)
- [Validação controlada do ingresso no Azure AD híbrido](hybrid-azuread-join-control.md)

> [!NOTE]
> O Azure Active Directory não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

Verifique se o Azure AD Connect sincronizou os objetos de computador dos dispositivos que você quer que sejam unidos ao Azure AD híbrido com o Azure AD. Se os objetos de computador pertencerem a UOs (unidades organizacionais) específicas, configure as UOs para sincronização no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador usando o Azure AD Connect, consulte [filtragem baseada em unidade organizacional](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

A partir da versão 1.1.819.0, Azure AD Connect inclui um assistente para configurar o ingresso no Azure AD híbrido. O assistente simplifica significativamente o processo de configuração. O assistente configura os SCPs (pontos de conexão do serviço) para registro do dispositivo.

As etapas de configuração neste artigo se baseiam no uso do assistente no Azure AD Connect.

O ingresso no Azure AD híbrido requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft dentro da rede da organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (se você usa ou planeja usar o SSO contínuo)

Se sua organização exigir acesso à Internet por meio de um proxy de saída, é recomendável [implementar a descoberta automática de proxy da Web (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) para habilitar os computadores com Windows 10 para o registro de dispositivo com o Azure AD. Para resolver problemas de configuração e gerenciamento de WPAD, consulte [Solucionando problemas de detecção automática](/previous-versions/tn-archive/cc302643(v=technet.10)).

Se você não usar o WPAD, poderá definir as configurações de proxy no computador, começando com o Windows 10 1709. Para obter mais informações, consulte [configurações de proxy WinHTTP implantadas por GPO](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se você definir as configurações de proxy em seu computador usando as configurações de WinHTTP, qualquer computador que não possa se conectar ao proxy configurado não conseguirá se conectar à Internet.

Se sua organização exigir acesso à Internet por meio de um proxy de saída autenticado, verifique se os computadores com Windows 10 podem se autenticar com êxito no proxy de saída. Como os computadores com Windows 10 executam o registro de dispositivo usando o contexto da máquina, configure a autenticação de proxy de saída usando o contexto da máquina. Acompanhe com o provedor de proxy de saída nos requisitos de configuração.

Verifique se o dispositivo pode acessar os recursos da Microsoft acima na conta do sistema usando o script [testar conectividade de registro de dispositivo](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Configurar ingresso no Azure AD híbrido

Para configurar uma junção híbrida do Azure AD usando Azure AD Connect:

1. Inicie o Azure AD Connect e selecione **Configurar**.

   ![Bem-vindo](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. Em **tarefas adicionais**, selecione **Configurar opções de dispositivo**e, em seguida, selecione **Avançar**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Em **visão geral**, selecione **Avançar**.

   ![Visão geral](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. Em **conectar ao Azure ad**, insira as credenciais de um administrador global para seu locatário do Azure AD.  

   ![Conectar ao AD do Azure](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. Em **Opções de dispositivo**, selecione **Configurar ingresso no Azure ad híbrido**e, em seguida, selecione **Avançar**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Na **configuração do SCP**, para cada floresta em que você deseja Azure ad Connect para configurar o SCP, conclua as etapas a seguir e, em seguida, selecione **Avançar**.

   1. Selecione a **floresta**.
   1. Selecione um **serviço de autenticação**.
   1. Selecione **Adicionar** para inserir as credenciais de administrador corporativo.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Em **sistemas operacionais de dispositivos**, selecione os sistemas operacionais que os dispositivos em seu ambiente de Active Directory usam e, em seguida, selecione **Avançar**.

   ![Sistema operacional do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Em **pronto para configurar**, selecione **Configurar**.

   ![Pronto para configurar](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. Em **configuração concluída**, selecione **sair**.

   ![Configuração concluída](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos ingressados no domínio forem dispositivos de nível inferior do Windows, você deverá:

- Definir as configurações de Intranet Local para registro do dispositivo
- Configurar o SSO contínuo
- Instalar o Workplace Join da Microsoft em computadores Windows de nível inferior

> [!NOTE]
> O suporte do Windows 7 foi encerrado em 14 de janeiro de 2020. Para obter mais informações, consulte o [suporte do Windows 7 terminou](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Definir as configurações de Intranet Local para registro do dispositivo

Para concluir a junção híbrida do Azure AD de seus dispositivos de nível inferior do Windows e para evitar prompts de certificado quando os dispositivos são autenticados no Azure AD, você pode enviar por push uma política para seus dispositivos ingressados no domínio para adicionar as seguintes URLs à zona da intranet local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Você também deve habilitar **a permissão permitir atualizações na barra de status por meio do script** na zona da intranet local do usuário.

### <a name="configure-seamless-sso"></a>Configurar o SSO contínuo

Para concluir a junção híbrida do Azure AD de seus dispositivos de nível inferior do Windows em um domínio gerenciado que usa a [sincronização de hash de senha](../hybrid/whatis-phs.md) ou a autenticação de [passagem](../hybrid/how-to-connect-pta.md) como seu método de autenticação de nuvem do Azure AD, você também deve [Configurar o SSO contínuo](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Instalar o Workplace Join da Microsoft em computadores Windows de nível inferior

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar [o Microsoft Workplace Join para computadores que não são do Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). O Workplace Join da Microsoft para computadores sem Windows 10 está disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o  [Microsoft Endpoint Configuration Manager](/configmgr/). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro `quiet`. A versão atual do Configuration Manager oferece benefícios em relação às versões anteriores, como a capacidade de controlar os registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa une silenciosamente o dispositivo com o Azure AD usando as credenciais do usuário depois que ele se autentica com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registro

Verifique o estado de registro do dispositivo em seu locatário do Azure usando **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** . Esse cmdlet está no [módulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ao usar o cmdlet **Get-MSolDevice** para verificar os detalhes do serviço:

- É necessário que haja um objeto com a **identificação do dispositivo** correspondente à ID no cliente do Windows.
- O valor de **DeviceTrustType** é **ingressado no domínio**. Essa configuração equivale ao estado **ingressou no Azure AD híbrido** na página **Dispositivos** no portal do Azure AD.
- Para dispositivos que são usados no acesso condicional, o valor de **habilitado** é **true** e o **DeviceTrustLevel** é **gerenciado**.

Para verificar os detalhes do serviço:

1. Abra o Windows PowerShell como administrador.
1. Digite `Connect-MsolService` para se conectar ao locatário do Azure.  
1. Digite `get-msoldevice -deviceId <deviceId>`.
1. Verifique se **Habilitado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Solucionar problemas de implementação

Se você tiver problemas ao concluir a junção híbrida do Azure AD para dispositivos Windows ingressados no domínio, consulte:

- [Solução de problemas de dispositivos ingressados no Azure Active Directory híbrido](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Avance para o próximo artigo para saber como gerenciar identidades de dispositivo usando o portal do Azure.
> [!div class="nextstepaction"]
> [Gerenciar identidades do dispositivo](device-management-azure-portal.md)
