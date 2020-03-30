---
title: Configure uma federação direta com um AD FS para B2B - Azure AD
description: Saiba como configurar o AD FS como um provedor de identidade para federação direta para que os hóspedes possam entrar em seus aplicativos AD do Azure
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272837"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>Exemplo: Federação Direta com Serviços ativos da Federação de Diretórios (AD FS) (pré-visualização)
|     |
| --- |
| Federação direta é um recurso de pré-visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como configurar a [federação direta](direct-federation.md) usando os Serviços de Federação de DiretórioS Ativos (AD FS) como um provedor de identidade SAML 2.0 ou WS-Fed. Para apoiar a federação direta, certos atributos e reclamações devem ser configurados no provedor de identidade. Para ilustrar como configurar um provedor de identidade para federação direta, usaremos o Active Directory Federation Services (AD FS) como exemplo. Mostraremos como configurar o AD FS tanto como um provedor de identidade SAML quanto como um provedor de identidade WS-Fed.

> [!NOTE]
> Este artigo descreve como configurar o AD FS para fins de saml e WS-Fed para fins de ilustração. Para integrações diretas da federação onde o provedor de identidade é AD FS, recomendamos usar o WS-Fed como protocolo. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>Configure AD FS para federação direta SAML 2.0
O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo SAML com requisitos específicos listados abaixo. Para ilustrar as etapas de configuração do SAML, esta seção mostra como configurar o AD FS para SAML 2.0. 

