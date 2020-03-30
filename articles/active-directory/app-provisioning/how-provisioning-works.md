---
title: Entenda como funciona o provisionamento ad do Azure | Microsoft Docs
description: Entenda como funciona o provisionamento do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 555fb39836054be05102f4c28167d72016805639
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481493"
---
# <a name="how-provisioning-works"></a>Como funciona o provisionamento

O provisionamento automático refere-se à criação de identidades e funções de usuário nos aplicativos em nuvem aos os que os usuários precisam acessar. Além de criar identidades de usuário, o provisionamento automático inclui a manutenção e a remoção de identidades de usuário à medida que o status ou as funções mudam. Antes de iniciar uma implantação, você pode rever este artigo para saber como funciona a provisão do Azure AD e obter recomendações de configuração. 

O **Azure AD Provisioning Service** fornece os usuários para aplicativos SaaS e outros sistemas, conectando-se a um ponto final de Gerenciamento de Identidade de Domínio Cruzado (SCIM) 2.0 fornecido pelo fornecedor de aplicativos. Este ponto final do SCIM permite que o Azure AD crie, atualize e remova os usuários de forma programática. Para aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções. O canal usado para provisionamento entre o Azure AD e o aplicativo é criptografado usando criptografia HTTPS TLS.


![Azure AD Provisioning Service](./media/how-provisioning-works/provisioning0.PNG)
Figura*1: O Serviço de Provisionamento Azure AD*

![Fluxo de trabalho](./media/how-provisioning-works/provisioning1.PNG)
de provisionamento de usuário de saída Figura*2: Fluxo de trabalho de provisionamento de usuário "Outbound" do Azure AD para aplicativos SaaS populares*

![Fluxo de trabalho](./media/how-provisioning-works/provisioning2.PNG)
de provisionamento de usuário de entrada Figura*3: Fluxo de trabalho de provisionamento de usuários "Inbound" de aplicativos populares de Gerenciamento de Capital Humano (HCM) para o Azure Active Directory e o Windows Server Active Directory*

## <a name="provisioning-using-scim-20"></a>Provisionamento usando SCIM 2.0

