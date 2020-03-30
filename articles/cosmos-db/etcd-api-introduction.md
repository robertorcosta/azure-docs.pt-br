---
title: Introdução à API Azure Cosmos DB etcd
description: Este artigo fornece uma visão geral e os principais benefícios da API etcd no Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498602"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introdução à API Azure Cosmos DB etcd (visualização)

O Azure Cosmos DB é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. Oferece distribuição global turnkey, dimensionamento elástico de throughput e armazenamento, latências de um dígito no percentil 99 e alta disponibilidade garantida, tudo apoiado por SLA's líderes do setor.

[Etcd](https://github.com/etcd-io/etcd) é uma loja distribuída de chaves/valor. Em [Kubernetes,](https://kubernetes.io/)etcd é usado para armazenar o estado e a configuração dos clusters Kubernetes. Garantir a disponibilidade, a confiabilidade e o desempenho do etcd é crucial para a saúde geral do cluster, escalabilidade, disponibilidade de elasticidade e desempenho de um cluster Kubernetes. 

A API etcd no Azure Cosmos DB permite que você use o Azure Cosmos DB como a loja backend para [OZure Kubernetes](../aks/index.yml). A API do etcd no Azure Cosmos DB está atualmente em versão prévia. O Azure Cosmos DB implementa o protocolo de fio etcd. Com a API etcd no Azure Cosmos DB, os desenvolvedores obterão automaticamente Kubernetes altamente confiáveis, [disponíveis](high-availability.md)e [distribuídos globalmente.](distribute-data-globally.md) Esta API permite que os desenvolvedores dimensionem a gestão estadual do Kubernetes em um serviço PaaS nativo em nuvem totalmente gerenciado. 

> [!NOTE]
> Ao contrário de outras APIs no Azure Cosmos DB, você não pode provisionar uma conta de API etcd através do portal Azure, CLI ou SDKs. Você pode provisionar uma conta de API etcd implantando apenas o modelo do Gerenciador de recursos; para obter etapas detalhadas, [consulte Como prover o Azure Kubernetes com o artigo do Azure Cosmos DB.](bootstrap-kubernetes-cluster.md) A Api Azure Cosmos DB etcd está atualmente em pré-visualização limitada. Você pode [se inscrever para a pré-visualização,](https://aka.ms/cosmosetcdapi-signup)preenchendo o formulário de inscrição.

## <a name="wire-level-compatibility"></a>Compatibilidade de nível de fio

O Azure Cosmos DB implementa o protocolo de fio da versão 3 do etcd, e permite que os servidores aPI [do nó mestre](https://kubernetes.io/docs/concepts/overview/components/) usem o Azure Cosmos DB, assim como faria em um ambiente etc. instalado localmente. A API etcd suporta autenticação mútua TLS. 

O diagrama a seguir mostra os componentes de um cluster Kubernetes. No mestre de cluster, o Servidor aPI usa a Azure Cosmos DB etcd API, em vez de instalado localmente etc. 

![Azure Cosmos DB implementando o protocolo de fio etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Principais benefícios

### <a name="no-etcd-operations-management"></a>Sem gerenciamento de operações etcd

Como um serviço de nuvem nativo totalmente gerenciado, o Azure Cosmos DB remove a necessidade de os desenvolvedores do Kubernetes configurarem e gerenciarem etc. A API etcd no Azure Cosmos DB é escalável, altamente disponível, tolerante a falhas e oferece alto desempenho. A sobrecarga de configuração de replicação em vários nós, a realização de atualizações de rolamento, patches de segurança e o monitoramento da saúde etcd são tratadas pelo Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuição global & alta disponibilidade 

Ao usar a API etcd, o Azure Cosmos DB garante 99,99% de disponibilidade para leituras e gravações de dados em uma única região e 99,999% de disponibilidade em várias regiões. 

### <a name="elastic-scalability"></a>Escalabilidade elástica

O Azure Cosmos DB oferece escalabilidade elástica para solicitações de leitura e gravação em diferentes regiões.
À medida que o cluster Kubernetes cresce, a conta da API no Azure Cosmos DB escala sem qualquer tempo de inatividade. Armazenar dados etcd no Azure Cosmos DB, em vez dos nós mestres do Kubernetes também permite dimensionamento de nó mestre mais flexível. 

### <a name="security--enterprise-readiness"></a>Prontidão empresarial de & de segurança

Quando os dados etcd são armazenados no Azure Cosmos DB, os desenvolvedores do Kubernetes obtêm automaticamente a [criptografia incorporada em repouso,](database-encryption-at-rest.md) [certificações e conformidade, e](compliance.md)recursos de backup e [restauração](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) suportados pelo Azure Cosmos DB. 

## <a name="next-steps"></a>Próximas etapas

* [Como usar o Kubernetes do Azure com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principais benefícios do Azure Cosmos DB](introduction.md)
* [Guia de partida rápida do motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)