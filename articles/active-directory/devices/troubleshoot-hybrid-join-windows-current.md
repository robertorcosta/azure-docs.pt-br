---
title: Solução de problemas híbrido sustação do Azure Active Directory juntou-se aos dispositivos
description: Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos do Windows 10 e do Windows Server 2016.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331783"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Solução de problemas híbrido sustação do Azure Active Directory juntou-se aos dispositivos 

O conteúdo deste artigo é aplicável aos dispositivos que executam o Windows 10 ou o Windows Server 2016.

Para outros clientes do Windows, consulte o artigo [Solução de problemas o Azure Active Directory híbrido juntou-se a dispositivos de nível baixo](troubleshoot-hybrid-join-windows-legacy.md).

Este artigo pressupõe que você tenha [dispositivos configurados e ingressados no Azure Active Directory híbrido](hybrid-azuread-join-plan.md) para dar suporte aos seguintes cenários:

- Acesso Condicional baseado no dispositivo
- [Roaming corporativo de configurações](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Configurar o Hello for Business](../active-directory-azureadjoin-passport-deployment.md)

Este documento fornece orientação de solução de problemas para resolver possíveis problemas. 

Para Windows 10 e Windows Server 2016, o ingresso do Azure Active Directory híbrido oferece suporte à atualização de 10 de novembro de 2015 e superior do Windows 10.

## <a name="troubleshoot-join-failures"></a>Solução de problemas une falhas

### <a name="step-1-retrieve-the-join-status"></a>Etapa 1: Recuperar o status do ingresso 

**Para recuperar o status do ingresso:**

1. Abra um prompt de comando como administrador
2. Digite `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Etapa 2: Avaliar o status do ingresso 

Examine os seguintes campos e garanta que eles tenham os valores esperados:

#### <a name="domainjoined--yes"></a>DomainJoined : YES  

Esse campo indica se o dispositivo ingressou em um Active Directory local ou não. Se o valor for **NO**, o dispositivo não poderá executar um ingresso do Azure AD híbrido.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Esse campo indica se o dispositivo está registrado no Azure AD como um dispositivo pessoal (marcado como *Ingressado no Espaço de Trabalho*). Esse valor deve ser **NO** para um computador ingressado no domínio, que também é ingressado no Azure AD híbrido. Se o valor for **YES**, uma conta corporativa ou de estudante terá sido adicionada antes da conclusão do ingresso do Azure AD híbrido. Nesse caso, a conta é ignorada ao usar a versão de Atualização de Aniversário do Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

Este campo indica se o dispositivo está unido. O valor será **SIM** se o dispositivo for um dispositivo aderido ao Azure AD ou a um dispositivo adepto a Azure AD híbrido.
Se o valor for **NO**, a associação ao Azure AD ainda não terá sido concluída. 

Prossiga para os próximos passos para mais solução de problemas.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Passo 3: Encontre a fase em que a adesão falhou e o código de erro

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e acima

Procure a subseção 'Registro prévio' na seção 'Dados de diagnóstico' da saída de status de adesão. Esta seção é exibida somente se o dispositivo estiver unido ao domínio e não puder participar do Azure AD híbrido.
O campo 'Fase de erro' denota a fase da falha de juntação enquanto 'Client ErrorCode' denota o código de erro da operação Participar.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Versões mais antigas do Windows 10

Use registros do Visualizador de Eventos para localizar a fase e o código de erro das falhas de adesão.

1. Abra os logs de **registro do dispositivo** do usuário no visualizador do evento. Localizado em **aplicativos e serviços Log** > **Microsoft** > **Windows** > User Device**Registration**
2. Procure eventos com os seguintes eventosIDs 304, 305, 307.

![Evento de registro de falhas](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Evento de registro de falhas](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Passo 4: Verifique possíveis causas e resoluções das listas abaixo

#### <a name="pre-check-phase"></a>Fase de pré-verificação

Possíveis razões para o fracasso:

- O dispositivo não tem linha de visão para o controlador de domínio.
   - O dispositivo deve estar na rede interna da organização ou em VPN com linha de visão de rede para um controlador de domínio ad (Active Directory) local.

#### <a name="discover-phase"></a>Descobrir fase

Possíveis razões para o fracasso:

- Objeto SCP (Service Connection Point, ponto de conexão de serviço) mal configurado/incapaz de ler objeto SCP da DC.
   - Um objeto SCP válido é necessário na floresta AD, à qual o dispositivo pertence, que aponta para um nome de domínio verificado no Azure AD.
   - Os detalhes podem ser encontrados na seção [Configurar um ponto de conexão de serviço](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Falha em conectar e buscar os metadados de descoberta do ponto final da descoberta.
   - O dispositivo deve ser `https://enterpriseregistration.windows.net`capaz de acessar, no contexto do SISTEMA, para descobrir os pontos finais de registro e autorização. 
   - Se o ambiente local exigir um proxy de saída, o administrador de TI deve garantir que a conta do computador do dispositivo seja capaz de descobrir e autenticar silenciosamente o proxy de saída.
