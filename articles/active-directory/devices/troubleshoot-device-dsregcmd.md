---
title: Solução de problemas usando o comando dsregcmd - Azure Active Directory
description: Usando a saída de dsregcmd para entender o estado dos dispositivos no Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128755"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Dispositivos de solução de problemas usando o comando dsregcmd

O utilitário dsregcmd /status deve ser executado como uma conta de usuário de domínio.

## <a name="device-state"></a>Estado do dispositivo

Esta seção lista os parâmetros de estado de adesão do dispositivo. A tabela abaixo lista os critérios para que o dispositivo esteja em vários estados de adesão.

| AzureAdJoin | EnterpriseJoined | DomínioUnidos | Estado do dispositivo |
| ---   | ---   | ---   | ---   |
| YES | Não | Não | Azure AD Juntou-se |
| Não | Não | YES | Domínio Unido |
| YES | Não | YES | Anúncio híbrido juntado |
| Não | YES | YES | DRS no local |

> [!NOTE]
> O estado de Workplace Join (registrado no Azure AD) é exibido na seção "Estado do Usuário"

- **AzureAdJoined:** - Defina para "SIM" se o dispositivo estiver unido ao Azure AD. "Não" de outra forma.
- **EnterpriseJoined:** - Defina para "SIM" se o dispositivo estiver unido a um DRS no local. Um dispositivo não pode ser tanto EnterpriseJoined quanto AzureAdJoin.
- **DomainJoined:** - Defina para "SIM" se o dispositivo estiver unido a um domínio (AD).
- **Nome do domínio:** - Defina o nome do domínio se o dispositivo estiver unido a um domínio.

### <a name="sample-device-state-output"></a>Saída do estado do dispositivo de amostra

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Detalhes do Dispositivo

Exibido somente quando o dispositivo é Azure AD aderido ou adesão ao Azure AD híbrido (não registrado no Azure AD). Esta seção lista os detalhes de identificação do dispositivo armazenados na nuvem.

- **DeviceId:** - ID exclusivo do dispositivo no inquilino Azure AD
- **Impressão digital:** - Impressão digital do certificado do dispositivo 
- **DeviceCertificateValidity:** - Validade do certificado do dispositivo
- **KeyContainerId:** - ContainerId da chave privada do dispositivo associada ao certificado do dispositivo
- **KeyProvider:** - KeyProvider (Hardware/Software) usado para armazenar a chave privada do dispositivo.
- **TpmProtected:** - "SIM" se a chave privada do dispositivo for armazenada em um TPM de hardware.

### <a name="sample-device-details-output"></a>Saída de detalhes do dispositivo de amostra

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Detalhes do locatário

Exibido somente quando o dispositivo é Azure AD aderido ou adesão ao Azure AD híbrido (não registrado no Azure AD). Esta seção lista os detalhes comuns do inquilino quando um dispositivo é juntado ao Azure AD.

> [!NOTE]
> Se os URLs MDM nesta seção estiverem vazios, ele indicará que o MDM não estava configurado ou o usuário atual não está no escopo da inscrição do MDM. Verifique as configurações de mobilidade no Azure AD para revisar sua configuração de MDM.

> [!NOTE]
> Mesmo se você vir URLs MDM isso não significa que o dispositivo seja gerenciado por um MDM. As informações são exibidas se o inquilino tiver configuração de MDM para registro automático, mesmo que o dispositivo em si não seja gerenciado. 

### <a name="sample-tenant-details-output"></a>Produção de detalhes do inquilino da amostra

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Estado do usuário

Esta seção lista o status de vários atributos para o usuário atualmente conectado ao dispositivo.

> [!NOTE]
> O comando deve ser executado em um contexto de usuário para recuperar o status válido.

- **NgcSet:** - Defina para "SIM" se uma tecla Dolá do Windows estiver definida para a atual logada no usuário.
- **NgcKeyId:** - ID da tecla Windows Hello se estiver configurado para o usuário conectado atual.
- **CanReset:** - Denota se a tecla Windows Hello pode ser redefinida pelo usuário. 
- **Valores possíveis:** - DestructiveOnly, NonDestructiveOnly, DestructiveandNonDestructive ou Unknown se errar. 
- **WorkplaceJoined:** - Definido como "SIM" se as contas registradas do Azure AD tiverem sido adicionadas ao dispositivo no contexto NTUSER atual.
- **WamDefaultSet:** - Defina para "SIM" se uma WebAccount padrão WAM for criada para o usuário logado. Este campo pode exibir um erro se dsreg /status for executado a partir de um prompt de comando elevado. 
- **WamDefaultAuthority:** - Definido como "organizações" para a Azure AD.
- **WamDefaultId:** -https://login.microsoft.comAlways " para Azure AD.
- **WamDefaultGUID:** - Guid do provedor WAM (conta Azure AD/Microsoft) para a WebAccount WAM padrão. 

