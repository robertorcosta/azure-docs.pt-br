---
title: 'Azure AD Connect: conta de serviço do ADSync | Microsoft Docs'
description: Este tópico descreve a conta de serviço do ADSync e fornece as melhores práticas em relação à conta.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478713"
---
# <a name="adsync-service-account"></a>Conta do serviço ADSync
O Azure AD Connect instala um serviço no local que orquestra a sincronização entre o Active Directory e o Azure Active Directory.  O serviço de sincronização Microsoft AD Sync (ADSync) é executado em um servidor no ambiente local.  As credenciais para o serviço são definidas por padrão nas instalações do Expresso, mas podem ser personalizadas para atender aos seus requisitos de segurança organizacional.  Essas credenciais não são usadas para se conectar às florestas locais ou ao Diretório Ativo do Azure.

Escolher a conta de serviço do ADSync é uma decisão importante de planejamento a tomar antes de instalar o Azure AD Connect.  Qualquer tentativa de alterar as credenciais após a instalação resultará na falha do serviço no início, perdendo acesso ao banco de dados de sincronização e deixando de autenticar com seus diretórios conectados (Azure e AD DS).  Nenhuma sincronização ocorrerá até que as credenciais originais sejam restauradas.

## <a name="the-default-adsync-service-account"></a>A conta de serviço Padrão ADSync

Quando executado em um servidor membro, o serviço AdSync é executado no contexto de uma Conta de Serviço Virtual (VSA).  Devido a uma limitação do produto, uma conta de serviço personalizada é criada quando instalada em um controlador de domínio.  Se a conta de serviço de configurações expressa não atender aos requisitos de segurança organizacional, implante o Azure AD Connect escolhendo a opção Personalizar.  Em seguida, escolha a opção de conta de serviço que atende aos requisitos da sua organização.

>[!NOTE]
>A conta de serviço padrão quando instalada em um controlador de domínio é do formulário Domínio\AAD_InstallationIdentifier.  A senha para esta conta é gerada aleatoriamente e apresenta desafios significativos para recuperação e rotação de senhas.  A Microsoft recomenda personalizar a conta de serviço durante a instalação inicial em um controlador de domínio para usar uma conta de serviço gerenciada autônoma ou em grupo (sMSA / gMSA)

|Azure AD Connect localização|Conta de serviço criada|
|-----|-----|
|Servidor membro|NT SERVICE\ADSync|
|Controlador de domínio|Domínio\AAD_74dc30c01e80 (ver nota)|

## <a name="custom-adsync-service-accounts"></a>Contas de serviço personalizadas do ADSync
A Microsoft recomenda a execução do serviço ADSync no contexto de uma Conta de Serviço Virtual ou de uma conta de serviço gerenciada independente ou em grupo.  O administrador de domínio também pode optar por criar uma conta de serviço provisionada para atender aos requisitos específicos de segurança organizacional.   Para personalizar a conta de serviço usada durante a instalação, escolha a opção Personalizar na página Configurações expressas abaixo.   As seguintes opções estão disponíveis:

- conta padrão – O Azure AD Connect irá provisionar a conta de serviço conforme descrito acima
- conta de serviço gerenciado – use um MSA autônomo ou de grupo provisionado pelo administrador
- conta de domínio – use uma conta de serviço de domínio provisionada pelo administrador

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnosticando as alterações da conta de serviço do ADSync
Alterar as credenciais para o serviço ADSync após a instalação resultará na falha do serviço no início, perdendo acesso ao banco de dados de sincronização e não autenticando com seus diretórios conectados (Azure e AD DS).  A concessão de acesso ao banco de dados à nova conta de serviço do ADSync é insuficiente para se recuperar desse problema. Nenhuma sincronização ocorrerá até que as credenciais originais sejam restauradas.

O serviço ADSync emitirá uma mensagem de nível de erro para o registro de eventos quando ele não puder iniciar.  O conteúdo da mensagem variará dependendo se o banco de dados incorporado (localdb) ou o SQL completo estão em uso.  A seguir, exemplos das entradas de registro de eventos que podem estar presentes.

### <a name="example-1"></a>Exemplo 1

As chaves de criptografia de serviço do AdSync não puderam ser encontradas e foram recriadas.  A sincronização não ocorrerá até que este problema seja corrigido.

Solução de problemas este problema As chaves de criptografia Do Microsoft Azure AD Sync ficarão inacessíveis se as credenciais do log on do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, use o aplicativo Serviços para alterar a conta Log On de volta ao seu valor originalmente configurado (ex. NT SERVICE\AdSync) e reiniciar o serviço.  Isso restaurará imediatamente o funcionamento correto do serviço AdSync.

Consulte o [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) a seguir para obter mais informações.

### <a name="example-2"></a>Exemplo 2

O serviço não foi capaz de iniciar porque uma conexão com o banco de dados local (localdb) não pôde ser estabelecida.

Solução de problemas neste problema O serviço Microsoft Azure AD Sync perderá a permissão para acessar o provedor de banco de dados local se as credenciais do log on do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, use o aplicativo Serviços para alterar a conta Log On de volta ao seu valor originalmente configurado (ex. NT SERVICE\AdSync) e reiniciar o serviço.  Isso restaurará imediatamente o funcionamento correto do serviço AdSync.

Consulte o [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) a seguir para obter mais informações.

Detalhes adicionais As seguintes informações de erro foram devolvidas pelo provedor:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [a integração de suas identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).