- Falha em se conectar ao ponto final do reino do usuário e realizar a descoberta do reino. (Versão 1809 do Windows 10 e somente posteriores)
   - O dispositivo deve ser `https://login.microsoftonline.com`capaz de acessar, no contexto do SISTEMA, realizar a descoberta de reinos para o domínio verificado e determinar o tipo de domínio (gerenciado/federado).
   - Se o ambiente local exigir um proxy de saída, o administrador de TI deve garantir que o contexto do SISTEMA no dispositivo seja capaz de descobrir e autenticar silenciosamente o proxy de saída.

**Códigos de erro comuns:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Motivo: Não é possível ler o objeto SCP e obter as informações do inquilino do Azure AD.
   - Resolução: Consulte a seção [Configure um ponto de conexão de serviço](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Motivo: Falha na descoberta genérica. Falha ao obter os metadados de descoberta do DRS.
   - Resolução: Encontre o suberro abaixo para investigar mais.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Motivo: Operação cronometrada durante a execução do Discovery.
   - Resolução: Certifique-se de que `https://enterpriseregistration.windows.net` está acessível no contexto do SISTEMA. Para obter mais informações, consulte os [requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites)da seção .
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Motivo: Falha genérica da descoberta do reino. Falha ao determinar o tipo de domínio (gerenciado/federado) do STS. 
   - Resolução: Encontre o suberro abaixo para investigar mais.

**Códigos comuns de suberro:**

Para encontrar o código de erro de suberro para o código de erro de detecção, use um dos seguintes métodos.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e acima

Procure 'DRS Discovery Test' na seção 'Dados de diagnóstico' da saída de status de adesão. Esta seção é exibida somente se o dispositivo estiver unido ao domínio e não puder participar do Azure AD híbrido.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Versões mais antigas do Windows 10

Use registros do Visualizador de Eventos para localizar a fase e o código de erro das falhas de adesão.

1. Abra os logs de **registro do dispositivo** do usuário no visualizador do evento. Localizado em **aplicativos e serviços Log** > **Microsoft** > **Windows** > User Device**Registration**
2. Procure eventos com os seguintes eventosIDs 201

![Evento de registro de falhas](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Erros de rede

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Motivo: A conexão com o servidor não pôde ser estabelecida
   - Resolução: Garantir a conectividade de rede aos recursos necessários da Microsoft. Para obter mais informações, consulte [os requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Motivo: tempo de rede geral.
   - Resolução: Garantir a conectividade de rede aos recursos necessários da Microsoft. Para obter mais informações, consulte [os requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Motivo: A pilha de rede não foi capaz de decodificar a resposta do servidor.
   - Resolução: Certifique-se de que o proxy de rede não está interferindo e modificando a resposta do servidor.

###### <a name="http-errors"></a>Erros de HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Motivo: Objeto SCP configurado com ID de inquilino errado. Ou nenhuma assinatura ativa foi encontrada no inquilino.
   - Resolução: Certifique-se de que o objeto SCP esteja configurado com o ID de inquilino Azure AD correto e assinaturas ativas ou presente no inquilino.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Motivo: HTTP 503 do servidor DRS.
   - Resolução: O servidor está indisponível no momento. futuras tentativas de adesão provavelmente terão sucesso quando o servidor estiver novamente on-line.

###### <a name="other-errors"></a>Outros erros

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Motivo: Resposta do servidor JSON não pôde ser analisado. Provavelmente devido ao retorno do proxy HTTP 200 com uma página de auth HTML.
   - Resolução: Se o ambiente local exigir um proxy de saída, o administrador de TI deve garantir que o contexto do SISTEMA no dispositivo seja capaz de descobrir e autenticar silenciosamente o proxy de saída.

#### <a name="authentication-phase"></a>Fase de autenticação

Aplicável apenas para contas de domínio federadas.

Razões para o fracasso:

- Não é possível obter um token de acesso silenciosamente para recurso DRS.
   - Os dispositivos Windows 10 adquirem o auth token do serviço da federação usando a Autenticação Integrada do Windows para um ponto final ativo do WS-Trust. Detalhes: [Configuração do serviço da Federação](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Códigos de erro comuns:**

Use os logs do Visualizador de Eventos para localizar o código de erro, o código de erro, o código de erro do servidor e a mensagem de erro do servidor.

1. Abra os logs de **registro do dispositivo** do usuário no visualizador do evento. Localizado em **aplicativos e serviços Log** > **Microsoft** > **Windows** > User Device**Registration**
2. Procure eventos com o seguinte eventoID 305

![Evento de registro de falhas](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Erros de configuração

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Motivo: O protocolo de autenticação não é o WS-Trust.
   - Resolução: O provedor de identidade local deve suportar o WS-Trust 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Motivo: O serviço da federação no local não retornou uma resposta XML.
   - Resolução: Certifique-se de que o ponto final do MEX está retornando um XML válido. Certifique-se de que o proxy não está interferindo e retornando respostas não-xml.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Motivo: Não foi possível descobrir o ponto final para autenticação de nome de usuário/senha.
   - Resolução: Verifique as configurações do provedor de identidade no local. Certifique-se de que os pontos finais do WS-Trust estão ativados e garanta que a resposta MEX contenha esses pontos finais corretos.

##### <a name="network-errors"></a>Erros de rede

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Motivo: tempo de rede geral.
   - Resolução: Certifique-se de que `https://login.microsoftonline.com` está acessível no contexto do SISTEMA. Certifique-se de que o provedor de identidade no local esteja acessível no contexto do SISTEMA. Para obter mais informações, consulte [os requisitos de conectividade de rede](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Motivo: A conexão com o ponto final do auth foi abortada.
   - Resolução: Tente novamente depois de algum tempo ou tente se juntar a partir de um local de rede estável alternativo.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Motivo: O TLS (Transport Layer Security, segurança da camada de transporte), anteriormente conhecido como Secure Sockets Layer (SSL), certificado enviado pelo servidor não pôde ser validado.
   - Resolução: Verifique a distorção de tempo do cliente. Tente novamente depois de algum tempo ou tente se juntar a partir de um local de rede estável alternativo. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Motivo: A tentativa `https://login.microsoftonline.com` de conexão falhou.
   - Resolução: Verifique a `https://login.microsoftonline.com`conexão da rede com .

##### <a name="other-errors"></a>Outros erros

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Motivo: O token SAML do provedor de identidade local não foi aceito pelo Azure AD.
   - Resolução: Verifique as configurações do servidor da federação. Procure o código de erro do servidor nos registros de autenticação.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Motivo: a resposta do Servidor WS-Trust relatou exceção de falha e não conseguiu a afirmação
   - Resolução: Verifique as configurações do servidor da federação. Procure o código de erro do servidor nos registros de autenticação.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Motivo: Recebeu um erro ao tentar obter o token de acesso do ponto final do token.
   - Resolução: Procure o erro subjacente no registro ADAL. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Motivo: Falha geral do ADAL
   - Resolução: Procure o código de erro de suberro ou o código de erro do servidor nos registros de autenticação.
    
#### <a name="join-phase"></a>Adere à Fase

Razões para o fracasso:

Encontre o tipo de registro e procure o código de erro da lista abaixo.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e acima

Procure a subseção 'Registro prévio' na seção 'Dados de diagnóstico' da saída de status de adesão. Esta seção é exibida somente se o dispositivo estiver unido ao domínio e não puder participar do Azure AD híbrido.
O campo 'Tipo de Registro' denota o tipo de adesão realizada.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Versões mais antigas do Windows 10

Use registros do Visualizador de Eventos para localizar a fase e o código de erro das falhas de adesão.

1. Abra os logs de **registro do dispositivo** do usuário no visualizador do evento. Localizado em **aplicativos e serviços Log** > **Microsoft** > **Windows** > User Device**Registration**
2. Procure eventos com os seguintes eventosIDs 204

![Evento de registro de falhas](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>Erros HTTP retornados do servidor DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Motivo: Recebeu uma resposta de erro do DRS com ErrorCode: "DirectoryError"
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Motivo: Recebeu uma resposta de erro do DRS com ErrorCode: "AuthenticationError" e ErrorSubCode NÃO é "DeviceNotFound". 
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Motivo: Recebeu uma resposta de erro do DRS com ErrorCode: "DirectoryError"
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.

##### <a name="tpm-errors"></a>Erros de TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Motivo: a operação TPM falhou ou foi inválida
   - Resolução: Provavelmente devido a uma imagem sysprep ruim. Certifique-se de que a máquina a partir da qual a imagem sysprep foi criada não seja adesão ao Azure AD, o Azure AD híbrido se juntou ou o Azure AD registrado.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Motivo: Erro genérico de TPM. 
   - Resolução: Desabilite o TPM em dispositivos com este erro. A versão 1809 do Windows 10 e superior detecta automaticamente falhas de TPM e completa a adesão híbrida do Azure AD sem usar o TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Motivo: TPM no modo FIPS não suportado no momento.
   - Resolução: Desabilite o TPM em dispositivos com este erro. O Windows 1809 detecta automaticamente falhas de TPM e completa a adesão híbrida do Azure AD sem usar o TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Motivo: TPM bloqueado.
   - Resolução: Erro transitório. Aguarde o período de recarga. Junte-se à tentativa depois de algum tempo deve ter sucesso. Mais informações podem ser [encontradas](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) no artigo Fundamentos TPM

##### <a name="network-errors"></a>Erros de rede

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Motivo: Tempo de rede geral tentando registrar o dispositivo no DRS
   - Resolução: Verifique a `https://enterpriseregistration.windows.net`conectividade da rede para .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Motivo: O nome ou endereço do servidor não pôde ser resolvido.
   - Resolução: Verifique a `https://enterpriseregistration.windows.net`conectividade da rede para . Certifique-se de que a resolução DNS para o nome do host é precisa no n/w e no dispositivo.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Motivo: A conexão com o servidor foi encerrada de forma anormal.
   - Resolução: Tente novamente depois de algum tempo ou tente se juntar a partir de um local de rede estável alternativo.

##### <a name="federated-join-server-errors"></a>Federado adere a erros de servidor

| Código de erro do servidor | Mensagem de erro do servidor | Motivos possíveis | Resolução |
| --- | --- | --- | --- |
| DirectoryError | A solicitação é restringida temporariamente. Por favor, tente depois de 300 segundos. | Erro esperado. Possivelmente devido a fazer vários pedidos de registro em rápida sucessão. | Tente se juntar após o período de recarga |

##### <a name="sync-join-server-errors"></a>Sincronizar erros do servidor de adesão

| Código de erro do servidor | Mensagem de erro do servidor | Motivos possíveis | Resolução |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: <UUID> Inquilino não encontrado. Esse erro pode acontecer se não houver assinaturas ativas para o inquilino. Verifique com o administrador da assinatura. | ID do inquilino no objeto SCP está incorreto | Certifique-se de que o objeto SCP está configurado com o ID de inquilino Azure AD correto e assinaturas ativas e presente no inquilino. |
| DirectoryError | O objeto do dispositivo pelo dado id não é encontrado. | Erro esperado para sincronização. O objeto do dispositivo não foi sincronizado entre a AD e a Azure AD | Aguarde que a sincronização do Azure AD Connect seja concluída e a próxima tentativa de sincronização após a conclusão da sincronização resolverá o problema |
| Erro de autenticação | A verificação do SID do computador alvo | O certificado no dispositivo Azure AD não corresponde ao certificado usado para assinar a bolha durante a sincronização. Este erro normalmente significa que a sincronização ainda não foi concluída. |  Aguarde que a sincronização do Azure AD Connect seja concluída e a próxima tentativa de sincronização após a conclusão da sincronização resolverá o problema |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Passo 5: Colete logs e entre em contato com o suporte da Microsoft

Receba roteiros públicos aqui: [ https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Abra um prompt de comando `start_ngc_tracing_public.cmd`de admin e execute .
2. Realize as etapas para reproduzir o problema.
3. Pare de executar o `stop_ngc_tracing_public.cmd`script de registro executando .
4. Zip e enviar os `%SYSTEMDRIVE%\TraceDJPP\*` registros para análise.

## <a name="troubleshoot-post-join-issues"></a>Solucionar problemas pós-adesão

### <a name="retrieve-the-join-status"></a>Recuperar o status de ingresso 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: SIM e AzureADPrt: SIM
  
Esses campos indicam se o usuário foi autenticado com êxito no Azure AD ao se conectar ao dispositivo. Se os valores forem **NO**, talvez o motivo seja:

- Chave de armazenamento ruim no TPM associado ao dispositivo no momento do registro (verifique o KeySignTest durante a execução elevada).
- ID de logon alternativo
- Proxy HTTP não encontrado

## <a name="known-issues"></a>Problemas conhecidos
- Em Configurações -> Contas -> Access Work ou School, os dispositivos aderidos ao Azure AD híbrido podem mostrar duas contas diferentes, uma para o Azure AD e outra para AD no local, quando conectadaa a hotspots móveis ou redes WiFi externas. Este é apenas um problema de IA e não tem nenhum impacto na funcionalidade. 
 
## <a name="next-steps"></a>Próximas etapas

Continue [a solucionar problemas usando o comando dsregcmd](troubleshoot-device-dsregcmd.md)

Para perguntas, consulte as [Perguntas frequentes sobre o gerenciamento de dispositivos](faq.md)
