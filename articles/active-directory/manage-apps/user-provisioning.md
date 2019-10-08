---
title: Provisionamento de usuário automatizado em aplicativo SaaS no Azure AD | Microsoft Docs
description: Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar contínua e automaticamente as contas de usuário em vários aplicativos SaaS de terceiros.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ef652b05f62218ee1d0e72543bfa546f0c14abe
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001703"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatize o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory

O Azure Active Directory (AD do Azure) permite automatizar a criação, a manutenção e a remoção de identidades de usuário em aplicativos de nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), como dropbox, Salesforce, ServiceNow e muito mais. Isso é conhecido como provisionamento automatizado de usuário para aplicativos SaaS.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

Esse recurso permite que você:

- Crie automaticamente novas contas nos sistemas certos quando novas pessoas entrarem em sua equipe ou organização.
- Desative automaticamente as contas nos sistemas certos quando as pessoas deixarem a equipe ou organização.
- Mantenha as identidades em seus aplicativos e sistemas atualizadas com base nas alterações no diretório ou em seu sistema de recursos humanos.
- Provisione objetos que não são de usuário, como grupos, para aplicativos que dão suporte a eles.

O provisionamento automatizado de usuários também inclui essa funcionalidade:

- Capacidade de corresponder identidades existentes entre sistemas de origem e de destino.
- Os mapeamentos de atributos personalizável que definem quais dados do usuário devem fluir do sistema de origem para o sistema de destino.
- Alertas de email opcionais para erros de provisionamento.
- Logs de relatórios e de registros para ajudá-lo a monitorar e a solucionar problemas.

## <a name="why-use-automated-provisioning"></a>Por que usar o provisionamento automatizado?

Alguns motivos comuns para usar esse recurso incluem:

- Evitando custos, ineficiências e erro humano associados a processos manuais de provisionamento.
- Evitando os custos associados à hospedagem e à manutenção dos scripts e das soluções de provisionamento personalizadas.
- Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles deixam a organização.
- Importar facilmente um grande número de usuários para um sistema ou aplicativo SaaS específico.
- Ter um único conjunto de políticas para determinar quem é provisionado e quem pode entrar em um aplicativo.

## <a name="how-does-automatic-provisioning-work"></a>Como funciona o provisionamento automático?

O **serviço de provisionamento do Azure ad** provisiona usuários para aplicativos SaaS e outros sistemas conectando-se aos pontos de extremidade da API de gerenciamento de usuários fornecidos por cada fornecedor de aplicativos. Esses pontos de extremidade de API de gerenciamento permitem que o Azure AD crie, atualize e remova usuários por meio de programação. Para os aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções.

