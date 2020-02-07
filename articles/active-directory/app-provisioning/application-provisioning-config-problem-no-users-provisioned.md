---
title: Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD
description: Como solucionar problemas comuns enfrentados ao não ver os usuários que aparecem em uma Galeria de Aplicativos do Azure AD que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0147644e11ec1d54ac38f8099c9e65c25aa94962
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067247"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD
Após o provisionamento automático ser configurado para um aplicativo (incluindo verificar se as credenciais de aplicativo fornecidas ao Azure AD para se conectar ao aplicativo são válidas), os usuários e/ou grupos são provisionados no aplicativo. O provisionamento é determinado pelos seguintes fatores:

-   Quais usuários e grupos têm sido **atribuídos** para o aplicativo. Observe que não há suporte para o provisionamento de grupos aninhados ou grupos do Office 365. Para obter mais informações sobre atribuição, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Estejam os **mapeamentos de atributos** habilitados ou não, e configurados para sincronizar atributos válidos do Azure AD para o aplicativo. Para obter mais informações sobre mapeamentos de atributo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
-   Se há ou não um **filtro de escopo** presente que está filtrando usuários com base nos valores de atributo específico. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Se você observar que os usuários não estão sendo provisionados, consulte os [logs de provisionamento (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) no Azure AD. Pesquise por entradas de log referentes a um usuário específico.

Você pode acessar os logs de provisionamento no portal do Azure selecionando **Azure Active Directory** &gt; **aplicativos empresariais** &gt; **Provisionando logs (versão prévia)** na seção **atividade** . Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Provisionando logs (versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os logs de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo a consulta do Azure AD para usuários atribuídos que estão no escopo para provisionamento, consultando o aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar
Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

- [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
- [Logs de provisionamento dizem que os usuários são ignorados e não provisionados, mesmo que sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar
Se você definir o **status de provisionamento** como **On** no **Azure Active Directory &gt; Enterprise apps &gt; \[nome do aplicativo\] &gt;** seção de provisionamento do portal do Azure. No entanto, nenhum outro detalhe de status é mostrado nessa página após recargas subsequentes, é provável que o serviço esteja em execução, mas ainda não tenha concluído um ciclo inicial. Verifique os **logs de provisionamento (versão prévia)** descritos acima para determinar quais operações o serviço está executando e se há erros.

>[!NOTE]
>Um ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. Sincronizações subsequentes após o ciclo inicial são mais rápidas, pois o serviço de provisionamento armazena marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial. O ciclo inicial melhora o desempenho das sincronizações subsequentes.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de provisionamento, digamos que os usuários sejam ignorados e não provisionados mesmo que sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de provisionamento, é importante examinar a guia **etapas** do log para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

- Foi **configurado um filtro de escopo** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações sobre a atribuição, consulte [Atribuir acesso de usuário ou grupo](../manage-apps/assign-user-or-group-access-portal.md).
- **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento é examinar e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Essa configuração inclui a definição da "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
- **Mapeamentos de atributos para grupos:** provisionamento do nome do grupo e detalhes do grupo, além de membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade habilitando ou desabilitando o **mapeamento** para objetos de grupo mostrados na guia **provisionamento** . Se o provisionamento de grupos estiver habilitado, certifique-se de examinar os mapeamentos de atributo para garantir que um campo apropriado esteja sendo usado para a "ID correspondente". Essa ID correspondente pode ser o nome de exibição ou o alias do email. O grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for populada para um grupo no Microsoft Azure AD.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

[Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
