---
title: Conceitos de floresta de recursos do Azure AD Domain Services | Microsoft Docs
description: Saiba o que é uma floresta de recursos no Azure Active Directory Domain Services e como elas beneficiam a sua organização em um ambiente híbrido com opções de autenticação de usuário limitadas ou preocupações de segurança.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e0e5dde246dbcd5e5cb2e4ae923872a59a539d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476409"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Conceitos e recursos da floresta de recursos do Azure Active Directory Domain Services

O Azure AD DS (Active Directory Domain Services) fornece uma experiência de conexão para aplicativos de linha de negócios, herdados e locais. Os usuários, grupos e hashes de senha de usuários locais e na nuvem são sincronizados com o domínio gerenciado do Azure AD DS. Esses hashes de senha sincronizados são o que fornece aos usuários um conjunto de credenciais que eles podem usar para o AD DS local, o Office 365 e o Azure Active Directory.

Embora seja seguro e forneça benefícios de segurança adicionais, algumas organizações não podem sincronizar os hashes de senhas de usuário para o Azure AD ou o Azure AD DS. Os usuários em uma organização podem não saber senha deles porque usam apenas a autenticação de cartão inteligente. Essas limitações impedem que algumas organizações usem o Azure AD DS para realizar lift-and-shift em aplicativos clássicos locais para o Azure.

Para atender a essas necessidades e restrições, você pode criar um domínio gerenciado do Azure AD DS que usa uma floresta de recursos. Este artigo conceitual explica o que são as florestas e como elas confiam em outros recursos para fornecer um método de autenticação seguro. No momento, as florestas de recursos do Azure AD DS estão em versão prévia.

> [!IMPORTANT]
> Atualmente, as florestas de recursos do Azure AD DS não dão suporte ao Azure HDInsight ou aos Arquivos do Azure. As florestas de usuário padrão do Azure AD DS dão suporte a esses dois serviços adicionais.

## <a name="what-are-forests"></a>O que são florestas?

Uma *floresta* é um constructo lógico usado pelo AD DS (Active Directory Domain Services) para agrupar um ou mais *domínios*. Em seguida, os domínios armazenam objetos para usuários ou grupos e fornecem serviços de autenticação.

No Azure AD DS, a floresta contém apenas um domínio. As florestas do AD DS locais geralmente contêm muitos domínios. Em grandes organizações, especialmente depois de fusões e aquisições, você poderá terminar com várias florestas locais que contêm vários domínios.

Por padrão, um domínio gerenciado do Azure AD DS é criado como uma floresta de *usuários*. Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local. As contas de usuário podem se autenticar diretamente no domínio gerenciado do Azure AD DS, por exemplo, para entrar em uma VM ingressada no domínio. Uma floresta de usuário funciona quando os hashes de senha podem ser sincronizados e os usuários não estão usando métodos de conexão exclusivos, como a autenticação de cartão inteligente.

Em uma floresta de *recursos* do Azure AD DS, os usuários se autenticam em uma *relação de confiança* da floresta unidirecional nos AD DS locais. Com essa abordagem, os objetos de usuário e os hashes de senha não são sincronizados com o Azure AD DS. Os objetos de usuário e as credenciais existem somente no AD DS local. Essa abordagem permite que as empresas hospedem recursos e plataformas de aplicativo no Azure que dependem da autenticação clássica, tais como LDAPS, Kerberos ou NTLM, mas todas as preocupações ou problemas de autenticação são removidos. No momento, as florestas de recursos do Azure AD DS estão em versão prévia.

As florestas de recursos também fornecem a capacidade de realizar lift-and-shift nos seus aplicativos, um componente por vez. Muitos aplicativos locais herdados têm vários níveis, geralmente usando um servidor Web ou front-end e muitos componentes relacionados ao banco de dados. Esses níveis dificultam a realização de lift-and-shift do aplicativo inteiro para a nuvem em uma etapa. Com as florestas de recursos, você pode elevar o seu aplicativo na nuvem em uma abordagem em fases, que facilita mover o seu aplicativo para o Azure.

## <a name="what-are-trusts"></a>O que são relações de confiança?

