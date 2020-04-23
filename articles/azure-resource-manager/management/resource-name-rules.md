---
title: Restrições de nomeação de recursos
description: Mostra as regras e restrições para nomear recursos do Azure.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: fab1ab2bb779b3826c852e49da7970030d34594d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086378"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Regras de nomenclatura e restrições para recursos do Azure

Este artigo resume as regras de nomeação e restrições para os recursos do Azure. Para obter recomendações sobre como nomear recursos, consulte [Convenções recomendadas de nomeação e marcação](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Os nomes dos recursos são insensíveis a casos, a menos que especificamente anotados na coluna caracteres válidos.

Nas tabelas a seguir, o termo alfanumérico refere-se a:

* **de a** a **z** (letras minúsculas)
* **A** a **Z** (letras maiúsculas)
* **0** a **9** (números)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | grupo de recursos | 3-63 | Letras e números minúsculos.<br><br>Comece com letras minúsculas. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | serviço | global | 1-50 | Alfanumérica.<br><br>Comece com a carta. |
> | serviço / apis | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / problemas | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / problemas / anexos | Questão | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / problemas / comentários | Questão | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / operações | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / operações / tags | operação | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / lançamentos | api | 1-80 | Alfanumérica, sublinhado e hífen.<br><br>Comece e termine com alfanumérica ou sublinhada. |
> | serviço / apis / schemas | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / tagDescriptions | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / apis / tags | api | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / api-versão-sets | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / autorizaçãoServidores | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / backends | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / certificados | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço /diagnóstico | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço /grupos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / grupos / usuários | group | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | prestadores de serviços /identidade | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / madeireiros | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / notificações | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / notificações / destinatárioE-mails | notificação | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / openidConnectProviders | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço /políticas | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço /produtos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / produtos / apis | product | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / produtos / grupos | product | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / produtos / tags | product | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço /propriedades | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / assinaturas | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço /tags | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviços /modelos | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |
> | serviço / usuários | serviço | 1-256 | Não posso usar:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | configuraçãoLojas | grupo de recursos | 5 a 50 | Alfanumérica, sublinhado e hífen. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | locks | escopo de atribuição | 1-90 | Alfanuméricas, períodos, sublinhados, hífens e parênteses.<br><br>Não pode terminar no período. |
> | policyassignments | escopo de atribuição | 1-128 nome de exibição<br><br>1-260 nome de recurso | O nome de exibição pode conter qualquer caractere.<br><br>O nome do `%` recurso não pode incluir e não pode terminar com período ou espaço. |
> | Policydefinitions | escopo de definição | 1-128 nome de exibição<br><br>1-260 nome de recurso | O nome de exibição pode conter qualquer caractere.<br><br>O nome do `%` recurso não pode incluir e não pode terminar com período ou espaço. |
> | policySetDefinitions | escopo de definição | 1-128 nome de exibição<br><br>1-260 nome de recurso | O nome de exibição pode conter qualquer caractere.<br><br>O nome do `%` recurso não pode incluir e não pode terminar com período ou espaço.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | automationaccounts | grupo de recursos | 6-50 | Alfanuméricas e hífens.<br><br>Comece com letra, e termine com alfanumérico. |
> | automaçãoContas/ certificados | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço.  |
> | automaçãoContas/conexões | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automaçãoContas / credenciais | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automaçãoContas / runbooks | conta de automação | 1-63 | Alfanumérica, sublinhado e hífen.<br><br>Comece com a carta.  |
> | automaçãoContas/cronogramas | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automaçãoContas / variáveis | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |
> | automaçãoContas / observadores | conta de automação | 1-63 |  Alfanumérica, sublinhado e hífen.<br><br>Comece com a carta. |
> | automaçãoContas / webhooks | conta de automação | 1-128 | Não posso usar:<br> `<>*%&:\?.+/` <br><br>Não pode acabar com o espaço. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | batchAccounts | Região | 3-24 | Letras e números minúsculos. |
> | contas em lote / aplicativos | conta lote | 1-64 | Alfanumérica, sublinhado e hífen. |
> | lotesContas/certificados | conta lote | 5-45 | Alfanumérica, sublinhado e hífen. |
> | lotesContas/pools | conta lote | 1-64 | Alfanumérica, sublinhado e hífen. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | membros do blockchain | global | 2-20 | Letras e números minúsculos.<br><br>Comece com letras minúsculas. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. |
> | botServices / canais | serviço bot | 2-64 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. |
> | botServices / Conexões | serviço bot | 2-64 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. |
> | enterpriseChannels | grupo de recursos | 2-64 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. Hífens consecutivos não são permitidos. |
> | Redis / firewallRegras | Redis | 1-256 | Alfanumérica |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | perfis | grupo de recursos | 1-260 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |
> | perfis / pontos finais | global | 1-50 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | certificateOrders | grupo de recursos | 3-30 | Alfanumérica. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 2-64 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | availabilitySets | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Termine com alfanumérica ou sublinhada. |
> | discoConfiguraçãodescriptografia | grupo de recursos | 1-80 | Alfanumérica salienta. |
> | disks | grupo de recursos | 1-80 | Alfanumérica salienta. |
> | galleries | grupo de recursos | 1-80 | Alfanuméricas e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / aplicações | galeria | 1-80 | Alfanumérica, hífens e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / aplicações /versões | aplicativo | Inteiro de 32 bits | Números e períodos. |
> | galerias / imagens | galeria | 1-80 | Alfanumérica, hífens e períodos.<br><br>Comece e termine com alfanumérico. |
> | galerias / imagens / versões | image | Inteiro de 32 bits | Números e períodos. |
> | images | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Termine com alfanumérica ou sublinhada. |
> | snapshots | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Termine com alfanumérica ou sublinhada. |
> | virtualMachines | grupo de recursos | 1-15 (Windows)<br>1-64 (Linux)<br><br>Consulte a observação abaixo. | Não posso usar:<br> `\/""[]:|<>+=;,?*@&`<br><br>Não posso começar com sublinhado. Não pode terminar com menstruação ou hífen. |
> | virtualMachineScaleSets | grupo de recursos | 1-15 (Windows)<br>1-64 (Linux)<br><br>Consulte a observação abaixo. | Não posso usar:<br> `\/""[]:|<>+=;,?*@&`<br><br>Não posso começar com sublinhado. Não pode terminar com menstruação ou hífen. |

> [!NOTE]
> As máquinas virtuais Azure têm dois nomes distintos: nome do recurso e nome do host. Quando você cria uma máquina virtual no portal, o mesmo valor é usado para ambos os nomes. As restrições na tabela anterior são para o nome do host. O nome real do recurso pode ter até 64 caracteres.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | containerGroups | grupo de recursos | 1-63 | Letras minúsculas, números e hífens.<br><br>Não pode começar ou terminar com hífen. Hifens consecutivos não são permitidos. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | registries | global | 5 a 50 | Alfanumérica. |
> | registros / buildTasks | Registro | 5 a 50 | Alfanumérica. |
> | registros / buildTasks/steps | tarefa de build | 5 a 50 | Alfanumérica. |
> | registros / replicação | Registro | 5 a 50 | Alfanumérica. |
> | registros / escopoMapas | Registro | 5 a 50 | Alfanumérica, hífens e sublinhados. |
> | registros / tarefas | Registro | 5 a 50 | Alfanumérica, hífens e sublinhados. |
> | registros / tokens | Registro | 5 a 50 | Alfanumérica, hífens e sublinhados. |
> | registros / webhooks | Registro | 5 a 50 | Alfanumérica. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | managedClusters | grupo de recursos | 1-63 | Alfanumérica, sublinhado e hífen.<br><br>Comece e termine com alfanumérico. |
> | openShiftClusters gerenciados | grupo de recursos | 1-30 | Alfanumérica. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | hubs | grupo de recursos | 1-64 | Alfanumérica.<br><br>Comece com a carta.  |
> | hubs / autorizaçãoPolíticas | hub | 1-50 | Alfanumérica, sublinhados e períodos.<br><br>Comece e termine com alfanumérico. |
> | hubs / conectores | hub | 1-128 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / conectores/mapeamentos | conector | 1-128 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / interações | hub | 1-128 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / kpi | hub | 1-512 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / links | hub | 1-512 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / previsões | hub | 1-512 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs /perfis | hub | 1-128 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / relationshipLinks | hub | 1-512 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / relacionamentos | hub | 1-512 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / roleAssignments | hub | 1-128 | Alfanumérica salienta.<br><br>Comece com a carta. |
> | hubs / visualizações | hub | 1-512 | Alfanumérica salienta.<br><br>Comece com a carta. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | associações | grupo de recursos | 1-180 | Não posso usar:<br>`%&\\?/`<br><br>Não pode terminar com período ou espaço. |
> | provedores de recursos | grupo de recursos | 3-64 | Não posso usar:<br>`%&\\?/`<br><br>Não pode terminar com período ou espaço. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | jobs | grupo de recursos | 3-24 | Alfanuméricas, hífens, sublinhados e períodos. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | workspaces | grupo de recursos | 3-30 | Alfanumérica, sublinhados e hífens |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | factories | global | 3-63 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |
> | fábricas / fluxos de dados | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / conjuntos de dados | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / integraçãoRuntimes | fábrica | 3-63 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |
> | fábricas / serviços vinculados | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / gasodutos | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / gatilhos | fábrica | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |
> | fábricas / gatilhos / rerunTriggers | gatilho | 1-260 | Não posso usar:<br>`<>*#.%&:\\+?/`<br><br>Comece com alfanumérico. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras e números minúsculos. |
> | contas / computaçãoPolíticas | account | 3-60 | Alfanumérica, hífens e sublinhados. |
> | contas / dadosLakeStoreContas | account | 3-24 | Letras e números minúsculos. |
> | contas / firewallRegras | account | 3-50 | Alfanumérica, hífens e sublinhados. |
> | contas / armazenamentoContas | account | 3-60 | Alfanumérica, hífens e sublinhados. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | Letras e números minúsculos. |
> | contas / firewallRegras | account | 3-50 | Alfanumérica, hífens e sublinhados. |
> | contas / virtualNetworkRules | account | 3-50 | Alfanumérica, hífens e sublinhados. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | services | grupo de recursos | 2-62 | Alfanuméricas, hífens, períodos e sublinhados.<br><br>Comece com alfanumérico. |
> | serviços/projetos | serviço | 2-57 | Alfanuméricas, hífens, períodos e sublinhados.<br><br>Comece com alfanumérico. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Letras minúsculas, hífens e números.<br><br>Não pode começar ou terminar com hífen. |
> | servidores /bancos de dados | servers | 1-63 | Alfanuméricas e hífens. |
> | servidores / firewallRegras | servers | 1-128 | Alfanumérica, hífens e sublinhados. |
> | servidores / virtualNetworkRules | servers | 1-128 | Alfanuméricas e hífens. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Letras minúsculas, hífens e números.<br><br>Não pode começar ou terminar com hífen. |
> | servidores /bancos de dados | servers | 1-63 | Alfanuméricas e hífens. |
> | servidores / firewallRegras | servers | 1-128 | Alfanumérica, hífens e sublinhados. |
> | servidores / virtualNetworkRules | servers | 1-128 | Alfanuméricas e hífens. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | Letras minúsculas, hífens e números.<br><br>Não pode começar ou terminar com hífen. |
> | servidores /bancos de dados | servers | 1-63 | Alfanuméricas e hífens. |
> | servidores / firewallRegras | servers | 1-128 | Alfanumérica, hífens e sublinhados. |
> | servidores / virtualNetworkRules | servers | 1-128 | Alfanuméricas e hífens. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | Alfanuméricas e hífens.<br><br>Não pode acabar com hífen. |
> | IotHubs / certificados | Hub de IoT | 1-64 | Alfanuméricas, hífens, períodos e sublinhados. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanuméricas, hífens, períodos e sublinhados. |
> | provisionamentoServiços | grupo de recursos | 3-64 | Alfanuméricas e hífens.<br><br>Termine com alfanumérico. |
> | provisionamentoServiços/certificados | provisionamentoServiços | 1-64 | Alfanuméricas, hífens, períodos e sublinhados. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | labs | grupo de recursos | 1-50 | Alfanumérica, sublinhado e hífen. |
> | laboratórios / imagens personalizadas | Laboratório | 1-80 | Alfanumérica, sublinhados, hífens e parênteses. |
> | laboratórios / fórmulas | Laboratório | 1-80 | Alfanumérica, sublinhados, hífens e parênteses. |
> | laboratórios / máquinas virtuais | Laboratório | 1-15 (Windows)<br>1-64 (Linux) | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. Não podem ser todos os números. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-31 | Letras minúsculas, números e hífens.<br><br>Comece com letras ou número minúsculos. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | domains | grupo de recursos | 3-50 | Alfanuméricas e hífens. |
> | domínios / tópicos | domínio | 3-50 | Alfanuméricas e hífens. |
> | eventSubscriptions | grupo de recursos | 3-64 | Alfanuméricas e hífens. |
> | topics | grupo de recursos | 3-50 | Alfanuméricas e hífens. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | grupo de recursos | 6-50 | Alfanuméricas e hífens.<br><br>Comece com a carta. Termine com letra ou número. |
> | namespaces | global | 6-50 | Alfanuméricas e hífens.<br><br>Comece com a carta. Termine com letra ou número. |
> | namespaces / AuthorizationRules | namespace | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | namespaces / desastreRecoveryConfigs | namespace | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | namespaces / eventhubs | namespace | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | namespaces / eventhubs / autorizaçãoRegras | hub de eventos | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |
> | namespaces / eventhubs / grupos de consumidores | hub de eventos | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com letra ou número. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | global | 3-59 | Alfanuméricas e hífens<br><br>Comece e termine com letra ou número. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | jobs | grupo de recursos | 2-64 | Alfanuméricas e hífens.<br><br>Comece com a carta. |

## <a name="microsoftinsights"></a>Microsoft.insights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | components | grupo de recursos | 1-260 | Não posso usar:<br>`%&\?/` <br><br>Não pode terminar com espaço ou período.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | Letras minúsculas, números e hífens.<br><br>Comece com letras ou número minúsculos. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | vaults | global | 3-24 | Alfanuméricas e hífens.<br><br>Comece com a carta. Termine com letra ou dígito. Não pode conter hífens consecutivos. |
> | cofres / segredos | Cofre | 1-127 | Alfanuméricas e hífens. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | global | 4-22 | Letras e números minúsculos.<br><br>Comece com a carta. |
> | /clusters / bancos de dados | cluster | 1-260 | Alfanuméricas, hífens, espaços e períodos. |
> | /clusters / bancos de dados / dataConexões | Banco de Dados | 1-40 | Alfanuméricas, hífens, espaços e períodos. |
> | /clusters / bancos de dados / conexões eventhub | Banco de Dados | 1-40 | Alfanuméricas, hífens, espaços e períodos. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | integrationAccounts | grupo de recursos | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoContas/assembléias | conta de integração | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoContas/lotesConfigurações | conta de integração | 1-20 | Alfanumérica. |
> | integraçãoContas/certificados | conta de integração | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoContas / mapas | conta de integração | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoContas/parceiros | conta de integração | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoConfigurações de processos de contas/rosettanet | conta de integração | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoContas /esquemas | conta de integração | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoContas/sessões | conta de integração | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |
> | integraçãoServiceEnvironments | grupo de recursos | 1-80 | Alfanuméricas, hífens, períodos e sublinhados. |
> | integraçãoServiceEnvironments/managedApis | ambiente de serviço de integração | 1-80 | Alfanuméricas, hífens, períodos e sublinhados. |
> | workflows | grupo de recursos | 1-80 | Alfanuméricas, hífens, sublinhados, períodos e parênteses. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | commitmentPlans | grupo de recursos | 1-260 | Não posso usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | webServices | grupo de recursos | 1-260 | Não posso usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |
> | workspaces | grupo de recursos | 1-260 | Não posso usar:<br>`<>*%&:?+/\\`<br><br>Não pode terminar com um espaço. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | workspaces | grupo de recursos | 3-33 | Alfanuméricas e hífens. |
> | espaços de trabalho / computação | workspace | 2-16 | Alfanuméricas e hífens. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | userAssignedIdentities | grupo de recursos | 3-128 | Alfanumérica, hífens e sublinhados<br><br>Comece com letra ou número. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | accounts | grupo de recursos | 1-98 (para nome do grupo de recursos e nome da conta) | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | mediaservices | grupo de recursos | 3-24 | Letras e números minúsculos. |
> | mediaservices / liveEvents | Serviço de mídia | 1-32 | Alfanuméricas e hífens.<br><br>Comece com alfanumérico. |
> | mediaservices / liveEvents / liveOutputs | Evento ao vivo | 1-256 | Alfanuméricas e hífens.<br><br>Comece com alfanumérico. |
> | mediaservices / streamingEndpoints | Serviço de mídia | 1-24 | Alfanuméricas e hífens.<br><br>Comece com alfanumérico. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | applicationGateways | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | applicationSecurityGroups | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | azureFirewalls | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Termine com alfanumérica ou sublinhada. |
> | bastiõesanfitriões | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | connections | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | dnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um período. Por exemplo, **contoso.com** tem 2 rótulos. | Cada rótulo pode conter alfanuméricas, sublinhadas e hífens.<br><br>Cada rótulo é separado por um período. |
> | expressRouteCircuits | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolíticas | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | firewallPolicies / ruleGroups | política de firewall | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | frontDoors | global | 5-64 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |
> | loadBalancers | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | loadBalancers / inboundNatRules | balanceador de carga | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualNetworkGateways | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkInterfaces | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkSecurityGroups / securityRules | grupo de segurança de rede | 1-80 |  Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | networkWatchers | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | privateDnsZones | grupo de recursos | 1-63 caracteres<br><br>2 a 34 rótulos<br><br>Cada rótulo é um conjunto de caracteres separados por um período. Por exemplo, **contoso.com** tem 2 rótulos. | Cada rótulo pode conter alfanuméricas, sublinhadas e hífens.<br><br>Cada rótulo é separado por um período. |
> | privateDnsZones / virtualNetworkLinks | zona dns privada | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPAddresses | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | publicIPPrefixes | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeFilters | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeFiltros / routeFilterRules | filtro de rota | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeTables | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | routeTables / rotas | route table | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | serviceEndpointPolicies | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | trafficmanagerprofiles | global | 1-63 | Alfanumérica, hífens e períodos.<br><br>Comece e termine com alfanumérico. |
> | virtualNetworkGateways | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualNetworks | grupo de recursos | 2-64 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | redes virtuais / subredes | rede virtual | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | redes virtuais / virtualNetworkPeerings | rede virtual | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | virtualWans | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnGateways | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnGateways / vpnConexões | gateway de VPN | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |
> | vpnSites | grupo de recursos | 1-80 | Alfanumérica, sublinhados, períodos e hífens.<br><br>Comece com alfanumérico. Fim alfanumérico ou sublinhado. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | namespaces | global | 6-50 | Alfanuméricas e hífens<br><br>Comece e termine com alfanumérico. |
> | namespaces / AuthorizationRules | namespace | 1-256 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece alfanumérico. |
> | namespaces / notificationHubs | namespace | 1-260 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece alfanumérico. |
> | namespaces / notificationHubs / AuthorizationRules | centro de notificação | 1-256 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece alfanumérico. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | grupo de recursos | 4-63 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |
> | workspaces | grupo de recursos | 4-63 | Alfanuméricas e hífens.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | solutions | workspace | N/D | Para soluções de autoria da Microsoft, o nome deve estar no padrão:<br>`SolutionType(WorkspaceName)`<br><br>Para soluções de autoria de terceiros, o nome deve estar no padrão:<br>`SolutionType[WorkspaceName]`<br><br>Por exemplo, um nome válido é:<br>`AntiMalware(contoso-IT)`<br><br>O tipo de solução é sensível a maiúsculas e minúsculas. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | dashboards | grupo de recursos | 3-160 | Alfanuméricas e hífens.<br><br>Para usar caracteres restritos, adicione uma tag chamada **hidden-title** com o nome do painel que você deseja usar. O portal exibe esse nome ao mostrar o painel. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Alfanuméricas e hífens.<br><br>Não pode começar com hífen. Não posso usar hífens consecutivos. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | Letras ou números minúsculos<br><br>Comece com letras minúsculas. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | vaults | grupo de recursos | 2-50 | Alfanuméricas e hífens.<br><br>Comece com a carta. |
> | cofres / backupPolíticas | cofre | 3-150 | Alfanuméricas e hífens.<br><br>Comece com a carta. Não pode acabar com hífen. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | namespaces | global | 6-50 | Alfanuméricas e hífens.<br><br>Comece com uma letra. Termine com uma letra ou número. |
> | namespaces / AuthorizationRules | namespace | 1-50 |  Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | namespaces / Conexões híbridas | namespace | 1-260 | Alfanuméricas, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | namespaces / HybridConnections/authorizationRules | conexão híbrida | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com alfanumérico. |
> | namespaces / WcfRelays | namespace | 1-260 | Alfanuméricas, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | namespaces / WcfRelays / autorizaçãoRegras | Relé Wcf | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Comece e termine com alfanumérico. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | deployments | grupo de recursos | 1-64 | Alfanumérica, sublinhados, parênteses, hífens e períodos. |
> | grupos de recursos | subscription | 1-90 | Alfanumérica, sublinhados, parênteses, hífens, períodos e caracteres unicodificados que correspondem à [documentação regex](/rest/api/resources/resourcegroups/createorupdate).<br><br>Não pode terminar com o período. |
> | Tagnames | recurso | 1-512 | Não posso usar:<br>`<>%&\?/` |
> | tagNomes / tagValues | nome da marca | 1-256 | Todos os caracteres. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | namespaces | global | 6-50 | Alfanuméricas e hífens.<br><br>Comece com uma letra. Termine com uma letra ou número.<br><br>Para obter mais informações, consulte [Criar namespace](/rest/api/servicebus/create-namespace). |
> | namespaces / AuthorizationRules | namespace | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Começar e terminar com alphnumeric. |
> | namespaces / desastreRecoveryConfigs | global | 6-50 | Alfanuméricas e hífens.<br><br>Comece com a carta. Termine com alfanumérico. |
> | namespaces / migraçãoConfigurações | namespace |  | Deve estar sempre **$default.** |
> | namespaces / filas | namespace | 1-260 | Alfanuméricas, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | namespaces / filas / autorizaçãoRegras | fila | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Começar e terminar com alphnumeric. |
> | namespaces / tópicos | namespace | 1-260 | Alfanuméricas, períodos, hífens, sublinhados e cortes.<br><br>Comece e termine com alfanumérico. |
> | namespaces / tópicos / autorizaçãoRegras | topic | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Começar e terminar com alphnumeric. |
> | namespaces / tópicos / assinaturas | topic | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Começar e terminar com alphnumeric. |
> | namespaces / tópicos / assinaturas / regras | subscription | 1-50 | Alfanuméricas, períodos, hífens e sublinhados.<br><br>Começar e terminar com alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | Letras minúsculas, números e hífens.<br><br>Comece com letras minúsculas. Termine com letra ou número minúsculo. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | signalR | global | 3-63 | Alfanuméricas e hífens.<br><br>Comece com a carta. Termine com letra ou número.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | Letras minúsculas, números e hífens.<br><br>Não pode começar ou terminar com hífen. |
> | servers | global | 1-63 | Letras minúsculas, números e hífens.<br><br>Não pode começar ou terminar com hífen. |
> | servidores /administradores | Servidor |  | Deve ser `ActiveDirectory`. |
> | servidores /bancos de dados | Servidor | 1-128 | Não posso usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com período ou espaço. |
> | servidores / bancos de dados / sincroniaGrupos | Banco de Dados | 1-150 | Alfanumérica, hífens e sublinhados. |
> | servidores / elásticosPools | Servidor | 1-128 | Não posso usar:<br>`<>*%&:\/?`<br><br>Não pode terminar com período ou espaço. |
> | servidores / failoverGroups | global | 1-63 | Letras minúsculas, números e hífens.<br><br>Não pode começar ou terminar com hífen. |
> | servidores / firewallRegras | Servidor | 1-128 | Não posso usar:<br>`<>*%&:;\/?`<br><br>Não pode terminar com o período. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | Letras e números minúsculos. |
> | armazenamentoContas / blobServices | do Azure |  | Deve ser `default`. |
> | armazenamentoContas / blobServices / contêineres | do Azure | 3-63 | Letras minúsculas, números e hífens.<br><br>Comece com letras ou número minúsculos. Não posso usar hífens consecutivos. |
> | armazenamentoContas / arquivosServiços | do Azure |  | Deve ser `default`. |
> | armazenamentoContas / arquivoServiços / ações | do Azure | 3-63 | Letras minúsculas, números e hífens.<br><br>Não pode começar ou terminar com hífen. Não posso usar hífens consecutivos. |
> | armazenamentoContas/gerenciamentoPolíticas | do Azure |  | Deve ser `default`. |
> | blob | contêiner | 1-1024 | Quaisquer caracteres de URL, sensíveis a maiúsculas e minúsculas |
> | fila | do Azure | 3-63 | Letras minúsculas, números e hífens.<br><br>Não pode começar ou terminar com hífen. Não posso usar hífens consecutivos. |
> | tabela | do Azure | 3-63 | Alfanumérica.<br><br>Comece com a carta. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | storageSyncServices | grupo de recursos | 1-260 | Alfanuméricas, espaços, períodos, hífens e sublinhados.<br><br>Não pode terminar com período ou espaço. |
> | armazenamentoSyncServices / syncGroups | serviço de sincronização de armazenamento | 1-260 | Alfanuméricas, espaços, períodos, hífens e sublinhados.<br><br>Não pode terminar com período ou espaço. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | managers | grupo de recursos | 2-50 | Alfanuméricas e hífens.<br><br>Comece com a carta. Termine com alfanumérico. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | streamingjobs | grupo de recursos | 3-63 | Alfanumérica, hífens e sublinhados. |
> | streamingjobs / funções | trabalho de streaming | 3-63 | Alfanumérica, hífens e sublinhados. |
> | streamingjobs / entradas | trabalho de streaming | 3-63 | Alfanumérica, hífens e sublinhados. |
> | streamingjobs / saídas | trabalho de streaming | 3-63 | Alfanumérica, hífens e sublinhados. |
> | streamingjobs / transformações | trabalho de streaming | 3-63 | Alfanumérica, hífens e sublinhados. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | environments | grupo de recursos | 1-90 | Não posso usar:<br>`'<>%&:\?/#` |
> | ambientes / políticas de acesso | environment | 1-90 | Não posso usar:<br> `'<>%&:\?/#` |
> | ambientes / eventSources | environment | 1-90 | Não posso usar:<br>`'<>%&:\?/#` |
> | ambientes / referênciaConjuntos de dados | environment | 3-63 | Alfanumérica |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entidade | Escopo | Comprimento | Caracteres válidos |
> | --- | --- | --- | --- |
> | serverfarms | grupo de recursos | 1-40 | Alfanuméricas e hífens. |
> | sites | global | 2-60 | Contém alfanuméricas e hífens.<br><br>Não pode começar ou terminar com hífen. |
> | sites / slots | site | 2-59 | Alfanuméricas e hífens. |

## <a name="next-steps"></a>Próximas etapas

Para obter recomendações sobre como nomear recursos, consulte [Pronto: Convenções recomendadas de nomeação e marcação](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
