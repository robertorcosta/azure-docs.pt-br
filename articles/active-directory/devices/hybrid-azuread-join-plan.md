---
title: Plan hybrid Azure Active Directory join - Azure Active Directory
description: Saiba como configurar dispositivos adicionados ao Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a4f85aeaf2fb263ba2df8f34a51f9e25c212aff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379314"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>How To: Plan your hybrid Azure Active Directory join implementation

De maneira semelhante a um usuário, um dispositivo é outra identidade principal que você deseja proteger e usá-la para proteger seus recursos a qualquer hora e em qualquer local. É possível atingir essa meta colocando e gerenciando identidades de dispositivo no Azure AD usando um dos métodos a seguir:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Ao colocar os dispositivos no Azure AD, você maximiza a produtividade dos usuários por meio de SSO (logon único) em toda a nuvem e recursos locais. At the same time, you can secure access to your cloud and on-premises resources with [Conditional Access](../active-directory-conditional-access-azure-portal.md).

If you have an on-premises Active Directory (AD) environment and you want to join your AD domain-joined computers to Azure AD, you can accomplish this by doing hybrid Azure AD join. Este artigo fornece as etapas relacionadas para implementar uma associação híbrida do Azure AD em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

This article assumes that you are familiar with the [Introduction to device identity management in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> The minimum required domain controller version for Windows 10 hybrid Azure AD join is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação híbrida do AD do Azure, você deve se familiarizar com:

|   |   |
| --- | --- |
| ![Verificação][1] | Dispositivos com suporte de revisão |
| ![Verificação][1] | Você deve saber de coisas de revisão |
| ![Verificação][1] | Review controlled validation of hybrid Azure AD join |
| ![Verificação][1] | Select your scenario based on your identity infrastructure |
| ![Verificação][1] | Review on-premises AD UPN support for hybrid Azure AD join |

## <a name="review-supported-devices"></a>Dispositivos com suporte de revisão

Ingresso no Azure AD híbrido oferece suporte a dispositivos de uma ampla variedade de Windows. Como a configuração para dispositivos que executam versões mais antigas do Windows requer etapas adicionais ou diferentes, os dispositivos com suporte são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

For devices running the Windows desktop operating system, supported version are listed in this article [Windows 10 release information](https://docs.microsoft.com/windows/release-information/). As a best practice, Microsoft recommends you upgrade to the latest version of Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Windows 8.1
- Windows 7. For support information on Windows 7, see [Support for Windows 7 is ending](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. For support information on Windows Server 2008 and 2008 R2, see [Prepare for Windows Server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

Como primeira etapa do planejamento, você deve revisar seu ambiente e determinar se precisa dar suporte a dispositivos de baixo nível do Windows.

## <a name="review-things-you-should-know"></a>Você deve saber de coisas de revisão

Hybrid Azure AD join is currently not supported if your environment consists of a single AD forest synchronizing identity data to more than one Azure AD tenant.

If your environment uses virtual desktop infrastructure (VDI), see [Device identity and desktop virtualization](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Hybrid Azure AD join is supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Hybrid Azure AD join. Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. Starting from WIndows 10 1903 release, TPMs 1.2 are not used for hybrid Azure AD join and devices with those TPMs will be considered as if they don't have a TPM.

Hybrid Azure AD join is not supported for Windows Server running the Domain Controller (DC) role.

Hybrid Azure AD join is not supported on Windows down-level devices when using credential roaming or user profile roaming or mandatory profile.

If you are relying on the System Preparation Tool (Sysprep) and if you are using a **pre-Windows 10 1809** image for installation, make sure that image is not from a device that is already registered with Azure AD as Hybrid Azure AD join.

If you are relying on a Virtual Machine (VM) snapshot to create additional VMs, make sure that snapshot is not from a VM that is already registered with Azure AD as Hybrid Azure AD join.

If your Windows 10 domain joined devices are [Azure AD registered](overview.md#getting-devices-in-azure-ad) to your tenant, it could lead to a dual state of Hybrid Azure AD joined and Azure AD registered device. We recommend upgrading to Windows 10 1803 (with KB4489894 applied) or above to automatically address this scenario. In pre-1803 releases, you will need to remove the Azure AD registered state manually before enabling Hybrid Azure AD join. In 1803 and above releases, the following changes have been made to avoid this dual state:

- Any existing Azure AD registered state would be automatically removed <i>after the device is Hybrid Azure AD joined</i>.
- You can prevent your domain joined device from being Azure AD registered by adding this registry key - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- In Windows 10 1803, if you have Windows Hello for Business configured, the user needs to re-setup Windows Hello for Business after the dual state clean up.This issue has been addressed with KB4512509

> [!NOTE]
> The Azure AD registered device will not be automatically removed if it is managed by Intune.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Review controlled validation of hybrid Azure AD join

When all of the pre-requisites are in place, Windows devices will automatically register as devices in your Azure AD tenant. The state of these device identities in Azure AD is referred as hybrid Azure AD join. More information about the concepts covered in this article can be found in the article [Introduction to device identity management in Azure Active Directory](overview.md).

Organizations may want to do a controlled validation of hybrid Azure AD join before enabling it across their entire organization all at once. Review the article [controlled validation of hybrid Azure AD join](hybrid-azuread-join-control.md) to understand how to accomplish it.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Select your scenario based on your identity infrastructure

Hybrid Azure AD join works with both, managed and federated environments depending on whether the UPN is routable or non-routable. See bottom of the page for table on supported scenarios.  

### <a name="managed-environment"></a>Ambiente de leitura

Um ambiente gerenciado pode ser implantado por meio da [PHS (Sincronização de Hash de Senha)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ou [PTA (Autenticação de Passagem)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) com [Logon Único Contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Esses cenários não exigem que você configure um servidor de federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que dá suporte aos requisitos a seguir. Se você tem um ambiente federado usando o AD FS (Serviços de Federação do Active Directory), os requisitos abaixo já são compatíveis.

- **WIAORMULTIAUTHN claim:** This claim is required to do hybrid Azure AD join for Windows down-level devices.
- **WS-Trust protocol:** This protocol is required to authenticate Windows current hybrid Azure AD joined devices with Azure AD. Quando você estiver usando o AD FS, será necessário habilitar os seguintes pontos de extremidade WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> O **adfs/services/trust/2005/windowstransport** e também o **adfs/services/trust/13/windowstransport** devem ser habilitados como pontos de extremidade voltados para a intranet e NÃO devem ser expostos como pontos de extremidade voltados a uma extranet por meio do proxy de aplicativo Web. Para saber mais sobre como desabilitar os pontos de extremidade do Windows do WS-Trust, confira [Desabilitar pontos de extremidade do Windows do WS-Trust no proxy](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Veja quais pontos de extremidade estão habilitados por meio do console de gerenciamento do AD FS em **Serviço** > **Pontos de extremidade**.

> [!NOTE]
> O Azure AD não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar o ingresso no Azure AD híbrido. O assistente permite simplificar significativamente o processo de configuração. Se a instalação da versão necessária do Azure AD Connect não for uma opção, consulte [como configurar manualmente o registro do dispositivo](hybrid-azuread-join-manual.md). 

Based on the scenario that matches your identity infrastructure, see:

- [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Review on-premises AD UPN support for Hybrid Azure AD join

Às vezes, seus UPNs do AD local podem ser diferentes dos UPNs do Azure AD. Nesses casos, o ingresso no Azure AD Híbrido do Windows 10 dá suporte limitado aos UPNs do AD local com base no [método de autenticação](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), no tipo de domínio e na versão do Windows 10. Há dois tipos de UPNs do AD local que podem existir em seu ambiente:

- Routable UPN: A routable UPN has a valid verified domain, that is registered with a domain registrar. Por exemplo, se contoso.com é o domínio primário do Azure AD, contoso.org é o domínio primário no AD local pertencente à Contoso e [verificado no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Non-routable UPN: A non-routable UPN does not have a verified domain. É aplicável somente dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio primário no Azure AD, contoso.local é o domínio primário no AD local, mas não é um domínio verificável na Internet e é usado apenas na rede da Contoso.

A tabela a seguir fornece detalhes sobre o suporte a esses UPNs do AD local no ingresso no Azure AD Híbrido do Windows 10

| Tipo de UPN do AD local | Tipo de domínio | Versão do Windows 10 | Descrição |
| ----- | ----- | ----- | ----- |
| Roteável | Federado | Da versão 1703 | Disponível para o público geral |
| Não roteável | Federado | Da versão 1803 | Disponível para o público geral |
| Roteável | Gerenciado | Da versão 1803 | Generally available, Azure AD SSPR on Windows lockscreen is not supported |
| Não roteável | Gerenciado | Sem suporte | |

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
> [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