As organizações que têm mais de um domínio geralmente precisam que os usuários acessem recursos compartilhados em um domínio diferente. O acesso a esses recursos compartilhados requer que os usuários em um domínio sejam autenticados para outro domínio. Para fornecer essas funcionalidades de autenticação e autorização entre clientes e servidores em domínios diferentes, precisa haver uma *relação de confiança* entre os dois domínios.

Com as relações de confiança do domínio, os mecanismos de autenticação de cada domínio confiam nas autenticações provenientes do outro domínio. As relações de confiança ajudam a fornecer acesso controlado a recursos compartilhados em um domínio de recursos (o domínio *confiante*), verificando se as solicitações de autenticação recebidas são provenientes de uma autoridade confiável (o domínio *confiável*). As relações de confiança atuam como pontes que permitem que somente solicitações de autenticação validadas transitem entre domínios.

A maneira como uma relação de confiança passa solicitações de autenticação depende de como ela está configurada. As relações de confiança podem ser configuradas de uma das seguintes maneiras:

* **Unidirecional** – Fornece acesso do domínio confiável aos recursos no domínio confiante.
* **Bidirecional** – Fornece acesso de cada domínio aos recursos no outro domínio.

As relações de confiança também são configuradas para lidar com relações de confiança adicionais de uma das seguintes maneiras:

* **Não transitiva** – A relação de confiança existe somente entre os dois domínios de parceiro de confiança.
* **Transitiva** – A relação de confiança se estende automaticamente para todos os outros domínios nos quais qualquer um dos parceiros confia.

Em alguns casos, as relações de confiança são automaticamente estabelecidas quando os domínios são criados. Em outras ocasiões, você precisa escolher um tipo de relação de confiança e estabelecer explicitamente as relações apropriadas. Os tipos específicos de relações de confiança usados e a estrutura dessas relações de confiança dependem de como o serviço de diretório Active Directory é organizado e se diferentes versões do Windows coexistem na rede.

## <a name="trusts-between-two-forests"></a>Relações de confiança entre duas florestas

Você pode estender as relações de confiança de domínio em uma única floresta para outra floresta criando manualmente uma relação de confiança da floresta unidirecional ou bidirecional. Uma relação de confiança da floresta é transitiva e existe apenas entre dois domínios raiz de floresta.

* Uma relação de confiança da floresta unidirecional permite que todos os usuários em uma floresta confiem em todos os domínios da outra floresta.
* Uma relação de confiança da floresta bidirecional forma uma relação de confiança transitiva entre cada domínio em ambas as florestas.

A transitividade das relações de confiança da floresta é limitada aos dois parceiros de floresta. A relação de confiança da floresta não se estende a florestas adicionais que qualquer um dos parceiros confia.

