---
title: Configure o fluxo de trabalho de consentimento de administrador - Azure Active Directory | Microsoft Docs
description: Saiba como configurar uma maneira de os usuários finais solicitarem acesso a aplicativos que requerem consentimento do administrador.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430211"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Configure o fluxo de trabalho de consentimento de administrador (visualização)

Este artigo descreve como ativar o recurso de fluxo de trabalho de consentimento de admin (preview), que dá aos usuários finais uma maneira de solicitar acesso a aplicativos que requerem consentimento do admin.

Sem um fluxo de trabalho de consentimento de admin, um usuário em um inquilino onde o consentimento do usuário é desativado será bloqueado quando tentar acessar qualquer aplicativo que exija permissões para acessar dados organizacionais. O usuário vê uma mensagem de erro genérica que diz que não está autorizada a acessar o aplicativo e que deve pedir ajuda ao seu anúncio. Mas, muitas vezes, o usuário não sabe com quem entrar em contato, então ou desiste ou cria uma nova conta local no aplicativo. Mesmo quando um admin é notificado, nem sempre há um processo simplificado para ajudar o admin conceder acesso e notificar seus usuários.
 
O fluxo de trabalho de consentimento do conselho de trabalho dá aos admins uma maneira segura de conceder acesso a aplicativos que requerem aprovação administrativa. Quando um usuário tenta acessar um aplicativo, mas não consegue fornecer consentimento, ele pode enviar um pedido de aprovação administrativa. A solicitação é enviada por e-mail para os admins que foram designados como revisores. Um revisor toma medidas sobre a solicitação, e o usuário é notificado da ação.

Para aprovar solicitações, um revisor deve ser um administrador global, administrador de aplicativos em nuvem ou administrador de aplicativos. O revisor já deve ter uma dessas funções de admin atribuídas; simplesmente designá-los como um revisor não eleva seus privilégios.

## <a name="enable-the-admin-consent-workflow"></a>Habilite o fluxo de trabalho de consentimento de admin

Para habilitar o fluxo de trabalho de consentimento do admin e escolher os revisores:

