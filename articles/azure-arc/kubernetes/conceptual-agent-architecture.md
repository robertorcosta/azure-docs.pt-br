---
title: Arquitetura do agente kubernetes habilitado para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral da arquitetura dos agentes kubernetes habilitados para o Azure Arc
keywords: Kubernetes, Arc, Azure, contêineres
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561299"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Arquitetura do agente kubernetes habilitado para Arc do Azure

O [kubernetes](https://kubernetes.io/) pode ser usado para implantar cargas de trabalho em contêineres em ambientes híbridos e de várias nuvens de maneira consistente. O kubernetes habilitado para Arc do Azure pode ser usado como um plano de controle centralizado para gerenciar de forma consistente a política, a governança e a segurança entre esses ambientes heterogêneos. Esse artigo fornece:

* Uma visão geral da arquitetura de conexão de um cluster com o Azure Arc.
* O padrão de conectividade seguido por agentes.
* Uma descrição dos dados trocados entre o ambiente de cluster e o Azure.

## <a name="deploy-agents-to-your-cluster"></a>Implantar agentes no cluster

A maioria dos data centers locais impõe regras de rede estritas que impedem a comunicação de entrada no firewall usado no limite da rede. O kubernetes habilitado para Arc do Azure funciona com essas restrições habilitando apenas pontos de extremidade de egresso seletivos para comunicação de saída e não exigindo nenhuma porta de entrada no firewall. Os agentes kubernetes habilitados para o Azure Arc iniciam as conexões de saída.

![Visão geral da arquitetura](./media/architectural-overview.png)

Conecte um cluster ao arco do Azure usando as seguintes etapas:

1. Crie um cluster kubernetes em sua escolha de infraestrutura (VMware vSphere, Amazon Web Services, Google Cloud Platform, etc.). 

    > [!NOTE]
    > Os clientes são obrigados a criar e gerenciar o ciclo de vida do próprio cluster kubernetes, pois o Arc habilitado do Azure kubernetes atualmente dá suporte apenas à anexação de clusters kubernetes existentes ao Azure Arc.  

1. Inicie o registro de arco do Azure para seu cluster usando CLI do Azure.
    * CLI do Azure usa Helm para implantar o gráfico Helm do agente no cluster.
    * Os nós de cluster iniciam uma comunicação de saída para o [registro de contêiner da Microsoft](https://github.com/microsoft/containerregistry) e efetuam pull das imagens necessárias para criar os seguintes agentes no `azure-arc` namespace:

        | Agente | Descrição |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | O kubernetes habilitado para Arc do Azure atualmente dá suporte apenas a [identidades atribuídas do sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). o clusteridentityoperator faz a primeira comunicação de saída necessária para buscar o certificado MSI (identidade de serviço gerenciado) usado por outros agentes para comunicação com o Azure. |
        | `deployment.apps/config-agent` | Inspeciona o cluster conectado para obter os recursos de configuração de controle do código-fonte aplicados no cluster e atualiza o estado de conformidade |
        | `deployment.apps/controller-manager` | Um operador de operadores que orquestra as interações entre os componentes Arc do Azure |    
        | `deployment.apps/metrics-agent` | Coleta métricas de outros agentes ARC para garantir que esses agentes estejam apresentando um desempenho ideal |
        | `deployment.apps/cluster-metadata-operator` | Coleta metadados do cluster-versão do cluster, contagem de nós e versão do agente Arc do Azure |
        | `deployment.apps/resource-sync-agent` | Sincroniza os metadados de cluster mencionados acima para o Azure |
        | `deployment.apps/flux-logs-agent` | Coleta logs dos operadores de fluxo implantados como parte da configuração de controle do código-fonte |
    
1. Depois que todos os pods do agente kubernetes habilitados para o Azure Arc entrarem em `Running` estado, verifique se o cluster está conectado ao arco do Azure. Você deve ver:
    * Um recurso kubernetes habilitado para o Azure Arc no [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Esse recurso é acompanhado no Azure como uma projeção do cluster kubernetes gerenciado pelo cliente, não o próprio cluster kubernetes propriamente dito.
    * Os metadados do cluster, como a versão kubernetes, a versão do agente e o número de nós, são exibidos no recurso kubernetes habilitado para Arc do Azure como metadados.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Troca de dados entre o ambiente de cluster e o Azure

| Tipo de dados | Cenário | Modo de comunicação |
| --------- | -------- | ------------------ |
| Versão do cluster do Kubernetes | Metadados do cluster | Pushes do agente para o Azure |
| Número de nós no cluster | Metadados do cluster | Pushes do agente para o Azure |
| Versão do agente | Metadados do cluster | Pushes do agente para o Azure |
| Tipo de distribuição kubernetes | Metadados do cluster | CLI do Azure envios por push para o Azure |
| Tipo de infraestrutura (AWS/GCP/vSphere/...) | Metadados do cluster | CLI do Azure envios por push para o Azure |
| contagem de vCPU de nós no cluster | Cobrança | CLI do Azure envios por push para o Azure |
| Pulsação do agente | Integridade de recursos | Pushes do agente para o Azure |
| Consumo de recursos (memória/CPU) por agentes | Diagnóstico e suporte | Pushes do agente para o Azure |
| Logs de todos os contêineres de agente | Diagnóstico e suporte | Pushes do agente para o Azure |
| Disponibilidade de atualização do agente | Atualização do agente | Pull do agente do Azure |
| Estado desejado da configuração-URL do repositório git, parâmetros do operador de fluxo, chave privada, conteúdo de hosts conhecidos, nome de usuário HTTPS, token/senha | Configuração | Pull do agente do Azure |
| Status da instalação do operador de fluxo | Configuração | Pushes do agente para o Azure |
| Azure Policy atribuições que precisam de imposição de gatekeeper no cluster | Azure Policy | Pull do agente do Azure |
| Status de auditoria e conformidade de imposição de política no cluster | Azure Policy | Pushes do agente para o Azure |
| Métricas e logs de cargas de trabalho do cliente | Azure Monitor | O agente envia por push para Log Analytics recurso de espaço de trabalho no locatário e assinatura do cliente |

## <a name="connectivity-status"></a>Status da conectividade

| Status | Descrição |
| ------ | ----------- |
| Connecting | O recurso kubernetes habilitado para Arc do Azure foi criado em Azure Resource Manager, mas o serviço ainda não recebeu a pulsação do agente. |
| Conectado | O serviço kubernetes habilitado para Arc do Azure recebeu uma pulsação do agente em algum momento nos 15 minutos anteriores. |
| Offline | O recurso kubernetes habilitado para Arc do Azure foi conectado anteriormente, mas o serviço não recebeu nenhuma pulsação do agente por 15 minutos. |
| Expirado | O certificado MSI (identidade de serviço gerenciada) tem uma janela de expiração de 90 dias após ser emitido. Quando esse certificado expira, o recurso é considerado `Expired` e todos os recursos, como configuração, monitoramento e política, param de funcionar nesse cluster. Mais informações sobre como lidar com os recursos de kubernetes habilitados para Arc do Azure expirados podem ser encontrados [aqui](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) |

## <a name="understand-connectivity-modes"></a>Entender os modos de conectividade

| Modo de conectividade | Descrição |
| ----------------- | ----------- |
| Totalmente conectado | Os agentes sempre podem entrar em contato com o Azure. A experiência é ideal nesse caso, pois há pouco atraso na propagação de configurações (para GitOps), imposição de políticas (em Azure Policy e gatekeeper) e coleta de métricas e logs de cargas de trabalho (em Azure Monitor) |
| Semiconectado | O certificado MSI obtido pelo `clusteridentityoperator` é válido por um máximo de 90 dias antes de o certificado expirar. Depois que o certificado expirar, o recurso de kubernetes habilitado para o Azure Arc deixará de funcionar. Exclua e recrie o recurso kubernetes e os agentes habilitados para o Azure ARC para obter todos os recursos de arco para trabalhar no cluster. Durante os 90 dias, é recomendável que os usuários conectem o cluster pelo menos uma vez a cada 30 dias. |
| Desconectado | Os clusters kubernetes em ambientes desconectados sem nenhum acesso ao Azure atualmente não têm suporte no kubernetes habilitado para Arc do Azure. Se esse recurso for de seu interesse, envie ou vote em uma ideia no [Fórum UserVoice do arco do Azure](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Próximas etapas

* [Conectar um cluster ao arco do Azure](./connect-cluster.md)
* [Visão geral conceitual das configurações](./conceptual-configurations.md)