### <a name="sample-user-state-output"></a>Produção do estado do usuário da amostra

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Estado SSO

Esta seção pode ser ignorada para dispositivos registrados no Azure AD.

> [!NOTE]
> O comando deve ser executado em um contexto de usuário para recuperar o status válido para esse usuário.

- **AzureAdPrt:** - Defina para "SIM" se um PRT estiver presente no dispositivo para o usuário conectado.
- **AzureAdPrtUpdateTime:** - Definir o tempo em UTC quando o PRT foi atualizado pela última vez.
- **AzureAdPrtExpiryTime:** - Defina o tempo em UTC quando o PRT vai expirar se não for renovado.
- **AzureAdPrtAuthority:** - URL de autoridade azure AD
- **EnterprisePrt:** - Defina para "SIM" se o dispositivo tiver PRT a partir de ADFS no local. Para dispositivos híbridos azure AD, o dispositivo pode ter PRT tanto do Azure AD quanto do AD no local simultaneamente. Os dispositivos aderidos no local terão apenas um Enterprise PRT.
- **EnterprisePrtUpdateTime:** - Defina o tempo na UTC quando o Enterprise PRT foi atualizado pela última vez.
- **EnterprisePrtExpiryTime:** - Defina o tempo na UTC quando a PRT vai expirar se não for renovada.
- **EnterprisePrtAuthority:** - URL de autoridade DaDFS

### <a name="sample-sso-state-output"></a>Amostra de produção do estado sso

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Dados de diagnóstico

### <a name="pre-join-diagnostics"></a>Pré-adesão aos diagnósticos

Esta seção é exibida somente se o dispositivo estiver unido ao domínio e não puder participar do Azure AD híbrido.

Esta seção realiza vários testes para ajudar a diagnosticar falhas de adesão. Esta seção também inclui os detalhes do anterior (?). Essas informações incluem a fase de erro, o código de erro, o ID de solicitação do servidor, o status http de resposta do servidor, a mensagem de erro de resposta do servidor.

- **Contexto do Usuário:** - O contexto em que os diagnósticos são executados. Valores possíveis: SISTEMA, Usuário NÃO ELEVADO, Usuário ELEVADO. 

   > [!NOTE]
   > Uma vez que a adesão real é realizada no contexto do SISTEMA, a execução dos diagnósticos no contexto do SISTEMA é mais próxima do cenário real de adesão. Para executar diagnósticos no contexto do SISTEMA, o comando dsregcmd /status deve ser executado a partir de um prompt de comando elevado.

- **Tempo do cliente:** - O tempo do sistema na UTC.
- **Teste de conectividade AD:** - O teste realiza um teste de conectividade ao controlador de domínio. O erro neste teste provavelmente resultará em erros de Participar na fase de pré-verificação.
- **Teste de configuração AD:** - O teste lê e verifica se o objeto SCP está configurado corretamente na floresta AD no local. Erros neste teste provavelmente resultariam em erros de Join na fase de descoberta com o código de erro 0x801c001d.
- **Teste de descoberta do DRS:** - O teste obtém os pontos finais do DRS a partir do ponto final de metadados de detecção e executa uma solicitação de reino do usuário. Erros neste teste provavelmente resultariam em erros de Participar na fase de descoberta.
- **Teste de conectividade DRS:** - O teste realiza o teste básico de conectividade ao ponto final do DRS.
- **Teste de aquisição de token:** - O teste tenta obter um token de autenticação Azure AD se o inquilino do usuário estiver federado. Erros neste teste provavelmente resultariam em erros de Join na fase auth. Se auth falhar, a sync join será tentada como recuo, a menos que o recuo seja explicitamente desativado com as configurações de chave de registro abaixo.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Recuo para Sync-Join:** - Definido como "Ativado" se a tecla de registro acima, para evitar que o recuo sincronize a juntação com falhas de auth, NÃO esteja presente. Esta opção está disponível a partir do Windows 10 1803 e posterior.
- **Registro Prévio:** - Tempo de tentativa de Adesão anterior ocorreu. Apenas as tentativas de Adesão falhadas são registradas.
- **Fase de Erro:** - A fase da junta em que foi abortada. Os valores possíveis são pré-check, discover, auth, join.
- **Código de erro do cliente:** - Código de erro do cliente retornado (HRESULT).
- **Código de erro do servidor:** - Código de erro do servidor se uma solicitação foi enviada para o servidor e o servidor respondeu com um código de erro. 
- **Mensagem do servidor:** - Mensagem do servidor retornada junto com o código de erro.
- **Https Status:** - Http status retornado pelo servidor.
- **Solicitação de ID:** - O cliente solicitadoId enviado para o servidor. Útil para se correlacionar com logs do lado do servidor.