Serviço de provisionamento do AD ![Azure @ no__t-1 @ no__t-2Figure 1: Serviço de provisionamento do Azure AD*

fluxo de trabalho de provisionamento de usuário do @no__t 0Outbound @ no__t-1 @ no__t-2Figure 2: fluxo de trabalho de provisionamento do usuário de "saída" do Azure AD para aplicativos SaaS populares*

fluxo de trabalho de provisionamento de usuário do @no__t 0Inbound @ no__t-1 @ no__t-2Figure 3: fluxo de trabalho de provisionamento do usuário de "entrada" de aplicativos de HCM (gerenciamento de capital humano) para o Azure Active Directory e o Windows Server Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento de usuário automático do Azure AD?

O Azure AD apresenta suporte integrado para muitos sistemas de recursos humanos e aplicativos SaaS populares e suporte genérico para aplicativos que implementam partes específicas do [padrão SCIM 2,0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

### <a name="pre-integrated-applications"></a>Aplicações pré-integradas

Para obter uma lista de todos os aplicativos para os quais o Azure Active Directory dá suporte em um conector de provisionamento pré-integrado, consulte a [lista de tutoriais de aplicativo para provisionamento do usuário](../saas-apps/tutorial-list.md).

Para entrar em contato com a equipe de engenharia do Azure AD e solicitar o suporte de provisionamento para mais aplicativos, envie uma mensagem no [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Para que um aplicativo dê suporte a provisionamento automatizado de usuários, primeiro ele deve fornecer as APIs de gerenciamento necessárias que permitam que programas externos automatizem a criação, a manutenção e a remoção de usuários. Portanto, nem todos os aplicativos SaaS são compatíveis com esse recurso. Para aplicativos que dão suporte a APIs de gerenciamento de usuários, a equipe de engenharia do Azure AD pode criar um conector de provisionamento para esses aplicativos, e esse trabalho é priorizado de acordo com as necessidades dos clientes atuais e potenciais.

### <a name="connecting-applications-that-support-scim-20"></a>Conectando aplicativos que oferecem suporte a SCIM 2.0

Para obter informações sobre como conectar aplicativos de forma genérica que implementem APIs de gerenciamento de usuário baseadas em SCIM 2.0, consulte [Como usar SCIM para provisionar automaticamente usuários e grupos do Azure Active Directory para aplicativos](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o provisionamento automático para um aplicativo?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Use o portal de Azure Active Directory para configurar o serviço de provisionamento do Azure AD para um aplicativo selecionado.

1. Abra o **[portal de Azure Active Directory](https://aad.portal.azure.com)** .
1. Selecione **aplicativos empresariais** no painel esquerdo. É mostrada uma lista de todos os aplicativos configurados.
1. Escolha **+ novo aplicativo** para adicionar um aplicativo. Adicione um dos seguintes, dependendo do seu cenário:

   - A opção **Adicionar seu próprio aplicativo** dá suporte a integrações scim de desenvolvimento personalizado.
   - Todos os aplicativos na seção **Adicionar da galeria** > **aplicativos em destaque** dão suporte ao provisionamento automático. Veja a lista [de tutoriais de aplicativos para provisionamento de usuários](../saas-apps/tutorial-list.md) para os adicionais.

1. Forneça todos os detalhes e selecione **Adicionar**. O novo aplicativo é adicionado à lista de aplicativos empresariais e é aberto na tela de gerenciamento de aplicativos.
1. Selecione **provisionamento** para gerenciar as configurações de provisionamento de conta de usuário para o aplicativo.

   ![Mostra a tela de configurações de provisionamento](./media/user-provisioning/provisioning_settings0.PNG)

1. Selecione a opção automática para o **modo de provisionamento** para especificar configurações para credenciais de administrador, mapeamentos, início e parada e sincronização.

   - Expanda **credenciais de administrador** para inserir as credenciais necessárias para que o Azure ad se conecte à API de gerenciamento de usuários do aplicativo. Esta seção também permite habilitar notificações por email se as credenciais falharem ou o trabalho de provisionamento entrar em [quarentena](#quarantine).
   - Expanda **mapeamentos** para exibir e editar os atributos de usuário que fluem entre o Azure AD e o aplicativo de destino quando as contas de usuário são provisionadas ou atualizadas. Se o aplicativo de destino oferecer suporte a ele, esta seção permitirá que você configure o provisionamento de grupos e contas de usuário opcionalmente. Selecione um mapeamento na tabela para abrir o editor de mapeamento à direita, onde você pode exibir e personalizar os atributos de usuário.

     Os **filtros de escopo** informam ao serviço de provisionamento quais usuários e grupos no sistema de origem devem ser provisionados ou desprovisionados para o sistema de destino. No painel **mapeamento de atributo** , selecione **escopo do objeto de origem** para filtrar em valores de atributo específicos. Por exemplo, você pode especificar que apenas os usuários com um atributo "Departamento" de "Vendas" devem estar no escopo para provisionamento. Para obter mais informações, consulte [Como usar filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).

     Para saber mais, confira [Personalizar mapeamentos de atributo](customize-application-attributes.md).

   - **As configurações** controlam a operação do serviço de provisionamento para um aplicativo, incluindo se ele está em execução no momento. O menu **escopo** permite especificar se apenas usuários e grupos atribuídos devem estar no escopo para provisionamento ou se todos os usuários no diretório do Azure ad devem ser provisionados. Para saber mais sobre "como atribuir" usuários e grupos, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](assign-user-or-group-access-portal.md).

Na tela gerenciamento de aplicativos, selecione **Provisionando logs (versão prévia)** para exibir os registros de cada operação executada pelo serviço de provisionamento do Azure AD. Para saber mais, confira o [guia de relatório de provisionamento](check-status-user-account-provisioning.md).

![Exemplo-tela de logs de provisionamento para um aplicativo](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> O serviço de provisionamento de usuário do Azure AD também pode ser configurado e gerenciado usando-se a [API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="what-happens-during-provisioning"></a>O que acontece durante o provisionamento?

Quando o Azure AD é o sistema de origem, o serviço de provisionamento usa o [recurso Consulta Diferencial da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) para monitorar usuários e grupos. O serviço de provisionamento executa um ciclo inicial no sistema de origem e no sistema de destino, seguido por ciclos incrementais periódicos.

### <a name="initial-cycle"></a>Ciclo inicial

Quando o serviço de provisionamento for iniciado, a primeira sincronização já será executada:

1. Consultar todos os usuários e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributo](customize-application-attributes.md).
1. Filtre os usuários e os grupos retornados usando qualquer [atribuição](assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).
1. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados. Exemplo: Se o nome userPrincipal no sistema de origem for o atributo correspondente e for mapeado para userName no sistema de destino, o serviço de provisionamento consultará o sistema de destino em busca de userNames que correspondam aos valores de nome de Principal no sistema de origem.
1. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Após a correspondência da conta de usuário, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se os mapeamentos de atributo contiverem atributos de "referência", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.
1. Mantenha uma marca-d ' água no final do ciclo inicial, que fornece o ponto de partida para os ciclos incrementais posteriores.

Alguns aplicativos, como ServiceNow, G Suite e Box, oferecem suporte não apenas ao provisionamento de usuários, mas também ao provisionamento de grupos e de seus membros. Nesses casos, se o provisionamento de grupo estiver habilitado nos [mapeamentos](customize-application-attributes.md), o serviço de provisionamento sincronizará os usuários e os grupos e, posteriormente, sincronizará as associações de grupo.

### <a name="incremental-cycles"></a>Ciclos incrementais

Após o ciclo inicial, todos os outros ciclos irão:

1. Consultar o sistema de origem em busca de eventuais usuários e grupos que tenham sido atualizados desde a última marca d'água armazenada.
1. Filtre os usuários e os grupos retornados usando qualquer [atribuição](assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).
1. Quando um usuário é atribuído ou está no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties)especificados.
1. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Se for uma conta atribuída recentemente que é correspondida, o serviço de provisionamento detecta e armazena em cache a ID do sistema de destino para o novo usuário, que é usado para executar todas as operações futuras nesse usuário.
1. Se os mapeamentos de atributo contiverem atributos de "referência", o serviço fará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.
1. Se um usuário que estava anteriormente no escopo para provisionamento for removido do escopo (inclusive sendo desatribuído), o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
1. Se um usuário que estava anteriormente no escopo para provisionamento for desabilitado ou excluído no sistema de origem, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
1. Se um usuário que estava anteriormente no escopo para provisionamento for excluído no sistema de origem, o serviço excluirá o usuário no sistema de destino. No Azure AD, os usuários são excluídos por 30 dias após serem excluídos de maneira reversível.
1. Mantenha uma nova marca d' água no final do ciclo incremental, que fornece o ponto de partida para os ciclos incrementais posteriores.

> [!NOTE]
> Opcionalmente, você pode desabilitar as caixas de seleção **criar**, **Atualizar**ou **excluir** usando as **ações do objeto de destino** na seção [mapeamentos](customize-application-attributes.md) . A lógica para desabilitar um usuário durante uma atualização também é controlada por meio de um mapeamento de atributos em um campo como "accountEnabled".

O serviço de provisionamento continua executando ciclos incrementais back-to-back indefinidamente, em intervalos definidos no [tutorial específico de cada aplicativo](../saas-apps/tutorial-list.md), até que um dos seguintes eventos ocorra:

- O serviço é interrompido manualmente usando-se o portal do Azure ou o comando apropriado da API do Graph 
- Um novo ciclo inicial é disparado usando a opção **limpar estado e reiniciar** na portal do Azure ou usando o comando API do Graph apropriado. Esta ação limpa qualquer marca d' água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- Um novo ciclo inicial é disparado devido a uma alteração nos mapeamentos de atributo ou filtros de escopo. Essa ação também limpa qualquer marca-d ' água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- O processo de provisionamento entra em quarentena (veja abaixo) devido a uma alta taxa de erros e permanece em quarentena por mais de quatro semanas. Neste caso, o serviço será desabilitado automaticamente.

### <a name="errors-and-retries"></a>Erros e novas tentativas

Se um usuário individual não puder ser adicionado, atualizado ou excluído no sistema de destino devido a um erro no sistema de destino, a operação será repetida no próximo ciclo de sincronização. Se o usuário continuar falhando, as novas tentativas começarão a ocorrer em uma frequência reduzida, voltando gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores devem verificar os [logs de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) para determinar a causa raiz e executar a ação apropriada. Entre as falhas comuns podem estar:

- Usuários que não tenham um atributo preenchido no sistema de origem necessário no sistema de destino
- Os usuários têm um valor de atributo no sistema de origem para o qual há uma restrição exclusiva no sistema de destino e o mesmo valor está presente em outro registro de usuário

Essas falhas podem ser resolvidas ajustando-se os valores de atributo do usuário afetado no sistema de origem ou os mapeamentos de atributos para não causar conflitos.

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente devido a um erro (por exemplo, para credenciais de administrador inválidas), o trabalho de provisionamento entrará em um estado de "quarentena". Esse estado é indicado no [relatório de Resumo de provisionamento](check-status-user-account-provisioning.md) e por email se as notificações por email tiverem sido configuradas no portal do Azure.

Quando em quarentena, a frequência de ciclos incrementais é reduzida gradualmente para uma vez por dia.

O trabalho de provisionamento será removido da quarentena depois que todos os erros incorretos forem corrigidos e o próximo ciclo de sincronização for iniciado. Se permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado.

## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo levará para provisionar os usuários?

O desempenho depende se o seu trabalho de provisionamento está executando um ciclo de provisionamento inicial ou um ciclo incremental. Para obter detalhes sobre quanto tempo o provisionamento leva e como monitorar o status do serviço de provisionamento, consulte [verificar o status do provisionamento do usuário](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Como eu posso saber se os usuários estão sendo provisionados corretamente?

Todas as operações executadas pelo serviço de provisionamento de usuário são registradas nos logs de provisionamento do Azure AD [(versão prévia)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Isso inclui todas as operações de leitura e gravação feitas nos sistemas de origem e de destino, bem como os dados de usuário que foram lidos ou gravados durante cada operação.

Para obter informações sobre como ler os logs de provisionamento no portal do Azure, consulte o guia de [relatórios de provisionamento](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Como solucionar problemas com o provisionamento do usuário?

Para obter diretrizes baseada em cenário sobre como solucionar problemas de provisionamento automático de usuário, consulte [Problemas ao configurar e provisionar usuários para um aplicativo](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quais são as práticas recomendadas para implementar o provisionamento automático de usuário?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Para um plano de implantação passo a passo de exemplo para provisionamento de usuário de saída para um aplicativo, veja o [Guia de implantação de identidade para o provisionamento de usuário](https://aka.ms/userprovisioningdeploymentplan).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>O provisionamento automático de usuário para o trabalho de aplicativos SaaS com usuários de B2B do Azure Active Directory?

Sim, é possível usar o serviço de provisionamento de usuários do Azure AD para provisionar usuários B2B (ou convidados) no Azure AD para aplicativos SaaS.

No entanto, para que os usuários B2B entrem no aplicativo SaaS usando o Azure AD, o aplicativo SaaS deve ter seu recurso de logon único baseado em SAML configurado de forma específica. Para obter mais informações sobre como configurar aplicativos SaaS para dar suporte a entradas de usuários de B2B, consulte [Configurar aplicativos SaaS para colaboração B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>O provisionamento automático de usuário para o trabalho de aplicativos SaaS com grupos dinâmicos no Microsoft Azure Active Directory?

Sim. Quando configurado para "sincronizar apenas usuários e grupos atribuídos", o serviço de provisionamento de usuários do Azure AD pode provisionar ou desprovisionar usuários em um aplicativo SaaS com base em se eles são membros de um [grupo dinâmico](../users-groups-roles/groups-create-rule.md). Grupos dinâmicos também funcionam com a opção "sincronizar todos os usuários e grupos".

No entanto, o uso de grupos dinâmicos pode afetar o desempenho geral de provisionamento do usuário de ponta a ponta do Azure Active Directory para os aplicativo SaaS. Ao usar grupos dinâmicos, mantenha essas advertências e recomendações em mente:

- O quão rápido um usuário em um grupo dinâmico é provisionado ou desprovisionado em um aplicativo SaaS depende de quanto tempo o grupo dinâmico pode avaliar as alterações de associação. Para obter informações sobre como verificar o status de processamento de um grupo dinâmico, consulte [Verificar o status de processamento para uma regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Ao usar grupos dinâmicos, as regras devem ser cuidadosamente consideradas com o provisionamento e desprovisionamento de usuários em mente, como uma perda de associação resulta em um evento de desprovisionamento.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>O provisionamento automático de usuário para o trabalho de aplicativos SaaS com grupos dinâmicos no Azure Active Directory?

Nº Quando configurado para "sincronizar apenas usuários e grupos atribuídos", o serviço de provisionamento de usuários do Azure AD não é capaz de ler ou provisionar usuários que estão em grupos aninhados. Só é capaz de ler e provisionar usuários que são membros imediatos do grupo explicitamente atribuído.

Essa é uma limitação de "atribuições baseadas em grupo para aplicativos", o que também afeta o logon único e é descrito em [Usar um grupo para gerenciar o acesso a aplicativos SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Como alternativa, você deve atribuir explicitamente (ou, de outra forma, [escopo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) os grupos que contêm os usuários que precisam ser provisionados.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Está Provisionando entre o Azure AD e um aplicativo de destino usando um canal criptografado?

Sim. Usamos a criptografia SSL HTTPS para o destino do servidor.

## <a name="related-articles"></a>Artigos relacionados

- [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
- [Personalizando mapeamentos de atributo para provisionamento de usuário](customize-application-attributes.md)
- [Gravando expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
- [Filtros de escopo para provisionamento de usuário](define-conditional-rules-for-provisioning-user-accounts.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Azure AD para aplicativos](use-scim-to-provision-users-and-groups.md)
- [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
