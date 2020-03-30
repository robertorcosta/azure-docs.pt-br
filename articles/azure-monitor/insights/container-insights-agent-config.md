---
title: Configure o Monitor Azure para coleta de dados de agentes de contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o Monitor Azure para agente de contêineres para controlar a coleta de registros de variáveis de ambiente e stdout/stdout.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933019"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configure a coleta de dados do agente para o Monitor Azure para contêineres

O Monitor Azure para contêineres coleta variáveis stdout, stderr e ambientais de cargas de trabalho de contêineres implantados em clusters Kubernetes gerenciados do agente contêiner. Você pode configurar as configurações de coleta de dados do agente criando um Kubernetes ConfigMaps personalizado para controlar essa experiência. 

Este artigo demonstra como criar o ConfigMap e configurar a coleta de dados com base em suas necessidades.

>[!NOTE]
>Para o Azure Red Hat OpenShift, um arquivo ConfigMap de modelo é criado no espaço *de nome de registro de openshift-azure.* 
>

## <a name="configmap-file-settings-overview"></a>Visão geral das configurações do arquivo ConfigMap

Um arquivo ConfigMap de modelo é fornecido que permite editá-lo facilmente com suas personalizações sem ter que criá-lo do zero. Antes de começar, você deve revisar a documentação do Kubernetes sobre [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e familiarizar-se com como criar, configurar e implantar o ConfigMaps. Isso permitirá que você filtre stderr e stdout por namespace ou em todo o cluster, e variáveis de ambiente para qualquer contêiner executado em todos os pods/nós do cluster.

>[!IMPORTANT]
>A versão mínima do agente suportada para coletar variáveis stdout, stderr e ambientais de cargas de trabalho de contêineres é ciprod06142019 ou posterior. Para verificar a versão do agente, na guia **Nó,** selecione um nó e no painel de propriedades o valor da nota da propriedade **Agent Image Tag.** Para obter informações adicionais sobre as versões do agente e o que está incluído em cada versão, consulte [notas de liberação do agente](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Configurações de coleta de dados

A seguir estão as configurações que podem ser configuradas para controlar a coleta de dados.

|Chave |Tipo de dados |Valor |Descrição |
|----|----------|------|------------|
|`schema-version` |Corda (sensível ao caso) |v1 |Esta é a versão de esquema usada pelo agente ao analisar este ConfigMap. Aversão ao esquema atualmente suportada é v1. A modificação desse valor não é suportada e será rejeitada quando o ConfigMap for avaliado.|
|`config-version` |String | | Suporta a capacidade de acompanhar a versão deste arquivo de configuração no sistema/repositório de controle de origem. Os caracteres máximos permitidos são 10, e todos os outros personagens são truncados. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true ou false | Isso controla se a coleta de troncos de contêineres stdout estiver habilitada. Quando definidos `true` e sem namespaces são excluídos para a coleta de troncos stdout (configuração`log_collection_settings.stdout.exclude_namespaces` abaixo), os logs stdout serão coletados de todos os recipientes em todos os pods/nós do cluster. Se não for especificado no ConfigMaps, o valor padrão será `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Matriz separada por comma |Os espaços de nomes array of Kubernetes para os quais os logs stdout não serão coletados. Esta configuração só `log_collection_settings.stdout.enabled` é `true`eficaz se estiver definida como . Se não especificado no ConfigMap, `exclude_namespaces = ["kube-system"]`o valor padrão será .|
|`[log_collection_settings.stderr] enabled =` |Boolean | true ou false |Isso controla se a coleta de troncos de contêineres stderr estiver habilitada. Quando definidos `true` e sem namespaces são excluídos para a coleta de troncos stdout (configuração),`log_collection_settings.stderr.exclude_namespaces` os logs stderr serão coletados de todos os recipientes em todos os pods/nós do cluster. Se não for especificado no ConfigMaps, o valor padrão será `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Matriz separada por comma |Conjunto de nomes Kubernetes para os quais os logs stderr não serão coletados. Esta configuração só `log_collection_settings.stdout.enabled` é `true`eficaz se estiver definida como . Se não especificado no ConfigMap, `exclude_namespaces = ["kube-system"]`o valor padrão será . |
| `[log_collection_settings.env_var] enabled =` |Boolean | true ou false | Esta configuração controla a coleta de variáveis de ambiente em `enabled = true` todos os pods/nós no cluster e é padrão quando não especificado no ConfigMaps. Se a coleta de variáveis de ambiente estiver globalmente ativada, você poderá `AZMON_COLLECT_ENV` desativá-la para um contêiner específico definindo a variável de ambiente **como Falsa** com uma configuração de arquivo Docker ou no [arquivo de configuração do Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) a seção **env:.** Se a coleta de variáveis de ambiente for globalmente desativada, então você não poderá habilitar a coleta para um contêiner específico (ou seja, a única substituição que pode ser aplicada no nível do contêiner é desativar a coleta quando já estiver ativada globalmente.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Boolean | true ou false | Esta configuração controla o enriquecimento do registro de contêineres para preencher os valores de propriedade Nome e Imagem para cada registro de registro gravado na tabela ContainerLog para todos os logs de contêiner no cluster. Ele é `enabled = false` padrão quando não especificado no ConfigMap. |

ConfigMaps é uma lista global e só pode haver um ConfigMap aplicado ao agente. Você não pode ter outro ConfigMaps anulando as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configure e implante o ConfigMaps

Execute as seguintes etapas para configurar e implantar o arquivo de configuração ConfigMap no cluster.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o arquivo de yaml ConfigMap e salve-o como container-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Essa etapa não é necessária ao trabalhar com o Azure Red Hat OpenShift, uma vez que o modelo ConfigMap já existe no cluster.

2. Edite o arquivo de yaml ConfigMap com suas personalizações para coletar variáveis stdout, stderr e/ou ambientais. Se você estiver editando o arquivo de yaml ConfigMap para `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Azure Red Hat OpenShift, primeiro execute o comando para abrir o arquivo em um editor de texto.

    - Para excluir espaços de nome específicos para a coleta de log stdout, configure a chave/valor usando o seguinte exemplo: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Para desativar a coleta de variáveis de ambiente `[log_collection_settings.env_var] enabled = true` para um contêiner específico, defina a chave/valor para permitir a coleta variável globalmente e siga as etapas [aqui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) para concluir a configuração do contêiner específico.
    
    - Para desativar o cluster de coleta de log stderr, configure `[log_collection_settings.stderr] enabled = false`a chave/valor usando o seguinte exemplo: .

3. Para clusters diferentes do Azure Red Hat OpenShift, crie ConfigMap `kubectl apply -f <configmap_yaml_file.yaml>` executando o seguinte comando kubectl: em clusters diferentes do Azure Red Hat OpenShift. 
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Para o Azure Red Hat OpenShift, salve suas alterações no editor.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todas as cápsulas omsagent no cluster serão reiniciadas. A reinicialização é uma reinicialização para todas as cápsulas omsagent, nem todas reiniciadas ao mesmo tempo. Quando as reinicializações são concluídas, uma mensagem é exibida semelhante `configmap "container-azm-ms-agentconfig" created`à seguinte e inclui o resultado: .

## <a name="verify-configuration"></a>Verificar a configuração

Para verificar se a configuração foi aplicada com sucesso a um cluster diferente do Azure Red `kubectl logs omsagent-fdf58 -n=kube-system`Hat OpenShift, use o seguinte comando para rever os logs de um pod de agente: . Se houver erros de configuração dos pods omsagent, a saída mostrará erros semelhantes aos seguintes:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Erros relacionados à aplicação de alterações de configuração também estão disponíveis para revisão. As seguintes opções estão disponíveis para executar a solução adicional de problemas de alterações de configuração:

- A partir de um agente `kubectl logs` pod logs usando o mesmo comando. 

    >[!NOTE]
    >Este comando não é aplicável ao cluster Azure Red Hat OpenShift.
    > 

- De registros ao vivo. Os registros ao vivo mostram erros semelhantes aos seguintes:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- A partir da tabela **KubeMonAgentEvents** em seu espaço de trabalho Log Analytics. Os dados são enviados a cada hora com a gravidade do *erro* para erros de configuração. Se não houver erros, a entrada na tabela terá dados com *informações*de gravidade, que não reportam erros. A propriedade **Tags** contém mais informações sobre o pod e o ID do contêiner em que ocorreu o erro e também a primeira ocorrência, última ocorrência e contagem na última hora.

- Com o Azure Red Hat OpenShift, verifique os registros omsagent pesquisando a tabela **ContainerLog** para verificar se a coleta de log de registro openshift-azure está ativada.

Depois de corrigir os erros no ConfigMap em clusters diferentes do Azure Red Hat OpenShift, salve o arquivo `kubectl apply -f <configmap_yaml_file.yaml`yaml e aplique o ConfigMaps atualizado executando o comando: . Para o Azure Red Hat OpenShift, edite e salve o ConfigMaps atualizado executando o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Aplicando configMap atualizado

Se você já implantou um ConfigMap em clusters diferentes do Azure Red Hat OpenShift e deseja atualizá-lo com uma configuração mais nova, `kubectl apply -f <configmap_yaml_file.yaml`você pode editar o arquivo ConfigMap que você usou anteriormente e, em seguida, aplicar usando o mesmo comando de antes, . Para o Azure Red Hat OpenShift, edite e salve o ConfigMaps atualizado executando o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todas as cápsulas omsagent no cluster serão reiniciadas. A reinicialização é uma reinicialização para todas as cápsulas omsagent, nem todas reiniciadas ao mesmo tempo. Quando as reinicializações são concluídas, uma mensagem é exibida semelhante `configmap "container-azm-ms-agentconfig" updated`à seguinte e inclui o resultado: .

## <a name="verifying-schema-version"></a>Verificando a versão do esquema

As versões de esquema de configuração suportadas estão disponíveis como anotação de pod (versões de esquema) no pod omsagent. Você pode vê-los com o seguinte comando kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

A saída mostrará semelhante ao seguinte com as versões do esquema de anotação:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Próximas etapas

- O Monitor Azure para contêineres não inclui um conjunto predefinido de alertas. Revise os [alertas de desempenho criar com o Azure Monitor para contêineres](container-insights-alerts.md) para aprender como criar alertas recomendados para alta utilização de CPU e memória para suportar seus DevOps ou processos e procedimentos operacionais.

- Com o monitoramento habilitado para coletar a utilização de recursos e saúde de seu AKS ou cluster híbrido e cargas de trabalho em execução neles, aprenda [a usar o](container-insights-analyze.md) Monitor Azure para contêineres.

- Exibir [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos pré-definidos para avaliar ou personalizar para alertar, visualizar ou analisar seus clusters.
