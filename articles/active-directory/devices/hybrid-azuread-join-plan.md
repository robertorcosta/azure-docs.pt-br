---
title: Planejar a adesão do Azure Active Directory - Azure Active Directory
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
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129250"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como: Planeje seu Diretório Ativo azure híbrido aderir à implementação

De maneira semelhante a um usuário, um dispositivo é outra identidade principal que você deseja proteger e usá-la para proteger seus recursos a qualquer hora e em qualquer local. É possível atingir essa meta colocando e gerenciando identidades de dispositivo no Azure AD usando um dos métodos a seguir:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Ao colocar os dispositivos no Azure AD, você maximiza a produtividade dos usuários por meio de SSO (logon único) em toda a nuvem e recursos locais. Ao mesmo tempo, você pode garantir acesso aos recursos da nuvem e do local com [acesso condicional](../active-directory-conditional-access-azure-portal.md).

Se você tem um ambiente de AD (Active Directory) no local e deseja se juntar aos computadores adeptos do domínio AD ao Azure AD, você pode conseguir isso fazendo a adesão híbrida do Azure AD. Este artigo fornece as etapas relacionadas para implementar uma associação híbrida do Azure AD em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que você está familiarizado com a introdução ao gerenciamento de identidade de [dispositivos no Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> A versão mínima necessária do controlador de domínio para windows 10 híbrido Azure AD join é Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação híbrida do AD do Azure, você deve se familiarizar com:

|   |   |
| --- | --- |
| ![Verificação][1] | Dispositivos com suporte de revisão |
| ![Verificação][1] | Você deve saber de coisas de revisão |
| ![Verificação][1] | Revisar validação controlada do Azure AD híbrido se juntar |
| ![Verificação][1] | Selecione seu cenário com base em sua infra-estrutura de identidade |
| ![Verificação][1] | Revise no local o suporte a AD UPN para a adesão híbrida do Azure AD |

## <a name="review-supported-devices"></a>Dispositivos com suporte de revisão

Ingresso no Azure AD híbrido oferece suporte a dispositivos de uma ampla variedade de Windows. Como a configuração para dispositivos que executam versões mais antigas do Windows requer etapas adicionais ou diferentes, os dispositivos com suporte são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
  - **Nota**: Os clientes de nuvem do Azure National exigem a versão 1809
- Windows Server 2019

Para dispositivos que executam o sistema operacional windows desktop, a versão suportada está listada neste artigo [informações de versão do Windows 10](/windows/release-information/). Como uma prática recomendada, a Microsoft recomenda que você atualize para a versão mais recente do Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Windows 8.1
- O suporte do Windows 7 foi encerrado em 14 de janeiro de 2020. Para obter mais informações, consulte [O suporte para o Windows 7 terminou](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Para obter informações sobre suporte no Windows Server 2008 e 2008 R2, consulte [Prepare-se para o fim do suporte do Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

Como primeira etapa do planejamento, você deve revisar seu ambiente e determinar se precisa dar suporte a dispositivos de baixo nível do Windows.

## <a name="review-things-you-should-know"></a>Você deve saber de coisas de revisão

### <a name="unsupported-scenarios"></a>Cenários sem suporte
- A adesão do Azure AD híbrido não é suportada no momento se o seu ambiente consiste em uma única floresta AD sincronizando dados de identidade para mais de um inquilino Azure AD.

- O adesão do Azure AD híbrido não é suportado para o Windows Server executando a função Controlador de Domínio (DC).

- O Ad do Azure Híbrido não é suportado em dispositivos de nível baixo do Windows ao usar roaming de credenciais ou roaming de perfil de usuário ou perfil obrigatório.

- O Sistema Operacional Core do Servidor não suporta nenhum tipo de registro de dispositivo.

### <a name="os-imaging-considerations"></a>Considerações sobre imagem do SISTEMA OPERACIONAL
- Se você estiver confiando na Ferramenta de Preparação do Sistema (Sysprep) e se estiver usando uma imagem **pré-Windows 10 1809** para instalação, certifique-se de que a imagem não é de um dispositivo que já está registrado no Azure AD como AD Híbrido Azure.

- Se você estiver confiando em um instantâneo da Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o snapshot não é de uma VM que já está registrada no Azure AD como Azure AD híbrido.

- Se você estiver usando [o Unified Write Filter](/windows-hardware/customize/enterprise/unified-write-filter) e tecnologias similares que limpam as alterações no disco na reinicialização, elas devem ser aplicadas após a adesão do Ad Hybrid Azure. Habilitar tais tecnologias antes da conclusão da adesão do Hybrid Azure AD resultará na não conexão do dispositivo em cada reinicialização

### <a name="handling-devices-with-azure-ad-registered-state"></a>Dispositivos de manuseio com estado registrado no Azure AD
Se os dispositivos de adesão ao seu domínio do Windows 10 forem [Azure AD registrados](overview.md#getting-devices-in-azure-ad) no seu inquilino, isso pode levar a um duplo estado de adesão do Hybrid Azure AD e do dispositivo registrado no Azure AD. Recomendamos a atualização para o Windows 10 1803 (com KB4489894 aplicado) ou acima para abordar automaticamente este cenário. Nas versões pré-1803, você precisará remover o estado registrado do Azure AD manualmente antes de habilitar a adesão do Hybrid Azure AD. Em 1803 e acima de versões, as seguintes alterações foram feitas para evitar este estado duplo:

- Qualquer estado registrado do Azure AD existente para um usuário seria automaticamente removido <i>após o dispositivo ser aderido ao Hybrid Azure AD e o mesmo usuário fizer login</i>. Por exemplo, se o Usuário A tiver um estado De AD do Azure registrado no dispositivo, o estado duplo para o Usuário A será limpo somente quando o Usuário A fizer login no dispositivo. se houver vários usuários no mesmo dispositivo, o estado duplo é limpo individualmente quando esses usuários fazem login.
- Você pode impedir que seu dispositivo de adesão ao domínio seja registrado no Azure AD adicionando esta chave de registro - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- No Windows 10 1803, se você tiver o Windows Hello for Business configurado, o usuário precisa reconfigurar o Windows Hello for Business após a limpeza do estado duplo. Esta questão foi tratada com KB4512509

> [!NOTE]
> O dispositivo registrado no Azure AD não será removido automaticamente se for gerenciado pela Intune.

### <a name="additional-considerations"></a>Considerações adicionais
- Se o ambiente usar a infra-estrutura virtual de desktop (VDI), consulte [a identidade do dispositivo e a virtualização do desktop](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

- O Ad do Azure Híbrido é suportado para OTPM 2.0 compatível com FIPS e não suportado para OPm 1.2. Se seus dispositivos tiverem o TPM 1.2 compatível com FIPS, você deve desabilitá-los antes de prosseguir com a adesão do Hybrid Azure AD. A Microsoft não fornece nenhuma ferramenta para desativar o modo FIPS para TPMs, pois depende do fabricante de TPM. Entre em contato com o OEM de hardware para obter suporte. 

- A partir da versão do Windows 10 1903, os TPMs 1.2 não são usados com a adesão híbrida do Azure AD e os dispositivos com esses TPMs serão considerados como se não tivessem um TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Revisar validação controlada do Azure AD híbrido se juntar

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows se registrarão automaticamente como dispositivos no seu inquilino Azure AD. O estado dessas identidades de dispositivos no Azure AD é referido como adesão a Azure AD híbrido. Mais informações sobre os conceitos abordados neste artigo podem ser encontradas no artigo [Introdução ao gerenciamento de identidade de dispositivos no Azure Active Directory](overview.md).

As organizações podem querer fazer uma validação controlada da adesão híbrida do Azure AD antes de habilitá-lo em toda a sua organização de uma só vez. Revise a [validação controlada do azure ad híbrido para](hybrid-azuread-join-control.md) entender como realizá-lo.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecione seu cenário com base em sua infra-estrutura de identidade

O Azure AD híbrido funciona com ambientes gerenciados e federados, dependendo se o UPN é roteável ou não. Veja a parte inferior da página para ver os cenários suportados.  

### <a name="managed-environment"></a>Ambiente de leitura

Um ambiente gerenciado pode ser implantado por meio da [PHS (Sincronização de Hash de Senha)](/azure/active-directory/hybrid/whatis-phs) ou [PTA (Autenticação de Passagem)](/azure/active-directory/hybrid/how-to-connect-pta) com [Logon Único Contínuo](/azure/active-directory/hybrid/how-to-connect-sso).

Esses cenários não exigem que você configure um servidor de federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que dá suporte aos requisitos a seguir. Se você tem um ambiente federado usando o AD FS (Serviços de Federação do Active Directory), os requisitos abaixo já são compatíveis.

- **Alegação WIAORMULTIAUTHN:** Esta reivindicação é necessária para fazer a adesão híbrida do Azure AD para dispositivos de nível baixo do Windows.
- **Protocolo WS-Trust:** Este protocolo é necessário para autenticar o Azure AD híbrido atual do Windows juntou dispositivos com o Azure AD. Quando você estiver usando o AD FS, será necessário habilitar os seguintes pontos de extremidade WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> O **adfs/services/trust/2005/windowstransport** e também o **adfs/services/trust/13/windowstransport** devem ser habilitados como pontos de extremidade voltados para a intranet e NÃO devem ser expostos como pontos de extremidade voltados a uma extranet por meio do proxy de aplicativo Web. Para saber mais sobre como desativar os pontos finais do WS-Trust Windows, consulte [Desativar os pontos finais do WS-Trust Windows no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Você pode ver quais pontos finais são habilitados através do console de gerenciamento AD FS em **Service** > **Endpoints**.

> [!NOTE]
> O Azure AD não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar o ingresso no Azure AD híbrido. O assistente permite simplificar significativamente o processo de configuração. Se a instalação da versão necessária do Azure AD Connect não for uma opção, consulte [como configurar manualmente o registro do dispositivo](hybrid-azuread-join-manual.md). 

Com base no cenário que corresponde à sua infra-estrutura de identidade, veja:

- [Configure diretório ativo híbrido do Azure para ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configure o Azure Active Directory híbrido para ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Revise os usuários de AD no local Suporte upn para aadesão do Hybrid Azure AD

Às vezes, as UPNs dos usuários de AD no local podem ser diferentes das UPNs AD do Azure. Nesses casos, o ingresso no Azure AD Híbrido do Windows 10 dá suporte limitado aos UPNs do AD local com base no [método de autenticação](/azure/security/fundamentals/choose-ad-authn), no tipo de domínio e na versão do Windows 10. Há dois tipos de UPNs do AD local que podem existir em seu ambiente:

- Routable users UPN: Um UPN routable tem um domínio verificado válido, que é registrado em um registrador de domínio. Por exemplo, se contoso.com é o domínio primário do Azure AD, contoso.org é o domínio primário no AD local pertencente à Contoso e [verificado no Azure AD](/azure/active-directory/fundamentals/add-custom-domain)
- USUÁRIOS não roteáveis UPN: Uma UPN não routable não tem um domínio verificado. É aplicável somente dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio primário no Azure AD, contoso.local é o domínio primário no AD local, mas não é um domínio verificável na Internet e é usado apenas na rede da Contoso.

> [!NOTE]
> As informações nesta seção se aplicam apenas a uma UPN de usuários locais. Não se aplica a um sufixo de domínio de computador no local (exemplo: computer1.contoso.local).

A tabela a seguir fornece detalhes sobre o suporte a esses UPNs do AD local no ingresso no Azure AD Híbrido do Windows 10

| Tipo de UPN do AD local | Tipo de domínio | Versão do Windows 10 | Descrição |
| ----- | ----- | ----- | ----- |
| Roteável | Federado | Da versão 1703 | Disponível para o público geral |
| Não roteável | Federado | Da versão 1803 | Disponível para o público geral |
| Roteável | Gerenciada | Da versão 1803 | Geralmente disponível, o Azure AD SSPR na tela de bloqueio do Windows não é suportado |
| Não roteável | Gerenciada | Sem suporte | |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configure o Azure Active Directory híbrido para ambiente](hybrid-azuread-join-federated-domains.md)
> federado[Configure híbrido Azure Active Directory se una para ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