O serviço de provisionamento Azure AD usa o [protocolo SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) para provisionamento automático. O serviço se conecta ao ponto final do SCIM para o aplicativo e usa o esquema de objetos do usuário SCIM e APIs REST para automatizar o provisionamento e o desprovisionamento de usuários e grupos. Um conector de provisionamento baseado em SCIM é fornecido para a maioria dos aplicativos na galeria Azure AD. Ao criar aplicativos para o Azure AD, os desenvolvedores podem usar a API de gerenciamento de usuários SCIM 2.0 para criar um ponto final SCIM que integra o Azure AD para provisionamento. Para obter detalhes, consulte [Build a SCIM endpoint e configure o provisionamento do usuário](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Para solicitar um conector automático de provisionamento Azure AD para um aplicativo que não possui um, preencha uma [solicitação de aplicativo do Diretório Ativo do Azure](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Autorização

As credenciais são necessárias para que o Azure AD se conecte à API de gerenciamento de usuários do aplicativo. Enquanto você estiver configurando provisionamento automático do usuário para um aplicativo, você precisará inserir credenciais válidas. Você pode encontrar tipos de credenciais e requisitos para o aplicativo, referindo-se ao tutorial do aplicativo. No portal Azure, você poderá testar as credenciais fazendo com que o Azure AD tente se conectar ao aplicativo de provisionamento do aplicativo usando as credenciais fornecidas.

Se o sinal único baseado em SAML também estiver configurado para o aplicativo, o limite de armazenamento interno por aplicativo do Azure AD é de 1024 bytes. Esse limite inclui todos os certificados, tokens secretos, credenciais e dados de configuração relacionados associados a uma única instância de um aplicativo (também conhecido como registro principal de serviço no Azure AD). Quando o login único baseado em SAML é configurado, o certificado usado para assinar os tokens SAML geralmente consome mais de 50% do espaço. Quaisquer itens adicionais (tokens secretos, URIs, endereços de e-mail de notificação, nomes de usuário e senhas) que você inserir durante a configuração de provisionamento do usuário podem exceder o limite de armazenamento. Para obter mais informações, consulte [As credenciais do administrador de salvamento de problemas enquanto configuram o provisionamento do usuário](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Atributos de mapeamento

Quando você habilita o provisionamento de usuários para um aplicativo SaaS de terceiros, o portal Azure controla seus valores de atributos através de mapeamentos de atributos. Os mapeamentos determinam os atributos de usuário que fluem entre o Azure AD e o aplicativo-alvo quando as contas de usuário são provisionadas ou atualizadas.

Há um conjunto pré-configurado de atributos e mapeamentos de atributos entre objetos de usuário Do Azure AD e os objetos de usuário de cada aplicativo SaaS. Alguns aplicativos gerenciam outros tipos de objetos junto com os Usuários, como Grupos.

Ao configurar o provisionamento, é importante rever e configurar os mapeamentos e fluxos de trabalho de atributos que definem quais propriedades do usuário (ou grupo) fluem do Azure AD para o aplicativo. Revise e configure a propriedade correspondente **(Match objects using this attribute)** que é usada para identificar e combinar exclusivamente usuários/grupos entre os dois sistemas.

Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Assim, você pode alterar ou excluir mapeamentos de atributos existentes ou criar novos mapeamentos de atributos. Para obter detalhes, consulte [Personalização de mapeamentos de atributos de provisionamento de usuários para aplicativos SaaS](../manage-apps/customize-application-attributes.md).

Quando você configura o provisionamento de um aplicativo SaaS, um dos tipos de mapeamentos de atributos que você pode especificar é o mapeamento de expressão. Para esses mapeamentos, você deve escrever uma expressão semelhante a um script que permita transformar os dados de seus usuários em formatos mais aceitáveis para o aplicativo SaaS. Para obter detalhes, consulte [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>Escopo baseado em atribuição

Para o provisionamento de saída do Azure AD para um aplicativo SaaS, depender [de atribuições de usuários ou grupos](../manage-apps/assign-user-or-group-access-portal.md) é a maneira mais comum de determinar quais usuários estão no escopo do provisionamento. Como as atribuições do usuário também são usadas para habilitar o logon único, o mesmo método pode ser usado para gerenciar tanto o acesso quanto o provisionamento. O escopo baseado em atribuição não se aplica a cenários de provisionamento de entrada, como Workday e Successfactors.

* **Grupos.** Com um plano de licença Azure AD Premium, você pode usar grupos para atribuir acesso a um aplicativo SaaS. Em seguida, quando o escopo de provisionamento for definido apenas para **Sincronizar usuários e grupos atribuídos,** o serviço de provisionamento Azure AD irá provisionar ou desprovisionar os usuários com base em se eles são membros de um grupo atribuído ao aplicativo. O objeto de grupo em si não é provisionado a menos que o aplicativo suporte objetos de grupo.

* **Grupos dinâmicos.** O serviço de provisionamento de usuários Azure AD pode ler e provisionar usuários em [grupos dinâmicos](../users-groups-roles/groups-create-rule.md). Tenha essas ressalvas e recomendações em mente:

  * Grupos dinâmicos podem impactar o desempenho do provisionamento de ponta a ponta de aplicativos Azure AD para SaaS.

  * A rapidez com que um usuário em um grupo dinâmico é provisionado ou desprovisionado em um aplicativo SaaS depende da rapidez com que o grupo dinâmico pode avaliar as alterações de membros. Para obter informações sobre como verificar o status de processamento de um grupo dinâmico, consulte [Verificar o status de processamento de uma regra de membro](../users-groups-roles/groups-create-rule.md).

  * Quando um usuário perde a participação no grupo dinâmico, é considerado um evento de desprovisionamento. Considere esse cenário ao criar regras para grupos dinâmicos.

* **Grupos aninhados.** O serviço de provisionamento de usuários Azure AD não pode ler ou provisionar usuários em grupos aninhados. O serviço só pode ler e provisionar usuários que são membros imediatos de um grupo explicitamente designado. Essa limitação de "atribuições baseadas em grupo para aplicativos" também afeta a logon único (veja [Usando um grupo para gerenciar o acesso a aplicativos SaaS](../users-groups-roles/groups-saasapps.md)). Em vez disso, atribua diretamente ou de outra forma escopo [nos](define-conditional-rules-for-provisioning-user-accounts.md) grupos que contêm os usuários que precisam ser provisionados.

### <a name="attribute-based-scoping"></a>Escopo baseado em atributos 

Você pode usar filtros de escopo para definir regras baseadas em atributos que determinam quais usuários estão provisionados para um aplicativo. Este método é comumente usado para provisionamento de entrada de aplicativos HCM para Azure AD e Active Directory. Os filtros de escopo são configurados como parte dos mapeamentos de atributos para cada conector de provisionamento de usuário do Azure AD. Para obter detalhes sobre a configuração de filtros de escopo baseados em atributos, consulte [provisionamento de aplicativos baseados em atributos com filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>Usuários B2B (convidado)

É possível usar o serviço de provisionamento de usuários Azure AD para prover usuários B2B (ou convidados) em aplicativos Azure AD para SaaS. No entanto, para que os usuários B2B entrem no aplicativo SaaS usando o Azure AD, o aplicativo SaaS deve ter seu recurso de login único baseado em SAML configurado de forma específica. Para obter mais informações sobre como configurar aplicativos SaaS para dar suporte a entradas de usuários de B2B, consulte [Configurar aplicativos SaaS para colaboração B2B](../b2b/configure-saas-apps.md).

Observe que o userPrincipalName para um usuário convidado é muitas@domain.comvezes armazenado como "alias#EXT# ". quando o userPrincipalName é incluído em seus mapeamentos de atributos como um atributo de origem, o #EXT# é despojado do userPrincipalName. Se você precisar que o #EXT# esteja presente, substitua o userPrincipalName por originalUserPrincipalName como o atributo de origem. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Ciclos de provisionamento: Iniciais e incrementais

Quando o Azure AD é o sistema de origem, o serviço de provisionamento usa a [consulta Usar delta para rastrear alterações nos dados do Microsoft Graph](https://docs.microsoft.com/graph/delta-query-overview) para monitorar usuários e grupos. O serviço de provisionamento executa um ciclo inicial contra o sistema de origem e o sistema de destino, seguido por ciclos incrementais periódicos.

### <a name="initial-cycle"></a>Ciclo inicial

Quando o serviço de provisionamento for iniciado, o primeiro ciclo será:

1. Consultar todos os usuários e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributo](customize-application-attributes.md).

2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](../manage-apps/assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um usuário é atribuído ou no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes especificados](customize-application-attributes.md#understanding-attribute-mapping-properties). Exemplo: se o nome userPrincipal no sistema de origem for o atributo correspondente e for mapeado para userName no sistema de destino, o serviço de provisionamento consultará o sistema de destino em busca de userNames que correspondam aos valores de nome de Principal no sistema de origem.

4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena o ID do sistema de destino para o novo usuário. Este ID é usado para executar todas as operações futuras desse usuário.

5. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Depois que a conta de usuário é combinada, o serviço de provisionamento detecta e armazena o ID do sistema de destino para o novo usuário. Este ID é usado para executar todas as operações futuras desse usuário.

6. Se os mapeamentos de atributos contiverem atributos de "referência", o serviço faz atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.

7. Persista uma marca d'água no final do ciclo inicial, que fornece o ponto de partida para os ciclos incrementais posteriores.

Alguns aplicativos como ServiceNow, G Suite e Box suportam não apenas provisionamento de usuários, mas também grupos de provisionamento e seus membros. Nesses casos, se o provisionamento em grupo estiver ativado nos [mapeamentos,](customize-application-attributes.md)o serviço de provisionamento sincroniza os usuários e os grupos e, posteriormente, sincroniza os membros do grupo.

### <a name="incremental-cycles"></a>Ciclos incrementais

Após o ciclo inicial, todos os outros ciclos irão:

1. Consultar o sistema de origem em busca de eventuais usuários e grupos que tenham sido atualizados desde a última marca d'água armazenada.

2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](../manage-apps/assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).

3. Quando um usuário é atribuído ou no escopo para provisionamento, o serviço consulta o sistema de destino para um usuário correspondente usando os [atributos correspondentes especificados](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta e armazena o ID do sistema de destino para o novo usuário. Este ID é usado para executar todas as operações futuras desse usuário.

5. Se um usuário correspondente for encontrado, ele será atualizado usando os atributos fornecidos pelo sistema de origem. Se for uma conta recém-atribuída que é compatível, o serviço de provisionamento detecta e armazena o ID do sistema de destino para o novo usuário. Este ID é usado para executar todas as operações futuras desse usuário.

6. Se os mapeamentos de atributos contiverem atributos de "referência", o serviço faz atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.

7. Se um usuário que estava anteriormente no escopo para provisionamento for removido do escopo, inclusive não sendo atribuído, o serviço desativará o usuário no sistema de destino por meio de uma atualização.

8. Se um usuário que estava anteriormente no escopo para provisionamento for desabilitado ou excluído no sistema de origem, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.

9. Se um usuário que estava anteriormente no escopo para provisionamento for excluído no sistema de origem, o serviço excluirá o usuário no sistema de destino. No Azure AD, os usuários são duramente excluídos 30 dias depois de serem excluídos.

10. Persista uma nova marca d'água no final do ciclo incremental, que fornece o ponto de partida para os ciclos incrementais posteriores.

> [!NOTE]
> Você pode, opcionalmente, desativar as operações **Criar,** **Atualizar**ou **Excluir** usando as caixas de seleção **de ações de objeto-alvo** na seção [Mapeamentos.](customize-application-attributes.md) A lógica para desabilitar um usuário durante uma atualização também é controlada por meio de um mapeamento de atributos em um campo como "accountEnabled".

O serviço de provisionamento continua executando ciclos incrementais indefinidamente, em intervalos definidos no [tutorial específico para cada aplicativo](../saas-apps/tutorial-list.md). Os ciclos incrementais continuam até que ocorra um dos seguintes eventos:

- O serviço é interrompido manualmente usando o portal Azure ou usando o comando API do Microsoft Graph apropriado.
- Um novo ciclo inicial é acionado usando a opção **Limpar estado e reinicialização** no portal Azure, ou usando o comando API do Microsoft Graph apropriado. Esta ação limpa qualquer marca d'água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- Um novo ciclo inicial é acionado devido a uma alteração nos mapeamentos de atributos ou filtros de escopo. Esta ação também limpa qualquer marca d'água armazenada e faz com que todos os objetos de origem sejam avaliados novamente.
- O processo de provisionamento entra em quarentena (veja abaixo) devido a uma alta taxa de erro, e permanece em quarentena por mais de quatro semanas. Neste caso, o serviço será desabilitado automaticamente.

### <a name="errors-and-retries"></a>Erros e novas tentativas

Se um erro no sistema de destino impedir que um usuário individual seja adicionado, atualizado ou excluído no sistema de destino, a operação será repetida novamente no próximo ciclo de sincronização. Se o usuário continuar falhando, as novas tentativas começarão a ocorrer em uma frequência reduzida, voltando gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores devem verificar os [registros de provisionamento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) para determinar a causa raiz e tomar as medidas apropriadas. Entre as falhas comuns podem estar:

- Usuários que não tenham um atributo preenchido no sistema de origem necessário no sistema de destino
- Os usuários que têm um valor de atributo no sistema de origem para o qual há uma restrição única no sistema de destino, e o mesmo valor está presente em outro registro de usuário

Resolva essas falhas ajustando os valores de atributo para o usuário afetado no sistema de origem ou ajustando os mapeamentos de atributos para que eles não causem conflitos.

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas feitas contra o sistema de destino falharem consistentemente por causa de um erro (por exemplo, credenciais de administrador inválidas), o trabalho de provisionamento entrará em um estado de "quarentena". Este estado é indicado no [relatório de resumo de provisionamento](../manage-apps/check-status-user-account-provisioning.md) e via e-mail se as notificações de e-mail foram configuradas no portal Azure.

Quando em quarentena, a frequência de ciclos incrementais é gradualmente reduzida a uma vez por dia.

O trabalho de provisionamento sai da quarentena depois que todos os erros ofensivos são corrigidos e o próximo ciclo de sincronização começa. Se permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado. Saiba mais aqui sobre o status de quarentena [aqui](../manage-apps/application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Quanto tempo demora o provisionamento

O desempenho depende se o seu trabalho de provisionamento está executando um ciclo inicial de provisionamento ou um ciclo incremental. Para obter detalhes sobre quanto tempo de provisionamento leva e como monitorar o status do serviço de provisionamento, consulte [Verificar o status do provisionamento do usuário](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Como saber se os usuários estão sendo provisionados corretamente

Todas as operações executadas pelo serviço de provisionamento do usuário são registradas nos registros de provisionamento Azure AD [(preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Os registros incluem todas as operações de leitura e gravação feitas para os sistemas de origem e destino, e os dados do usuário que foram lidos ou gravados durante cada operação. Para obter informações sobre como ler os registros de provisionamento no portal Azure, consulte o [guia de relatórios de provisionamento](../manage-apps/check-status-user-account-provisioning.md).

## <a name="de-provisioning"></a>Desprovisionamento

O serviço de provisionamento Azure AD mantém os sistemas de origem e destino em sincronia, desprovisionando contas quando os usuários não devem mais ter acesso. 

O serviço de provisionamento AD do Azure excluirá um usuário em um aplicativo quando o aplicativo suupportssoft exclui (solicitação de atualização com ativo = falso) e qualquer um dos seguintes eventos ocorrem:

* A conta de usuário é excluída no Azure AD
*   O usuário não é atribuído a partir do aplicativo
*   O usuário não encontra mais um filtro de escopo e sai do escopo
    * Por padrão, o serviço de provisionamento Azure AD exclui ou desativa usuários que saem do escopo. Se você quiser substituir esse comportamento padrão, você pode definir um sinalizador para [pular as exclusões fora do escopo](../app-provisioning/skip-out-of-scope-deletions.md).
*   A propriedade AccountEnabled é definida como Falsa

Se um dos quatro eventos acima ocorrer e o aplicativo-destino não suportar exclusões suaves, o serviço de provisionamento enviará uma solicitação DELETE para excluir permanentemente o usuário do aplicativo. 

30 dias após um usuário ser excluído no Azure AD, ele será excluído permanentemente do inquilino. Neste ponto, o serviço de provisionamento enviará uma solicitação DELETE para excluir permanentemente o usuário no aplicativo. A qualquer momento durante a janela de 30 dias, você pode [excluir manualmente um usuário permanentemente](../fundamentals/active-directory-users-restore.md), o que envia uma solicitação de exclusão para o aplicativo.

Se você vir um atributo IsSoftDeleted em seus mapeamentos de atributos, ele será usado para determinar o estado do usuário e se enviar uma solicitação de atualização com ativo = falso para soft excluir o usuário. 

## <a name="next-steps"></a>Próximas etapas

[Planejar uma implantação de provisionamento do usuário automática](../app-provisioning/plan-auto-user-provisioning.md)

[Configurar provisionamento para um aplicativo da galeria](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Crie um ponto final scim e configure provisionamento ao criar seu próprio aplicativo](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Solucionaproblemas problemas com a configuração e provisionamento de usuários para um aplicativo](../manage-apps/application-provisioning-config-problem.md).
