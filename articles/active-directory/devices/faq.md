---
title: Perguntas frequentes sobre o gerenciamento de dispositivo do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a27c9ae1b75b9517bd3af92486df96434c5b34fb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207380"
---
# <a name="azure-active-directory-device-management-faq"></a>Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory

## <a name="general-faq"></a>General FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q: I registered the device recently. Por que não consigo ver o dispositivo nas informações do meu usuário no portal do Azure? Or why is the device owner marked as N/A for hybrid Azure Active Directory (Azure AD) joined devices?

**A:** Windows 10 devices that are hybrid Azure AD joined don't show up under **USER devices**.
Use o modo de exibição **Todos os dispositivos** no portal do Azure. Você também pode usar um cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) do PowerShell.

Apenas os dispositivos a seguir estão listados sob os **dispositivos do usuário**:

- Todos os dispositivos pessoais que não são ingressados no Azure AD híbrido. 
- Todos os dispositivos não Windows 10 ou Windows Server 2016.
- Todos os dispositivos não Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q: How do I know what the device registration state of the client is?

**A:** In the Azure portal, go to **All devices**. Procure o dispositivo usando a identificação do dispositivo. Verifique o valor na coluna de tipo de associação. Às vezes, o dispositivo pode ter sido redefinido ou recriado. Portanto, é essencial verificar também o estado do registro do dispositivo no dispositivo:

