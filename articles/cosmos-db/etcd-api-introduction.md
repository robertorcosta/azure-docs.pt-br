---
title: Introdução à API do etcd do Azure Cosmos DB
description: Este artigo fornece uma visão geral e os principais benefícios da API do etcd do Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85118161"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introdução à API do etcd do Azure Cosmos DB (versão prévia)

O Azure Cosmos DB é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. Ele oferece distribuição global imediata, dimensionamento elástico da taxa de transferência e do armazenamento, latências de milissegundos de um dígito no 99º percentil e garantia de alta disponibilidade, tudo isso com suporte de SLAs líderes do setor.

[Etcd](https://github.com/etcd-io/etcd) é um armazenamento de valor/chave distribuída. No [Kubernetes](https://kubernetes.io/), o etcd é usado para armazenar o estado e a configuração dos clusters do Kubernetes. Garantir a disponibilidade, a confiabilidade e o desempenho do etcd é crucial para a integridade geral do cluster, a escalabilidade, a disponibilidade de elasticidade e o desempenho de um cluster do Kubernetes.

A API do etcd no Azure Cosmos DB permite que você use o Azure Cosmos DB como armazenamento de back-end para Kubernetes do Azure. A API do etcd no Azure Cosmos DB está atualmente em versão prévia. O Azure Cosmos DB implementa o protocolo de transmissão etcd. Com a API do etcd no Azure Cosmos DB, os desenvolvedores receberão automaticamente Kubernetes altamente disponíveis confiáveis, [disponíveis](high-availability.md) e [distribuídos globalmente](distribute-data-globally.md). Essa API permite aos desenvolvedores dimensionar o gerenciamento de estado do Kubernetes em um serviço de PaaS nativo de nuvem totalmente gerenciado. 

> [!NOTE]
> Ao contrário de outras APIs no Azure Cosmos DB, você não pode provisionar uma conta de API do etcd por meio do portal do Azure, da CLI ou dos SDKs. Para provisionar uma conta do API do etcd basta implantar o modelo do Resource Manager. Para obter o procedimento detalhado, consulte [Como provisionar o Kubernetes do Azure com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md). A API do etcd do Azure Cosmos DB está atualmente em versão prévia limitada. Você pode [se inscrever para a versão prévia](https://aka.ms/cosmosetcdapi-signup) ao preencher o formulário de inscrição.

## <a name="wire-level-compatibility"></a>Compatibilidade de nível de conexão

O Azure Cosmos DB implementa o protocolo de transmissão etcd versão 3 e permite que os servidores de API do [nó mestre](https://kubernetes.io/docs/concepts/overview/components/) usem o Azure Cosmos DB exatamente como ele faria em um etcd instalado localmente. A API do etcd dá suporte à autenticação mútua de TLS. 

O diagrama a seguir mostra os componentes de um cluster do Kubernetes. No mestre de cluster, o servidor de API usa a API do etcd do Azure Cosmos DB, em vez do etcd instalado localmente. 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="O Azure Cosmos DB implementa o protocolo de transmissão etcd" border="false":::

## <a name="key-benefits"></a>Principais benefícios

### <a name="no-etcd-operations-management"></a>Nenhum gerenciamento de operações do etcd

Como um serviço de nuvem nativo totalmente gerenciado, o Azure Cosmos DB elimina a necessidade de os desenvolvedores de Kubernetes instalarem e gerenciarem o etcd. A API do etcd no Azure Cosmos DB é escalonável, altamente disponível, tolerante a falhas e oferece alto desempenho. A sobrecarga da configuração da replicação em vários nós, da execução de atualizações sem interrupção, de patches de segurança e do monitoramento da integridade do etcd é tratada pelo Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuição global e HA 

Com a API do etcd, o Azure Cosmos DB garante 99,99% de disponibilidade para leituras e gravações de dados em uma única região e 99,999% de disponibilidade em várias regiões. 

### <a name="elastic-scalability"></a>Escalabilidade elástica

O Azure Cosmos DB oferece escalabilidade elástica para solicitações de leitura e gravação em diferentes regiões.
À medida que o cluster do Kubernetes cresce, a conta da API do etcd no Azure Cosmos DB é escalada de forma elástica sem nenhum tempo de inatividade. Armazenar dados do etcd no Azure Cosmos DB, em vez de usar os nós mestres do Kubernetes, também permite escalonamento mais flexível do nó mestre. 

### <a name="security--enterprise-readiness"></a>Segurança e preparação corporativa

Quando os dados do etcd são armazenados no Azure Cosmos DB, os desenvolvedores do Kubernetes obtêm automaticamente [criptografia interna em repouso](database-encryption-at-rest.md), [certificações e conformidade](compliance.md)e [recursos de backup e restauração](online-backup-and-restore.md) suportados pelo Azure Cosmos DB. 

## <a name="next-steps"></a>Próximas etapas

* [Como usar o Kubernetes do Azure com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Benefícios principais do uso do Azure Cosmos DB](introduction.md)
* [Guia de Início Rápido do mecanismo AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)