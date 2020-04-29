---
title: Introdução à API do Azure Cosmos DB etcd
description: Este artigo fornece uma visão geral e os principais benefícios da API do etcd no Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498602"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Introdução à API do Azure Cosmos DB etcd (versão prévia)

O Azure Cosmos DB é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. Ele oferece distribuição global completa, dimensionamento elástico de taxa de transferência e armazenamento, latências de milissegundos de dígito único no 99 º percentil e alta disponibilidade garantida, tudo apoiado por SLAs líderes do setor.

[Etcd](https://github.com/etcd-io/etcd) é um repositório de chave/valor distribuído. No [kubernetes](https://kubernetes.io/), etcd é usado para armazenar o estado e a configuração dos clusters do kubernetes. Garantir a disponibilidade, a confiabilidade e o desempenho do etcd é crucial para a integridade geral do cluster, a escalabilidade, a disponibilidade de elasticidade e o desempenho de um cluster kubernetes. 

A API etcd no Azure Cosmos DB permite que você use Azure Cosmos DB como o armazenamento de back-end para o [Azure kubernetes](../aks/index.yml). A API do etcd no Azure Cosmos DB está atualmente em versão prévia. Azure Cosmos DB implementa o protocolo de transmissão etcd. Com a API do etcd na Azure Cosmos DB, os desenvolvedores obterão automaticamente kubernetes altamente confiáveis, [disponíveis](high-availability.md)e [distribuídas globalmente](distribute-data-globally.md) . Essa API permite aos desenvolvedores dimensionar o gerenciamento de estado kubernetes em um serviço de PaaS nativo de nuvem totalmente gerenciado. 

> [!NOTE]
> Ao contrário de outras APIs no Azure Cosmos DB, você não pode provisionar uma conta de API etcd por meio do portal do Azure, da CLI ou dos SDKs. Você pode provisionar uma conta de API do etcd implantando apenas o modelo do Resource Manager; para obter etapas detalhadas, consulte [como provisionar o Azure kubernetes com Azure Cosmos DB](bootstrap-kubernetes-cluster.md) artigo. Azure Cosmos DB API etcd está atualmente em visualização limitada. Você pode [se inscrever para a versão prévia](https://aka.ms/cosmosetcdapi-signup), preenchendo o formulário de inscrição.

## <a name="wire-level-compatibility"></a>Compatibilidade de nível de conexão

Azure Cosmos DB implementa o protocolo de transmissão do etcd versão 3 e permite que os servidores de API [do nó mestre](https://kubernetes.io/docs/concepts/overview/components/) usem Azure Cosmos DB assim como fariam em um ambiente etcd instalado localmente. A API etcd dá suporte à autenticação mútua TLS. 

O diagrama a seguir mostra os componentes de um cluster kubernetes. No mestre de cluster, o servidor de API usa Azure Cosmos DB API etcd, em vez de etcd localmente instalado. 

![Azure Cosmos DB implementando o etcd Wire-Protocol](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Principais benefícios

### <a name="no-etcd-operations-management"></a>Nenhum gerenciamento de operações de etcd

Como um serviço de nuvem nativo totalmente gerenciado, Azure Cosmos DB elimina a necessidade de os desenvolvedores de kubernetes instalarem e gerenciarem o etcd. A API etcd no Azure Cosmos DB é escalonável, altamente disponível, tolerante a falhas e oferece alto desempenho. A sobrecarga de configurar a replicação em vários nós, executar atualizações sem interrupção, patches de segurança e monitorar a integridade do etcd são manipuladas pelo Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Distribuição global & alta disponibilidade 

Usando a API do etcd, o Azure Cosmos DB garante 99,99% de disponibilidade para leituras e gravações de dados em uma única região e 99,999% de disponibilidade em várias regiões. 

### <a name="elastic-scalability"></a>Escalabilidade elástica

O Azure Cosmos DB oferece escalabilidade elástica para solicitações de leitura e gravação em diferentes regiões.
À medida que o cluster kubernetes cresce, a conta da API etcd no Azure Cosmos DB é dimensionada de forma elástica sem nenhum tempo de inatividade. Armazenar dados do etcd em Azure Cosmos DB, em vez de nós mestres do kubernetes, também permite o dimensionamento mais flexível do nó mestre. 

### <a name="security--enterprise-readiness"></a>Segurança & preparação para empresas

Quando os dados do etcd são armazenados em Azure Cosmos DB, os desenvolvedores de kubernetes obtêm automaticamente a [criptografia interna em repouso](database-encryption-at-rest.md), [certificações e conformidade](compliance.md), além dos [recursos de backup e restauração](online-backup-and-restore.md) com suporte do Azure Cosmos DB. 

## <a name="next-steps"></a>Próximas etapas

* [Como usar o Kubernetes do Azure com o Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Principais benefícios do Azure Cosmos DB](introduction.md)
* [Guia de início rápido do mecanismo AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)