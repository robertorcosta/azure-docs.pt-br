---
title: Conceitos de floresta de recursos para serviços de domínio Azure AD | Microsoft Docs
description: Saiba o que é uma floresta de recursos nos Serviços de Domínio do Azure Active Directory e como eles beneficiam sua organização em ambiente híbrido com opções limitadas de autenticação do usuário ou preocupações de segurança.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: a583e32cbc3d58d5dfc5616335b2f38ad20fac14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233604"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Conceitos e recursos de floresta de recursos para serviços de domínio do Diretório Ativo do Azure

O Azure Active Directory Domain Services (AD DS) oferece uma experiência de login para aplicativos legados, locais e de linha de negócios. Usuários, grupos e hashes de senhas de usuários locais e na nuvem são sincronizados com o domínio gerenciado pelo Azure AD DS. Esses hashes de senha sincronizados são o que dá aos usuários um único conjunto de credenciais que eles podem usar para os AD DS, Office 365 e Azure Active Directory.

Embora seguras e forneçam benefícios adicionais de segurança, algumas organizações não podem sincronizar essas senhas de usuário hashes com a Azure AD ou Azure AD DS. Os usuários de uma organização podem não saber sua senha porque eles só usam autenticação de cartão inteligente. Essas limitações impedem que algumas organizações usem o Azure AD DS para levantar e transferir aplicativos clássicos no local para o Azure.

Para atender a essas necessidades e restrições, você pode criar um domínio gerenciado pelo Azure AD DS que usa uma floresta de recursos. Este artigo conceitual explica o que são florestas e como eles confiam em outros recursos para fornecer um método de autenticação seguro. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

> [!IMPORTANT]
> As florestas de recursos Azure AD DS não suportam atualmente arquivos Azure HDInsight ou Azure. As florestas de usuários Padrão Azure AD DS suportam esses dois serviços adicionais.

## <a name="what-are-forests"></a>O que são florestas?

Uma *floresta* é um construto lógico usado pelo Active Directory Domain Services (AD DS) para agrupar um ou mais *domínios*. Os domínios então armazenam objetos para usuários ou grupos e fornecem serviços de autenticação.

No Azure AD DS, a floresta contém apenas um domínio. As florestas AD DS no local geralmente contêm muitos domínios. Em grandes organizações, especialmente após fusões e aquisições, você pode acabar com várias florestas locais que, em seguida, contêm vários domínios.

Por padrão, um domínio gerenciado pelo Azure AD DS é criado como uma floresta *de usuários.* Esse tipo de floresta sincroniza todos os objetos do Azure AD, incluindo qualquer conta de usuário criada em um ambiente do AD DS local. As contas de usuário podem autenticar diretamente contra o domínio gerenciado pelo Azure AD DS, como fazer login em uma VM com um domínio. Uma floresta de usuários funciona quando os hashes de senha podem ser sincronizados e os usuários não estão usando métodos exclusivos de login, como autenticação de cartão inteligente.

Em uma floresta de *recursos* Azure AD DS, os usuários autenticam sobre uma *confiança* florestal unidirecional a partir de seus AD DS no local. Com essa abordagem, os objetos do usuário e os hashes de senha não são sincronizados com o Azure AD DS. Os objetos e credenciais do usuário só existem no AD DS no local. Essa abordagem permite que as empresas hospedem recursos e plataformas de aplicativos no Azure que dependem da autenticação clássica, como LDAPS, Kerberos ou NTLM, mas quaisquer problemas ou preocupações de autenticação são removidos. As florestas de recursos Azure AD DS estão atualmente em pré-visualização.

As florestas de recursos também fornecem a capacidade de levantar e shiftseus aplicativos um componente por vez. Muitos aplicativos legados no local são multi-hierárquicos, muitas vezes usando um servidor web ou front-end e muitos componentes relacionados ao banco de dados. Esses níveis dificultam o levantamento e a mudança de toda a aplicação para a nuvem em um passo. Com florestas de recursos, você pode elevar sua aplicação para a nuvem em abordagem em fases, o que facilita a movimentação do seu aplicativo para o Azure.

## <a name="what-are-trusts"></a>O que são confianças?

