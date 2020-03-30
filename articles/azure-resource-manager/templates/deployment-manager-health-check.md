---
title: Implantação de integração em saúde - Gerente de Implantação do Azure
description: Descreve como implantar um serviço em muitas regiões com o Azure Deployment Manager. Ele mostra as práticas de implantação segura para verificar a estabilidade da sua implantação antes de distribuir a todas as regiões.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273794"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzir o lançamento da integração de saúde ao Azure Deployment Manager (visualização pública)

[O Azure Deployment Manager](./deployment-manager-overview.md) permite que você execute implementações em etapas dos recursos do Azure Resource Manager. Os recursos são implantados região por região de forma ordenada. A verificação integrada de saúde do Azure Deployment Manager pode monitorar os lançamentos e interromper automaticamente os lançamentos problemáticos, para que você possa solucionar problemas e reduzir a escala do impacto. Esse recurso pode reduzir a indisponibilidade de serviços causada por regressões em atualizações.

## <a name="health-monitoring-providers"></a>Prestadores de vigilância em saúde

Para facilitar ao máximo a integração de integridade, a Microsoft tem trabalhado com algumas das principais empresas de monitoramento de integridade de serviço para fornecer uma solução copiar/colar simples visando integrar as verificações de integridade às suas implantações. Se você ainda não está usando um monitor de saúde, essas são ótimas soluções para começar:

| ![azure gerente de implantação de saúde monitor de saúde datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure deployment gerente de saúde monitor sitesite24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure gerente de implantação de saúde provedor de onda de onda](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, a plataforma líder de monitoramento e análise para ambientes modernos em nuvem. Veja [como o Datadog se integra ao Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, a solução de monitoramento de serviços em nuvem pública e privada. Veja [como o Site24x7 se integra ao Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, a plataforma de monitoramento e análise para ambientes de aplicativos multi-nuvem. Veja [como o Wavefront se integra com o Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Como a saúde do serviço é determinada

[Os prestadores de serviços](#health-monitoring-providers) de vigilância em saúde oferecem diversos mecanismos de monitoramento dos serviços e alertam sobre quaisquer problemas de saúde dos serviços. [O Azure Monitor](../../azure-monitor/overview.md) é um exemplo de uma dessas ofertas. O Monitor Azure pode ser usado para criar alertas quando certos limites são excedidos. Por exemplo, o pico de memória e utilização da CPU além dos níveis esperados quando você implanta uma nova atualização ao seu serviço. Quando notificado, você pode tomar ações corretivas.

Esses provedores de saúde normalmente oferecem APIs REST para que o status dos monitores do seu serviço possa ser examinado programáticamente. As APIs REST podem voltar com um simples sinal saudável/insalubre (determinado pelo código de resposta HTTP) e/ou com informações detalhadas sobre os sinais que está recebendo.

A nova etapa *healthCheck* no Azure Deployment Manager permite que você declare códigos HTTP que indiquem um serviço saudável ou, para resultados mais complexos do REST, você pode até mesmo especificar expressões regulares que, se coincidirem, indicam uma resposta saudável.

O fluxo para a configuração com as verificações de saúde do Azure Deployment Manager:

1. Crie seus monitores de saúde através de um prestador de serviços de saúde de sua escolha.
1. Crie uma ou mais etapas de saúdeVerifique como parte do lançamento do Azure Deployment Manager. Preencha as etapas de saúdeConfira as etapas com as seguintes informações:

    1. O URI para a API REST para seus monitores de saúde (conforme definido pelo seu prestador de serviços de saúde).
    1. Informações de autenticação. Atualmente, apenas a autenticação de estilo de tecla API é suportada.
    1. [Códigos de status HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) ou expressões regulares que definem uma resposta saudável. Observe que você pode fornecer expressões regulares, que TODAS devem corresponder para que a resposta seja considerada saudável, ou você pode fornecer expressões das quais ANY deve corresponder para que a resposta seja considerada saudável. Ambos os métodos são suportados.

    O seguinte Json é um exemplo:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Invoque as etapas de healthCheck no momento apropriado na implantação do Azure Deployment Manager. No exemplo a seguir, uma etapa de verificação de saúde é invocada **em postDeploymentSteps** do **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Para passar por um exemplo, consulte [Tutorial: Use o health check no Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fases de um exame de saúde

Neste momento, o Azure Deployment Manager sabe como consultar a saúde do seu serviço e em que fases em seu rollout para fazê-lo. No entanto, o Azure Deployment Manager também permite uma configuração profunda do tempo dessas verificações. Uma etapa healthCheck é executada em 3 fases sequenciais, todas com durações configuráveis: 

1. Aguarde

    1. Depois que uma operação de implantação é concluída, as VMs podem estar reiniciando, reconfigurando com base em novos dados ou até mesmo sendo iniciadas pela primeira vez. Também leva tempo para que os serviços comecem a emitir sinais de saúde para serem agregados pelo provedor de vigilância em saúde em algo útil. Durante esse processo tumultuado, pode não fazer sentido verificar se a saúde do serviço já que a atualização ainda não atingiu um estado estável. De fato, o serviço pode estar oscilando entre estados saudáveis e insalubres à medida que os recursos se instalam. 
    1. Durante a fase de Espera, a saúde do serviço não é monitorada. Isso é usado para permitir aos recursos implantados o tempo para assar antes de iniciar o processo de verificação de saúde. 
1. Elástico

    1. Uma vez que é impossível saber em todos os casos quanto tempo os recursos levarão para assar antes de se tornarem estáveis, a fase Elástica permite um período de tempo flexível entre quando os recursos são potencialmente instáveis e quando eles são necessários para manter uma saúde estável Estado.
    1. Quando a fase Elástica começa, o Azure Deployment Manager começa a sondar o ponto final rest fornecido para a saúde do serviço periodicamente. O intervalo de votação é configurável. 
    1. Se o monitor de saúde voltar com sinais indicando que o serviço é insalubre, esses sinais são ignorados, a fase Elástica continua e a votação continua. 
    1. Assim que o monitor de saúde volta com sinais indicando que o serviço está saudável, a fase Elástica termina e a fase Estado Saudável começa. 
    1. Assim, a duração especificada para a fase Elástica é o tempo máximo que pode ser gasto pesquisando para a saúde do serviço antes que uma resposta saudável seja considerada obrigatória. 
1. HealthyState

    1. Durante a fase Estado Saudável, a saúde do serviço é continuamente pesquisada no mesmo intervalo da fase Elástica. 
    1. Espera-se que o serviço mantenha sinais saudáveis do prestador de vigilância em saúde durante toda a duração especificada. 
    1. Se em algum momento uma resposta insalubre for detectada, o Azure Deployment Manager interromperá toda a implantação e devolverá a resposta REST carregando os sinais de serviço insalubres.
    1. Uma vez encerrada a duração do HealthyState, o healthCheck está completo e a implantação continua até o próximo passo.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre como integrar o monitoramento de saúde no Azure Deployment Manager. Vá para o próximo artigo para saber como implantar com o Gerenciador de implantação.

> [!div class="nextstepaction"]
> [Tutorial: integre a verificação de saúde no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)