### <a name="sample-pre-join-diagnostics-output"></a>Produção de diagnósticos pré-aderido à amostra

O exemplo a seguir mostra o teste de diagnóstico falhando com um erro de descoberta.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

O exemplo a seguir mostra que os testes de diagnóstico estão passando, mas a tentativa de registro falhou com um erro de diretório, que é esperado para sincronização. Uma vez que o trabalho de sincronização do Azure AD Connect seja concluído, o dispositivo poderá participar.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnóstico pós-adesão

Esta seção exibe a saída de verificações de sanidade realizadas em um dispositivo unido à nuvem.

- **AadRecoveryAtivado:** - Se "SIM", as chaves armazenadas no dispositivo não são utilizáveis e o dispositivo está marcado para recuperação. O próximo sinal irá acionar o fluxo de recuperação e recadastrar o dispositivo.
- **KeySignTest:** - Se "PASSED" as teclas do dispositivo estiverem em boa saúde. Se o KeySignTest falhar, o dispositivo geralmente será marcado para recuperação. O próximo sinal irá acionar o fluxo de recuperação e recadastrar o dispositivo. Para dispositivos híbridos Azure AD unidos, a recuperação é silenciosa. Enquanto o Azure AD se juntou ou o Azure AD foi registrado, os dispositivos solicitarão que a autenticação do usuário recupere e registre novamente o dispositivo, se necessário. **O KeySignTest requer privilégios elevados.**

#### <a name="sample-post-join-diagnostics-output"></a>Amostra de saída de diagnóstico pós-adesão

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Verificação do pré-requisito ngc

Esta seção realiza as verificações perquisite para o provisionamento do Windows Hello for Business (WHFB). 

> [!NOTE]
> Você pode não ver os detalhes de verificação pré-requisito do NGC em dsregcmd /status se o usuário já estiver configurado com sucesso whfb.

- **IsDeviceJoined:** - Defina para "SIM" se o dispositivo estiver unido ao Azure AD.
- **IsUserAzureAD:** - Defina para "SIM" se o usuário conectado estiver presente no Azure AD .
- **PolíticaHabilitada:** - Defina para "SIM" se a diretiva WHFB estiver habilitada no dispositivo.
- **PostLogonEnabled:** - Defina para "SIM" se a inscrição do WHFB for acionada nativamente pela plataforma. Se estiver definido como "NÃO", indica que a inscrição do Windows Hello for Business é acionada por um mecanismo personalizado
- **DispositivoElegível:** - Definido como "SIM" se o dispositivo atender ao requisito de hardware para se inscrever no WHFB.
- **SessionIsNotRemote:** - Defina para "SIM" se o usuário atual estiver conectado diretamente ao dispositivo e não remotamente.
- **CertMatrícula:** - Específico para implantação do WHFB Certificate Trust, indicando a autoridade de inscrição de certificados para WHFB. Defina como "autoridade de inscrição" se a fonte da política WHFB for Política de Grupo, "gerenciamento de dispositivos móveis" se a fonte for MDM. "nenhum" de outra forma
- **AdfsRefreshToken:** - Específico para implantação do WHFB Certificate Trust. Só está presente se o CertRegistration for "autoridade de matrícula". Indica se o dispositivo possui um PRT corporativo para o usuário.
- **AdfsRaIsReady:** - Específico para implantação do WHFB Certificate Trust.  Só está presente se o CertRegistration for "autoridade de matrícula". Defina como "SIM" se o ADFS indicar nos metadados de detecção que ele suporta WHFB *e* se o modelo de certificado de logon estiver disponível.
- **LogonCertTemplateReady:** - Específico para implantação do WHFB Certificate Trust. Só está presente se o CertRegistration for "autoridade de matrícula". Defina como "SIM" se o modelo de certificado de logon for válido e ajudar a solucionar problemas do ADFS RA.
- **PreReqResultado:** - Fornece resultado de toda avaliação pré-requisito whfb. Definido como "Provision will" se a inscrição do WHFB for lançada como uma tarefa pós-logon quando o usuário fizer login na próxima vez.

### <a name="sample-ngc-prerequisite-check-output"></a>Amostra de saída de pré-requisito NGC

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Próximas etapas

Para perguntas, consulte as [Perguntas frequentes sobre o gerenciamento de dispositivos](faq.md)