Organizações que têm mais de um domínio muitas vezes precisam que os usuários acessem recursos compartilhados em um domínio diferente. O acesso a esses recursos compartilhados requer que os usuários em um domínio se autentiquem em outro domínio. Para fornecer esses recursos de autenticação e autorização entre clientes e servidores em diferentes domínios, deve haver uma *confiança* entre os dois domínios.

Com os trusts de domínio, os mecanismos de autenticação de cada domínio confiam nas autenticações provenientes do outro domínio. Os trusts ajudam a fornecer acesso controlado a recursos compartilhados em um domínio de recursos (o domínio *confiável)* verificando se as solicitações de autenticação recebidas vêm de uma autoridade confiável (o domínio *confiável).* Os trusts atuam como pontes que só permitem que pedidos de autenticação validados viajem entre domínios.

A forma como um trust passa solicitações de autenticação depende de como ela é configurada. As confianças podem ser configuradas de uma das seguintes maneiras:

* **Unidirecional** - fornece acesso do domínio confiável aos recursos no domínio confiável.
* **Bidirecional** - fornece acesso de cada domínio a recursos no outro domínio.

Os trusts também são configurados para lidar com relacionamentos adicionais de confiança de uma das seguintes maneiras:

* **Não transitivo** - A confiança existe apenas entre os dois domínios de parceiros de confiança.
* **Transitivo** - A confiança se estende automaticamente a quaisquer outros domínios em que qualquer um dos parceiros confia.

Em alguns casos, as relações de confiança são automaticamente estabelecidas quando os domínios são criados. Outras vezes, você deve escolher um tipo de confiança e estabelecer explicitamente as relações apropriadas. Os tipos específicos de confiança utilizados e a estrutura dessas relações de confiança dependem de como o serviço de diretório do Active Directory é organizado e se diferentes versões do Windows coexistem na rede.

## <a name="trusts-between-two-forests"></a>Confia entre duas florestas

Você pode estender as confianças de domínio dentro de uma única floresta para outra floresta, criando manualmente uma confiança florestal unidirecional ou bidirecional. Uma confiança florestal é uma confiança transitiva que existe apenas entre um domínio raiz florestal e um segundo domínio raiz florestal.

* Uma confiança florestal unidirecional permite que todos os usuários de uma floresta confiem em todos os domínios da outra floresta.
* Uma confiança florestal bidirecional forma uma relação de confiança transitória entre todos os domínios em ambas as florestas.

A transitoriedade dos fundos florestais limita-se aos dois parceiros florestais. O fundo florestal não se estende a florestas adicionais confiáveis por nenhum dos parceiros.