![Diagrama da relação de confiança de floresta do Azure AD DS com o AD DS local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Você pode criar diferentes configurações de relação de confiança do domínio e da floresta dependendo da estrutura do Active Directory da organização. O Azure AD DS dá suporte apenas a uma relação de confiança da floresta unidirecional. Nessa configuração, os recursos no Azure AD DS podem confiar em todos os domínios em uma floresta local.

## <a name="supporting-technology-for-trusts"></a>Tecnologia de suporte para relações de confiança

As relações de confiança usam vários serviços e recursos, como o DNS, para localizar controladores de domínio em florestas parceiras. As relações de confiança também dependem de protocolos de autenticação NTLM e Kerberos e de autorização baseada em Windows e mecanismos de controle de acesso para ajudar a fornecer uma infraestrutura de comunicações segura entre as florestas e os domínios do Active Directory. Os seguintes serviços e recursos ajudam a dar suporte a relações de confiança bem-sucedidas.

### <a name="dns"></a>DNS

O AD DS precisa de DNS para nomenclatura e localização do DC (controlador de domínios). O suporte a seguir do DNS é fornecido para que o AD DS funcione com êxito:

* Um serviço de resolução de nomes que permite que os hosts de rede e os serviços localizem os DCs.
* Uma estrutura de nomenclatura que permite que uma empresa reflita a estrutura organizacional dela nos nomes dos domínios de serviço de diretório.

Um namespace de domínio DNS geralmente é implantado de modo que ele espelhe o namespace de domínio do AD DS. Se houver um namespace de DNS existente antes da implantação do AD DS, o namespace de DNS normalmente será particionado para o Active Directory, e uma delegação e um subdomínio de DNS da raiz da floresta do Active Directory serão criados. Em seguida, nomes de domínio DNS adicionais serão adicionados para cada domínio filho do Active Directory.

O DNS também é usado para dar suporte à localização dos DCs do Active Directory. As zonas DNS são populadas com registros de recursos DNS que permitem que os serviços e hosts de rede localizem DCs do Active Directory.

### <a name="applications-and-net-logon"></a>Aplicativos e Logon de Rede

Os aplicativos e o serviço Logon de Rede são componentes do modelo de canal de segurança distribuída do Windows. Os aplicativos integrados ao Windows Server e ao Active Directory usam protocolos de autenticação para se comunicarem com o serviço Logon de Rede para que um caminho seguro no qual a autenticação ocorrerá possa ser estabelecido.

### <a name="authentication-protocols"></a>Protocolos de autenticação

Os DCs do Active Directory autenticam usuários e aplicativos usando um dos seguintes protocolos:

* **Protocolo de autenticação Kerberos versão 5**
    * O protocolo Kerberos versão 5 é o protocolo de autenticação padrão usado por computadores locais que executam o Windows e dão suporte a sistemas operacionais de terceiros. Esse protocolo é especificado no RFC 1510 e é totalmente integrado com Active Directory, protocolo SMB, HTTP e RPC (chamada de procedimento remoto), bem como com os aplicativos cliente e de servidor que usam esses protocolos.
    * Quando o protocolo Kerberos é usado, o servidor não precisa entrar em contato com o DC. Em vez disso, o cliente recebe um tíquete de um servidor solicitando-o de um DC no domínio da conta do servidor. Em seguida, o servidor valida o tíquete sem consultar nenhuma outra autoridade.
    * Se um computador envolvido em uma transação não der suporte ao protocolo Kerberos versão 5, o protocolo NTLM será usado.

* **Protocolo de autenticação NTLM**
    * O protocolo NTLM é um protocolo de autenticação de rede clássico usado por sistemas operacionais mais antigos. Por motivos de compatibilidade, ele é usado por domínios do Active Directory para processar solicitações de autenticação de rede provenientes de aplicativos projetados para clientes e servidores baseados em Windows anteriores e para sistemas operacionais de terceiros.
    * Quando o protocolo NTLM é usado entre um cliente e um servidor, o servidor precisa contatar um serviço de autenticação de domínio em um DC para verificar as credenciais do cliente. O servidor autentica o cliente encaminhando as credenciais do cliente para um DC no domínio da conta do cliente.
    * Quando dois domínios ou florestas do Active Directory são conectados por uma relação de confiança, as solicitações de autenticação feitas usando esses protocolos podem ser roteadas para fornecer acesso aos recursos em ambas as florestas.

## <a name="authorization-and-access-control"></a>Autenticação e controle de acessos

As tecnologias de autorização e de relação de confiança funcionam em conjunto para fornecer uma infraestrutura de comunicações protegida entre domínios ou florestas do Active Directory. A autorização determina qual é o nível de acesso do usuário aos recursos em um domínio. As relações de confiança facilitam a autorização entre domínios de usuários fornecendo um caminho para autenticar usuários em outros domínios para que as solicitações deles para recursos compartilhados nesses domínios possam ser autorizadas.

Quando uma solicitação de autenticação feita em um domínio confiante é validada pelo domínio confiável, ela é passada para o recurso de destino. Em seguida, o recurso de destino determina se deve autorizar a solicitação específica feita pelo usuário, serviço ou computador no domínio confiável com base na configuração de controle de acesso.

As relações de confiança fornecem esse mecanismo para validar solicitações de autenticação que são passadas para um domínio confiante. Os mecanismos de controle de acesso no computador de recursos determinam o nível final de acesso permitido ao solicitante no domínio confiável.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre relações de confiança, consulte [Como as relações de confiança da floresta funcionam no Azure AD DS?][concepts-trust]

Para começar a criar um domínio gerenciado do Azure AD DS com uma floresta de recursos, consulte [Criar e configurar um domínio gerenciado do Azure AD DS][tutorial-create-advanced]. Em seguida, você pode [Criar uma relação de confiança da floresta de saída para um domínio local (versão prévia)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
