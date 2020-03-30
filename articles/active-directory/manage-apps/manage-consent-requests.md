---
title: Gerenciamento de consentimento para aplicativos e avaliação de pedidos de consentimento - Azure AD
description: Saiba como gerenciar solicitações de consentimento quando o consentimento do usuário estiver desativado ou restrito e como avaliar uma solicitação de consentimento de um solicitante de entrada em todo o inquilino para um aplicativo.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367838"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gerenciamento de consentimento para aplicativos e avaliação de solicitações de consentimento

A Microsoft [recomenda](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) desativar o consentimento do usuário final para aplicativos. Isso centralizará o processo de tomada de decisão com a equipe de segurança e administrador de identidade da sua organização.

Depois que o consentimento do usuário final é desativado ou restrito, existem várias considerações importantes para garantir que sua organização permaneça segura e ainda permita que aplicativos críticos de negócios sejam usados. Essas etapas são cruciais para minimizar o impacto na equipe de suporte da sua organização e nos administradores de TI, ao mesmo tempo em que impedem o uso de contas não gerenciadas em aplicativos de terceiros.

## <a name="process-changes-and-education"></a>Mudanças de processos e educação

 1. Considere ativar o fluxo de trabalho de consentimento do [administrador (visualização)](configure-admin-consent-workflow.md) para permitir que os usuários solicitem a aprovação do administrador diretamente da tela de consentimento.

 2. Certifique-se de que todos os administradores entendam as permissões e a [estrutura de consentimento,](../develop/consent-framework.md)como funciona o prompt de [consentimento](../develop/application-consent-experience.md) e como avaliar uma solicitação de consentimento administrativo em todo [o inquilino.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Revise os processos existentes da sua organização para que os usuários solicitem a aprovação do administrador de um aplicativo e façam atualizações, se necessário. Se os processos forem alterados:
    * Atualize a documentação, monitoramento, automação e assim por diante.
    * Comunicar alterações no processo para todos os usuários afetados, desenvolvedores, equipes de suporte e administradores de TI.

## <a name="auditing-and-monitoring"></a>Auditoria e monitoramento

1. [Aplicativos de auditoria e permissões concedidas](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) em sua organização para garantir que nenhum aplicativo injustificado ou suspeito tenha sido concedido acesso a dados anteriormente.

2. Revisar [Detectar e Remediar subsídios de consentimento ilícito no Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) para práticas recomendadas adicionais e salvaguardas contra aplicativos suspeitos que solicitam o consentimento do OAuth.

3. Se sua organização tiver a licença apropriada:

    * Use recursos adicionais [de auditoria de aplicativos OAuth no Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth).
    * Use [a pasta de trabalho do Monitor do Azure para monitorar permissões e atividades relacionadas](../reports-monitoring/howto-use-azure-monitor-workbooks.md) ao consentimento. A carteira de trabalho *Consent Insights* fornece uma visão dos aplicativos por número de solicitações de consentimento falhadas. Isso pode ser útil para priorizar os aplicativos para que os administradores revisem e decidam se concedem ou não o consentimento administrativo.

### <a name="additional-considerations-for-reducing-friction"></a>Considerações adicionais para reduzir o atrito

Para minimizar o impacto em aplicativos confiáveis e essenciais para os negócios que já estão em uso, considere conceder proativamente o consentimento do administrador a aplicativos que tenham um alto número de subsídios de consentimento do usuário:

1. Faça um inventário dos aplicativos já adicionados à sua organização com alto uso, com base em logs de login ou atividade de concessão de consentimento. Um [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) PowerShell pode ser usado para descobrir aplicativos de forma rápida e fácil com um grande número de subsídios de consentimento do usuário.

2. Avalie as principais aplicações que ainda não foram concedidas o consentimento do admin.

   > [!IMPORTANT]
   > Avalie cuidadosamente um aplicativo antes de conceder o consentimento do inquilino, mesmo que muitos usuários da organização já tenham consentido por si mesmos.

3. Para cada aplicação aprovada, conceda o consentimento do admin em todo o inquilino usando um dos métodos documentados abaixo.

4. Para cada aplicativo aprovado, considere [restringir o acesso do usuário](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Avaliando um pedido de consentimento de admin em todo o inquilino

Conceder o consentimento do inquilino é uma operação sensível.  As permissões serão concedidas em nome de toda a organização, e podem incluir permissões para tentar operações altamente privilegiadas. Por exemplo, gerenciamento de função, acesso total a todas as caixas de correio ou todos os sites e personificação completa do usuário.

Antes de conceder o consentimento do administrador em todo o inquilino, você deve garantir que você confie no aplicativo e no editor do aplicativo, para o nível de acesso que você está concedendo. Se você não está confiante de que entende quem controla o aplicativo e por que o aplicativo está solicitando as permissões, *não conceda consentimento*.

A lista a seguir fornece algumas recomendações a serem consideradas ao avaliar um pedido de autorização de admin.

* **Entenda as [permissões e](../develop/consent-framework.md) a estrutura de consentimento na plataforma de identidade da Microsoft.**

* **Entenda a diferença entre [permissões delegadas e permissões de aplicativo.](../develop/v2-permissions-and-consent.md#permission-types)**

   As permissões de aplicativo permitem que o aplicativo acesse os dados de toda a organização, sem qualquer interação do usuário. As permissões delegadas permitem que o aplicativo atue em nome de um usuário que em algum momento foi assinado no aplicativo.

* **Entenda as permissões que estão sendo solicitadas.**

   As permissões solicitadas pelo aplicativo estão listadas no [prompt de consentimento](../develop/application-consent-experience.md). A expansão do título de permissão exibirá a descrição da permissão. A descrição das permissões de aplicativo geralmente terminará em "sem um usuário conectado". A descrição das permissões delegadas geralmente terminará com "em nome do usuário inscrito". Permissões para a API do Microsoft Graph são descritas em [Microsoft Graph Permissions Reference]- consulte a documentação de outras APIs para entender as permissões que elas expõem.

   Se você não entende uma permissão sendo solicitada, *não conceda consentimento*.

* **Entenda qual aplicativo está solicitando permissões e quem publicou o aplicativo.**

   Desconfie de aplicações maliciosas que tentam se parecer com outras aplicações.

   Se você duvida da legitimidade de um aplicativo ou de seu editor, *não conceda consentimento*. Em vez disso, procure confirmação adicional (por exemplo, diretamente do editor do aplicativo).

* **Certifique-se de que as permissões solicitadas estão alinhadas com os recursos esperados do aplicativo.**

   Por exemplo, um aplicativo que ofereça o gerenciamento de sites do SharePoint pode exigir acesso delegado para ler todas as coleções do site, mas não necessariamente precisaria de acesso total a todas as caixas de correio ou privilégios de representação total no diretório.

   Se você suspeitar que o aplicativo está solicitando mais permissões do que precisa, *não conceda consentimento*. Entre em contato com o editor do aplicativo para obter mais detalhes.

## <a name="granting-consent-as-an-administrator"></a>Concedendo consentimento como administrador

### <a name="granting-tenant-wide-admin-consent"></a>Concessão de consentimento de admin em todo o inquilino

Consulte [o consentimento do administrador de todo o inquilino grant para um pedido](grant-admin-consent.md) de instruções passo-a-passo para conceder o consentimento do administrador em todo o inquilino do portal Azure, usando o Azure AD PowerShell, ou a partir do próprio prompt de consentimento.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concessão de consentimento em nome de um usuário específico

Em vez de conceder consentimento para toda a organização, um administrador também pode usar a [API microsft graph](https://docs.microsoft.com/graph/use-the-api) para conceder consentimento às permissões delegadas em nome de um único usuário. Para obter mais informações, consulte [Obter acesso em nome de um usuário](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Limitando o acesso do usuário a aplicativos

O acesso dos usuários aos aplicativos ainda pode ser limitado mesmo quando o consentimento do inquilino foi concedido. Para obter mais informações sobre como exigir a atribuição do usuário a um aplicativo, consulte [métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).

Para obter uma visão geral mais ampla, incluindo como lidar com cenários complexos adicionais, consulte [o uso do Azure AD para gerenciamento de acesso a aplicativos](what-is-access-management.md).

## <a name="next-steps"></a>Próximas etapas

[Cinco etapas para proteger sua infraestrutura de identidade](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Configure o fluxo de trabalho de consentimento de administrador](configure-admin-consent-workflow.md)

[Configure como os usuários finais concordam com aplicativos](configure-user-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)