1. Faça login no [portal Azure](https://portal.azure.com) como administrador global.
2. Clique em **Todos os serviços** na parte superior do menu de navegação à esquerda. A **Extensão do Active Directory do Azure** é aberta.
3. Na caixa de pesquisa do filtro, digite "**Azure Active Directory**" e selecione o item **Diretório Ativo do Azure.**
4. No menu de navegação, clique em **Aplicativos corporativos**. 
5. Em **Gerenciar,** selecione **Configurações do usuário**.
6. De acordo **com as solicitações de consentimento do Admin (Preview)**, definido **os usuários podem solicitar o consentimento do google a aplicativos aos seus não podem consentir com** **o Yes**.

   ![Configurar configurações do fluxo de trabalho de consentimento de administrador](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Defina as seguintes configurações:

   * **Selecione os usuários para revisar as solicitações de consentimento de admin**. Selecione revisores para esse fluxo de trabalho a partir de um conjunto de usuários que possuem as funções de administrador global, administrador de aplicativos em nuvem e administrador de aplicativos.
   * **Os usuários selecionados receberão notificações por e-mail para solicitações**. Habilite ou desative notificações de e-mail aos revisores quando uma solicitação for feita.  
   * **Os usuários selecionados receberão lembretes de expiração de solicitação**. Habilite ou desative notificações de e-mail de lembrete aos revisores quando uma solicitação estiver prestes a expirar.  
   * **O pedido de consentimento expira após (dias)**. Especifique quanto tempo as solicitações permanecem válidas.

7. Selecione **Salvar**. Pode levar até uma hora para que o recurso seja ativado.

> [!NOTE]
> Você pode adicionar ou remover revisores para este fluxo de trabalho modificando a lista **de revisores de solicitações de consentimento do Admin Select.** Observe que uma limitação atual deste recurso é que os revisores podem manter a capacidade de revisar solicitações que foram feitas enquanto foram designadas como revisoras.

## <a name="how-users-request-admin-consent"></a>Como os usuários solicitam consentimento do admin

Depois que o fluxo de trabalho de consentimento do admin for ativado, os usuários podem solicitar a aprovação do admin para um aplicativo ao que não estão autorizados a consentir. As etapas a seguir descrevem a experiência do usuário ao solicitar aprovação. 

1. O usuário tenta entrar no aplicativo.

2. A **mensagem de aprovação necessária** é exibida. O usuário digita uma justificativa para precisar de acesso ao aplicativo e, em seguida, seleciona **a aprovação de Solicitação**.

   ![Solicitação e justificativa do usuário de consentimento do admin](media/configure-admin-consent-workflow/end-user-justification.png)

3. Uma **mensagem enviada** confirma que a solicitação foi submetida ao admin. Se o usuário enviar várias solicitações, apenas a primeira solicitação será submetida ao admin.

   ![Solicitação e justificativa do usuário de consentimento do admin](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. O usuário recebe uma notificação por e-mail quando sua solicitação é aprovada, negada ou bloqueada. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Revise e tome medidas sobre pedidos de consentimento de admin

Revisar os pedidos de consentimento do admin e tomar medidas:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como um dos revisores registrados do fluxo de trabalho de consentimento do administrador.
2. Selecione **Todos os serviços** na parte superior do menu de navegação à esquerda. A **Extensão do Active Directory do Azure** é aberta.
3. Na caixa de pesquisa do filtro, digite "**Azure Active Directory**" e selecione o item Diretório Ativo do **Azure.**
4. No menu de navegação, clique em **Aplicativos corporativos**.
5. Em **Atividade,** selecione Solicitações de consentimento do **Admin (Preview)**.

   > [!NOTE]
   > Os revisores só verão pedidos de admin que foram criados depois que eles foram designados como revisores.

1. Selecione o aplicativo que está sendo solicitado.
2. Revisar detalhes sobre o pedido:  

   * Para ver quem está solicitando acesso e por quê, selecione a guia **Solicitado.**
   * Para ver quais permissões estão sendo solicitadas pelo aplicativo, selecione **As permissões de revisão e o consentimento**.

8. Avalie a solicitação e tome as medidas apropriadas:

   * **Aprove o pedido.** Para aprovar um pedido, conceda o consentimento do conselho de crédito ao pedido. Uma vez aprovada a solicitação, todos os solicitantes são notificados de que tiveram acesso.  
   * **Negar o pedido.** Para negar um pedido, você deve fornecer uma justificativa que será fornecida a todos os solicitantes. Uma vez que um pedido é negado, todos os solicitantes são notificados de que foram negados acesso ao aplicativo. Negar uma solicitação não impedirá que os usuários solicitem o consentimento do aplicativo novamente no futuro.  
   * **Bloqueie a solicitação.** Para bloquear uma solicitação, você deve fornecer uma justificativa que será fornecida a todos os solicitantes. Uma vez que uma solicitação é bloqueada, todos os solicitantes são notificados de que tiveram acesso negado ao aplicativo. O bloqueio de uma solicitação cria um objeto principal de serviço para o aplicativo em seu inquilino em um estado desativado. Os usuários não poderão solicitar o consentimento do admin para o aplicativo no futuro.
 
## <a name="email-notifications"></a>Notificações por email
 
Se configurado, todos os revisores receberão notificações por e-mail quando:

* Um novo pedido foi criado
* Um pedido expirou
* Um pedido está se aproximando da data de validade  
 
Os solicitantes receberão notificações por e-mail quando:

* Eles enviam um novo pedido de acesso
* Seu pedido expirou.
* Seu pedido foi negado ou bloqueado
* Seu pedido foi aprovado.
 
## <a name="audit-logs"></a>Logs de auditoria 
 
A tabela abaixo descreve os cenários e os valores de auditoria disponíveis para o fluxo de trabalho de consentimento do admin. 

> [!NOTE]
> O contexto de usuário do ator de auditoria está atualmente ausente em todos os cenários. Esta é uma limitação conhecida na versão de pré-visualização.


|Cenário  |Serviço de Auditoria  |Auditar categoria  |Atividade de Auditoria  |Ator de Auditoria  |Limitações do registro de auditoria  |
|---------|---------|---------|---------|---------|---------|
|Admin permitindo o fluxo de trabalho de solicitação de consentimento        |Revisões de acesso           |Gerenciamento de usuários           |Crie modelo de política de governança          |Contexto do aplicativo            |No momento, você não pode encontrar o contexto do usuário            |
|Admin desativando o fluxo de trabalho de solicitação de consentimento       |Revisões de acesso           |Gerenciamento de usuários           |Excluir modelo de política de governança          |Contexto do aplicativo            |No momento, você não pode encontrar o contexto do usuário           |
|Admin atualizando as configurações do fluxo de trabalho de consentimento        |Revisões de acesso           |Gerenciamento de usuários           |Atualizar o modelo de política de governança          |Contexto do aplicativo            |No momento, você não pode encontrar o contexto do usuário           |
|Usuário final criando um pedido de consentimento de admin para um aplicativo       |Revisões de acesso           |Política         |Criar solicitação           |Contexto do aplicativo            |No momento, você não pode encontrar o contexto do usuário           |
|Revisores aprovando um pedido de consentimento de admin       |Revisões de acesso           |Gerenciamento de usuários           |Aprovar todas as solicitações no fluxo de negócios          |Contexto do aplicativo            |Atualmente, você não pode encontrar o contexto do usuário ou o ID do aplicativo que foi concedido o consentimento do admin.           |
|Revisores negando um pedido de consentimento do admin       |Revisões de acesso           |Gerenciamento de usuários           |Aprovar todas as solicitações no fluxo de negócios          |Contexto do aplicativo            | Atualmente você não pode encontrar o contexto de usuário do ator que negou um pedido de consentimento de admin          |

## <a name="faq"></a>Perguntas frequentes 

**Eu liguei este fluxo de trabalho, mas ao testar a funcionalidade, por que não posso ver o novo prompt "Aprovação necessária" que me permite solicitar acesso?**

Depois de ligar o recurso, pode levar até 60 minutos para os usuários finais verem a atualização. Você pode verificar se a configuração fez efeito corretamente visualizando `https://graph.microsoft.com/beta/settings` o valor **EnableAdminConsentRequests** na API.

**Como revisor, por que não posso ver todos os pedidos pendentes?**

Os revisores só podem ver pedidos de admin que foram criados depois que foram designados como revisores. Então, se você foi adicionado recentemente como um revisor, você não verá nenhum pedido que foi criado antes de sua atribuição.

**Como revisor, por que vejo vários pedidos para o mesmo aplicativo?**
  
Se um desenvolvedor de aplicativos tiver configurado seu aplicativo para usar o consentimento estático e dinâmico para solicitar acesso aos dados do usuário final, você verá duas solicitações de consentimento de admin. Uma solicitação representa as permissões estáticas, e a outra representa as permissões dinâmicas.

**Como solicitante, posso verificar o status do meu pedido?**  

Não, por enquanto os solicitantes só podem receber atualizações por meio de notificações por e-mail.

**Como revisor, é possível aprovar o pedido, mas não para todos?**
 
Se você está preocupado em conceder o consentimento do admin e permitir que todos os usuários do inquilino usem o aplicativo, recomendamos que você negue a solicitação. Em seguida, conceda manualmente o consentimento do admin restringindo o acesso ao aplicativo, exigindo atribuição do usuário e atribuindo usuários ou grupos ao aplicativo. Para obter mais informações, confira [Métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o consentimento de aplicativos, consulte a [estrutura de consentimento do Azure Active Directory](../develop/consent-framework.md).

[Configure como os usuários finais concordam com aplicativos](configure-user-consent.md)

[Conceder consentimento de admin em todo o inquilino para um aplicativo](grant-admin-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
