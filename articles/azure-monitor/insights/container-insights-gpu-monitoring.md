---
title: Configure o monitoramento da GPU com o Monitor Azure para contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o monitoramento de clusters Kubernetes com os nós habilitados para GPU NVIDIA e AMD com o Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373304"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Configure o monitoramento da GPU com o Monitor Azure para contêineres

A partir da versão do agente *ciprod03022019*, o monitor Azure para contêiners agente integrado agora suporta o uso de GPU (unidades de processamento gráfico) em nódulos de cluster Kubernetes com reconhecimento de GPU e monitora pods/contêineres solicitando e usando recursos de GPU.

## <a name="supported-gpu-vendors"></a>Fornecedores de GPU suportados

O Azure Monitor for Containers suporta o monitoramento de clusters de GPU dos seguintes fornecedores de GPU:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

O Monitor Do Azure para contêineres começa automaticamente a monitorar o uso de GPU em nós e a GPU solicitando pods e cargas de trabalho coletando as seguintes métricas em intervalos de 60 seg e armazenando-as na tabela **InsightMetrics:**

|Nome da métrica |Dimensão métrica (tags) |Descrição |
|------------|------------------------|------------|
|contêinerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Porcentagem de tempo durante o período de amostra passado (60 segundos) durante o qual a GPU estava ocupada/ativamente processando para um contêiner. Ciclo de trabalho é um número entre 1 e 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Cada contêiner pode especificar limites como uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU. |
|contêinerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Cada contêiner pode solicitar uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU.|
|contêinerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantidade de memória GPU em bytes disponíveis para uso em um recipiente específico. |
|contêinerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantidade de memória GPU em bytes usados por um recipiente específico. |
|nodeGpuLocável |container.azm.ms/clusterId, container.azm.ms/clusterName, GPUVendor |Número de GPUs em um nó que pode ser usado por Kubernetes. |
|nóGpuCapacidade |container.azm.ms/clusterId, container.azm.ms/clusterName, GPUVendor |Número total de GPUs em um nó. |

## <a name="gpu-performance-charts"></a>Gráficos de desempenho de GPU 

O Azure Monitor para contêineres inclui gráficos pré-configurados para as métricas listadas anteriormente na tabela como uma pasta de trabalho de GPU para cada cluster. Você pode encontrar a **GPU** de nó da pasta de trabalho gpu diretamente de um cluster AKS selecionando **Livros** de Trabalho no painel esquerdo e **na** lista de itens de visão de trabalho no Insight.

## <a name="next-steps"></a>Próximas etapas

- Veja [Usar GPUs para cargas de trabalho intensivas em computação no Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS) para aprender como implantar um cluster AKS que inclui nós habilitados para GPU.

- Saiba mais sobre [GPU Otimizada VM SKUs no Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Revise [o suporte à GPU em Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) para saber mais sobre o suporte experimental do Kubernetes para gerenciar GPUs em um ou mais nós em um cluster.