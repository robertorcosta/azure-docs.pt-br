---
title: Use a escala automática do Azure com métricas de convidados em um modelo de conjunto de escala Linux
description: Saiba como fazer dimensionamento automático usando métricas de convidado em um modelo de Conjunto de Dimensionamento de Máquinas Virtuais do Linux
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 8021b7b8feb6dc06fb2e48bc4e825200a1baad33
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273640"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Dimensionamento automático usando métricas de convidado em um modelo de conjunto de dimensionamento do Linux

Existem dois tipos amplos de métricas no Azure que são coletadas de VMs e conjuntos de escala: métricas de host e métricas guest. Em um nível alto, se você quiser usar métricas padrão de CPU, disco e rede, então as métricas do host são um bom ajuste. Se, no entanto, você precisar de uma seleção maior de métricas, então as métricas dos convidados devem ser analisadas.

As métricas do host não exigem configuração adicional porque são coletadas pela VM host, enquanto as métricas dos hóspedes exigem que você instale a [extensão do Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) ou a [extensão Linux Azure Diagnostics](../virtual-machines/linux/diagnostic-extension.md) na VM convidada. Um motivo comum para usar métricas de convidado, em vez de métricas de host, é que as métricas de convidado fornecem uma seleção maior de métricas do que as métricas de host. Um exemplo disso são as métricas de consumo de memória, que só estão disponíveis por meio de métricas de convidado. As métricas de host com suporte estão listadas [aqui](../azure-monitor/platform/metrics-supported.md) e métricas de convidado comumente usadas estão listadas [aqui](../azure-monitor/platform/autoscale-common-metrics.md). Este artigo mostra como modificar o [modelo de conjunto de escala viável básico](virtual-machine-scale-sets-mvss-start.md) para usar regras de escala automática com base em métricas de convidados para conjuntos de escala Linux.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Em um [artigo anterior,](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo de conjunto de escala supérdia. Agora usaremos esse modelo anterior e o modificaremos para criar um modelo que implante um conjunto de escala Linux com escala de métrica de convidado.

Primeiro, adicione parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico armazena dados de métrica em uma [tabela](../cosmos-db/table-storage-how-to-use-dotnet.md) nesta conta de armazenamento. Do Agente de Diagnóstico do Linux versão 3.0 em diante, não há mais suporte para usar uma chave de acesso de armazenamento. Nesse caso, use um [Token SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Em seguida, modifique o conjunto de dimensionamento `extensionProfile` para incluir a extensão de diagnóstico. Nessa configuração, especifique a ID do recurso do conjunto de dimensionamento cujas métricas serão coletadas, bem como a conta de armazenamento e o token SAS a serem usados para armazenar as métricas. Especifique com que frequência as métricas são agregadas (neste caso, a cada minuto) e quais métricas devem ser acompanhadas (neste caso, o percentual de memória usada). Para obter informações mais detalhadas sobre essa configuração e métricas diferentes da porcentagem de memória usada, consulte [esta documentação](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Finalmente, adicione um recurso `autoscaleSettings` para configurar o dimensionamento automático com base nessas métricas. Esse recurso tem uma cláusula `dependsOn` que faz referência a conjunto de dimensionamento para garantir que o conjunto de dimensionamento exista antes de tentar realizar seu dimensionamento automático. Se você escolher uma métrica diferente para fazer o dimensionamento automático, use o `counterSpecifier` da configuração de extensão de diagnóstico, como o `metricName` na configuração de dimensionamento automático. Para obter mais informações sobre a configuração de dimensionamento automático, consulte as [práticas recomendadas de dimensionamento automático](../azure-monitor/platform/autoscale-best-practices.md) e a [documentação de referência da API REST do Azure Monitor](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
