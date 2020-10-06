---
title: Solução de problemas comuns
description: Saiba como solucionar problemas com a criação de definições de política, o SDK e o complemento para kubernetes.
ms.date: 10/05/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6026dc75187c8a70203a2484380eed70d519599d
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743430"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Solucionar erros usando Azure Policy

Você pode encontrar erros ao criar definições de política, trabalhar com o SDK ou configurar o [Azure Policy para](../concepts/policy-for-kubernetes.md) o complemento kubernetes. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrar detalhes do erro

O local dos detalhes do erro depende da ação que causa o erro.

- Ao trabalhar com uma política personalizada, experimente na portal do Azure para obter comentários sobre o esquema ou revisar os dados de [conformidade](../how-to/get-compliance-data.md) resultantes para ver como os recursos foram avaliados.
- Ao trabalhar com vários SDK, o SDK fornece detalhes sobre o motivo da falha da função.
- Ao trabalhar com o complemento do kubernetes, comece com o [registro em log](../concepts/policy-for-kubernetes.md#logging) no cluster.

## <a name="general-errors"></a>Erros gerais

### <a name="scenario-alias-not-found"></a>Cenário: alias não encontrado

#### <a name="issue"></a>Problema

Azure Policy usa [aliases](../concepts/definition-structure.md#aliases) para mapear para Azure Resource Manager Propriedades.

#### <a name="cause"></a>Causa

Um alias incorreto ou não existente é usado em uma definição de política.

#### <a name="resolution"></a>Resolução

Primeiro, valide se a propriedade do Gerenciador de recursos tem um alias. Use a [extensão Azure Policy para Visual Studio Code](../how-to/extension-for-vscode.md), o [grafo de recursos do Azure](../../resource-graph/samples/starter.md#distinct-alias-values)ou o SDK para pesquisar aliases disponíveis. Se o alias de uma propriedade do Gerenciador de recursos não existir, crie um tíquete de suporte.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Cenário: detalhes de avaliação não atualizados

#### <a name="issue"></a>Problema

Um recurso está no estado "não iniciado" ou os detalhes de conformidade não são atuais.

#### <a name="cause"></a>Causa

Uma nova atribuição de política ou iniciativa leva cerca de 30 minutos para ser aplicada. Recursos novos ou atualizados dentro do escopo de uma atribuição existente ficam disponíveis cerca de 15 minutos depois. Uma verificação de conformidade padrão ocorre A cada 24 horas. Para obter mais informações, consulte os [gatilhos de avaliação](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Resolução

Primeiro, aguarde o tempo apropriado para que uma avaliação seja concluída e os resultados de conformidade sejam disponibilizados no portal do Azure ou no SDK. Para iniciar uma nova verificação de avaliação com Azure PowerShell ou API REST, consulte [verificação de avaliação sob demanda](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-not-as-expected"></a>Cenário: conformidade não conforme o esperado

#### <a name="issue"></a>Problema

Um recurso não está no estado de avaliação, seja _compatível_ ou _sem conformidade_, o que é esperado para esse recurso.

#### <a name="cause"></a>Causa

O recurso não está no escopo correto para a atribuição de política ou a definição de política não funciona conforme o esperado.

#### <a name="resolution"></a>Resolução

Siga estas etapas para solucionar problemas de definição de política:

1. Primeiro, aguarde o tempo apropriado para que uma avaliação seja concluída e os resultados de conformidade sejam disponibilizados no portal do Azure ou no SDK. Para iniciar uma nova verificação de avaliação com Azure PowerShell ou API REST, consulte [verificação de avaliação sob demanda](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verifique se os parâmetros de atribuição e o escopo de atribuição estão definidos corretamente.
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - Mode ' all' para todos os tipos de recurso.
   - Modo ' indexado ' se a definição de política verificar marcas ou local.
1. Verifique se o escopo do recurso não é [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento](../concepts/exemption-structure.md).
1. Se a conformidade de uma atribuição de política mostrar `0/0` recursos, nenhum recurso foi determinado para ser aplicável dentro do escopo de atribuição. Verifique a definição de política e o escopo de atribuição.
1. Para um recurso sem conformidade que deveria estar em conformidade, verifique [determinando os motivos da não conformidade](../how-to/determine-non-compliance.md). A comparação da definição com o valor da propriedade avaliada indica por que um recurso não está em conformidade.
   - Se o **valor de destino** estiver errado, revise a definição de política.
   - Se o **valor atual** estiver errado, valide a carga do recurso por meio de `resources.azure.com` .
1. Verifique [a solução de problemas: imposição não conforme o esperado](#scenario-enforcement-not-as-expected) para outros problemas comuns e soluções.

Se você ainda tiver um problema com sua definição de política interna duplicada e personalizada ou definição personalizada, crie um tíquete de suporte sob a **criação de uma política** para encaminhar o problema corretamente.

### <a name="scenario-enforcement-not-as-expected"></a>Cenário: imposição não conforme esperado

#### <a name="issue"></a>Problema

Um recurso que deve ser acionado pelo Azure Policy não é e não há nenhuma entrada no log de [atividades do Azure](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Causa

A atribuição de política foi configurada para [imposiçãomode](../concepts/assignment-structure.md#enforcement-mode) de _desabilitado_. Enquanto o modo de imposição está desabilitado, o efeito de política não é imposto e não há nenhuma entrada no log de atividades.

#### <a name="resolution"></a>Resolução

Siga estas etapas para solucionar a imposição da sua atribuição de política:

1. Primeiro, aguarde o tempo apropriado para que uma avaliação seja concluída e os resultados de conformidade sejam disponibilizados no portal do Azure ou no SDK. Para iniciar uma nova verificação de avaliação com Azure PowerShell ou API REST, consulte [verificação de avaliação sob demanda](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verifique se os parâmetros de atribuição e o escopo de atribuição estão definidos corretamente e se **imimport** está _habilitado_. 
1. Verifique o [modo de definição de política](../concepts/definition-structure.md#mode):
   - Mode ' all' para todos os tipos de recurso.
   - Modo ' indexado ' se a definição de política verificar marcas ou local.
1. Verifique se o escopo do recurso não é [excluído](../concepts/assignment-structure.md#excluded-scopes) ou [isento](../concepts/exemption-structure.md).
1. Verifique se a carga do recurso corresponde à lógica da política. Isso pode ser feito [capturando um rastreamento Har](../../../azure-portal/capture-browser-trace.md) ou revisando as propriedades do modelo ARM.
1. Verifique [a solução de problemas: conformidade não conforme o esperado](#scenario-compliance-not-as-expected) para outros problemas e soluções comuns.

Se você ainda tiver um problema com sua definição de política interna duplicada e personalizada ou definição personalizada, crie um tíquete de suporte sob a **criação de uma política** para encaminhar o problema corretamente.

### <a name="scenario-denied-by-azure-policy"></a>Cenário: negado por Azure Policy

#### <a name="issue"></a>Problema

A criação ou atualização de um recurso foi negada.

#### <a name="cause"></a>Causa

Uma atribuição de política para o escopo no qual seu recurso novo ou atualizado está atende aos critérios de uma definição de política com um efeito de [negação](../concepts/effects.md#deny) . Recursos reuniões essas definições são impedidas de serem criadas ou atualizadas.

#### <a name="resolution"></a>Resolução

A mensagem de erro de uma atribuição de política de negação inclui a definição de política e as IDs de atribuição de política. Se as informações de erro na mensagem forem perdidas, elas também estarão disponíveis no [log de atividades](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Use essas informações para obter mais detalhes para entender as restrições de recursos e ajustar as propriedades de recurso em sua solicitação para corresponder os valores permitidos.

## <a name="template-errors"></a>Erros de modelo

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Cenário: funções com suporte de política processadas pelo modelo

#### <a name="issue"></a>Problema

O Azure Policy dá suporte a várias funções e funções de modelo de Azure Resource Manager (modelo ARM) que estão disponíveis somente em uma definição de política. O Gerenciador de recursos processa essas funções como parte de uma implantação em vez de como parte de uma definição de política.

#### <a name="cause"></a>Causa

O uso de funções com suporte, como `parameter()` ou `resourceGroup()` , resulta no resultado processado da função no momento da implantação em vez de deixar a função para a definição de política e o mecanismo de Azure Policy para processar.

#### <a name="resolution"></a>Resolução

Para passar uma função por para fazer parte de uma definição de política, escape toda a cadeia de caracteres com a `[` aparência da propriedade `[[resourceGroup().tags.myTag]` . O caractere de escape faz com que o Gerenciador de recursos trate o valor como uma cadeia de caracteres ao processar o modelo. Azure Policy, em seguida, coloca a função na definição de política, permitindo que ela seja dinâmica conforme o esperado. Para obter mais informações, consulte [sintaxe e expressões em modelos de Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Erros de instalação de complemento

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Cenário: a instalação usando o gráfico Helm falha na senha

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha com uma das seguintes mensagens:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Causa

A senha gerada inclui uma vírgula ( `,` ) em que o gráfico Helm está sendo dividido.

#### <a name="resolution"></a>Resolução

Escape a vírgula ( `,` ) no valor da senha ao executar `helm install azure-policy-addon` com uma barra invertida ( `\` ).

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Cenário: a instalação usando o gráfico Helm falha porque o nome já existe

#### <a name="issue"></a>Problema

O `helm install azure-policy-addon` comando falha com a seguinte mensagem:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Causa

O gráfico Helm com o nome `azure-policy-addon` já foi instalado ou parcialmente instalado.

#### <a name="resolution"></a>Resolução

Siga as instruções para [remover o Azure Policy para o complemento kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on)e execute o `helm install azure-policy-addon` comando novamente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas por meio [do Microsoft Q&a](/answers/topics/azure-policy.html).
- Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
