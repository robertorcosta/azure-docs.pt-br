---
title: Tutorial para configurar o Azure Active Directory B2C com o Jumio
titleSuffix: Azure AD B2C
description: Tutorial para configurar o Azure Active Directory B2C com o Jumio para verificação de ID automatizada, protegendo os dados do cliente
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817293"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Tutorial para configurar o Jumio com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o Azure AD B2C com o [Jumio](https://www.jumio.com/). Jumio é um serviço de verificação de ID, que permite a verificação de ID automatizada em tempo real, protegendo os dados do cliente.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). O locatário está vinculado à sua assinatura do Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração do Jumio inclui os seguintes componentes:

- Azure AD B2C – o servidor de autorização, responsável por verificar as credenciais do usuário, também conhecido como provedor de identidade

- Jumio – o serviço que usa os detalhes de ID fornecidos pelo usuário e o verifica.

- API REST intermediária – essa API implementa a integração entre Azure AD B2C e o serviço Jumio.

- Armazenamento de BLOBs – usado para fornecer arquivos de interface do usuário personalizados para as políticas de Azure AD B2C.

O diagrama de arquitetura a seguir mostra a implementação.

![Captura de tela para jumio-arquitetura-diagrama](./media/partner-jumio/jumio-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. Os usuários selecionam inscrever-se para criar uma nova conta e inserir informações na página. Azure AD B2C coleta os atributos de usuário.
| 2. | Azure AD B2C chama a API de camada intermediária e passa os atributos de usuário.
| 3. | A API de camada intermediária coleta atributos de usuário e os transforma em um formato que a API Jumio pode consumir. Depois de enviá-lo para Jumio.
| 4. | Depois que o Jumio consome as informações e a processa, ele retorna o resultado para a API de camada intermediária.
| 5. | A API de camada intermediária processa as informações e envia informações relevantes de volta para Azure AD B2C.
| 6. | Azure AD B2C recebe informações de volta da API de camada intermediária. Se ele mostrar uma resposta de falha, uma mensagem de erro será exibida para o usuário. Se ele mostrar uma resposta de êxito, o usuário será autenticado e gravado no diretório.

## <a name="onboard-with-jumio"></a>Integração com o Jumio

1. Para criar uma conta do Jumio, entre em contato com [Jumio](https://www.jumio.com/contact/)

2. Depois que uma conta é criada, as informações são usadas na configuração da API. As seções a seguir descrevem o processo.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Configurar Azure AD B2C com Jumio

### <a name="part-1---deploy-the-api"></a>Parte 1-implantar a API

Implante o [código de API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) fornecido em um serviço do Azure. O código pode ser publicado no Visual Studio, seguindo estas [instruções](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Você precisará da URL do serviço implantado para configurar o Azure AD com as configurações necessárias.

### <a name="part-2---deploy-the-client-certificate"></a>Parte 2-implantar o certificado do cliente

1. A chamada à API Jumio é protegida por um certificado de cliente. Crie um certificado autoassinado usando o código de exemplo do PowerShell mencionado abaixo:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. O certificado será então exportado para o local especificado para { ``your-local-path`` }.

3. Seguindo as instruções mencionadas neste [documento](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate), importe o certificado para o serviço de Azure app.

### <a name="part-3---create-a-signingencryption-key"></a>Parte 3-criar uma chave de assinatura/criptografia

Crie uma cadeia de caracteres aleatória com um comprimento maior que 64 caracteres que contenha apenas alfabetos ou números.

Por exemplo: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Use o script do PowerShell abaixo para criar um valor alfanumérico de comprimento de 64 caracteres.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Parte 4-configurar a API

As configurações do aplicativo podem ser [configuradas no serviço de aplicativo no Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Com esse método, as configurações podem ser configuradas com segurança sem verificá-las em um repositório. Você precisará fornecer as seguintes configurações para a API REST:

| Configurações do aplicativo | Fonte | Observações |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Configuração da conta do Jumio |     |
|JumioSettings:AuthPassword | Configuração da conta do Jumio |     |
|AppSettings: SigningCertThumbprint|Impressão digital do certificado autoassinado criado|  |
|AppSettings: IdTokenSigningKey| Chave de assinatura criada usando o PowerShell | |
| AppSettings: IdTokenEncryptionKey |Chave de criptografia criada usando o PowerShell
| AppSettings: IdTokenIssuer | Emissor a ser usado para o token JWT (é preferível um valor de GUID) |
| AppSettings: IdTokenAudience  | Público a ser usado para o token JWT (um valor de GUID é preferencial) |
|AppSettings: BaseRedirectUrl | A URL base da política B2C | https://{nome-do-locatário}. b2clogin. com/{Your-ID-do-aplicativo}|
| WEBSITE_LOAD_CERTIFICATES| Impressão digital do certificado autoassinado criado |

### <a name="part-5---deploy-the-ui"></a>Parte 5-implantar a interface do usuário

1. Configure um [contêiner de armazenamento de BLOBs em sua conta de armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Armazene os arquivos de interface do usuário da [pasta de interface do usuário](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) em seu contêiner de BLOB.

#### <a name="update-ui-files"></a>Atualizar arquivos da interface do usuário

1. Nos arquivos da interface do usuário, vá para a pasta [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Abra cada arquivo HTML.

3. Localize e substitua {Your-interface-blob-container-URL} pela URL do contêiner de BLOB.

4. Localize e substitua {Your-intermediário-API-URL} pela URL do serviço de aplicativo de API intermediário.

>[!NOTE]
> Como prática recomendada, recomendamos que os clientes adicionem notificação de autorização na página coleção de atributos. Notifique os usuários que as informações serão enviadas a serviços de terceiros para verificação de identidade.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Parte 6-configurar a política de Azure AD B2C

1. Vá para a [política de Azure ad B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) na pasta políticas.

2. Siga este [documento](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) para baixar o [pacote de início do LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configure a política para o locatário Azure AD B2C.

>[!NOTE]
>Atualize as políticas fornecidas para relacionar-se ao seu locatário específico.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **estrutura de experiência de identidade**.

2. Selecione o **SignUpSignIn**criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo**: selecione o aplicativo registrado (exemplo é JWT)

   b. **URL de resposta**: selecione a **URL de redirecionamento**

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. O serviço Jumio será chamado durante o fluxo, depois que o atributo de usuário for criado. Se o fluxo estiver incompleto, verifique se o usuário não está salvo no diretório.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introdução às políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
