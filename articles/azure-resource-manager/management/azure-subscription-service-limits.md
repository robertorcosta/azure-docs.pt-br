---
title: Limites e cotas de assinatura do Azure
description: Fornece uma lista de assinaturas comuns do Azure e limites de serviço, cotas e restrições. Este artigo inclui informações sobre como aumentar os limites junto com os valores máximos.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 325f7b3d03435945779c1f42e13681dcfd9604b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334868"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Assinatura do Azure e limite de serviços, cotas e restrições

Este documento lista alguns dos limites mais comuns do Microsoft Azure, que também são chamados de cotas.

Para saber mais sobre os preços do Azure, consulte [a visão geral dos preços do Azure](https://azure.microsoft.com/pricing/). Lá, você pode estimar seus custos usando a [calculadora de preços.](https://azure.microsoft.com/pricing/calculator/) Você também pode ir para a página de detalhes de preços para um serviço específico, por exemplo, [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Para obter dicas sobre como ajudar a gerenciar custos, consulte [Evitar custos inesperados com o gerenciamento de custo e cobrança do Azure](../../billing/billing-getting-started.md).

## <a name="managing-limits"></a>Gerenciamento de limites

> [!NOTE]
> Alguns serviços têm limites ajustáveis.
>
> Quando um serviço não tem limites ajustáveis, as tabelas a seguir usam o limite de **cabeçalho**. Nesses casos, a inadimplência e os limites máximos são os mesmos.
>
> Quando o limite pode ser ajustado, as tabelas incluem **cabeçalhos de limite padrão** e **máximo.** O limite pode ser elevado acima do limite padrão, mas não acima do limite máximo.
>
> Se você quiser elevar o limite ou cota acima do limite padrão, [abra uma solicitação de suporte ao cliente on-line sem custo.](../templates/error-resource-quota.md)

[As assinaturas de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não são elegíveis para aumentos de limite ou cotas. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [Atualize sua assinatura de teste gratuito do Azure para uma assinatura Pay-As-You-Go](../../billing/billing-upgrade-azure-subscription.md) e o [FAQ de assinatura de teste gratuito](https://azure.microsoft.com/free/free-account-faq).

Alguns limites são gerenciados a nível regional.

Vamos usar cotas vCPU como um exemplo. Para solicitar um aumento de cota com suporte para vCPUs, você deve decidir quantas vCPUs você deseja usar em quais regiões. Em seguida, você faz uma solicitação específica para cotas de vCPU do grupo de recursos Do Zure para os valores e regiões que deseja. Se você precisar usar 30 vCPUs na Europa Ocidental para executar seu aplicativo lá, você solicita especificamente 30 vCPUs na Europa Ocidental. Sua cota de vCPU não é aumentada em nenhuma outra região - apenas a Europa Ocidental tem a cota de 30 vCPU.

Como resultado, decida quais devem ser as cotas do grupo de recursos do Azure para sua carga de trabalho em qualquer região. Em seguida, solicite esse valor em cada região em que você deseja implantar. Para obter ajuda sobre como determinar suas cotas atuais para regiões específicas, consulte [Resolver erros para cotas de recursos](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Limites gerais

Para obter limites em nomes de recursos, consulte [Regras de nomeação e restrições para os recursos do Azure](resource-name-rules.md).

Para obter informações sobre limites de leitura e gravação da API do Resource Manager, consulte [solicitações do Gerenciador de Recursos de limitação](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limites do grupo de gestão

Os seguintes limites se aplicam aos [grupos de gestão.](../../governance/management-groups/overview.md)

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limites de assinatura

Os limites a seguir se aplicam quando você usa o Azure Resource Manager e os grupos de recursos do Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites do grupo de recursos

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limites do Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limites de Gerenciamento de API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limites do Serviço de Aplicativo

Os seguintes limites do Serviço de Aplicativo incluem limites para Aplicativos Web, Aplicativos Móveis e Aplicativos de API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limites de automação

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Limites do Cache Redis do Azure

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limites dos Serviços de Nuvem do Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limites da Busca Cognitiva do Azure

Os tipos de preço determinam a capacidade e os limites de seu serviço Search. Eles incluem:

* **O** serviço gratuito multi-inquilino, compartilhado com outros assinantes do Azure, destina-se a projetos de avaliação e pequenos projetos de desenvolvimento.
* **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor, com até três réplicas para cargas de trabalho de consulta altamente disponíveis.
* **Standard**, que inclui S1, S2, S3 e S3 High Density, é para cargas de trabalho de produção maiores. Existem vários níveis dentro do nível Padrão para que você possa escolher uma configuração de recurso que melhor corresponda ao seu perfil de carga de trabalho.

**Limites por assinatura**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço Search**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Para saber mais sobre limites em um nível mais granular, como tamanho do documento, consultas por segundo, chaves, solicitações e respostas, consulte [limites de serviço na Pesquisa Cognitiva do Azure](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Limites dos Serviços Cognitivos do Azure

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limites do Azure Cosmos DB

Para os limites do Azure Cosmos DB, consulte [Limites no Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limites do Azure Data Explorer

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Banco de Dados do Azure para MySQL

Para limites do Banco de Dados do Azure para MySQL, consulte [Limitações no Banco de Dados do Azure para MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Banco de Dados do Azure para PostgreSQL

Para limites do Banco de Dados do Azure para PostgreSQL, consulte [Limitações no Banco de Dados do Azure para PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limites das funções do Azure

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Limites de serviço do Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limites de Aprendizado de Máquina do Azure

Os valores mais recentes para cotas de Computação de Machine Learning do Azure podem ser encontrados na página de cotas do [Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limites do Azure Mapas

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limites do Monitor Do Azure

### <a name="alerts"></a>Alertas

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Grupos de ação

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Consultas de log e idioma

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Workspaces do Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Limites do Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Limites de serviço do SignalR do Azure

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Limites do Backup

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limites de lote

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Limites clássicos do modelo de implantação

Se você usar o modelo de implantação clássico em vez do modelo de implantação do Azure Resource Manager, os seguintes limites se aplicam.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limites das Instâncias de Contêiner

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limites do Registro de Contêiner

A tabela a seguir detalha os recursos e limites dos níveis de [serviço](../../container-registry/container-registry-skus.md)Básico, Padrão e Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limites da rede de entrega de conteúdo

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limites de fábrica de dados

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limites do Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Limites do Data Lake Store

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limites do Compartilhamento de Dados

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limites do Serviço de Migração do Banco de Dados

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Limites da Grade de Eventos

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limites de Hubs de Eventos

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Limites do Gerenciador de Identidade

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>Limites da Central de IoT
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limites do Hub IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limites do Serviço de Provisionamento de Dispositivos no Hub IoT

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limites do Cofre da Chave

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Limites de Serviços de Mídia

[!INCLUDE [azure-mediaservices-limits](../../../includes/azure-mediaservices-limits.md)]

## <a name="mobile-services-limits"></a>Limites de Serviços Móveis

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limites de autenticação multifatorial

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limites de rede

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limites do ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Limites de WAN virtuais

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limites do Gateway de Aplicativo

A tabela a seguir se aplica aos SKUs v1, v2, Standard e WAF, salvo indicação em contrário.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limites do Observador de Rede

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limites de Link Privado

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Limites do Gerenciador de Tráfego

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limites do Bastião do Azure

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limites do Azure DNS

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limites de Firewall do Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limites do Azure Front Door Service

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limites dos Hubs de Notificação

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Limites de controle de acesso baseado em função

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Limites de Barramento de Serviço

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limites da Recuperação de Site

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limites de banco de dados SQL

Para os limites do Banco de Dados SQL, consulte [os limites de recursos do Banco de Dados SQL para bancos de dados únicos,](../../sql-database/sql-database-vcore-resource-limits-single-databases.md)os limites de recursos do Banco de Dados [SQL para grupos elásticos e bancos de dados agrupados](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)e [os limites de recursos do Banco de Dados SQL para instâncias gerenciadas.](../../sql-database/sql-database-managed-instance-resource-limits.md)

## <a name="sql-data-warehouse-limits"></a>Limites do SQL Data Warehouse

Para os limites do SQL Data Warehouse, consulte [os limites de recursos do SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Limites de armazenamento

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Para obter mais informações sobre limites para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Limites do provedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limites de Armazenamento de Blobs do Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Limites de Arquivos do Azure

Para obter mais informações sobre os limites do Azure Files, consulte [metas de escalabilidade e desempenho do Azure Files](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Limites de Sincronização de Arquivo do Azure

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limites de Armazenamento de Filas do Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limites de Armazenamento de Tabelas do Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Para obter mais informações, consulte [tamanhos de máquinas virtuais](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="managed-virtual-machine-disks"></a>Discos de máquina virtual gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquina virtual não gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limites do sistema StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limites do Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limites de Máquinas virtuais

### <a name="virtual-machines-limits"></a>Limites de Máquinas virtuais

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de Máquinas Virtuais – Gerenciador de Recursos do Azure

Os limites a seguir se aplicam quando você usa o Azure Resource Manager e os grupos de recursos do Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limites da Galeria de Imagens Compartilhadas

Há limites, por assinatura, para a implantação de recursos usando galerias de imagens compartilhadas:

- 100 galerias de imagens compartilhadas, por assinatura, por região
- 1.000 definições de imagem, por assinatura, por região
- 10.000 versões de imagem, por assinatura, por região

## <a name="virtual-machine-scale-sets-limits"></a>A escala da máquina virtual define limites

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Confira também

* [Entenda os limites e aumentos do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Tamanhos de serviço sinuosos e de nuvem virtuais para o Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tamanhos para serviços de nuvem do Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Regras de nomenclatura e restrições para recursos do Azure](resource-name-rules.md)