![Diagrama da confiança florestal do Azure AD DS para o AD DS no local](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Você pode criar diferentes configurações de domínio e confiança florestal, dependendo da estrutura do Active Directory da organização. O Azure AD DS só suporta uma confiança florestal unidirecional. Nesta configuração, os recursos no Azure AD DS podem confiar em todos os domínios em uma floresta local.

## <a name="supporting-technology-for-trusts"></a>Tecnologia de suporte para trusts

Os trusts usam vários serviços e recursos, como o DNS para localizar controladores de domínio em florestas parceiras. Os trusts também dependem dos protocolos de autenticação NTLM e Kerberos e dos mecanismos de controle de acesso e autorização baseados no Windows para ajudar a fornecer uma infra-estrutura de comunicações segura em domínios e florestas do Active Directory. Os seguintes serviços e recursos ajudam a apoiar relacionamentos de confiança bem-sucedidos.

### <a name="dns"></a>DNS

O AD DS precisa de DNS para localização e nomeação do controlador de domínio (DC). O seguinte suporte do DNS é fornecido para que o DS ad funcione com sucesso:

* Um serviço de resolução de nomes que permite que hosts e serviços de rede localizem DCs.
* Uma estrutura de nomeação que permite que uma empresa reflita sua estrutura organizacional nos nomes de seus domínios de serviço de diretório.

Um namespace de domínio DNS é geralmente implantado que espelha o espaço de nome de domínio AD DS. Se houver um namespace dns existente antes da implantação do DS, o namespace do DNS é normalmente particionado para o Active Directory, e um subdomínio dNS e delegação para a raiz da floresta do Diretório Ativo é criado. Nomes de domínio DNS adicionais são adicionados para cada domínio filho do Active Directory.

O DNS também é usado para suportar a localização de DCs do Active Directory. As regiões DNS são preenchidas com registros de recursos DNS que permitem que hosts e serviços de rede localizem DCs do Active Directory.

### <a name="applications-and-net-logon"></a>Aplicativos e Logon Net

Ambos os aplicativos e o serviço Net Logon são componentes do modelo de canal de segurança distribuído do Windows. Os aplicativos integrados ao Windows Server e ao Active Directory usam protocolos de autenticação para se comunicar com o serviço Net Logon para que um caminho seguro possa ser estabelecido sobre qual autenticação pode ocorrer.

### <a name="authentication-protocols"></a>Protocolos de autenticação

Os DCs do Active Directory autenticam usuários e aplicativos usando um dos seguintes protocolos:

* **Protocolo de autenticação da versão 5 do Kerberos**
    * O protocolo Kerberos versão 5 é o protocolo de autenticação padrão usado por computadores locais que executam o Windows e suportam sistemas operacionais de terceiros. Este protocolo é especificado no RFC 1510 e está totalmente integrado com o Active Directory, o Server Message Block (SMB), http e remote procedure call (RPC), bem como os aplicativos de cliente e servidor que usam esses protocolos.
    * Quando o protocolo Kerberos é usado, o servidor não precisa entrar em contato com o DC. Em vez disso, o cliente recebe um ticket para um servidor solicitando um de um DC no domínio da conta do servidor. Em seguida, o servidor valida o ticket sem consultar nenhuma outra autoridade.
    * Se qualquer computador envolvido em uma transação não suportar o protocolo Kerberos versão 5, o protocolo NTLM é usado.

* **Protocolo de autenticação NTLM**
    * O protocolo NTLM é um protocolo clássico de autenticação de rede usado por sistemas operacionais mais antigos. Por razões de compatibilidade, ele é usado pelos domínios do Active Directory para processar solicitações de autenticação de rede que vêm de aplicativos projetados para clientes e servidores baseados no Windows anteriores e sistemas operacionais de terceiros.
    * Quando o protocolo NTLM é usado entre um cliente e um servidor, o servidor deve entrar em contato com um serviço de autenticação de domínio em um DC para verificar as credenciais do cliente. O servidor autentica o cliente encaminhando as credenciais do cliente para um DC no domínio da conta do cliente.
    * Quando dois domínios ou florestas do Active Directory são conectados por um trust, as solicitações de autenticação feitas usando esses protocolos podem ser roteadas para fornecer acesso a recursos em ambas as florestas.

## <a name="authorization-and-access-control"></a>Autenticação e controle de acessos

As tecnologias de autorização e confiança trabalham juntas para fornecer uma infra-estrutura de comunicações segura em domínios ou florestas do Active Directory. A autorização determina o nível de acesso que um usuário tem aos recursos em um domínio. Os trusts facilitam a autorização entre domínios dos usuários, fornecendo um caminho para autenticar usuários em outros domínios para que suas solicitações de recursos compartilhados nesses domínios possam ser autorizadas.

Quando uma solicitação de autenticação feita em um domínio confiável é validada pelo domínio confiável, ela é transmitida para o recurso de destino. O recurso de destino então determina se autoriza a solicitação específica feita pelo usuário, serviço ou computador no domínio confiável com base em sua configuração de controle de acesso.

Os trusts fornecem esse mecanismo para validar solicitações de autenticação que são passadas para um domínio confiável. Mecanismos de controle de acesso no computador de recursos determinam o nível final de acesso concedido ao solicitante no domínio confiável.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre trusts, [veja como os fundos florestais funcionam no Azure AD DS?][concepts-trust]

Para começar com a criação de um domínio gerenciado pelo Azure AD DS com uma floresta de recursos, consulte [Criar e configurar um domínio gerenciado pelo Azure AD DS][tutorial-create-advanced]. Em seguida, você pode [criar uma confiança florestal de saída para um domínio local (visualização)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
