---
title: Colete o registro de atividades do Azure com as configurações de diagnóstico - Monitor do Azure | Microsoft Docs
description: Use configurações de diagnóstico para encaminhar logs de atividade do Azure para logs do Monitor do Azure, armazenamento azure ou hubs de eventos do Azure.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096901"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Atualização para coleta e exportação de log de atividades do Azure
O [registro de atividades do Azure](platform-logs-overview.md) é um log de [plataforma](platform-logs-overview.md) que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. O método para enviar entradas de registro de atividades para [um centro de eventos ou conta de armazenamento](activity-log-export.md) ou para um espaço de trabalho do Log [Analytics](activity-log-collect.md) foi alterado para usar [configurações de diagnóstico](diagnostic-settings.md). Este artigo descreve a diferença entre os métodos e como limpar as configurações do legado na preparação para alterar as configurações de diagnóstico.


## <a name="differences-between-methods"></a>Diferenças entre métodos

### <a name="advantages"></a>Vantagens
O uso de configurações de diagnóstico tem as seguintes vantagens em relação aos métodos atuais:

- Método consistente para coletar todos os registros da plataforma.
- Coletar registro de atividades em várias assinaturas e inquilinos.
- Coleta de filtros para coletar apenas logs para categorias específicas.
- Colete todas as categorias de registro de atividades. Algumas categorias não são coletadas usando o método legado.
- Latência mais rápida para ingestão de troncos. O método anterior tem cerca de 15 minutos de latência, enquanto as configurações de diagnóstico adicionam apenas cerca de 1 minuto.

### <a name="considerations"></a>Considerações
Considere os seguintes detalhes da coleta de registros de atividades usando configurações de diagnóstico antes de habilitar esse recurso.

- A configuração de retenção para a coleta do registro atividade para o armazenamento Do Zure foi removida, o que significa que os dados serão armazenados indefinidamente até que você o remova.
- Atualmente, você só pode criar uma configuração de diagnóstico de nível de assinatura usando o portal Azure. Para usar outros métodos, como PowerShell ou CLI, você pode criar um modelo de Gerenciador de recursos.


### <a name="differences-in-data"></a>Diferenças de dados
As configurações de diagnóstico coletam os mesmos dados dos métodos anteriores usados para coletar o registro de atividade com as seguintes diferenças atuais:

As seguintes colunas foram removidas. A substituição por essas colunas está em um formato diferente, então você pode precisar modificar consultas de log que as usam. Você ainda pode ver colunas removidas no esquema, mas elas não serão preenchidas com dados.

| Coluna removida | Coluna de substituição |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| Substatus da atividade | AtividadeSubstatusValor |
| OperationName     | OperaçãoValor de Nome     |
| ResourceProvider  | ResourceProviderValue  |

A seguinte coluna foi adicionada:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> Em alguns casos, os valores nestas colunas podem estar em todas as maiúsculas. Se você tiver uma consulta que inclua essas colunas, você deve usar o [operador =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) para fazer uma comparação insensível do caso.

## <a name="work-with-legacy-settings"></a>Trabalhe com configurações de legado
As configurações de legado para a coleta do registro atividade continuarão a funcionar se você não optar por substituir por uma configuração de diagnóstico. Use o seguinte método para gerenciar o perfil de log para uma assinatura.

1. No menu Monitor do **Azure** no portal Azure, selecione **Registro de atividades**.
3. Clique **em Configurações de diagnóstico**.

   ![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique no banner roxo para a experiência do legado.

    ![Experiência de legado](media/diagnostic-settings-subscription/legacy-experience.png)


Consulte os artigos a seguir para obter detalhes sobre o uso dos métodos de coleta legados.

- [Coletar e analisar os logs de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](activity-log-collect.md)
- [Coletar ativos do Azure Activity no Azure Monitor através de inquilinos do Azure Active Directory](activity-log-collect-tenants.md)
- [Exportar registro de atividades do Azure para armazenamento ou Hubs de Eventos Do Azure](activity-log-export.md)

## <a name="disable-existing-settings"></a>Desativar as configurações existentes
Você deve desativar a coleta existente da Atividade antes de habilitá-la usando configurações de diagnóstico. Ter ambos habilitados pode resultar em dados duplicados.

### <a name="disable-collection-into-log-analytics-workspace"></a>Desativar a coleta no espaço de trabalho do Log Analytics

1. Abra o menu de espaços de trabalho do Log Analytics no portal Azure e selecione o espaço de trabalho para coletar o Registro de **atividades.**
2. Na seção Fontes de dados do espaço de **trabalho** do menu do espaço de trabalho, selecione O log de atividade **do Azure**.
3. Clique na assinatura que deseja desconectar.
4. Clique **em Desconectar** e, **em seguida, Sim** quando solicitado para confirmar sua escolha.

### <a name="disable-log-profile"></a>Desativar o perfil de log

1. Use o procedimento descrito em [Trabalho com configurações de legado](#work-with-legacy-settings) para abrir configurações de legado.
2. Desative qualquer coleta atual para centros de armazenamento ou eventos.



## <a name="activity-log-monitoring-solution"></a>Solução de monitoramento do Registro de Atividades
A solução de monitoramento do Azure Log Analytics inclui várias consultas de log e visualizações para analisar os registros do Registro de Atividades em seu espaço de trabalho do Log Analytics. Essa solução usa dados de log coletados em um espaço de trabalho do Log Analytics e continuará a funcionar sem alterações se você coletar o registro de atividade usando configurações de diagnóstico. Consulte [a solução de monitoramento do Activity Logs Analytics](activity-log-collect.md#activity-logs-analytics-monitoring-solution) para obter detalhes sobre essa solução.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Registro de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
* [Saiba mais sobre as configurações de diagnóstico](diagnostic-settings.md)
