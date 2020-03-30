---
title: Domínios Personalizados no Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Configure e gerencie domínios personalizados no Proxy de aplicativo Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481374"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Configure domínios personalizados com proxy de aplicativo Azure AD

Quando você publica um aplicativo através do Proxy do Azure Active Directory Application, você cria uma URL externa para seus usuários. Essa URL obtém o domínio padrão *seulocatário.msappproxy.net*. Por exemplo, se você publicar um aplicativo chamado *Despesas* em seu inquilino chamado *Contoso,* a URL externa será *\/https: /expenses-contoso.msappproxy.net*. Se você quiser usar seu próprio nome de domínio em vez de *msappproxy.net,* você pode configurar um domínio personalizado para o seu aplicativo. 

## <a name="benefits-of-custom-domains"></a>Benefícios de domínios personalizados

É uma boa ideia configurar domínios personalizados para seus aplicativos sempre que possível. Algumas razões para usar domínios personalizados incluem:

- Os links entre aplicativos funcionam mesmo fora da rede corporativa. Sem um domínio personalizado, se o aplicativo tiver links internos codificados para alvos fora do Proxy de aplicativo e os links não forem solucionáveis externamente, eles serão arrombados. Quando seus URLs internos e externos são os mesmos, você evita esse problema. Se você não for capaz de usar domínios personalizados, consulte [Redirecionar links codificados para aplicativos publicados com o Azure AD Application Proxy](../application-proxy-link-translation.md) para outras maneiras de resolver esse problema. 
  
- Seus usuários terão uma experiência mais fácil, pois podem chegar ao aplicativo com a mesma URL de dentro ou fora da sua rede. Eles não precisam aprender diferentes URLs internas e externas, ou rastrear sua localização atual. 

- Você pode controlar sua marca e criar os URLs que deseja. Um domínio personalizado pode ajudar a aumentar a confiança de seus usuários, porque os usuários vêem e usam um nome familiar em vez de *msappproxy.net*.

- Algumas configurações só funcionarão com domínios personalizados. Por exemplo, você precisa de domínios personalizados para aplicativos que usam o SAML (Security Assertion Markup Language, linguagem de marcação de afirmação de segurança), como quando você está usando o Active Directory Federation Services (AD FS), mas não pode usar o WS-Federation. Para obter mais informações, consulte [Trabalhar com aplicativos com reconhecimento de sinistros no Proxy de aplicativos](application-proxy-configure-for-claims-aware-applications.md). 

Se você não é capaz de fazer os URLs internos e externos coincidirem, não é tão importante usar domínios personalizados, mas você ainda pode aproveitar os outros benefícios. 

## <a name="dns-configuration-options"></a>Opções de configuração de DNS

Existem várias opções para configurar sua configuração de DNS, dependendo de suas necessidades:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Mesma URL interna e externa, comportamento interno e externo diferente 

Se você não quiser que seus usuários internos sejam direcionados através do Proxy do aplicativo, você pode configurar um *DNS de cérebro dividido*. Uma infra-estrutura DNS dividida direciona hosts internos para um servidor de nome de domínio interno e hosts externos para um servidor de nome de domínio externo, para resolução de nomes. 

