---
title: Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD
description: Como solucionar problemas comuns enfrentados ao não ver os usuários que aparecem em uma Galeria de Aplicativos do Azure AD que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522910"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD
Após o provisionamento automático ser configurado para um aplicativo (incluindo verificar se as credenciais de aplicativo fornecidas ao Azure AD para se conectar ao aplicativo são válidas), os usuários e/ou grupos são provisionados no aplicativo. O provisionamento é determinado pelos seguintes fatores:

-   Quais usuários e grupos têm sido **atribuídos** para o aplicativo. Observe que o provisionamento de grupos aninhados ou grupos office 365 não é suportado. Para obter mais informações sobre atribuição, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Estejam os **mapeamentos de atributos** habilitados ou não, e configurados para sincronizar atributos válidos do Azure AD para o aplicativo. Para obter mais informações sobre mapeamentos de atributo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
-   Se há ou não um **filtro de escopo** presente que está filtrando usuários com base nos valores de atributo específico. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Se você observar que os usuários não estão sendo provisionados, consulte os [logs de provisionamento (visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) no Azure AD. Pesquise por entradas de log referentes a um usuário específico.

Você pode acessar os logs de provisionamento no portal Azure selecionando os logs de provisionamento de &gt; aplicativos **corporativos** **do Azure Active Directory** &gt; **(visualização)** na seção **Atividade.** Você pode pesquisar os dados de provisionamento com base no nome do usuário ou no identificador no sistema de origem ou no sistema de destino. Para obter detalhes, consulte [Registros de provisionamento (visualização)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Os registros de provisionamento registram todas as operações realizadas pelo serviço de provisionamento, incluindo consulta ao Azure AD para usuários atribuídos que estão no escopo de provisionamento, consultando o aplicativo de destino para a existência desses usuários, comparando os objetos do usuário entre o sistema. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar
Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

- [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
- [Os registros de provisionamento dizem que os usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar
Se você definir o **Status de provisionamento** como **Ligado** na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** do portal do Azure. No entanto, nenhum outro detalhe de status é mostrado nessa página após recargas subseqüentes, é provável que o serviço esteja em execução, mas ainda não tenha concluído um ciclo inicial. Verifique os **registros de provisionamento (visualização) descritos** acima para determinar quais operações o serviço está realizando e se há algum erro.

>[!NOTE]
>Um ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório Azure AD e do número de usuários em escopo para provisionamento. As sincronizações subseqüentes após o ciclo inicial são mais rápidas, pois o serviço de provisionamento armazena marcas d'água que representam o estado de ambos os sistemas após o ciclo inicial. O ciclo inicial melhora o desempenho das sincronizações subseqüentes.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registros de provisionamento dizem que os usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos

Quando um usuário aparece como "ignorado" nos registros de provisionamento, é importante rever a guia **Etapas** do registro para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

- **Um filtro de escopo foi configurado** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte [filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações sobre a atribuição, consulte [Atribuir acesso de usuário ou grupo](../manage-apps/assign-user-or-group-access-portal.md).
- **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento é examinar e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Essa configuração inclui a definição da "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
- **Mapeamentos de atributos para grupos:** provisionamento do nome do grupo e detalhes do grupo, além de membros, se houver suporte para alguns aplicativos. Você pode ativar ou desativar essa funcionalidade ativando ou desativando o **Mapeamento** para objetos de grupo mostrados na guia **Provisionamento.** Se os grupos de provisionamento forem habilitados, certifique-se de revisar os mapeamentos de atributos para garantir que um campo apropriado esteja sendo usado para o "ID correspondente". Essa ID correspondente pode ser o nome de exibição ou o alias do email. O grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for populada para um grupo no Microsoft Azure AD.

## <a name="next-steps"></a>Próximas etapas

[Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