Para configurar a federação direta, os seguintes atributos devem ser recebidos na resposta SAML 2.0 do provedor de identidade. Esses atributos podem ser configurados vinculando-se ao arquivo XML do serviço de token de segurança on-line ou inserindo-os manualmente. Passo 12 em [Criar uma instância AD FS de teste](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como encontrar os pontos finais `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`do AD FS ou como gerar sua URL de metadados, por exemplo . 

|Atributo  |Valor  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O uri emissor do IdP parceiro, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |

As seguintes alegações precisam ser configuradas no token SAML 2.0 emitido pelo provedor de identidade:


|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


A próxima seção ilustra como configurar os atributos e reivindicações necessários usando o AD FS como exemplo de um provedor de identidade SAML 2.0.

### <a name="before-you-begin"></a>Antes de começar

Um servidor AD FS já deve ser configurado e funcionando antes de iniciar este procedimento. Para obter ajuda na configuração de um servidor AD FS, consulte [Criar uma instância AD FS 3.0 de teste em uma máquina virtual Do Zure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).

### <a name="add-the-claim-description"></a>Adicione a descrição da reivindicação

1. No servidor AD FS, selecione **ferramentas** > **de gerenciamento ad FS**.
2. No painel de navegação, selecione**Descrições de reivindicação de** **serviço** > .
3. Em **Ações,** selecione **Adicionar descrição de sinistro**.
4. Na janela Adicionar uma janela **Descrição de sinistro,** especifique os seguintes valores:

   - **Nome da exibição:** identificador persistente
   - **Identificador de sinistro:**`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - Selecione a caixa de seleção para **publicar esta descrição de reivindicação nos metadados da federação como um tipo de reclamação que este serviço de federação pode aceitar**.
   - Selecione a caixa de seleção para **publicar esta descrição de reivindicação nos metadados da federação como um tipo de reclamação que este serviço de federação pode enviar**.

5. Clique em **Ok**.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicione a confiança do partido que depende e as regras de reivindicação

1. No servidor AD FS, vá para **o gerenciamento de Ferramentas** > **AD FS**.
2. No painel de navegação, selecione **Trust Relationships** > **Trusting Party Trusts**.
3. Em **Ações,** selecione **Adicionar confiança do partido de confiança**. 
4. No assistente de confiança de parte sustiva para **Select Data Source**, use a opção Importar dados sobre a parte confiável publicada **on-line ou em uma rede local**. Especifique esta https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xmlURL de metadados da federação. Deixe outras seleções padrão. Selecione **Fechar**.
5. O **assistente Editar regras de reivindicação** é aberto.
6. No assistente **Editar regras de reivindicação,** selecione **Adicionar regra**. Em **'Escolher tipo de regra',** **selecione Enviar atributos LDAP como Reclamações**. Selecione **Avançar**.
7. Em **Configurar regra de reclamação,** especifique os seguintes valores: 

   - **Nome da regra de reivindicação**: Regra de reivindicação de e-mail 
   - **Armazenamento de atributos**: Active Directory 
   - **Atributo LDAP**: Endereços de e-mail 
   - **Tipo de reclamação de saída**: endereço de e-mail

8. Selecione **Concluir**.
9. A janela **Editar regras de reivindicação** mostrará a nova regra. Clique em **Aplicar**. 
10. Clique em **Ok**.  

### <a name="create-an-email-transform-rule"></a>Crie uma regra de transformação de e-mail
1. Vá para **Editar regras de reivindicação** e clique em Adicionar **regra**. Em **'Escolher tipo de regra',** **selecione Transformar uma reclamação recebida e** clique em **Seguir**. 
2. Em **Configurar regra de reclamação,** especifique os seguintes valores: 

   - **Nome da regra de reivindicação**: Regra de transformação de e-mail 
   - **Tipo de reclamação recebida**: Endereço de e-mail 
   - **Tipo de reivindicação de saída**: ID de nome 
   - **Formato iD de nome de saída :** Identificador persistente 
   - Selecione **Passar por todos os valores de sinistro**.

3. Clique em **concluir**. 
4. A janela **Editar regras de reivindicação** mostrará as novas regras. Clique em **Aplicar**. 
5. Clique em **OK**. O servidor AD FS está agora configurado para federação direta usando o protocolo SAML 2.0.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>Configure AD FS para federação direta WS-Fed 
O Azure AD B2B pode ser configurado para federar com provedores de identidade que usam o protocolo WS-Fed com os requisitos específicos listados abaixo. Atualmente, os dois provedores ws-fed foram testados para compatibilidade com a Azure AD incluem AD FS e Shibboleth. Aqui, usaremos os Serviços ativos da Federação de Diretórios (AD FS) como um exemplo do provedor de identidade WS-Fed. Para obter mais informações sobre como estabelecer uma confiança de parte confiável entre um provedor compatível com o WS-Fed com o Azure AD, baixe os Documentos de Compatibilidade do Provedor de Identidade Azure AD.

Para configurar a federação direta, os seguintes atributos devem ser recebidos na mensagem WS-Fed do provedor de identidade. Esses atributos podem ser configurados vinculando-se ao arquivo XML do serviço de token de segurança on-line ou inserindo-os manualmente. Passo 12 em [Criar uma instância AD FS de teste](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) descreve como encontrar os pontos finais `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`do AD FS ou como gerar sua URL de metadados, por exemplo .
 
|Atributo  |Valor  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Público     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O uri emissor do IdP parceiro, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |

Reivindicações necessárias para o token WS-Fed emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|ImutávelID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

A próxima seção ilustra como configurar os atributos e reivindicações necessários usando o AD FS como um exemplo de um provedor de identidade WS-Fed.

### <a name="before-you-begin"></a>Antes de começar
Um servidor AD FS já deve ser configurado e funcionando antes de iniciar este procedimento. Para obter ajuda na configuração de um servidor AD FS, consulte [Criar uma instância AD FS 3.0 de teste em uma máquina virtual Do Zure](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed).


### <a name="add-the-relying-party-trust-and-claim-rules"></a>Adicione a confiança do partido que depende e as regras de reivindicação 
1. No servidor AD FS, vá para **o gerenciamento de Ferramentas** > **AD FS**. 
1. No painel de navegação, selecione **Trust Relationships** > **Trusting Party Trusts**. 
1. Em **Ações,** selecione **Adicionar confiança do partido de confiança**.  
1. No assistente de confiança de parte suscetido a adicionar, para **Selecionar Origem de Dados,** use a opção **Importar dados sobre a parte confiável publicada on-line ou em uma rede local**. Especifique esta `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`URL de metadados da federação: .  Deixe outras seleções padrão. Selecione **Fechar**.
1. O **assistente Editar regras de reivindicação** é aberto. 
1. No assistente **Editar regras de reivindicação,** selecione **Adicionar regra**. Em **Escolher tipo de regra,** **selecione Enviar reivindicações usando uma regra personalizada**. Selecione *Avançar*. 
1. Em **Configurar regra de reclamação,** especifique os seguintes valores:

   - **Nome da regra de reivindicação**: Id Imutável de problema  
   - **Regra personalizada:**`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. Selecione **Concluir**. 
1. A janela **Editar regras de reivindicação** mostrará a nova regra. Clique em **Aplicar**.  
1. No mesmo assistente **Editar regras de reivindicação,** selecione **Adicionar regra**. No **tipo de regra cohose,** selecione Enviar **atributos LDAP como Reivindicações**. Selecione **Avançar**.
1. Em **Configurar regra de reclamação,** especifique os seguintes valores: 

   - **Nome da regra de reivindicação**: Regra de reivindicação de e-mail  
   - **Armazenamento de atributos**: Active Directory  
   - **Atributo LDAP**: Endereços de e-mail  
   - **Tipo de reclamação de saída**: endereço de e-mail 

1.  Selecione **Concluir**. 
1.  A janela **Editar regras de reivindicação** mostrará a nova regra. Clique em **Aplicar**.  
1.  Clique em **OK**. O servidor AD FS está agora configurado para federação direta usando o WS-Fed.

## <a name="next-steps"></a>Próximas etapas
Em seguida, você [configurará a federação direta no Azure AD](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) no portal Azure AD ou usando o PowerShell. 
