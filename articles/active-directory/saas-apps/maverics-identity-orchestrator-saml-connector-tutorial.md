---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Maverics Identity Orchestrator SAML Connector | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Maverics Identity Orchestrator SAML Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518920"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Maverics Identity Orchestrator SAML Connector

## <a name="introduction"></a>Introdução

O Strata fornece uma forma simples de integrar aplicativos locais ao Azure AD para autenticação e controle de acesso.

Este guia explica como configurar o Maverics Identity Orchestrator&trade; para:
* Migrar de maneira incremental os usuários de um sistema de identidade local no Azure AD durante o logon para um aplicativo local herdado.
* Encaminhar solicitações de logon de um produto herdado de gerenciamento de acesso à Web, como o CA SiteMinder ou o Oracle Access Manager, para o Azure AD.
* Autenticar usuários em aplicativos locais que são protegidos usando cabeçalhos HTTP ou cookies de sessão proprietários depois de autenticar o usuário no Azure AD.

O Strata fornece um software que é implantado no local ou na nuvem para descoberta, conexão e orquestração entre provedores de identidade para criar o gerenciamento de identidades distribuídas para empresas híbridas e de várias nuvens.

Este tutorial demonstrará como migrar um aplicativo Web local atualmente protegido por um produto de gerenciamento de acesso à Web herdado (CA SiteMinder) para usar o Azure AD para autenticação e controle de acesso.
1. Instalar o Maverics Identity Orchestrator&trade;
2. Registre seu aplicativo empresarial com o Azure AD e configure-o para usar o Maverics Zero Code Connector&trade; do SAML do Azure AD para o SSO baseado em SAML.
3. Integre o Maverics ao SiteMinder e ao repositório de usuários do LDAP.
4. Configure o Azure Key Vault e configure o Maverics para usá-lo como seu provedor de gerenciamento de segredos.
5. Demonstre a migração de usuário e a abstração de sessão usando o Maverics para fornecer acesso a um aplicativo Web Java local.

Para obter mais instruções de instalação e configuração, acesse https://strata.io/docs

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
- Assinatura do Maverics Identity Orchestrator SAML Connector habilitada para SSO (logon único). Para obter o software do Maverics, entre em contato pelo email sales@strata.io

## <a name="install-maverics-identity-orchestratortrade"></a>Instalar o Maverics Identity Orchestrator&trade;

Para começar a instalação do Maverics Identity Orchestrator, confira as instruções de instalação em https://strata.io/docs

## <a name="system-requirements"></a>Requisitos de sistema
### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>Dependências
* systemd

## <a name="installation"></a>Instalação

1. Obtenha o pacote RPM mais recente do Maverics. Copie o pacote para o sistema no qual deseja instalar o software do Maverics.

2. Instale o pacote do Maverics substituindo o nome de arquivo no lugar de `maverics.rpm`.

    `sudo rpm -Uvf maverics.rpm`

3. Após a instalação do Maverics, ele será executado como um serviço em `systemd`. Para confirmar se o serviço está em execução, execute o comando a seguir.

    `sudo systemctl status maverics`

Por padrão, o Maverics é instalado no diretório `/usr/local/bin`.

Após a instalação do Maverics, o arquivo `maverics.yaml` padrão é criado no diretório `/etc/maverics`. Antes de você editar a configuração para incluir `workflows` e `connectors`, o arquivo de configuração terá esta aparência:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>Opções de configuração
### <a name="version"></a>Versão
O campo `version` declara a versão do arquivo de configuração que está sendo usada. Se ela não for especificada, a versão de configuração mais recente será usada.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Endereço de escuta
`listenAddress` declara o endereço que o Orchestrator escutará. Se a seção do host do endereço estiver em branco, o Orchestrator escutará todos os endereços IP unicast e anycast disponíveis do sistema local. Se a seção da porta do endereço estiver em branco, um número da porta será escolhido automaticamente.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

O campo `tls` declara um mapa de objetos de segurança da camada de transporte. Os objetos TLS podem ser usados por conectores, bem como pelo servidor do Orchestrator. Para obter todas as opções do TLS disponíveis, confira a documentação do pacote do `transport`.

