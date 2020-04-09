---
title: Movendo a autenticação do aplicativo do AD FS para o Azure Active Directory
description: Este artigo serve para ajudar as organizações a entender como mover aplicativos para o Azure AD, concentrando-se em aplicativos SaaS federados.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891842"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Movendo a autenticação de aplicativos dos Serviços ativos da Federação de Diretórios para o Diretório Ativo do Azure

[O Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) oferece uma plataforma de identidade universal que fornece a suas pessoas, parceiros e clientes uma única identidade para acessar aplicativos e colaborar a partir de qualquer plataforma e dispositivo. O Azure AD tem um [conjunto completo de recursos de gerenciamento de identidade.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) A padronização da autenticação e autorização do aplicativo (app) para o Azure AD permite os benefícios que esses recursos proporcionam. 

> [!NOTE]
> Este artigo se concentra em mover a autenticação de aplicativos de serviços de diretório ativo e active directory para o Azure AD. Consulte o white paper [Migrando a autenticação do aplicativo para o Azure AD](https://aka.ms/migrateapps/whitepaper) para uma visão geral do planejamento deste movimento. O white paper discute como planejar a migração, testes e insights.

## <a name="introduction"></a>Introdução

Se você tem um diretório local que contém contas de usuário, você provavelmente terá muitos aplicativos para os quais os usuários autenticam. Cada um desses aplicativos é configurado para que os usuários acessem usando suas identidades. 


Os usuários também podem autenticar diretamente com o seu Diretório Ativo no local. Active Directory Federation Services (AD FS) é um serviço de identidade baseado no local. O AD FS amplia a capacidade de usar a funcionalidade de soa de login único (SSO) entre parceiros de negócios confiáveis sem exigir que os usuários entrem separadamente em cada aplicativo. Isto é conhecido como Federação.

Muitas organizações têm aplicativos de Software as a Service (SaaS) ou de linha de negócios (LOB) personalizados federados diretamente para a AD FS, ao lado de aplicativos baseados no Office 365 e no Azure AD. 

![Aplicativos conectados diretamente no local](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Para aumentar a segurança dos aplicativos, seu objetivo é ter um único conjunto de controles e políticas de acesso em seus ambientes locais e em nuvem.** 

![Aplicativos conectados através do Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Tipos de aplicativos para migrar

Migrar toda a autenticação do aplicativo para o Azure AD é ótimo, pois lhe dá um único plano de controle para gerenciamento de identidade e acesso.

Seus aplicativos podem usar protocolos modernos ou legados para autenticação. Considere os primeiros aplicativos de migração que usam protocolos de autenticação modernos (como SAML e Open ID Connect). Esses aplicativos podem ser reconfigurados para autenticar com o Azure AD através de um conector integrado em nossa Galeria de Aplicativos ou registrando o aplicativo no Azure AD. Os aplicativos que usam protocolos mais antigos podem ser integrados usando [o Aplicativo Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy). 

Para obter mais informações, veja [quais tipos de aplicativos posso integrar com o Azure AD?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)

Você pode usar o [relatório de atividade saque a ad fs para migrar aplicativos para o Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) se você tiver [o Azure AD Connect Health ativado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs). 

### <a name="the-migration-process"></a>O processo de migração

Durante o processo de mudança da autenticação do aplicativo para o Azure AD, teste adequadamente seus aplicativos e configuração. Recomendamos que você continue a usar ambientes de teste existentes para testes de migração que se deslocam para o ambiente de produção. Se um ambiente de teste não estiver disponível no momento, você pode configurar um usando [o Azure App Service](https://azure.microsoft.com/services/app-service/) ou o [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), dependendo da arquitetura do aplicativo.

Você pode optar por configurar um inquilino Azure AD de teste separado para usar à medida que você desenvolve suas configurações de aplicativo. 

Seu processo de migração pode ser assim:

**Estágio 1 – Estado atual: Aplicativo de produção autenticando com AD FS**

![Fase de migração 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Estágio 2 – OPCIONAL: Exemplo de teste do aplicativo que aponta para testar o inquilino do Azure**

Atualize a configuração para apontar a instância de teste do aplicativo para um inquilino ad teste Dozure e faça quaisquer alterações necessárias. O aplicativo pode ser testado com usuários no teste azure AD inquilino. Durante o processo de desenvolvimento, você pode usar ferramentas como [o Fiddler](https://www.telerik.com/fiddler) para comparar e verificar solicitações e respostas.

Se a configuração de um inquilino de teste separado não for viável, pule esta etapa e levante-se uma instância de teste de um aplicativo e aponte-o para o seu inquilino Azure AD de produção, conforme descrito na Fase 3 abaixo.

![Fase de migração 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fase 3 – Aplicativo de teste apontando para a produção do inquilino do Azure**

Atualize a configuração para apontar a instância de teste do aplicativo para a instância de produção do Azure. Agora você pode testar com os usuários em sua instância de produção. Se necessário, revise a seção deste artigo sobre usuários em transição.

![Fase de migração 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fase 4 – Aplicativo de produção apontando para o inquilino da produção AD**

Atualize a configuração do seu aplicativo de produção para apontar para o seu inquilino azure de produção.

![Fase de migração 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplicativos que autenticam com AD FS podem usar grupos de diretórios ativos para permissões. Use [a sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) para sincronizar dados de identidade entre seu ambiente local e o Azure AD antes de iniciar a migração. Verifique esses grupos e membros antes da migração para que você possa conceder acesso aos mesmos usuários quando o aplicativo for migrado.

### <a name="line-of-business-lob-apps"></a>Aplicativos de linha de negócios (LOB)

Os aplicativos LOB são desenvolvidos internamente pela sua organização ou disponíveis como um produto embalado padrão que está instalado em seu data center. Exemplos incluem aplicativos construídos em aplicativos do Windows Identity Foundation e SharePoint (não do SharePoint Online). 

Os aplicativos LOB que usam OAuth 2.0, OpenID Connect ou WS-Federation podem ser [integrados](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)com o Azure AD como registros de aplicativos . Integre aplicativos personalizados que usam o SAML 2.0 ou o WS-Federation como [aplicativos não-galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) na página de aplicativos corporativos no [portal Azure](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Adesão única baseada em SAML

Os aplicativos que usam o SAML 2.0 para autenticação podem ser configurados para o SSO (SSO baseado em SAML) baseado em [SAML.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) Com [o SSO baseado em SAML,](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)você pode mapear usuários para funções específicas de aplicativos com base em regras que você define em suas reivindicações SAML. 

Para configurar um aplicativo SaaS para o login único baseado em SAML, consulte Configure a [placa única baseada em SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications). 

![Capturas de tela do usuário SSO SAML ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Muitos aplicativos SaaS têm um [tutorial específico de aplicativo](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) que o passo através da configuração para o login único baseado em SAML.

![tutorial de aplicativo](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Alguns aplicativos podem ser migrados facilmente. Aplicativos com requisitos mais complexos, como declarações personalizadas, podem exigir configuração adicional no Azure AD e/ou no Azure AD Connect. Para obter informações sobre mapeamentos de sinistros suportados, consulte [mapeamento de reivindicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

Tenha em mente as seguintes limitações ao mapear atributos:

* Nem todos os atributos que podem ser emitidos no AD FS aparecerão no Azure AD como atributos para emitir aos tokens SAML, mesmo que esses atributos sejam sincronizados. Quando você editar o atributo, a lista de sosseção de valor mostrará os diferentes atributos disponíveis no Azure AD. Verifique [a configuração de sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) para garantir que um atributo necessário — por exemplo, samAccountName — esteja sendo sincronizado com o Azure AD. Você pode usar os atributos de extensão para emitir qualquer reclamação que não faça parte do esquema padrão do usuário no Azure AD.

* Em cenários mais comuns, somente a declaração NameID e outras declarações de identificador de usuário comuns são necessárias para um aplicativo. Para determinar se são necessárias reivindicações adicionais, examine quais alegações você está emitindo do AD FS.

* Nem todas as reclamações podem ser problemas, pois algumas reivindicações são protegidas no Azure AD. 

* A capacidade de usar tokens SAML criptografados está agora em visualização. [Veja como: personalizar reivindicações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

 

### <a name="software-as-a-service-saas-apps"></a>Software como serviço (SaaS)

Se o login do usuário em aplicativos SaaS, como Salesforce, ServiceNow ou Workday, e for integrado ao AD FS, você está usando o login federado para aplicativos SaaS. 

A maioria dos aplicativos SaaS já pode ser configurada no Azure AD. A Microsoft tem muitas conexões pré-configuradas para aplicativos SaaS na [galeria de aplicativos Azure AD,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)o que facilitará sua transição. Os aplicativos SAML 2.0 podem ser integrados ao Azure AD através da galeria de aplicativos Azure AD ou como [aplicativos não-galeria](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app). 

Aplicativos que usam OAuth 2.0 ou OpenID Connect podem ser [integrados](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)com o Azure AD da mesma forma que registros de aplicativos . Aplicativos que usam protocolos legados podem usar [o Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para autenticar com o Azure AD.

Para quaisquer problemas com o onboarding de seus aplicativos SaaS, você pode entrar em contato com o [alias de suporte ao SaaS Application Integration.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

**Certificados de assinatura SAML para SSO**: Os certificados de assinatura são uma parte importante de qualquer implantação do SSO. O Azure AD cria os certificados de assinatura para estabelecer o SSO federado baseado em SAML para seus aplicativos SaaS. Depois de adicionar aplicativos de galeria ou não, você configurará o aplicativo adicionado usando a opção SSO federado. Consulte [Gerenciar certificados para o login único federado no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplicativos e configurações que podem ser movidos hoje

Aplicativos que você pode mover facilmente hoje incluem aplicativos SAML 2.0 que usam o conjunto padrão de elementos de configuração e afirmações:

* Nome UPN

* Endereço de email

* Nome

* Sobrenome

* Atributo alternativo, como **NameID** do SAML, incluindo o atributo de email, o prefixo de email, employeeid, os atributos de extensão 1-15 a 15 ou **SamAccountName** local. Para saber mais, confira [Editando a declaração NameIdentifier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

* Declarações personalizadas.

As seguintes etapas de configuração exigem que as etapas adicionais de configuração migrem para o Azure AD:

* Regras de autorização personalizada ou autenticação multifatorial (MFA) em AD FS. Você os configura usando o recurso [Azure AD Conditional Access.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* Aplicativos com vários pontos finais da URL de resposta. Você os configura no Azure AD usando o PowerShell ou na interface do portal Azure.

* Os aplicativos de WS-Federation, como aplicativos do SharePoint que requerem tokens SAML versão 1.1. Você pode configurá-los manualmente usando o PowerShell. Você também pode adicionar um modelo genérico pré-integrado para aplicativos SharePoint e SAML 1.1 da galeria. Apoiamos o protocolo SAML 2.0.

* A emissão complexa de sinistros transforma regras. Para obter informações sobre mapeamentos de sinistros suportados, consulte:
   *  [Mapeamento de declarações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplicativos e configurações sem suporte no Azure AD atualmente

Aplicativos que requerem os seguintes recursos não podem ser migrados hoje.

**Recursos de protocolo**

* Suporte ao padrão WS-Trust ActAs

* Resolução do artefato SAML

* Verificação de assinatura de solicitações SAML assinadas  
Observe que solicitações assinadas são aceitas, mas a assinatura não é verificada.  
Dado que o Azure AD só retornará o token para pontos finais pré-configurados no aplicativo, a verificação de assinatura provavelmente não é necessária na maioria dos casos.

**Reivindicações em recursos de token**

* Reivindicações de lojas de atributos que não sejam o diretório AD do Azure, a menos que esses dados sejam sincronizados com o Azure AD. Para obter mais informações, consulte a visão geral da [API de sincronização do Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Emissão de atributos de vários valores de diretório. Por exemplo, não podemos emitir uma reclamação multivalorizada para endereços proxy no momento.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapear configurações do aplicativo de AD FS para Azure AD

A migração começa avaliando como o aplicativo é configurado no local e mapeando essa configuração para o Azure AD. O AD FS e o Azure AD funcionam da mesma forma, de modo que os conceitos de configuração de URLs de confiança, login e login e identificação se aplicam em ambos os casos. Documente as configurações de configuração do AD FS de seus aplicativos para que você possa configurá-los facilmente no Azure AD.

### <a name="map-app-configuration-settings"></a>Mapear configurações de configuração do aplicativo

A tabela a seguir descreve alguns dos mapeamentos mais comuns de configurações entre um AD FS Relying Party Trust para o Azure AD Enterprise Application:

* AD FS – Encontre a configuração no AD FS Relying Party Trust para o aplicativo. Clique com o botão direito do mouse na parte de confiança e selecione Propriedades. 

* Azure AD – A configuração é configurada dentro [do portal Azure](https://portal.azure.com/) nas propriedades de login único de cada aplicativo.

| Definição de configuração| AD FS| Como configurar no Azure AD| Token SAML |
| - | - | - | - |
| **URL de logon do aplicativo** <p>A URL para que o usuário faça login no aplicativo em um fluxo SAML iniciado pelo Provedor de Serviços (SP).| N/D| Abra a configuração básica do SAML a partir do sinal baseado em SAML| N/D |
| **URL de resposta de aplicativo** <p>A URL do aplicativo sob a perspectiva do provedor de identidade (IdP's). O IdP envia o usuário e o token aqui depois que o usuário fez o preenchimento no IdP.  Isso também é conhecido como **ponto final de afirmação saml do consumidor**.| Selecione a guia **Pontos finais**| Abra a configuração básica do SAML a partir do sinal baseado em SAML| Elemento de destino no token SAML. Valor de exemplo:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **URL de saída do aplicativo** <p>Esta é a URL para a qual as solicitações de "limpeza de inscrição" são enviadas quando um usuário sai de um aplicativo. O IdP envia a solicitação para assinar o usuário de todos os outros aplicativos também.| Selecione a guia **Pontos finais**| Abra a configuração básica do SAML a partir do sinal baseado em SAML| N/D |
| **Identificador de aplicativos** <p>Este é o identificador de aplicativo da perspectiva do IdP. O valor da URL de login é frequentemente usado para o identificador (mas nem sempre).  Às vezes, o aplicativo chama isso de "ID da entidade".| Selecione a guia **Identificadores**|Abra a configuração básica do SAML a partir do sinal baseado em SAML| Mapeia para o elemento **Audiência** no token SAML. |
| **Metadados da federação de aplicativos** <p>Esta é a localização dos metadados da federação do aplicativo. O IdP os usa para atualizar automaticamente definições de configuração específicas, como pontos de extremidade ou certificados de criptografia.| Selecione a guia **Monitorar**| N/D O Azure AD não suporta metadados de federação de aplicativos de consumo diretamente. Você pode importar manualmente os metadados da federação.| N/D |
| **Identificador de usuário/ ID de nome** <p>Atributo usado para indicar exclusivamente a identidade do usuário do Azure AD ou do AD FS para seu aplicativo.  Esse atributo é tipicamente o UPN ou o endereço de e-mail do usuário.| Regras de reivindicação. Na maioria dos casos, a regra de sinistro emite uma reclamação com um tipo que termina com o NameIdentifier.| Você pode encontrar o identificador sob o cabeçalho **Atributos e Reivindicações do Usuário**. Por padrão, o UPN é usado| Mapeia para o elemento **NameID** no token SAML. |
| **Outras reivindicações** <p>Exemplos de outras informações de reivindicação que são comumente enviadas do IdP para o aplicativo incluem nome, sobrenome, endereço de e-mail e membros do grupo.| No AD FS, você pode encontrar isso como outras regras de declaração na terceira parte confiável.| Você pode encontrar o identificador sob o cabeçalho **Atributos do Usuário & Reivindicações**. Selecione **Exibir** e edite todos os outros atributos de usuário.| N/D |


### <a name="map-identity-provider-idp-settings"></a>Configurações do Provedor de Identidade do Mapa (IdP)

Configure seus aplicativos para apontar para Azure AD versus AD FS para SSO. Aqui, estamos focando em aplicativos SaaS que usam o protocolo SAML. No entanto, esse conceito também se estende a aplicativos LOB personalizados.

> [!NOTE]
> Os valores de configuração do Azure AD seguem o padrão em que o ID do inquilino do Azure substitui {inquilino-id} e o ID do aplicativo substitui {app-id}. Você encontra essas informações no [portal Azure](https://portal.azure.com/) em Propriedades de > do Azure Active Directory: 

* Selecione id de diretório para ver seu ID do inquilino. 

* Selecione iD de inscrição para ver seu ID de inscrição.

 Em um alto nível, mapeie os seguintes elementos de configuração de aplicativos SaaS para o Azure AD. 

 

| Elemento| Valor da Configuração |
| - | - |
| Emissor do provedor de identidade| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| URL de login do provedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| URL de logout do provedor de identidade| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Localização de metadados da Federação| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Mapear configurações de SSO para aplicativos SaaS

Os aplicativos SaaS precisam saber para onde enviar solicitações de autenticação e como validar os tokens recebidos. A tabela a seguir descreve os elementos para configurar as configurações do SSO no aplicativo e seus valores ou localizações dentro do AD FS e do Azure AD

| Definição de configuração| AD FS| Como configurar no Azure AD |
| - | - | - |
| **URL de login do IdP** <p>URL de login do IdP do ponto de vista do aplicativo (onde o usuário é redirecionado para login).| A URL de login do AD FS é o nome de serviço da federação AD FS seguido por "/adfs/ls/." <p>Por exemplo:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Substitua {inquilino-id} pelo seu ID de inquilino. <p> Para aplicativos que usam o protocolo SAML-P:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Para aplicativos que usam o protocolo WS-Federation:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **URL de saída de inscrição do IdP**<p>URL de saída do IdP do ponto de vista do aplicativo (onde o usuário é redirecionado quando ele escolhe sair do aplicativo).| A URL de saída de entrada é a mesma da URL de login ou a mesma URL com "wa=wsignout1.0" anexado. Por exemplo:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Substitua {inquilino-id} pelo seu ID de inquilino.<p>Para aplicativos que usam o protocolo SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Para aplicativos que usam o protocolo WS-Federation:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificado de autenticação de token**<p>O IdP usa a chave privada do certificado para assinar tokens emitidos. Ele verifica se o token veio do mesmo IdP em que o aplicativo está configurado para confiar.| Encontre o certificado de autenticação de token do AD FS no Gerenciamento do AD FS, em **Certificados**.| Encontre-o no portal Azure nas **propriedades de login único** do aplicativo sob o certificado de assinatura **SAML do**cabeçalho . Lá, você pode baixar o certificado para carregar no aplicativo.  <p>Se o aplicativo tiver mais de um certificado, você pode encontrar todos os certificados no arquivo XML de metadados da federação. |
| **Identificador/"emissor"**<p>Identificador do IdP do ponto de vista do aplicativo (às vezes chamado de "ID emissor").<p>No token SAML, o valor aparece como o elemento Emissor.| O identificador para AD FS é geralmente o identificador de serviço da federação em AD FS Management under **Service > Edit Federation Service Properties**. Por exemplo:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Substitua {inquilino-id} pelo seu ID de inquilino.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **Metadados da federação IdP**<p>Localização dos metadados da federação disponíveis publicamente pelo IdP. (Alguns aplicativos usam metadados de federação como uma alternativa à configuração de URLs, identificadores e certificados de autenticação de token pelo administrador individualmente.)| Encontre a URL de metadados da federação AD FS no Gerenciamento de AD FS sob **> Deponto de Serviço > Metadados > tipo: Metadados da Federação**. Por exemplo:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| O valor correspondente para Azure AD segue o padrão [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml). Substitua {TenantDomainName} pelo nome do seu inquilino no formato "contoso.onmicrosoft.com".   <p>Para saber mais, confira [Metadados de federação](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Representar políticas de segurança do AD FS no Azure AD

Ao mover a autenticação do aplicativo para o Azure AD, crie mapeamentos das políticas de segurança existentes para suas variantes equivalentes ou alternativas disponíveis no Azure AD. Garantir que esses mapeamentos possam ser feitos ao mesmo tempo em que atender aos padrões de segurança exigidos pelos proprietários de aplicativos tornará o resto da migração do aplicativo significativamente mais fácil.

Para cada tipo de regra e seus exemplos, sugerimos aqui como a regra se parece no AD FS, o código equivalente da linguagem de regra AD FS e como este mapa no Azure AD.

### <a name="map-authorization-rules"></a>Regras de autorização de mapa

A seguir, exemplos de tipos de regras de autorização no AD FS, e como você pode mapeá-las para o Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Exemplo 1: Permitir acesso a todos os usuários

Permitir acesso a todos os usuários parece no AD FS: 

![Fase de migração 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Este mapa para o Azure AD de uma das seguintes maneiras:

No [portal Azure:](https://portal.azure.com/)
* Opção 1: Definir a atribuição do usuário necessária para Não ![editar política de controle de acesso para aplicativos SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Observe que a configuração da atribuição de usuário necessária para Sim requer que os usuários sejam designados ao aplicativo para obter acesso. Quando definido como Não, todos os usuários têm acesso. Este switch não controla o que mostra para os usuários na experiência Meus Aplicativos. 

 
* Opção 2: Na guia Usuários e grupos, atribua seu aplicativo ao grupo automático "Todos os Usuários". <p>
Você deve [habilitar grupos dinâmicos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) no seu inquilino Azure AD para que o grupo 'Todos os Usuários' padrão esteja disponível.

   ![Meus aplicativos SaaS no Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Exemplo 2: Permitir um grupo explicitamente

Autorização explícita do grupo no AD FS:


![Regras de Autorização de Emissão ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


É assim que a regra mapeia o Azure AD:

No [portal Azure,](https://portal.azure.com/)você [primeiro criará um grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) de usuários que corresponde ao grupo de usuários do AD FS e, em seguida, atribuirá permissões de aplicativos a esse grupo:

![Adicionar atribuição ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Exemplo 3: Autorizar um usuário específico

Autorização explícita do usuário no AD FS:

![Regras de Autorização de Emissão ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

É assim que a regra mapeia o Azure AD:

No [portal Azure,](https://portal.azure.com/)adicione um usuário ao aplicativo através da guia Adicionar atribuição do aplicativo, conforme mostrado abaixo:

![Meus aplicativos SaaS no Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Mapear regras de autenticação multifatorial 

Uma implantação local de [Autenticação Multifatorial (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) e AD FS ainda funcionará após a migração porque você está federado com OD FS. No entanto, considere migrar para os recursos de MFA incorporados do Azure que estão vinculados aos fluxos de trabalho de acesso condicional do Azure AD. 

A seguir, exemplos de tipos de regras de MFA no AD FS, e como você pode mapeá-las para o Azure AD com base em diferentes condições:

Configurações de regras MFA no AD FS:

![Configurações do Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Exemplo 1: Aplicar o MFA com base em usuários/grupos

O seletor usuário/grupos é uma regra que permite que você aplique o MFA em uma base por Grupos (Grupo SID) ou por usuário (SID primário). Além das atribuições do Usuário/Grupos, todas as caixas de seleção adicionais na ui de configuração AD FS MFA funcionam como regras adicionais que são avaliadas após a aplicação da regra Usuário/Grupos. 


Especificar regras MFA para um usuário ou um grupo no Azure AD:

1. Crie uma [nova política de acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)

2. Selecione **Atribuições**. Adicione os(s) ou grupos de usuários que deseja impor o MFA.

3. Configure as opções **de controles de acesso** conforme mostrado abaixo:  
‎

![Configurações do AAD MFA](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Exemplo 2: Impor MFA para dispositivos não registrados

Especificar regras MFA para dispositivos não registrados no Azure AD:

1. Crie uma [nova política de acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)

2. Defina as **atribuições para** **todos os usuários**.

3. Configure as opções **de controles de acesso** conforme mostrado abaixo:  
‎

![Configurações do AAD MFA](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Quando você define a opção Para múltiplos controles para Exigir um dos controles selecionados, significa que se qualquer uma das condições especificadas pela caixa de seleção for cumprida pelo usuário, elas terão acesso ao seu aplicativo.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Exemplo 3: Aplicar o MFA com base na localização

Especificar regras MFA com base na localização de um usuário no Azure AD:

1. Crie uma [nova política de acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)

1. Defina as **atribuições para** **todos os usuários**.

1. [Configure locais nomeados no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) de outra forma a federação de dentro de sua rede corporativa é confiável. 

1. Configure as **regras de Condições** para especificar os locais para os quais você gostaria de aplicar o MFA.

![Configurações do Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Configure as opções **de controles de acesso** conforme mostrado abaixo:


![Mapear políticas de controle de acesso](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Mapear atributos de Emite como regra de Sinistros

Aqui está um exemplo de como os atributos são mapeados no AD FS:

![Configurações do Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


É assim que a regra mapeia o Azure AD:

No [portal Azure,](https://portal.azure.com/)selecione **Aplicativos Corporativos,** **Único login**e adicione **atributos de token SAML,** conforme mostrado abaixo:

![Configurações do Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mapear políticas de controle de acesso incorporadas

O AD FS 2016 tem várias políticas de controle de acesso incorporadas que você pode escolher:

![Azure AD construído no controle de acesso](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Para implementar políticas incorporadas no Azure AD, você pode usar uma [nova política de acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) e configurar os controles de acesso, ou pode usar o designer de políticas personalizados no AD FS 2016 para configurar políticas de controle de acesso. O Editor de Regras tem uma lista exaustiva de opções de Licença e Exceto que podem ajudá-lo a fazer todos os tipos de permutações.

![Políticas de controle de acesso a Ad do Azure](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



Nesta tabela, listamos algumas opções úteis de Licença e Exceção e como eles mapeiam para o Azure AD. 


| | Como configurar a opção de permissão no Azure AD?| Como configurar Exceto opção no Azure AD? |
| - | - | - |
| A partir de uma rede específica| Mapas para [localização nomeada](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) no Azure AD| Use a opção **Excluir** para [locais confiáveis](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| De grupos específicos| [Defina uma atribuição usuário/grupos](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Use a opção **Excluir** em Usuários e Grupos |
| De dispositivos com nível de confiança específico| Defina isso a partir do controle 'Estado do dispositivo' em Atribuições -> Condições| Use a opção **Excluir** em Condição de Estado do Dispositivo e Inclua **todos os dispositivos** |
| Com Reivindicações Específicas na Solicitação| Esta configuração não pode ser migrada| Esta configuração não pode ser migrada |


Um exemplo de como configurar a opção Excluir para locais confiáveis no Portal Azure:

![Captura de tela de políticas de controle de acesso de mapeamento](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Usuários de transição de AD FS para Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Sincronizar grupos AD FS no Azure AD

Quando você mapeia as regras de autorização, os aplicativos que autenticam com o AD FS podem usar grupos de Diretório Ativo para permissões. Nesse caso, use [o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) para sincronizar esses grupos com o Azure AD antes de migrar os aplicativos. Certifique-se de verificar esses grupos e membros antes da migração para que você possa conceder acesso aos mesmos usuários quando o aplicativo for migrado.

Para obter mais informações, consulte [Pré-requisitos para usar atributos de grupo sincronizados do Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims).

### <a name="setup-user-self-provisioning"></a>Configurar autoprovisionamento do usuário 

Alguns aplicativos SaaS suportam a capacidade de autoprovisionar os usuários quando eles fazem login pela primeira vez no aplicativo. No Azure Active Directory (Azure AD), o termo provisionamento de aplicativos refere-se à criação automática de identidades e funções do usuário nos aplicativos[cloud (SaaS)](https://azure.microsoft.com/overview/what-is-saas/)aos os que os usuários precisam acessar. Os usuários que forem migrados já terão uma conta no aplicativo SaaS. Todos os novos usuários adicionados após a migração precisarão ser provisionados. Teste [o provisionamento do aplicativo SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) assim que o aplicativo for migrado.

### <a name="sync-external-users-in-azure-ad"></a>Sincronizar usuários externos no Azure AD

Os usuários externos existentes podem ser configurados de duas maneiras principais dentro do AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Usuários externos com uma conta local dentro de sua organização

Você continuará a ser capaz de usar essas contas da mesma forma que suas contas internas de usuário funcionam. Essas contas de usuário externos têm um nome principal dentro de sua organização, embora o e-mail da conta possa apontar externamente. À medida que você avança com sua migração, você pode aproveitar os benefícios que o [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) oferece migrando esses usuários para usar sua própria identidade corporativa quando tal identidade estiver disponível. Isso simplifica o processo de login desses usuários, pois eles são frequentemente assinados com seu próprio logon corporativo. A administração da sua organização também será facilitada, não tendo mais que gerenciar contas para usuários externos.

#### <a name="federated-external-identities"></a>Identidades externas federadas

Se você está atualmente federando uma organização externa, você tem algumas abordagens a tomar:

* [Adicione os usuários de colaboração B2B do Azure Active Directory no portal Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). Você pode enviar proativamente convites de colaboração B2B do portal administrativo Azure AD para a organização parceira para que os membros individuais continuem usando os aplicativos e ativos aos seus usos. 

* [Crie um fluxo de trabalho de inscrição B2B de autoatendimento](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) que gera uma solicitação para usuários individuais em sua organização parceira usando a API de convite B2B.

Não importa como seus usuários externos existentes estão configurados, eles provavelmente têm permissões que estão associadas à sua conta, seja em membros de grupo ou permissões específicas. Avalie se essas permissões precisam ser migradas ou limpas. Contas dentro de sua organização que representam um usuário externo precisam ser desativadas uma vez que o usuário tenha sido migrado para uma identidade externa. O processo de migração deve ser discutido com seus parceiros de negócios, pois pode haver uma interrupção na capacidade deles de se conectar em seus recursos.

## <a name="migrate-and-test-your-apps"></a>Migre e teste seus aplicativos

Acompanhe o processo de migração detalhado neste artigo.

Em seguida, vá para o [portal Azure](https://aad.portal.azure.com/) para testar se a migração foi um sucesso. Siga as instruções abaixo:
1. Selecione **Aplicativos Corporativos** > **Todos os aplicativos** e encontre seu aplicativo na lista.

1. Selecione **Gerenciar** > **usuários e grupos** para atribuir pelo menos um usuário ou grupo ao aplicativo.

1. Selecione **Gerenciar** > **acesso condicional**. Revise sua lista de políticas e garanta que você não está bloqueando o acesso ao aplicativo com uma [política de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

Dependendo de como você configura seu aplicativo, verifique se o SSO funciona corretamente. 

| Tipo de autenticação| Testes |
| - | - |
| Conexão OAuth / OpenID| Selecione **aplicativos corporativos > permissões** e certifique-se de que você consentiu com o aplicativo a ser usado em sua organização nas configurações do usuário para o seu aplicativo.  
‎ |
| SSO baseado em SAML| Use o botão [Configurações saml de teste](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) encontrada em **Single Sign-On**.  
‎ |
| SSO baseado em senha| Baixe e instale o [MyApps Secure Sign](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[in Extension](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction). Essa extensão ajuda você a iniciar qualquer um dos aplicativos em nuvem da sua organização que exigem que você use um processo de SSO.  
‎ |
| Proxy do Aplicativo| Certifique-se de que o conector está sendo executado e atribuído ao seu aplicativo. Visite o[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) [guia de solução de problemas](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) do Proxy do aplicativo para obter mais assistência.  
‎ |

> [!NOTE]
> Os cookies do antigo ambiente AD FS ainda serão persistentes nas máquinas do usuário. Esses cookies podem causar problemas com a migração, pois os usuários podem ser direcionados para o antigo ambiente de login do AD FS em comparação com o novo login Azure AD. Você pode precisar limpar os cookies do navegador do usuário manualmente ou usando um script. Você também pode usar o System Center Configuration Manager ou uma plataforma semelhante.

### <a name="troubleshoot"></a>Solucionar problemas

Se houver algum erro no teste dos aplicativos migrados, a solução de problemas pode ser o primeiro passo antes de voltar para as Partes de Confiam do AD FS existentes. Veja [como depurar o login único baseado em SAML em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues).

### <a name="rollback-migration"></a>Migração de reversão

Se a migração falhar, recomendamos que você deixe as Partes De Relying existentes nos servidores AD FS e remova o acesso às Partes Que Dependem. Isso permitirá um recuo rápido, se necessário durante a implantação.

### <a name="end-user-communication"></a>Comunicação do usuário final

Embora a janela de paralisação planejada em si possa ser mínima, você ainda deve planejar comunicar esses prazos proativamente aos funcionários enquanto faz o corte de AD FS para Azure AD. Certifique-se de que a experiência do aplicativo tenha um botão Feedback ou ponteiros para o seu helpdesk para problemas.

Uma vez que a implantação esteja concluída, você pode enviar comunicadoinformando os usuários da implantação bem-sucedida e lembrá-los de quaisquer novos passos que eles precisam tomar.

* Instrua os usuários a usar o [Painel de Acesso](https://myapps.microsoft.com.com/) para acessar todos os aplicativos migrados. 

* Lembre os usuários de que eles podem precisar atualizar suas configurações de MFA. 

* Se a redefinição de senha de autoatendimento for implantada, os usuários podem precisar atualizar ou verificar seus métodos de autenticação. Consulte modelos de comunicação do usuário final [MFA](https://aka.ms/mfatemplates) e [SSPR.](https://aka.ms/ssprtemplates)

Comunicação para usuários externos: Esse grupo de usuários geralmente é o mais criticamente impactado em caso de problemas. Isso é especialmente verdadeiro se sua postura de segurança ditar um conjunto diferente de regras de Acesso Condicional ou perfis de risco para parceiros externos. Certifique-se de que os parceiros externos estejam cientes do cronograma de migração na nuvem e tenham um prazo durante o qual eles são encorajados a participar de uma implantação piloto que testa todos os fluxos exclusivos da colaboração externa. Finalmente, certifique-se de que eles têm uma maneira de acessar seu helpdesk em caso de problemas de quebra.

## <a name="next-steps"></a>Próximas etapas
Leia [a autenticação do aplicativo migratório para o Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Configurar [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) e [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
