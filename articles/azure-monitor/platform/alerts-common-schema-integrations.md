---
title: Como integrar o esquema de alerta comum com os Logic Apps
description: Aprenda a criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668223"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Como integrar o esquema de alerta comum com os Logic Apps

Este artigo mostra como criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.

## <a name="overview"></a>Visão geral

O [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs) fornece um esquema JSON padronizado e extensível em todos os seus diferentes tipos de alerta. O esquema de alerta comum é mais útil quando alavancado de forma programática – através de webhooks, runbooks e aplicativos lógicos. Neste artigo, demonstramos como um único aplicativo lógico pode ser autor para lidar com todos os seus alertas. Os mesmos princípios podem ser aplicados a outros métodos programáticos. O aplicativo lógico descrito neste artigo cria variáveis bem definidas para os [campos 'essenciais',](alerts-common-schema-definitions.md#essentials)e também descreve como você pode lidar com a lógica específica [do tipo de alerta.](alerts-common-schema-definitions.md#alert-context)


## <a name="prerequisites"></a>Pré-requisitos 

Este artigo assume que o leitor está familiarizado com 
* Configuração de regras de alerta[(métrica,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric) [log,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)registro de [atividades)](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)
* Criação de [grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Habilitando o [esquema de alerta comum](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) de dentro de grupos de ação

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Crie um aplicativo lógico aproveitando o esquema de alerta comum

1. Siga as [etapas traçadas para criar seu aplicativo lógico](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Selecione o gatilho **Quando uma solicitação HTTP for recebida**.

    ![Gatilhos do aplicativo lógico](media/action-groups-logic-app/logic-app-triggers.png "Gatilhos do aplicativo lógico")

1.  Selecione **Editar** para alterar o gatilho de solicitação HTTP.

    ![Gatilhos de solicitação HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Gatilhos de solicitação HTTP")


1.  Copiar e colar o seguinte esquema:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Selecione **+** **Novo passo** e, em seguida, escolha Adicionar uma **ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

1. Nesta fase, você pode adicionar uma variedade de conectores (Microsoft Teams, Slack, Salesforce, etc.) com base em seus requisitos específicos de negócios. Você pode usar os "campos essenciais" fora da caixa. 

    ![Campos essenciais](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Campos essenciais")
    
    Alternativamente, você pode criar lógica condicional com base no tipo de alerta usando a opção 'Expressão'.

    ![Expressão lógica do aplicativo](media/alerts-common-schema-integrations/logic-app-expressions.png "Expressão lógica do aplicativo")
    
     O [campo 'monitoringService'](alerts-common-schema-definitions.md#alert-context) permite identificar exclusivamente o tipo de alerta, com base no qual você pode criar a lógica condicional.

    
    Por exemplo, o trecho abaixo verifica se o alerta é um alerta de log baseado no Application Insights e, se assim, imprime os resultados da pesquisa. Caso outra coisa, imprima "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Saiba mais sobre [como escrever expressões lógicas de aplicativos](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre grupos de ação](../../azure-monitor/platform/action-groups.md).
* [Saiba mais sobre o esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs).

