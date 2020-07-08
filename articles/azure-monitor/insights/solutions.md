---
title: Soluções de monitoramento no Azure Monitor | Microsoft Docs
description: As soluções de monitoramento no Azure Monitor são uma coleção de regras de lógica, visualização e aquisição de dados que fornecem métricas centradas em torno de uma área específica do problema.  Este artigo fornece informações sobre como instalar e usar soluções de monitoramento.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/12/2020
ms.openlocfilehash: 4edcb22ed6bd33b1174354cf0cbb9a590e35c207
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906880"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitoramento no Azure Monitor
As soluções de monitoramento aproveitam os serviços no Azure para fornecer análise adicional da operação de um determinado aplicativo ou serviço. Este artigo fornece uma breve visão geral das soluções de monitoramento no Azure e detalhes sobre como utilizá-las e instalá-las. É possível adicionar soluções de monitoramento ao Azure Monitor para qualquer aplicativo e serviço usado. Normalmente, estão disponíveis gratuitamente, mas coletam dados que podem invocar encargos de uso.

## <a name="use-monitoring-solutions"></a>Usar soluções de monitoramento

Abra a página de **Visão geral** no Azure Monitor para exibir um bloco para cada solução instalada no workspace. 

1. Vá para o [Portal do Azure](https://ms.portal.azure.com). Pesquise e selecione **Monitor**.
1. No menu **Insights**, selecione **Mais**.
1. Use as caixas suspensas na parte superior da tela para alterar o workspace ou o intervalo de tempo usado para os blocos.
1. Clique no bloco para uma solução abrir a exibição dela que inclui uma análise mais detalhada dos dados coletados.

![Visão geral](media/solutions/overview.png)

As soluções de monitoramento podem conter vários tipos de recursos do Azure e é possível exibir todos os recursos incluídos em uma solução como qualquer outro recurso. Por exemplo, todas as consultas de log incluídas na solução estão listadas em **Consultas de Solução** no [Gerenciador de consultas](../log-query/get-started-portal.md#load-queries) e é possível usar essas consultas ao executar análises ad hoc com [consultas de log](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Listar soluções de monitoramento instaladas

Use o procedimento a seguir para listar as soluções de monitoramento instaladas na assinatura.

1. Vá para o [Portal do Azure](https://ms.portal.azure.com). Pesquise e selecione **Soluções**.
1. As soluções instaladas em todos os workspaces estão listadas. O nome da solução é seguido pelo nome do workspace no qual ela está instalada.
1. Use as caixas suspensas na parte superior da tela para filtrar por assinatura ou grupo de recursos.


![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a página de resumo. Essa página exibe todas as exibições incluídas na solução e fornece opções diferentes para a própria solução e seu workspace. Visualize a página de resumo de uma solução usando um dos procedimentos acima para listar as soluções e, em seguida, clique no nome da solução.

![Propriedades da solução](media/solutions/solution-properties.png)

## <a name="install-a-monitoring-solution"></a>Instalar uma solução de monitoramento

As soluções de monitoramento da Microsoft e parceiros estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com). Você pode procurar soluções disponíveis e instalá-las usando o procedimento a seguir. Ao instalar uma solução, você deverá selecionar um [Espaço de Trabalho do Log Analytics](../platform/manage-access.md) no qual a solução será instalada e onde seus dados serão coletados.

1. Na [lista de soluções para a assinatura](#list-installed-monitoring-solutions), clique em **Adicionar**.
1. Procure ou pesquise uma solução. Você também pode procurar soluções [neste link de pesquisa](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Localize a solução de monitoramento desejada e leia sua descrição.
1. Clique em **Criar** para iniciar o processo de instalação.
1. Quando o processo de instalação iniciar, você deverá especificar o workspace do Log Analytics e fornecer a configuração necessária para a solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução a partir da comunidade

Os membros da comunidade podem enviar soluções de gerenciamento para Modelos de Início Rápido do Azure. É possível instalar essas soluções diretamente ou baixá-las para instalação posterior.

1. Siga o processo descrito no [Espaço de Trabalho do Log Analytics e na Conta de automação](#log-analytics-workspace-and-automation-account) para vincular um espaço de trabalho e uma conta.
2. Acesse os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/). 
3. Pesquise uma solução na qual você esteja interessado.
4. Selecione a solução nos resultados para exibir seus detalhes.
5. Clique no botão **Implantar no Azure**.
6. Você será solicitado a fornecer informações, como o grupo de recursos e o local, além dos valores de quaisquer parâmetros na solução.
7. Clique em **Comprar** para instalar a solução.

## <a name="log-analytics-workspace-and-automation-account"></a>Espaço de Trabalho do Log Analytics e Conta de automação

Todas as soluções de monitoramento exigem um [espaço de trabalho do Log Analytics](../platform/manage-access.md) para armazenar dados coletados pela solução e hospedar as exibições e pesquisas de logs. Algumas soluções também exigem uma [conta de Automação](../../automation/automation-security-overview.md) para conter runbooks e recursos relacionados. O workspace e a conta devem atender aos seguintes requisitos.

* Cada instalação de uma solução somente pode usar um espaço de trabalho do Log Analytics e uma conta de Automação. É possível instalar a solução separadamente em vários workspaces.
* Se uma solução exigir uma conta de Automação, o espaço de trabalho do Log Analytics e a conta de Automação deverão estar vinculadas entre si. Um espaço de trabalho do Log Analytics só pode ser vinculado a uma Conta de automação e uma Conta de automação só pode ser vinculada a um espaço de trabalho do Log Analytics.
* Para ser vinculado, o workspace do Log Analytics e a conta de Automação devem estar na mesma assinatura, mas podem estar em grupos de recursos diferentes implantados na mesma região. A exceção é um workspace na região Leste dos EUA e uma conta de Automação no Leste dos EUA 2.

Ao instalar uma solução por meio do Azure Marketplace, um workspace e uma conta de Automação serão solicitados. Se ainda não estiverem vinculados, o link entre eles será criado.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifique o link entre um espaço de trabalho do Log Analytics e uma conta de Automação

Você pode verificar o vínculo entre um espaço de trabalho do Log Analytics e uma Conta de automação usando o procedimento a seguir.

1. Selecione a conta de automação no portal do Azure.
1. Role até a seção **Recursos Relacionados** do menu e selecione **Workspace vinculado**.
1. Se o **Workspace** estiver vinculado a uma conta de Automação, essa página listará o workspace ao qual está vinculada. Se você selecionar o nome do workspace listado, será redirecionado à página de visão geral desse workspace.

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitoramento

Para remover uma solução instalada, localize-a na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a página de resumo e, em seguida, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

* Obter uma [lista de soluções de monitoramento da Microsoft](solutions-inventory.md).
* Saiba como [criar consultas](../log-query/log-query-overview.md) para analisar dados coletados por soluções de monitoramento.