- Para dispositivos Windows 10 e Windows Server 2016 ou posterior, execute `dsregcmd.exe /status`.
- Para versões de sistema operacional de nível inferior, execute `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**A:** For troubleshooting information, see these articles:
- [Troubleshooting devices using dsregcmd command](troubleshoot-device-dsregcmd.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q: I see the device record under the USER info in the Azure portal. E posso ver o estado como registrado no dispositivo. Am I set up correctly to use Conditional Access?

**A:** The device join state, shown by **deviceID**, must match the state on Azure AD and meet any evaluation criteria for Conditional Access. For more information, see [Require managed devices for cloud app access with Conditional Access](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>Q: Why do my users see an error message saying "Your organization has deleted the device" or "Your organization has disabled the device" on their Windows 10 devices ?

**A:** On Windows 10 devices joined or registered with Azure AD, users are issued a [Primary refresh token (PRT)](concept-primary-refresh-token.md) which enables single sign on. The validity of the PRT is based on the validaity of the device itself. Users see this message if the device is either deleted or disabled in Azure AD without initiating the action from the device itself. A device can be deleted or disabled in Azure AD one of the following scenarios: 

- User disables the device from the My Apps portal. 
- An administrator (or user) deletes or disables the device in the Azure portal or by using PowerShell
- Hybrid Azure AD joined only: An administrator removes the devices OU out of sync scope resulting in the devices being deleted from Azure AD
- Upgrading Azure AD connect to the version 1.4.xx.x. [Understanding Azure AD Connect 1.4.xx.x and device disappearance](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-device-disappearance).


See below on how these actions can be rectified.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Q: I disabled or deleted my device in the Azure portal or by using Windows PowerShell. But the local state on the device says it's still registered. O que devo fazer?

**A:** This operation is by design. In this case, the device doesn't have access to resources in the cloud. Administrators can perform this action for stale, lost or stolen devices to prevent unauthorized access. If this action was performed unintentionally, you'll need to re-enable or re-register the device as described below

- If the device was disabled in Azure AD, an administrator with sufficient privileges can enable it from the Azure AD portal  
  > [!NOTE]
  > If you are syncing devices using Azure AD Connect, hybrid Azure AD joined devices will be automatically re-enabled during the next  sync cycle. So, if you need to disable a hybrid Azure AD joined device, you need to disable it from your on-premises AD

 - If the device is deleted in Azure AD, you need to re-register the device. To re-register, you must take a manual action on the device. See below for instructions for re-registration based on the device state. 

      To re-register hybrid Azure AD joined Windows 10 and Windows Server 2016/2019 devices, take the following steps:

      1. Abra o prompt de comando como administrador.
      1. Digite `dsregcmd.exe /debug /leave`.
      1. Saia e entre para disparar a tarefa agendada que registra o dispositivo com o Azure AD novamente. 

      For down-level Windows OS versions that are hybrid Azure AD joined, take the following steps:

      1. Abra o prompt de comando como administrador.
      1. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      For Azure AD joined devices Windows 10 devices, take the following steps:

      1. Abra o prompt de comando como administrador
      1. Enter `dsregcmd /forcerecovery` (Note: You need to be an administrator to perform this action).
      1. Click "Sign in" in the dialog that opens up and continue with the sign in process.
      1. Sign out and sign in back to the device to complete the recovery.

      For Azure AD registered Windows 10 devices, take the following steps:

      1. Go to **Settings** > **Accounts** > **Access Work or School**. 
      1. Select the account and select **Disconnect**.
      1. Click on "+ Connect" and register the device again by going through the sign in process.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q: Why do I see duplicate device entries in the Azure portal?

**R:**

- Para o Windows 10 e o Windows Server 2016, tentativas repetidas de desunir e reingressar no mesmo dispositivo podem resultar em entradas duplicadas. 
- Cada usuário do Windows que usar **Adicionar Conta Corporativa ou de Estudante** cria um novo registro do dispositivo com o mesmo nome do dispositivo.
- Para versões do sistema operacional do Windows de nível inferior que são ingressadas no domínio do Azure Directory local, o registro automático cria um novo registro de dispositivo com o mesmo nome do dispositivo para cada usuário de domínio que entra no dispositivo. 
- Uma máquina conectada do Azure AD que foi limpa, reinstalada e reunida com o mesmo nome é exibida como outro registro com o mesmo nome de dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q: Does Windows 10 device registration in Azure AD support TPMs in FIPS mode?

**A:** Windows 10 device registration only supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Azure AD join or Hybrid Azure AD join. Note that Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. 

---

**Q: Why can a user still access resources from a device I disabled in the Azure portal?**

**A:** It takes up to an hour for a revoke to be applied from the time the Azure AD device is marked as disabled.

>[!NOTE] 
>Para dispositivos registrados, é recomendável apagar o dispositivo para garantir que os usuários não possam acessar os recursos. Para obter mais informações, confira [O que é o registro de dispositivo?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Q: Why are there devices marked as "Pending" under the REGISTERED column in the Azure portal?

**A**:  Pending indicates that the device is not registered. This state indicates that a device has been synchronized using Azure AD connect from an on-premises AD and is ready for device registration. These devices have the JOIN TYPE set to "Hybrid Azure AD joined". Learn more on [how to plan your hybrid Azure Active Directory join implementation](hybrid-azuread-join-plan.md).

>[!NOTE]
>A device can also change from having a registered state to "Pending"
>* If a device is deleted from Azure AD first and re-synchronized from an on-premises AD.
>* If a device is removed from a sync scope on Azure AD Connect and added back.
>
>In both cases, you must re-register the device manually on each of these devices. To review whether the device was previously registered, you can [troubleshoot devices using the dsregcmd command](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin an Azure AD joined device locally on the device?

**A:** For pure Azure AD joined devices, make sure you have an offline local administrator account or create one. Você não pode entrar com as credenciais de usuário do Azure AD. Em seguida, acesse **Configurações** > **Contas** > **Acessar corporativo ou de estudante**. Selecione sua conta e, em seguida, **Desconectar**. Siga os prompts e forneça as credenciais de administrador local, quando solicitado. Reinicie o dispositivo para concluir o processo de cancelar a associação.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q: Can my users' sign in to Azure AD joined devices that are deleted or disabled in Azure AD?

**R:** Sim. O Windows tem uma funcionalidade de armazenamento em cache de nome de usuário e senha que permite aos usuários que se conectaram anteriormente acessarem a área de trabalho rapidamente, mesmo sem conectividade de rede. 

Quando um dispositivo é excluído ou desabilitado no Azure AD, ele não é reconhecido pelo dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente continuam a acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. But as the device is deleted or disabled, users can't access any resources protected by device-based Conditional Access. 

Os usuários que não se conectaram anteriormente não podem acessar o dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q: Can a disabled or deleted user sign in to an Azure AD joined devices

**A:** Yes, but only for a limited time. Quando um usuário é excluído ou desabilitado no Azure AD, ele não é imediatamente conhecido para o dispositivo do Windows. Portanto, os usuários que se conectaram anteriormente podem acessar a área de trabalho com o nome de usuário e a senha armazenados em cache. 

Normalmente, o dispositivo está ciente do estado do usuário em menos de quatro horas. Em seguida, o Windows bloqueia o acesso desses usuários à área de trabalho. Como o usuário é excluído ou desabilitado no Azure AD, todos os seus tokens são revogados. Portanto, eles não podem acessar nenhum recurso. 

Os usuários excluídos ou desabilitados que não se conectaram anteriormente não podem acessar um dispositivo. Não há nenhum nome de usuário e senha armazenados em cache habilitados para eles. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Azure AD joined devices after changing their UPN?

**A:** Currently, UPN changes are not fully supported on Azure AD joined devices. Portanto, a autenticação com o Azure AD falha após o UPN mudar. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. Neste momento, os usuários precisam entrar no Windows por meio do bloco "Outro usuário" usando o novo UPN para resolver esse problema. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q: My users can't search printers from Azure AD joined devices. How can I enable printing from those devices?

**A:** To deploy printers for Azure AD joined devices, see [Deploy Windows Server Hybrid Cloud Print with Pre-Authentication](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Você precisa de um Windows Server local para implantar a impressão de nuvem híbrida. Atualmente, o serviço de impressão baseado em nuvem não está disponível. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q: How do I connect to a remote Azure AD joined device?

**A:** See [Connect to remote Azure Active Directory-joined PC](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q: Why do my users see *You can’t get there from here*?

**A:** Did you configure certain Conditional Access rules to require a specific device state? Se o dispositivo não atender aos critérios, os usuários são bloqueados e veem essa mensagem. Evaluate the Conditional Access policy rules. Verifique se o dispositivo atende aos critérios para evitar a mensagem.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q: Why don't some of my users get Azure Multi-Factor Authentication prompts on Azure AD joined devices?

**A:** A user might join or register a device with Azure AD by using Multi-Factor Authentication. Em seguida, o próprio dispositivo torna-se um segundo fator confiável para esse usuário. Sempre que o mesmo usuário faz logon no dispositivo e acessa um aplicativo, o Azure AD considera o dispositivo como um segundo fator. Ele permite que o usuário acesse diretamente os aplicativos sem prompts de autenticação multifator adicionais. 

Esse comportamento:

- É aplicável aos dispositivos associados do Azure AD e aos dispositivos registrados do Azure AD, mas não aos dispositivos associados híbridos do Azure AD.
- Não é aplicável a qualquer outro usuário que se conecte a esse dispositivo. Portanto, todos os outros usuários que acessam esse dispositivo serão apresentados a um desafio de autenticação multifator. Só então eles poderão acessar os aplicativos que exigem a autenticação multifator.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q: Why do I get a *username or password is incorrect* message for a device I just joined to Azure AD?

**A:** Common reasons for this scenario are as follows:

- Suas credenciais de usuário não são mais válidas.
- O computador não consegue se comunicar com o Azure Active Directory. Verifique se há problemas de conectividade de rede.
- Credenciais federadas requerem que o servidor de federação dê suporte a pontos de extremidade do WS-Trust que estejam habilitados e acessíveis. 
- Você habilitou a autenticação de passagem. Portanto, sua senha temporária precisa ser alterada quando você entrar.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q: Why do I see the *Oops… an error occurred!* dialog when I try to Azure AD join my PC?

**A:** This error happens when you set up Azure Active Directory enrollment with Intune. Certifique-se de que o usuário que tenta fazer o ingresso do Azure AD tenha a licença correta do Intune atribuída. Para obter mais informações, confira [Configurar registro para dispositivos Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q: Why did my attempt to Azure AD join a PC fail, although I didn't get any error information?

**A:** A likely cause is that you signed in to the device by using the local built-in administrator account. Crie uma conta local diferente antes de usar o ingresso do Azure Active Directory para concluir a configuração. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What are the MS-Organization-P2P-Access certificates present on our Windows 10 devices?

**A:** The MS-Organization-P2P-Access certificates are issued by Azure AD to both, Azure AD joined and hybrid Azure AD joined devices. Esses certificados são usados para habilitar a confiança entre os dispositivos no mesmo locatário para cenários de área de trabalho remotos. Um certificado é emitido para o dispositivo e o outro é emitido para o usuário. O certificado do dispositivo está presente no `Local Computer\Personal\Certificates` e é válido por um dia. Esse certificado é renovado (emitindo um novo certificado) se o dispositivo ainda estiver ativo no Azure AD. O certificado de usuário está presente no `Current User\Personal\Certificates` e esse certificado também é válido por um dia, mas é emitido sob demanda quando um usuário tenta uma sessão de área de trabalho remota para outro dispositivo ingressado do Azure AD. Ele não é renovado após a expiração. Esses dois certificados são emitidos usando o certificado MS-Organization-P2P-Access presente no `Local Computer\AAD Token Issuer\Certificates`. Esse certificado é emitido pelo Azure AD durante o registro do dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why do I see multiple expired certificates issued by MS-Organization-P2P-Access on our Windows 10 devices? How can I delete them?

**A:** There was an issue identified on Windows 10 version 1709 and lower where expired MS-Organization-P2P-Access certificates continued to exist on the computer store because of cryptographic issues. Your users could face issues with network connectivity, if you are using any VPN clients (for example, Cisco AnyConnect) that cannot handle the large number of expired certificates. Esse problema foi corrigido no Windows 10 versão 1803 para excluir automaticamente esses certificados MS-Organization-P2P-Access expirados. Você pode resolver esse problema atualizando seus dispositivos para o Windows 10 versão 1803. Se você não conseguir atualizar, você pode excluir esses certificados sem que seja causado nenhum impacto negativo.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Perguntas frequentes sobre ingresso no Azure AD Híbrido

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin a Hybrid Azure AD joined device locally on the device?

**A:** For hybrid Azure AD joined devices, make sure to turn off automatic registration. A tarefa agendada não registrará o dispositivo novamente. Agora, abra um prompt de comando como administrador e digite `dsregcmd.exe /debug /leave`. Ou execute este comando como um script em vários dispositivos para fazer um cancelamento de associação em massa.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q: Where can I find troubleshooting information to diagnose hybrid Azure AD join failures?

**A:** For troubleshooting information, see these articles:

- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q: Why do I see a duplicate Azure AD registered record for my Windows 10 hybrid Azure AD joined device in the Azure AD devices list?

**A:** When your users add their accounts to apps on a domain-joined device, they might be prompted with **Add account to Windows?** Se o usuário escolher **Sim** no prompt, o dispositivo registra com o Azure AD. O tipo de relação de confiança é marcado como registrado no Azure AD. Depois de habilitar o ingresso do Azure AD híbrido na organização, o dispositivo também será incluído no Azure AD híbrido. Em seguida, dois estados de dispositivo aparecem para o mesmo dispositivo. 

O ingresso do Azure AD híbrido tem precedência sobre o estado de registrado pelo Azure AD. So your device is considered hybrid Azure AD joined for any authentication and Conditional Access evaluation. Você pode excluir com segurança o registro do dispositivo registrado pelo Azure AD no portal do Azure AD. Saiba como [evitar ou limpar esse estado duplo no computador com Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Windows 10 hybrid Azure AD joined devices after changing their UPN?

**A:** Currently UPN changes are not fully supported with hybrid Azure AD joined devices. Embora os usuários possam entrar dispositivo e acessar seus aplicativos locais, a autenticação com o Azure AD falhará após a alteração de um UPN. Como resultado, os usuários têm problemas de SSO e Acesso Condicional em seus dispositivos. At this time, you need to unjoin the device from Azure AD (run "dsregcmd /leave" with elevated privileges) and rejoin (happens automatically) to resolve the issue. Estamos trabalhando para solucionar esse problema. No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q: Do Windows 10 hybrid Azure AD joined devices require line of sight to the domain controller to get access to cloud resources?

**A:** No, except when the user's password is changed. After Windows 10 hybrid Azure AD join is complete, and the user has signed in at least once, the device doesn't require line of sight to the domain controller to access cloud resources. Windows 10 can get single sign-on to Azure AD applications from anywhere with an internet connection, except when a password is changed. Users who sign in with Windows Hello for Business continue to get single sign-on to Azure AD applications even after a password change, even if they don't have line of sight to their domain controller. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q: What happens if a user changes their password and tries to login to their Windows 10 hybrid Azure AD joined device outside the corporate network?

**A:** If a password is changed outside the corporate network (for example, by using Azure AD SSPR), then the user sign in with the new password will fail. For hybrid Azure AD joined devices, on-premises Active Directory is the primary authority. When a device does not have line of sight to the domain controller, it is unable to validate the new password. So, user needs to establish connection with the domain controller (either via VPN or being in the corporate network) before they're able to sign in to the device with their new password. Otherwise, they can only sign in with their old password because of cached sign in capability in Windows. However, the old password is invalidated by Azure AD during token requests and hence, prevents single sign-on and fails any device-based Conditional Access policies. This issue doesn't occur if you use Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Perguntas frequentes sobre o registro do Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Q: How do I remove an Azure AD registered state for a device locally?

**R:** 
- For Windows 10 Azure AD registered devices, Go to **Settings** > **Accounts** > **Access Work or School**. Selecione sua conta e, em seguida, **Desconectar**. Device registration is per user profile on Windows 10.
- For iOS and Android, you can use the Microsoft Authenticator application **Settings** > **Device Registration** and select **Unregister device**.
- For macOS, you can use the Microsoft Intune Company Portal application to un-enroll the device from management and remove any registration. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Q: How can I block users from adding additional work accounts (Azure AD registered) on my corporate Windows 10 devices?

**A:** Enable the following registry to block your users from adding additional work accounts to your corporate domain joined, Azure AD joined or hybrid Azure AD joined Windows 10 devices. This policy can also be used to block domain joined machines from inadvertently getting Azure AD registered with the same user account. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q: Can I register Android or iOS BYOD devices?

**A:** Yes, but only with the Azure device registration service and for hybrid customers. Ele não é compatível com o serviço de registro de dispositivo local nos Serviços de Federação do Active Directory (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>Q: How can I register a macOS device?

**A:** Take the following steps:

1.  [Criar uma política de conformidade](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Define a Conditional Access policy for macOS devices](../active-directory-conditional-access-azure-portal.md) 

**Comentários:**

- The users included in your Conditional Access policy need a [supported version of Office for macOS](../conditional-access/technical-reference.md#client-apps-condition) to access resources. 
- Durante a primeira tentativa de acesso, os usuários são solicitados a registrar o dispositivo usando o portal da empresa.

---
## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [dispositivos registrados no Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos ingressados no Azure AD híbrido](concept-azure-ad-join-hybrid.md)
