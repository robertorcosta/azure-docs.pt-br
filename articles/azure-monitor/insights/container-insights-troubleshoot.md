---
title: Como solucionar problemas do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode solucionar e resolver problemas com o Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403376"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Solução de problemas do Azure Monitor para contêineres

Quando você configurar o monitoramento do cluster do AKS (Serviço de Kubernetes do Azure) com o Azure Monitor para contêineres, poderá encontrar um problema que impede a coleta de dados ou relatórios de status. Este artigo detalha alguns problemas comuns e etapas de solução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante a operação de onboarding ou atualização
Ao ativar o Azure Monitor para contêineres ou atualizar um cluster para suportar métricas de coleta, você pode receber um erro semelhante ao seguinte - *O cliente <> de identidade do usuário' com o id de objeto '<> do usuário' não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' sobre o escopo*

Durante o processo de onboarding ou atualização, a atribuição de **atribuição de função Monitoring Metrics Publisher** é tentada no recurso de cluster. O usuário que está adiando o processo para habilitar o Azure Monitor para contêineres ou a atualização para suportar a coleta de métricas deve ter acesso à permissão **Microsoft.Authorization/roleAssignments/write** no escopo de recursos do cluster AKS. Somente os membros das funções **incorporadas do Proprietário** e **do Administrador de Acesso ao Usuário** têm acesso a essa permissão. Se suas políticas de segurança exigirem atribuir permissões de nível granular, recomendamos que você visualize [funções personalizadas](../../role-based-access-control/custom-roles.md) e as atribua aos usuários que a necessitam. 

Você também pode conceder manualmente essa função a partir do portal Azure, realizando as seguintes etapas:

1. Faça login no [portal Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**, encontrado no canto superior esquerdo. Na lista de recursos, digite **Kubernetes**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Azure Kubernetes**.
3. Na lista de clusters Kubernetes, selecione um da lista.
2. No menu à esquerda, clique em **Acessar controle (IAM)**.
3. Selecione **+ Adicione** para adicionar uma atribuição de função e selecione a função **Monitoring Metrics Publisher** e o tipo de caixa **Select** **AKS** para filtrar os resultados apenas nos princípios de serviço de clusters definidos na assinatura. Selecione o da lista específica para esse cluster.
4. Selecione **Salvar** para finalizar a atribuição da função. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>O Azure Monitor para contêineres está habilitado, mas não relata nenhuma informação
Se o Azure Monitor for habilitado e configurado com sucesso, mas não puder visualizar informações de status ou nenhum resultado for devolvido de uma consulta de log, você diagnosticará o problema seguindo estas etapas: 

1. Verifique o status do agente executando o comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o status de implantação com a versão do agente *06072018* ou posterior usando o comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, que indica que ela foi implantada corretamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique o status do pod para verificar se ele está em execução usando o comando: `kubectl get pods --namespace=kube-system`

    A saída deve ser semelhante ao seguinte exemplo, com o status de *Executando* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Verifique os logs de agente. Quando o agente em contêineres é implantado, ele executa uma verificação rápida executando comandos do OMI e mostra a versão do agente e do provedor. 

5. Para verificar se o agente foi implantado com sucesso, execute o comando:`kubectl logs omsagent-484hw --namespace=kube-system`

    O status deve ser semelhante a este exemplo:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Mensagens de erro

A tabela abaixo resume os erros conhecidos que você pode encontrar ao usar o Monitor do Azure para contêineres.

| Mensagens de erro  | Ação |  
| ---- | --- |  
| Mensagem de erro `No data for selected filters`  | Pode levar algum tempo para estabelecer o fluxo de dados de monitoramento para clusters recém-criados. Aguarde pelo menos 10 a 15 minutos para que os dados apareçam para o seu cluster. |   
| Mensagem de erro `Error retrieving data` | Enquanto o cluster Azure Kubernetes Service está configurando para monitoramento de saúde e desempenho, uma conexão é estabelecida entre o cluster e o espaço de trabalho do Azure Log Analytics. Um espaço de trabalho do Log Analytics é usado para armazenar todos os dados de monitoramento do cluster. Esse erro pode ocorrer quando o espaço de trabalho do Log Analytics foi excluído. Verifique se o espaço de trabalho foi excluído e, se foi, você precisará reativar o monitoramento do seu cluster com o Azure Monitor para contêineres e especificar um espaço de trabalho existente ou criar um novo espaço de trabalho. Para reativar, você precisará [desativar](container-insights-optout.md) o monitoramento do cluster e [ativar](container-insights-enable-new-cluster.md) o Monitor Azure para contêineres novamente. |  
| `Error retrieving data` Depois de adicionar o Azure Monitor para contêineres por meio da cli do az aks | Ao habilitar `az aks cli`o monitoramento usando o Monitor Azure para contêineres pode não ser implantado corretamente. Verifique se a solução está implantada. Para fazer isso, vá para o espaço de trabalho do Log Analytics e veja se a solução está disponível selecionando **Soluções** no painel à esquerda. Para resolver esse problema, você precisará reimplantar a solução seguindo as instruções em [como implantar o Monitor do Azure para contêineres](container-insights-onboard.md) |  

Para ajudar a diagnosticar o problema, fornecemos um script de solução de problemas disponível [aqui](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>O Monitor Azure para o agente de contêineres ReplicaSet Pods não está programado em cluster kubernetes não-Azure

O Azure Monitor para o agente de contêineres ReplicaSet Pods tem uma dependência dos seguintes seletores de nó nos áldenos do trabalhador (ou agente) para o agendamento:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se os nós do trabalhador não tiverem etiquetas de nó anexadas, os pods do agente ReplicaSet não serão agendados. Consulte [Kubernetes para atribuir aos seletores de etiqueta seletores](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) instruções sobre como anexar o rótulo.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Os gráficos de desempenho não mostram CPU ou memória de nós e recipientes em um cluster não-Azure

O Azure Monitor para agente de contêineres Pods usa o ponto final do cAdvisor no agente de nó para coletar as métricas de desempenho. Verifique se o agente contêiner no nó `cAdvisor port: 10255` está configurado para permitir que sejam abertos em todos os nós do cluster para coletar métricas de desempenho.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Cluster Kubernetes não-Azure não está sendo exibido no Azure Monitor para contêineres

Para visualizar o cluster não-Azure Kubernetes no Azure Monitor para contêineres, é necessário acessar o Log Analytics que suporta esse Insight e no recurso de solução Container Insights **ContainerInsights *(workspace).***

## <a name="next-steps"></a>Próximas etapas

Com o monitoramento habilitado para capturar métricas de integridade para os nós de cluster do AKS e pods, essas métricas de integridade estão disponíveis no portal do Azure. Para saber como usar o Azure Monitor para contêineres, veja [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).