![DNS com partição de rede](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Diferentes URLs internas e externas 

Se os URLs internos e externos forem diferentes, você não precisa configurar o comportamento split-brain, porque o roteamento do usuário é determinado pela URL. Neste caso, você altera apenas o DNS externo e encaminha a URL externa para o ponto final do Proxy do aplicativo. 

Quando você seleciona um domínio personalizado para uma URL externa, uma barra de informações mostra a entrada CNAME que você precisa adicionar ao provedor de DNS externo. Você sempre pode ver essas informações indo para a página proxy do **aplicativo.**

## <a name="set-up-and-use-custom-domains"></a>Configurar e usar domínios personalizados

Para configurar um aplicativo local para usar um domínio personalizado, você precisa de um domínio personalizado do Azure Active Directory verificado, um certificado PFX para o domínio personalizado e um aplicativo on-premises para configurar. 

### <a name="create-and-verify-a-custom-domain"></a>Crie e verifique um domínio personalizado

Para criar e verificar um domínio personalizado:

1. No Azure Active Directory, selecione **Nomes de domínio personalizados** na navegação à esquerda e, em seguida, selecione **Adicionar domínio personalizado**. 
1. Digite seu nome de domínio personalizado e selecione **Adicionar domínio**. 
1. Na página de domínio, copie as informações de registro TXT para o seu domínio. 
1. Vá ao seu registrador de domínio e crie um novo registro TXT para o seu domínio, com base nas informações de DNS copiadas.
1. Depois de registrar o domínio, na página do domínio no Diretório Ativo do Azure, **selecione Verificar**. Uma vez **verificado**o status do domínio, você pode usar o domínio em todas as configurações do Azure AD, incluindo proxy de aplicativo. 

Para obter instruções mais detalhadas, consulte [Adicionar seu nome de domínio personalizado usando o portal Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Configure um aplicativo para usar um domínio personalizado

Para publicar seu aplicativo através do Proxy de aplicativo com um domínio personalizado:

1. Para um novo aplicativo, no Azure Active Directory, selecione **aplicativos Enterprise** na navegação à esquerda. Selecione **Novo aplicativo**. Na seção **Aplicativos locais**, selecione **Adicionar um aplicativo local**. 
   
   Para um aplicativo já em **aplicativos Enterprise,** selecione-o na lista e, em seguida, selecione **proxy de aplicativo** na navegação à esquerda. 

2. Na página Configurações do Proxy do aplicativo, digite um **Nome** se estiver adicionando seu próprio aplicativo no local.

3.  No campo **Url interno,** digite a URL interna do seu aplicativo.
   
4. No **campo Url externo,** baixe a lista e selecione o domínio personalizado que deseja usar.
   
5. Selecione **Adicionar**.
   
   ![Selecione domínio personalizado](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Se o domínio já tiver um certificado, o campo **Certificado** exibirá as informações do certificado. Caso contrário, selecione o campo **Certificado.** 
   
   ![Clique para carregar um certificado](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na página do **certificado SSL,** navegue e selecione seu arquivo de certificado PFX. Digite a senha do certificado e selecione **Certificado de upload**. Para obter mais informações sobre certificados, consulte a seção [Certificados para domínios personalizados.](#certificates-for-custom-domains)
   
   ![Carregar um certificado](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Um domínio personalizado só precisa de seu certificado carregado uma vez. Depois disso, o certificado carregado é aplicado automaticamente quando você usa o domínio personalizado para outros aplicativos.
   
8. Se você adicionou um certificado, na página **proxy do aplicativo,** selecione **Salvar**. 
   
9. Na barra de informações na página **proxy do aplicativo,** observe a entrada CNAME que você precisa adicionar à sua região De DNS. 
   
   ![Adicionar entrada CNAME DNS](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Siga as instruções em [Gerenciar registros dns e conjuntos de registros usando o portal Azure](../../dns/dns-operations-recordsets-portal.md) para adicionar um registro DNS que redireciona a nova URL externa para o domínio *msappproxy.net.*
   
11. Para verificar se o registro DNS está configurado corretamente, use o comando [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) para confirmar se sua URL externa é acessível e o domínio *msapproxy.net* aparece como um alias.

Seu aplicativo agora está configurado para usar o domínio personalizado. Certifique-se de atribuir usuários ao seu aplicativo antes de testá-lo ou liberá-lo. 

Para alterar o domínio de um aplicativo, selecione um domínio diferente da lista de saque na **URL externa** na página proxy do **aplicativo.** Faça upload de um certificado para o domínio atualizado, se necessário, e atualize o registro DNS. Se você não ver o domínio personalizado que deseja na lista de sossedenaem na **URL externa,** ele pode não ser verificado.

Para obter instruções mais detalhadas para o Proxy de aplicativos, consulte [Tutorial: Adicione um aplicativo local para acesso remoto através do Proxy de aplicativo no Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certificados para domínios personalizados

Um certificado cria a conexão TLS segura para seu domínio personalizado. 

### <a name="certificate-formats"></a>Formatos de certificado

Você deve usar um certificado PFX, para garantir que todos os certificados intermediários necessários sejam incluídos. O certificado deve incluir a chave privada.

Não há restrição nos métodos de assinatura do certificado. Cryptography de curva elíptica (ECC), Nome Alternativo de Assunto (SAN) e outros tipos de certificados comuns são suportados. 

Você pode usar certificados curinga, desde que o curinga corresponda à URL externa. Você deve usar certificados curinga para [aplicações curinga](application-proxy-wildcard.md). Se você quiser usar o certificado para também acessar subdomínios, você deve adicionar os curingas de subdomínio como nomes alternativos de assunto no mesmo certificado. Por exemplo, um certificado para * \*.adventure-works.com* não funcionará para * \*.apps.adventure-works.com* a menos que você adicione * \*.apps.adventure-works.com* como um nome alternativo de assunto. 

Você pode usar certificados emitidos por sua própria infra-estrutura de chaves públicas (PKI) se a cadeia de certificados estiver instalada em seus dispositivos clientes. O Intune pode implantar esses certificados em dispositivos gerenciados. Para dispositivos não gerenciados, você deve instalar manualmente esses certificados. 

Não recomendamos o uso de um CA raiz privada, uma vez que o CA raiz privado também precisaria ser empurrado para as máquinas clientes, o que pode introduzir muitos desafios.

### <a name="certificate-management"></a>Gerenciamento de certificados

Todo o gerenciamento de certificados é através das páginas de aplicativos individuais. Acesse a **página** proxy do aplicativo para acessar o campo **Certificado.**

Você pode usar o mesmo certificado para vários aplicativos. Se um certificado carregado funcionar com outro aplicativo, ele será aplicado automaticamente. Você não será solicitado a carregá-lo novamente quando adicionar ou configurar o aplicativo. 

Quando um certificado expira, você recebe um aviso dizendo para carregar outro certificado. Se o certificado for revogado, seus usuários poderão ver um aviso de segurança ao acessar o aplicativo. Para atualizar o certificado de um aplicativo, navegue até a página proxy do **aplicativo,** selecione **Certificado**e faça upload de um novo certificado. Se o certificado antigo não estiver sendo usado por outros aplicativos, ele será excluído automaticamente. 

## <a name="next-steps"></a>Próximas etapas
* [Habilite o logon único](application-proxy-configure-single-sign-on-with-kcd.md) aos seus aplicativos publicados com a autenticação do Azure AD.
* [Habilite o acesso condicional](../conditional-access/overview.md) aos seus aplicativos publicados.

