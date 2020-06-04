---
title: Planejar uma implantação de acesso condicional no Azure Active Directory
description: Saiba como criar políticas de acesso condicional e implantar efetivamente em sua organização.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d4ae1c9926c7ea1d18bf5c87fbed837edc2a5d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641441"
---
# <a name="plan--a-conditional-access-deployment"></a>Planejar uma implantação de acesso condicional

O planejamento da implantação de acesso condicional é essencial para garantir a estratégia de acesso obrigatória da sua organização para aplicativos e recursos.

Em um mundo em que o dispositivo móvel e a nuvem vêm em primeiro lugar, os usuários podem acessar os recursos da organização de qualquer lugar, usando uma grande variedade de dispositivos e aplicativos. Por causa disso, concentrar-se apenas em quem pode acessar um recurso não é mais suficiente. Você também precisa considerar onde o usuário está, o dispositivo que está sendo usado, o recurso que está sendo acessado e muito mais. 

O Acesso Condicional (CA) do Azure Active Directory (Azure AD) analisa sinais como usuário, dispositivo e local para automatizar decisões e impor políticas de acesso organizacional para o recurso. Você pode usar políticas de CA para aplicar controles de acesso como autenticação multifator (MFA). As políticas de CA permitem que você solicite aos usuários a MFA quando necessário para a segurança e fique fora do caminho do usuário quando não for necessário.

