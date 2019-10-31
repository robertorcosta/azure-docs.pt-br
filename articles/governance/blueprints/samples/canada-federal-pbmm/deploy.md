---
title: Exemplo do Canadá Federal PBMM Blueprint – implantar etapas
description: Implante as etapas para o exemplo do Canadá Federal PBMM Blueprint, incluindo detalhes do parâmetro de artefato do Blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 22806f497283c1885446eadd5f444dacf3769c12
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163332"
---
# <a name="deploy-the-canada-federal-pbmm-blueprint-samples"></a>Implantar os exemplos do Canadá Federal PBMM Blueprint

Para implantar os exemplos do Canadá Federal PBMM Blueprint, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre a amostra do **Canadá Federal PBMM** Blueprint em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do plano gráfico**: forneça um nome para a sua cópia do exemplo Blueprint.
   - **Local de definição**: Use as reticências e selecione o grupo de gerenciamento para salvar a cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **próximo: artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo Blueprint pode ser personalizada para seu ambiente e necessidades, mas essa modificação pode movê-la para fora do padrão.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **observações de alteração** , como "primeira versão publicada do exemplo do Canadá Federal PBMM Blueprint." Em seguida, selecione **Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** e pesquise e selecione **Política** no painel esquerdo. Na página **Política**, selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou sua cópia do exemplo Blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: o nome é preenchido previamente para você com base no nome do plano gráfico.
       Altere-o conforme necessário ou mantenha-o como está.
     - **Local**: selecione uma região na qual a identidade gerenciada deve ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição do Blueprint**: escolha uma versão **publicada** da sua cópia do exemplo Blueprint.

   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção de identidade gerenciada _atribuída ao sistema_ padrão.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Visualização\]: implantar o agente do Log Analytics para VMs do Linux |Atribuição de política |Workspace do Log Analytics para VMs Linux |Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Visualização\]: implantar o agente do Log Analytics para VMs do Linux |Atribuição de política |Opcional: lista de imagens de VM que têm suporte para o sistema operacional Linux para adicionar ao escopo |Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional: `[]` |
|\[Visualização\]: implantar o agente do Log Analytics para VMs do Windows |Atribuição de política |Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo |Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional: `[]` |
|\[Visualização\]: implantar o agente do Log Analytics para VMs do Windows |Atribuição de política |Workspace do Log Analytics para VMs do Windows |Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[\]de visualização: auditar controles PBMM federais do Canadá e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria |Atribuição de política |ID do workspace do Log Analytics para a qual as VMs devem ser configuradas |Esta é a ID (GUID) do workspace do Log Analytics para a qual as VMs devem ser configuradas. |
|\[\]de visualização: auditar controles PBMM federais do Canadá e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria |Atribuição de política |Lista de tipos de recurso que devem ter os logs de diagnóstico habilitados |Lista de tipos de recursos para auditar se a configuração do log de diagnóstico não estiver habilitada. Os valores aceitáveis podem ser encontrados em [Esquemas de logs de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type). |
|\[\]de visualização: auditar controles PBMM federais do Canadá e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria |Atribuição de política |Grupo de administradores |Group. Exemplo: `Administrator; myUser1; myUser2` |
|\[\]de visualização: auditar controles PBMM federais do Canadá e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria |Atribuição de política |Lista de usuários que devem ser incluídos no grupo de administradores de VM do Windows |Uma lista separada por ponto e vírgula de membros que devem ser incluídos no grupo local de Administradores. Exemplo: `Administrator; myUser1; myUser2` |
|Implantar a Proteção Avançada contra Ameaças em Contas de Armazenamento |Atribuição de política |Efeito |As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md). |
|Implantar Auditoria em servidores SQL |Atribuição de política |O valor em dias do período de retenção (0 é uma indicação de retenção ilimitada) |Dias de retenção (opcional, _180_ dias se não especificado) |
|Implantar Auditoria em servidores SQL |Atribuição de política |Nome do grupo de recursos da conta de armazenamento para auditoria do SQL Server |A auditoria grava eventos de banco de dados em um log de auditoria na sua conta de armazenamento do Azure (uma conta de armazenamento é criada em cada região em que um SQL Server é criado, que é compartilhado por todos os servidores nessa região). Importante-para uma operação adequada de auditoria, não exclua ou renomeie o grupo de recursos ou as contas de armazenamento. |
|Implantar configurações de diagnóstico para Grupos de Segurança de Rede |Atribuição de política |Prefixo da conta de armazenamento para diagnóstico do grupo de segurança de rede |Esse prefixo é combinado com o local do grupo de segurança de rede para formar o nome da conta de armazenamento criada. |
|Implantar configurações de diagnóstico para Grupos de Segurança de Rede |Atribuição de política |Nome do grupo de recursos da conta de armazenamento para diagnóstico do grupo de segurança de rede (precisa existir) |O grupo de recursos no qual a conta de armazenamento é criada. Esse grupo de recursos já precisa existir. |

## <a name="next-steps"></a>Próximos passos

Agora que você analisou as etapas para implantar o exemplo de PBMM Federal do Canadá, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Governo Federal do Canadá PBMM-visão geral](./index.md)
> [Planejamento Federal de PBMM do Canadá – mapeamento de controle](./control-mapping.md)

Artigos adicionais sobre plantas e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).