O Microsoft Azure exige a comunicação via TLS durante o uso do SSO baseado em SAML. Encontre mais informações [aqui](https://letsencrypt.org/getting-started/) para gerar seus certificados.

A chave `maverics` é reservada para o servidor do Orchestrator. Todas as outras chaves estão disponíveis e podem ser usadas para injetar um objeto TLS em um conector especificado.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>Arquivos de inclusão

`connectors` e `workflows` podem ser definidos nos próprios arquivos de configuração separados e referenciados por `maverics.yaml` usando `includeFiles` de acordo com o exemplo a seguir.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

Este tutorial usa um só arquivo de configuração `maverics.yaml`.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>Como usar o Azure Key Vault como o provedor de segredos

### <a name="secret-management"></a>Gerenciamento de segredos

O Maverics pode ser integrado a várias soluções de gerenciamento de segredos para carregar segredos. As integrações atuais incluem um arquivo, o Hashicorp Vault e o Azure Key Vault. Se nenhuma solução de gerenciamento de segredos for especificada, o Maverics usará como padrão o carregamento de segredos de `maverics.yaml` em texto sem formatação.
Para declarar um valor como um segredo em um arquivo de configuração `maverics.yaml`, empacote o segredo com colchetes angulares:

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>Arquivo

Para carregar segredos de um arquivo, adicione a variável de ambiente `MAVERICS_SECRET_PROVIDER` ao arquivo `/etc/maverics/maverics.env` com:

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

Em seguida, reinicie o serviço do Maverics: `sudo systemctl restart maverics`

O conteúdo do arquivo `secrets.yaml` pode ser preenchido com qualquer número de `secrets`.
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Cofre de Chave do Azure

As seguintes etapas mostram como configurar um Azure Key Vault usando o [portal do Azure](https://portal.azure.com) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest):

1. [Faça logon](https://portal.azure.com) usando o portal do Azure ou o comando da CLI:
    ```shell
    az login
    ```

2. [Crie um Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) ou usando o comando da CLI:
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Adicione os segredos ao Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) ou usando um comando da CLI:
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [Registre um aplicativo no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) ou usando um comando da CLI:
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [Autorize um aplicativo a usar um segredo](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) ou usando um comando da CLI:
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Para carregar segredos do Azure Key Vault, defina a variável de ambiente `MAVERICS_SECRET_PROVIDER` no arquivo `/etc/maverics/maverics.env` com as credenciais encontradas no arquivo azure-credentials.json usando o seguinte padrão: `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

Em seguida, reinicie o serviço do Maverics: `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Configurar seu aplicativo no Azure AD para o SSO baseado em SAML

1. No locatário do Azure Active Directory, acesse `Enterprise applications`, procure o `Maverics Identity Orchestrator SAML Connector` e selecione-o.

2. No 'Maverics Identity Orchestrator SAML Connector', na página Propriedades, defina `User assignment required?` como Não para permitir que o aplicativo funcione para usuários recém-migrados.

3. No 'Maverics Identity Orchestrator SAML Connector', na página Visão Geral, selecione `Setup single sign-on` e escolha `SAML`.

4. No 'Maverics Identity Orchestrator SAML Connector', em Logon baseado em SAML, edite a Configuração Básica do SAML.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

5. defina a `Entity ID` digitando uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.maverics.org`. A `Entity ID` precisa ser exclusiva entre os aplicativos do locatário. Salve o valor inserido aqui para que ele seja incluído na configuração do Maverics.

6. Defina a URL de Resposta usando o seguinte padrão: `https://<AZURECOMPANY.COM>/<MY_APP>/`. 

7. Defina a URL de Logon usando o padrão `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` e clique em Salvar.

8. Procure a seção Certificado de Autenticação SAML e clique no botão Copiar para copiar a URL de Metadados de Federação do Aplicativo e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Configuração do Maverics Identity Orchestrator Azure AD SAML Connector

O Maverics Identity Orchestrator Connector para Azure AD dá suporte a: 
- OpenID Connect
- Conexão SAML 

1. Para habilitar o SSO baseado em SAML, defina `authType: saml`.

1. Crie o valor para `samlMetadataURL`: `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. Agora, defina a URL para a qual o Azure será redirecionado novamente para o seu aplicativo depois que eles fizerem logon com as respectivas credenciais do Azure.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. Copie o valor da EntityID configurada acima: `samlEntityID: https://<SUBDOMAIN>.maverics.org`

1. Copie o valor da URL de Resposta que o Azure AD usará para executar uma operação POST com a resposta do SAML.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. Gere uma chave de assinatura JWT, usada para proteger as informações de sessão do Maverics Identity Orchestrator&trade;, com a [ferramenta OpenSSL](https://www.openssl.org/source/):

    ```shell 
    openssl rand 64 | base64
    ```
1. Copie a resposta para a propriedade de configuração `jwtSigningKey`: `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`

## <a name="attributes-and-attribute-mapping"></a>Atributos e mapeamento de atributos
O mapeamento de atributos é usado para definir o mapeamento de atributos do usuário de um diretório local de usuários de origem para o Azure AD quando os usuários são provisionados.

Os atributos determinam os dados do usuário que podem ser retornados para um aplicativo em uma declaração, transmitidos em cookies de sessão ou transmitidos ao aplicativo em variáveis de cabeçalho HTTP.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Configurar o YAML do Maverics Identity Orchestrator Azure AD SAML Connector

A configuração do Maverics Identity Orchestrator Connector para Azure AD será parecida com esta:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>Migrar os usuários para o Azure AD

Siga esta configuração para migrar de maneira incremental os usuários de um produto de gerenciamento de acesso à Web, como o CA SiteMinder, o Oracle Access Manager ou o IBM Tivoli; um diretório LDAP ou um banco de dados SQL.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>Configurar as permissões de aplicativo no Azure AD para criar usuários

1. No seu locatário do Azure Active Directory, procure `App registrations` e selecione o aplicativo 'Maverics Identity Orchestrator SAML Connector'.

2. No 'Maverics Identity Orchestrator SAML Connector', em Certificados e segredos, selecione `New client secret` e escolha a opção de expiração. Clique no botão Copiar para copiar o segredo e salve-o no computador.

3. No 'Maverics Identity Orchestrator SAML Connector', em Permissões de API, selecione `Add permission` e, em Solicitar permissões de API, escolha `Microsoft Graph` e `Application permissions`. Na próxima tela, escolha `User.ReadWrite.All` e `Add permissions`. Isso levará você novamente às permissões de API, em que você selecionará `Grant admin consent`.


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>Configurar o YAML do Maverics Identity Orchestrator Azure AD SAML Connector para a migração de usuário

Para habilitar o fluxo de trabalho de migração de usuário, adicione estas propriedades extras ao arquivo de configuração:
1. Defina a URL do Azure Graph: `graphURL: https://graph.microsoft.com`
1. Defina a URL do token OAuth seguindo o padrão: `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. Defina o segredo do cliente gerado acima: `oauthClientSecret: <CLIENT SECRET>`


A configuração final do Maverics Identity Orchestrator Connector para Azure AD será parecida com esta:
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>Configurar o Maverics Zero Code Connector&trade; para o SiteMinder

O conector do SiteMinder é usado para migrar usuários para o Azure AD e fazer logon de usuários em aplicativos locais herdados protegidos pelo SiteMinder por meio das identidades e das credenciais recém-criadas do Azure AD.

Para este tutorial, o SiteMinder foi configurado para proteger o aplicativo herdado com a autenticação baseada em formulários e usando o cookie `SMSESSION`. Para integração a um aplicativo que consome a autenticação e a sessão por meio de cabeçalhos HTTP, será necessário adicionar a configuração de emulação de cabeçalho ao conector.

Este exemplo mapeia o atributo `username` para o cabeçalho HTTP `SM_USER`:
```yaml
  headers:
    SM_USER: username
```

Defina o `proxyPass` como a localização para a qual as solicitações são feitas por proxy. Normalmente, esse é o host do aplicativo protegido.

`loginPage` deve corresponder à URL do formulário de logon atualmente usado pelo SiteMinder ao redirecionar os usuários para autenticação.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>Configurar o Maverics Zero Code Connector&trade; para LDAP

Quando os aplicativos são protegidos por um produto WAM como o SiteMinder, as identidades e os atributos de usuário normalmente são armazenados em um diretório LDAP.

Essa configuração de conector demonstra como se conectar ao diretório LDAP configurado como o repositório de usuários do SiteMinder, de modo que as informações de perfil de usuário corretas possam ser coletadas durante o fluxo de trabalho de migração e um usuário correspondente possa ser criado no Azure AD.

* `baseDN` especifica a localização do diretório na qual a pesquisa LDAP deve ser executada.

* `url` é o endereço e a porta do servidor LDAP com os quais deve ser feita a conexão.

* `serviceAccountUsername` é o nome de usuário usado para se conectar ao servidor LDAP, geralmente expresso como um DN de associação, por exemplo `CN=Directory Manager`.

* `serviceAccountPassword` é a senha usada para se conectar ao servidor LDAP. Esse valor é armazenado na instância do Azure Key Vault configurada anteriormente.  

* `userAttributes` define a lista de atributos relacionados ao usuário para consulta. Esses atributos são mapeados posteriormente para os atributos correspondentes do Azure AD.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>Configurar o fluxo de trabalho de migração

A configuração do fluxo de trabalho de migração determina como o Maverics migrará os usuários do SiteMinder/LDAP para o Azure AD.

Este fluxo de trabalho:
- Usa o conector do SiteMinder para fazer proxy do logon do SiteMinder. As credenciais do usuário são validadas por meio da autenticação do SiteMinder e transmitidas para as etapas a seguir do fluxo de trabalho.
- Recupera os atributos de perfil do usuário do repositório de usuários do SiteMinder.
- Faz uma solicitação à API do Microsoft Graph para criar o usuário no seu locatário do Azure AD.

Etapas:
1. Dê um nome ao fluxo de trabalho, por exemplo, Migração do SiteMinder para o Azure AD.
2. Especifique o `endpoint`, que é um caminho HTTP no qual o fluxo de trabalho é exposto, que dispara as `actions` desse fluxo de trabalho em resposta às solicitações. Geralmente, o `endpoint` corresponde ao aplicativo que está intermediado por proxy, por exemplo, `/my_app`. O valor precisa incluir as barras à esquerda e à direita.
3. Adicione as `actions` apropriadas ao fluxo de trabalho.
    - Defina o método `login` para o conector do SiteMinder. O valor do conector precisa corresponder ao valor do nome na configuração do conector.
     - Defina o método `getprofile` para o conector LDAP.
     - Defina o `createuser` para o conector do Azure AD.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>Confirmar o fluxo de trabalho de migração

1. Se o serviço do Maverics ainda não estiver em execução, inicie-o executando o seguinte comando: `sudo systemctl start maverics`

2. Navegue até a URL de logon com proxy: `http://host.company.com/my_app`.
3. Forneça as credenciais de usuário usadas para fazer logon no aplicativo durante a proteção do SiteMinder.
4. Navegue até a Página Inicial > Usuários | Todos os Usuários para confirmar se o usuário foi criado no locatário do Azure AD.  

## <a name="configure-the-session-abstraction-workflow"></a>Configurar o fluxo de trabalho de abstração de sessão

O fluxo de trabalho de abstração de sessão move a autenticação e o controle de acesso para o aplicativo Web local herdado para o Azure AD.

O conector do Azure usa o método `login` para redirecionar o usuário para a URL de logon, supondo que não exista uma sessão.

Após a autenticação, o token de sessão criado como resultado é transmitido para o Maverics e o método `emulate` do conector do SiteMinder é usado para emular a sessão baseada em cookie e/ou a sessão baseada em cabeçalho e decorar a solicitação com os atributos adicionais necessários para o aplicativo.

1. Dê um nome ao fluxo de trabalho, por exemplo, Abstração de Sessão do SiteMinder.
2. Especifique o `endpoint`, que corresponde ao aplicativo que está sendo intermediado por proxy. O valor precisa incluir barras à esquerda e à direita, por exemplo, `/my_app/`.
3. Adicione as `actions` apropriadas ao fluxo de trabalho.
    - Defina o método `login` para o conector do Azure. O valor `connector` precisa corresponder ao valor de `name` na configuração do conector.
    - Defina o método `emulate` para o conector do SiteMinder.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>Confirmar o fluxo de trabalho de abstração de sessão

1. Navegue até a URL do aplicativo com proxy: `https://<AZURECOMPANY.COM>/<MY_APP>`. O usuário será redirecionado para a página de logon com proxy.
2. Insira as credenciais de usuário do Azure AD.
3. O usuário deverá ser redirecionado para o aplicativo como se fosse autenticado diretamente pelo SiteMinder.