![Visão geral do Acesso Condicional](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

A Microsoft fornece políticas condicionais padrão chamadas [padrões de segurança](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) que garantem um nível básico de segurança. No entanto, sua organização pode precisar de mais flexibilidade do que os padrões de segurança oferecem. Você pode usar o acesso condicional para personalizar os padrões de segurança com mais granularidade e configurar novas políticas que atendam às suas necessidades.

## <a name="learn"></a>Saiba mais

Antes de começar, verifique se você entendeu como funciona o [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) e quando deve usá-lo.

### <a name="benefits"></a>Benefícios

Os benefícios da implantação do acesso condicional são:

* Aumentar a produtividade. Interrompa apenas os usuários com uma condição de entrada como a MFA quando um ou mais sinais recomendarem. As políticas de CA permitem controlar quando os usuários são solicitados a MFA, quando o acesso é bloqueado e quando eles devem usar um dispositivo confiável.

* gerenciar riscos. Automatizar a avaliação de riscos com condições de política significa que as entradas arriscadas são identificadas e corrigidas ou bloqueadas. O acoplamento do acesso condicional com [Proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/overview), que detecta anomalias e eventos suspeitos, permite que você direcione quando o acesso aos recursos é bloqueado ou restringido. 

* Abordar a governança e a conformidade. O acesso condicional permite que você audite o acesso a aplicativos, apresente os termos de uso para o consentimento e restrinja o acesso com base nas políticas de conformidade.

* Gerencie o custo. Mover políticas de acesso para o Azure AD reduz a dependência de soluções personalizadas ou locais para acesso condicional e seus custos de infraestrutura.

### <a name="license-requirements"></a>Requisitos de licença

Consulte [Requisitos de licença para usar o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

Se forem necessários recursos adicionais, talvez você também precise obter licenças relacionadas. Para obter mais informações, consulte [Preço do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Pré-requisitos

* Um locatário de trabalho do Azure AD com Azure AD Premium ou uma licença de avaliação habilitada. Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma conta com privilégios de administrador global para acesso condicional.

* Um usuário que não seja administrador com uma senha que você conheça, como testuser. Se você precisar criar um usuário, confira [Início rápido: Adicionar novos usuários ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

* Um grupo do qual o usuário não administrador seja membro. Se você precisar criar um grupo, confira como [Criar um grupo e adicionar membros no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal).

### <a name="training-resources"></a>Treinamento e recursos

Os recursos a seguir podem ser úteis à medida que você aprende sobre o acesso condicional:


**Vídeos**
* [O que é Acesso Condicional?](https://youtu.be/ffMAw2IVO7A)
* [Como implantar acesso condicional?](https://youtu.be/c_izIRNJNuk)
* [Como distribuir políticas de CA para usuários finais?](https://youtu.be/0_Fze7Zpyvc)
* [Acesso condicional com controles de dispositivo](https://youtu.be/NcONUf-jeS4)
* [Acesso condicional com Azure MFA](https://youtu.be/Tbc-SU97G-w)
* [Acesso condicional no Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)
* [Acesso condicional com base em dispositivo](https://in.video.search.yahoo.com/search/video;_ylt=AwrPiBX0yHRcZiMAhFa7HAx.;_ylu=X3oDMTB0N2poMXRwBGNvbG8Dc2czBHBvcwMxBHZ0aWQDBHNlYwNwaXZz?p=conditional+access+videos+microsoft&fr2=piv-web&fr=mcafee)

**Cursos online gratuitos na Pluralsight**
* [Projete o gerenciamento de identidades no Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Autenticação de design para Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Autorização de design para Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

**perguntas frequentes**

[Perguntas frequentes sobre acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/faqs)
## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.
### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos
Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [certifique-se de que você esteja envolvendo as partes interessadas certas](https://aka.ms/deploymentplans) e que as funções do projeto sejam claras.

### <a name="plan-communications"></a>Planejar comunicações
A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique de forma proativa a seus usuários como e quando sua experiência será alterada e como obter suporte em caso de problemas.

### <a name="plan-a-pilot"></a>Planejar um piloto
Quando novas políticas estiverem prontas para o seu ambiente, implante-as em fases no ambiente de produção. Primeiro, aplique uma política a um pequeno conjunto de usuários em um ambiente de teste e verifique se a política se comporta conforme o esperado. Consulte as [Práticas recomendadas para obter um piloto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

> [!NOTE]
> Para distribuir novas políticas não específicas para administradores, exclua todos os administradores. Isso garante que os administradores ainda possam acessar a política e fazer alterações ou revogá-la se houver um impacto significativo. Sempre valide a política com grupos de usuários menores antes de aplicar a todos os usuários.

## <a name="understand-ca-policy-components"></a>Entender os componentes da política de CA

As políticas de CA são instruções se-então: Se uma condição atribuída for atendida, aplique esses controles de acesso. 

![Visão geral do Acesso Condicional](media/plan-conditional-access/10.png)

Ao configurar políticas de CA, as condições são chamadas *atribuições*. As políticas de CA permitem que você imponha controles de acesso aos aplicativos da sua organização de acordo com determinadas condições.

![atribuições e controles de acesso ](media/plan-conditional-access/ca-policy-access.png)


Para obter mais informações, consulte [Criando política de CA](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies).

As [atribuições](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) definem o

* [os usuários e grupos](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-users-groups) a serem afetados pela política

* [aplicativos de nuvem ou ações](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-cloud-apps) aos quais a política será aplicada 

* [as condições](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions) sob as quais a política será aplicada. 
<p>

![tela de criação de política](media/plan-conditional-access/create-policy.png)

As configurações de [controles de acesso](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) determinam como impor uma política:

* [Conceder ou bloquear](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-grant) acesso a aplicativos de nuvem.

* Os [controles de sessão](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) permitem a limitação da experiência dentro de um aplicativo na nuvem.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Faça as perguntas certas para criar suas políticas

As políticas respondem perguntas sobre quem deve acessar seus recursos, quais recursos eles devem acessar e em quais condições. As políticas podem ser criadas para conceder acesso ou para bloquear o acesso. Lembre-se de fazer as perguntas certas sobre o que a sua política está tentando atingir. 

Documente as respostas para as perguntas de cada política antes de compilá-las. 

#### <a name="common-questions-about-assignments"></a>Perguntas comuns sobre Atribuições

[Usuários e grupos](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-users-groups)

* Quais usuários e grupos serão incluídos na política ou excluídos dela?

* Essa política inclui todos os usuários, grupo específico de usuários, funções de diretório ou usuários externos?

[Aplicativos na nuvem ou ações](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-cloud-apps)

* A qual(is) aplicativo(s) a política será aplicada?

* Quais ações do usuário estarão sujeitas a esta política?

[Condições](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions)

* Quais plataforma de dispositivo serão incluídas na política ou excluídas dela?

* Quais são os locais confiáveis da organização?

* Quais locais serão incluídos à política ou excluídos dela?

* Quais tipos de aplicativo cliente (navegador, dispositivos móveis, clientes de desktop, aplicativos com métodos de autenticação herdados) serão incluídos ou excluídos da política?

* Você tem políticas que seriam a exclusão de dispositivos ingressados no Azure AD ou dispositivos ingressados no Azure AD híbridos de políticas? 

* Se estiver usando a [Proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/overview), você deseja incorporar a proteção de risco de entrada?

#### <a name="common-questions-about-access-controls"></a>Perguntas comuns sobre controles de acesso

[Conceder ou Bloquear ](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-grant) 

Deseja conceder acesso aos recursos exigindo um ou mais dos seguintes itens?

* Exigir MFA

* Exigir que o dispositivo seja marcado como em conformidade

* Exigir um dispositivo ingressado no Azure AD híbrido

* Exigir um aplicativo cliente aprovado

* Requer política de proteção do aplicativo

[Controle de sessão](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session)

Deseja impor qualquer um dos seguintes controles de acesso em aplicativos de nuvem?

* Usar permissões de aplicativo imposto

* Usar o Controle de Aplicativos de Acesso Condicional

* Aplicar a frequência de entrada

* Usar sessão do navegador persistente

### <a name="access-token-issuance"></a>Emissão de token de acesso

É importante entender como os tokens de acesso são emitidos. 

![Acessar diagrama de token de emissão](media/plan-conditional-access/CA-policy-token-issuance.png)

**Observe que, se nenhuma atribuição for necessária e nenhuma política de CA estiver em vigor, o comportamento padrão é emitir um token de acesso**. 

Por exemplo, considere uma política em que:

Se o usuário estiver no grupo 1, force a MFA para acessar o aplicativo 1.

Se um usuário que não está no grupo 1 tentar acessar o aplicativo, nenhuma condição "If" será atendida e um token será emitido. Para excluir usuários fora do grupo 1, é necessário ter uma política separada para bloquear todos os outros usuários.

## <a name="follow-best-practices"></a>Seguir as práticas recomendadas

A estrutura de acesso condicional fornece uma excelente flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que é necessário examinar cuidadosamente cada política de configuração, antes de liberá-la, para evitar resultados indesejáveis.

### <a name="apply-ca-policies-to-every-app"></a>Aplicar políticas de CA a todos os aplicativos

Tokens de acesso são emitidos por padrão se uma condição de política de CA não disparar um controle de acesso. Verifique se cada aplicativo tem pelo menos uma política de acesso condicional aplicada

> [!IMPORTANT]
> Tenha muito cuidado ao usar o bloco e todos os aplicativos em uma única política. Isso pode bloquear os administradores fora do portal de administração do Azure, e as exclusões não poderão ser configuradas para pontos de extremidade importantes, como Microsoft Graph.

### <a name="minimize-the-number-of-ca-policies"></a>Minimizar o número de políticas de CA

A criação de uma política para cada aplicativo não é eficiente e leva a uma administração difícil. O acesso condicional só aplicará as primeiras 195 políticas por usuário. Recomendamos que você analise seus aplicativos e agrupe-os em aplicativos que têm os mesmos requisitos de recursos para os mesmos usuários. Por exemplo, se todos os aplicativos do Office 365 ou todos os aplicativos de RH tiverem os mesmos requisitos para os mesmos usuários, crie uma única política e inclua todos os aplicativos aos quais ele se aplica. 

### <a name="set-up-emergency-access-accounts"></a>Configurar contas de acesso de emergência

Se você configurar uma política incorretamente, ela poderá bloquear as organizações do portal do Azure. Você pode reduzir o impacto da falta acidental de acesso administrativo ao armazenar duas ou mais [contas de acesso de emergência](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) em sua organização.

* Crie uma conta de usuário dedicada à administração de política e excluída de todas as suas políticas.

* Cenário de interrupção para ambientes híbridos:

  * Crie um grupo de segurança local e sincronize-o com o Azure AD. O grupo de segurança deve conter sua conta de administração de política dedicada. 

   * ISENTAR este grupo de segurança de todas as políticas de CA.

   * Quando ocorrer uma interrupção de serviço, adicione os outros administradores ao grupo local conforme apropriado e force uma sincronização. Isso ativa sua isenção às políticas de CA.

### <a name="set-up-report-only-mode"></a>Configurar o modo somente relatório

Pode ser difícil prever o número e os nomes dos usuários afetados por iniciativas de implantação comuns, como:

* bloquear a autenticação herdada
* requer MFA
* implantar políticas de risco de entrada

O [modo somente de relatório ](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) permite que os administradores avaliem o impacto das políticas de CA antes de habilitá-las em seu ambiente.

Saiba como [configurar o modo somente de relatório em uma política de CA](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-report-only).

### <a name="plan-for-disruption"></a>Planejar a interrupção

Se você confia em um controle de acesso único, como MFA (autenticação multifator) ou um único local de rede, para proteger seus sistemas de TI, você fica suscetível a falhas de acesso se esse controle de acesso único ficar indisponível ou for configurado incorretamente. Para reduzir o risco de bloqueio durante interrupções imprevistas, [planeje estratégias](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) a serem adotadas para sua organização.

### <a name="set-naming-standards-for-your-policies"></a>Definir padrões de nomenclatura para suas políticas

O padrão de nomenclatura ajuda você a localizar as políticas e a entender a finalidade delas sem precisar abri-las no portal de administração do Azure. Recomendamos que você denomine sua política com:

* Um número sequencial

* O(s) aplicativo(s) em nuvem ao(s) qual(is) ela se aplica

* A resposta

* A quem ela se aplica

* Quando ela se aplica (se aplicável)

![Padrão de nomenclatura](media/plan-conditional-access/11.png)

**Exemplo**; Uma política para exigir MFA para usuários de marketing que acessam o aplicativo Dynamics CRP de redes externas pode ser:

![Padrão de nomenclatura](media/plan-conditional-access/naming-example.png)

Um nome descritivo ajuda você a manter uma visão geral da sua implementação de acesso condicional. O número de sequência será útil se você precisar fazer referência a uma política em uma conversa. Por exemplo, ao falar com um administrador ao telefone, você pode pedir a ele que abra a política CA01 para resolver um problema.

#### <a name="naming-standards-for-emergency-access-controls"></a>Padrões de nomenclatura para controles de acesso de emergência

Além das suas políticas ativas, você também deve implementar políticas desabilitadas que atuem como [controles secundários de acesso resilientes em cenários de interrupção/emergência](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls). O padrão de nomenclatura para as políticas de contingência deve incluir:
* HABILITAR EM EMERGÊNCIA no início para destacar o nome entre as outras políticas.

* O nome da interrupção a qual ele deve ser aplicado.

* Um número de sequência de ordenação para ajudar o administrador a saber em qual ordem as políticas devem ser habilitadas.

**Exemplo**

O nome a seguir indica que essa política é a primeira de quatro políticas para habilitar se houver uma interrupção de MFA:

EM01 - HABILITAR EM EMERGÊNCIA: Interrupção do MFA [1/4] - Exchange SharePoint: Requer um ingresso ao Azure Active Directory híbrido para usuários VIP.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Excluir os países dos quais você nunca espera uma entrada.

O Azure Active Directory permite criar [locais denominados](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition). Crie um local denominado que inclua todos os países dos quais você nunca esperará que uma entrada ocorra. Em seguida, crie uma política para todos os aplicativos que bloqueiam a entrada desse local denominado. **Lembre-se de isentar seus administradores dessa política**.

### <a name="plan-your-policy-deployment"></a>Planeje a implantação de política

Quando novas políticas estiverem prontas para seu ambiente, certifique-se de examinar cada política antes de liberá-la, para evitar resultados indesejáveis. Consulte a documentação a seguir para entender informações importantes sobre como as políticas são aplicadas e como evitar problemas

* [O que você deve saber](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)

* [O que você deve evitar](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)

## <a name="common-policies"></a>Políticas comuns

Ao planejar a solução de política de acesso condicional, avalie se é preciso criar políticas que atinjam os resultados a seguir.

### <a name="require-mfa"></a>Exigir MFA

Os casos de uso comuns para exigir acesso MFA são:

* [Por administradores](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-admin-mfa)

* [Para aplicativos específicos](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa)

* [Para todos os usuários](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa)

* [De locais de rede em que você não confia](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)

* [Para Gerenciamento do Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-azure-management)

### <a name="respond-to-potentially-compromised-accounts"></a>Responder a contas potencialmente comprometidas

Com políticas de acesso condicional, você pode implementar respostas automatizadas em credenciais de identidades potencialmente comprometidas. A probabilidade de que uma conta tenha sido comprometida é expressa na forma de níveis de risco. Há dois níveis de risco calculados pela proteção de identidade: risco de credenciais e risco de usuário. As três políticas padrão a seguir podem ser habilitadas.

* [Exigir que todos os usuários se registrem para MFA](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

* [Exigir uma alteração de senha para usuários que são de alto risco](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

* [Exigir MFA para usuários com risco de entrada médio ou alto](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

### <a name="require-managed-devices"></a>Requer dispositivos gerenciados

A proliferação de dispositivos com suporte para acessar os recursos de nuvem ajuda a melhorar a produtividade dos usuários. Não convém certos recursos em seu ambiente para ser acessado por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, [exija que os usuários possam acessá-los somente usando um dispositivo gerenciado](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices).

### <a name="require-approved-client-apps"></a>Requer aplicativos de cliente aprovados

Os funcionários usam dispositivos móveis para tarefas pessoais e corporativas. Para cenários de BYOD, você deve decidir se deseja gerenciar todo o dispositivo ou apenas os dados nele. Se estiver gerenciando apenas dados e acesso, você poderá [exigir aplicativos de nuvem aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) que possam proteger seus dados corporativos. por exemplo, você pode exigir que o email seja acessado somente por meio do Outlook Mobile e não por meio de um programa de email genérico.

### <a name="block-access"></a>Acesso bloqueado

A opção de [bloquear todo acesso](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-block-access) é eficiente. Ele pode ser usado, por exemplo, quando você estiver migrando um aplicativo para o Azure AD, mas não estiver pronto para que qualquer pessoa se conecte a ele ainda. Acesso bloqueado: 

* Supera todas as outras atribuições de um usuário

* Tem a capacidade de impedir que a organização inteira entre no seu locatário

> [!IMPORTANT]
> Se você criar uma política para bloquear o acesso para todos os usuários, certifique-se de excluir as contas de acesso de emergência e considere a exclusão de todos os administradores da política.

Outros cenários comuns em que você pode bloquear o acesso para seus usuários são:

* [Bloquear determinados locais de rede](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location) para acessar seus aplicativos de nuvem. Você pode usar essa política para bloquear determinados países de onde você sabe que o tráfego não deveria vir.

* O Azure AD é compatível com autenticação herdada. No entanto, a autenticação herdada não é compatível com MFA e muitos ambientes exigem que ele resolva a segurança da identidade. Nesse caso, é possível [impedir que aplicativos que usam autenticação herdada](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication) acessem os recursos do locatário.

## <a name="build-and-test-policies"></a>Compilar e testar políticas

Em cada estágio de sua implantação, certifique-se de que os resultados sejam os esperados. 

Quando novas políticas estiverem prontas, implante-as em fases no ambiente de produção:

* Comunique essa mudança interna aos usuários finais.

* Comece com um pequeno conjunto de usuários e verifique se a política se comporta conforme esperado.

* Ao expandir uma política para incluir mais usuários, continue a excluir todos os administradores. A exclusão de administradores garante que alguém ainda tenha acesso a uma política se for necessária uma alteração.

* Aplique uma política a todos os usuários somente depois que ele for totalmente testado. Verifique se você tem pelo menos uma conta de administrador à qual uma política não se aplica.

### <a name="create-test-users"></a>Criar usuários de teste

Crie um conjunto de usuários de teste que representem os usuários em seu ambiente de produção. A criação de usuários de teste permite verificar se as políticas funcionam conforme esperado antes de afetar os usuários reais e potencialmente interromper o acesso deles aos aplicativos e recursos.

Algumas organizações têm locatários de teste para essa finalidade. No entanto, pode ser difícil recriar todas as condições e os aplicativos em um locatário de teste para testar totalmente o resultado de uma política.

### <a name="create-a-test-plan"></a>Criar um plano de deste

O plano de teste é importante para que se tenha uma comparação entre os resultados esperados e os resultados reais. Você sempre deve ter uma expectativa antes de testar algo. A tabela a seguir descreve exemplos de casos de teste. Ajuste os cenários e os resultados esperados com base na configuração de suas políticas de autoridade de certificação.

| Política| Cenário| Resultado esperado |
| - | - | - |
| [Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)| Usuário autorizado entra no Aplicativo quando está no trabalho/em um local confiável| O usuário não é solicitado a usar a MFA |
| [Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)| Usuário autorizado entra no Aplicativo quando não está no trabalho/em um local confiável| É solicitado que o usuário use a MFA para se conectar com êxito |
| [Exigir MFA (para administradores)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)| Administrador global entra no Aplicativo| É solicitado que o administrador use MFA |
| [Entradas de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)| O usuário entra no Aplicativo usando um [navegador Tor](https://microsoft.sharepoint.com/azure/active-directory/active-directory-identityprotection-playbook)| É solicitado que o administrador use MFA |
| [Gerenciamento de dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)| O usuário autorizado tenta se conectar usando um dispositivo autorizado| Acesso concedido |
| [Gerenciamento de dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)| O usuário autorizado tenta se conectar usando um dispositivo não autorizado| Acesso bloqueado |
| [Alteração de senha para usuários arriscados](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)| O usuário autorizado tenta se conectar com credenciais comprometidas (conexão de alto risco)| O usuário é solicitado a alterar a senha ou o acesso é bloqueado com base na sua política |


 

### <a name="configure-the-test-policy"></a>Configure a política de testes

No [portal do Azure](https://portal.azure.com/), você configura as políticas de autoridade de certificação em Azure Active Directory > Segurança > Acesso Condicional.

Se você quiser saber mais sobre como criar políticas de CA, consulte este exemplo: [Política de CA para solicitar MFA quando um usuário entrar no portal do Azure](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json). Este início rápido ajuda você a:

* Familiarizar-se com a interface do usuário

* Obter uma primeira impressão de como funciona o acesso condicional

### <a name="enable-the-policy-in-report-only-mode"></a>Habilitar a política no modo somente de relatório

Para avaliar o impacto de sua política, comece habilitando a política no modo [somente de relatório](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only). As políticas somente de relatório são avaliadas durante a entrada, mas a concessão de controles e controles de sessão não são impostas. Depois de salvar a política no modo somente de relatório, você poderá ver o impacto em entradas em tempo real nos logs de entrada. Nos logs de entrada, selecione um evento e navegue até a guia somente de relatório para ver o resultado de cada política somente de relatório.


![modo somente relatório ](media/plan-conditional-access/report-only-mode.png)

Ao selecionar a política, você também pode ver como as atribuições e os controles de acesso da política foram avaliados usando a tela detalhes da política. Para que uma política seja aplicada a uma entrada, cada uma das atribuições configuradas deve ser satisfeita. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Entender o impacto de suas políticas usando a pasta de trabalho de informações e relatórios

Você pode exibir o impacto agregado de suas políticas de acesso condicional na pasta de trabalho de informações e relatórios. Para acessar a pasta de trabalho, você precisa de uma assinatura Azure Monitor e será necessário [transmitir seus logs de entrada para um espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Simular entradas usando a ferramenta e-se

Outra maneira de validar sua política de acesso condicional é usando a [ferramenta e-se](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access-what-if), que simula quais políticas se aplicam a um usuário que entra em circunstâncias hipotéticas. Selecione os atributos de entrada que você deseja testar (como usuário, aplicativo, plataforma de dispositivo e local) e veja quais políticas se aplicam.

> [!NOTE] 
> Embora uma execução simulada dê uma impressão do impacto que uma política de acesso condicional tem, ela não substitui uma execução de teste real.

### <a name="test-your-policy"></a>Testar sua política

Realize cada teste em seu plano de teste com usuários de teste.

**Certifique-se de testar os critérios de exclusão de uma política.** . Por exemplo, você pode excluir um usuário ou grupo de uma política que exige MFA. Teste se os usuários excluídos são solicitados a usar MFA, pois a combinação de outras políticas pode exigir MFA desses usuários.

### <a name="roll-back-policies"></a>Reversão de políticas

Caso você precise reverter as suas políticas recentemente implementadas, use uma ou várias das opções a seguir:

* **Desabilitar a política.** Desabilitar uma política garante que ela não seja aplicada quando um usuário tenta se conectar. Você sempre pode voltar e habilitar a política quando quiser usá-la.

![imagem habilitar política](media/plan-conditional-access/enable-policy.png)

* **Excluir um usuário ou grupo de uma política.** Se um usuário não puder acessar o aplicativo, opte por excluir o usuário da política.

![excluir usuários e grupos](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Essa opção deve ser usada com moderação, somente em situações em que o usuário é confiável. O usuário deve ser adicionado de volta à política ou ao grupo assim que possível.

* **Excluir a política.** Se a política não for mais necessária, [exclua-a](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

## <a name="manage-access-to-cloud-apps"></a>Gerenciar o acesso aos aplicativos de nuvem

Use as seguintes opções de gerenciamento para controlar e gerenciar suas políticas de autoridade de certificação:

![gerenciar-acesso](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Localizações nomeadas

A condição de localização de uma política de acesso condicional permite que você ligue as configurações de controle de acesso aos locais de rede dos usuários. Com [Localizações denominadas](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition), você pode criar agrupamentos lógicos de intervalos de endereços IP, países e regiões.

### <a name="custom-controls"></a>Controles personalizados

[Controles personalizados](https://docs.microsoft.com/azure/active-directory/conditional-access/controls) redirecionam os usuários para um serviço compatível para atender requisitos de autenticação fora do Azure Active Directory. Para satisfazer esse controle, o navegador do usuário é redirecionado para um serviço externo, realiza qualquer autenticação necessária e, em seguida, é redirecionado ao Azure Active Directory. O Azure Active Directory verifica a resposta e, se o usuário tiver sido autenticado ou validado com êxito, ele continua no fluxo de acesso condicional.

### <a name="terms-of-use"></a>Termos de uso

Antes de acessar determinados aplicativos de nuvem em seu ambiente, talvez você queira obter o consentimento dos usuários ao aceitar os Termos de uso (ToU). Siga este [Início rápido para criar os Termos de uso](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou).

### <a name="classic-policies"></a>Políticas clássicas

No [portal do Azure](https://portal.azure.com/), as políticas de autoridade de certificação estão em Azure Active Directory > Segurança > Acesso Condicional. Sua organização também pode ter políticas de autoridade de certificação mais antigas não criadas usando esta página. Essas políticas são conhecidas como políticas clássicas. Recomendamos que você [considere a migração dessas políticas clássicas no portal do Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices).

## <a name="troubleshoot-conditional-access"></a>Solucionar Problemas de Acesso Condicional

Quando um usuário estiver tendo um problema com uma política de autoridade de certificação, colete as informações a seguir para facilitar a solução de problemas.

* Nome de princípio de usuário

* Nome de exibição do usuário

* Nome do sistema operacional

* Carimbo de data/hora (aproximado é ok)

* Aplicativo de destino

* Tipo de aplicativo cliente (navegador vs cliente)

* ID de correlação (é exclusiva para a entrada)

Se o usuário recebeu uma mensagem com um link Mais detalhes, ele pode coletar a maioria dessas informações para você.

![Não é possível obter a mensagem de erro do aplicativo](media/plan-conditional-access/cant-get-to-app.png)

Após recolher as informações, consulte os recursos a seguir:

* [Problemas de conexão com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) – entenda os resultados de entrada inesperados relacionados ao acesso condicional usando mensagens de erro e o log de entradas do Azure AD.

* [Usando a ferramenta E-Se](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access-what-if) - Entenda por que uma política foi ou não foi aplicada a um usuário em uma circunstância específica ou se uma política seria aplicada em um estado conhecido.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre Autenticação Multifator](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

[Saiba mais sobre Proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

[Gerenciar políticas de CA com Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)
