---
title: Regiões e Zonas de Disponibilidade no Azure
description: Saiba mais sobre regiões e Zonas de Disponibilidade no Azure para atender aos seus requisitos técnicos e regulatórios.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 32cd6c23e1fe465a801e56fad5dd3461df860503
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182726"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regiões e Zonas de Disponibilidade no Azure

Os serviços de Microsoft Azure estão disponíveis globalmente para orientar suas operações de nuvem em um nível ideal. Você pode escolher a melhor região para suas necessidades com base nas considerações técnicas e regulatórias: recursos de serviço, residência de dados, requisitos de conformidade e latência.

## <a name="terminology"></a>Terminologia

Para entender melhor as regiões e Zonas de Disponibilidade no Azure, é útil entender os principais termos ou conceitos.

| Termo ou conceito | Descrição |
| --- | --- |
| region | Um conjunto de data centers implantado em um perímetro definido por latência e conectado por meio de uma rede regional de baixa latência. |
| geografia | Uma área do mundo que contém pelo menos uma região do Azure. As regiões geográficas definem um mercado discreto que preserva os limites de conformidade e residência de dados. As geografias permitem que os clientes com necessidades de conformidade e de residência de dados específicas mantenham seus dados e aplicativos próximos. As geografias são tolerantes a falhas para resistir à falha de região completa por meio de sua conexão à nossa infraestrutura de rede de alta capacidade dedicada. |
| Zona de disponibilidade | Locais físicos exclusivos em uma região. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. |
| região recomendada | Uma região que fornece a mais ampla variedade de recursos de serviço e foi projetada para dar suporte a Zonas de Disponibilidade agora ou no futuro. Elas são designadas na portal do Azure conforme **recomendado**. |
| região alternativa (outra) | Uma região que estende a superfície do Azure dentro de um limite de residência de dados em que também existe uma região recomendada. As regiões alternativas ajudam a otimizar a latência e a fornecer uma segunda região para as necessidades de recuperação de desastre. Eles não são projetados para dar suporte a Zonas de Disponibilidade (embora o Azure realize a avaliação regular dessas regiões para determinar se elas devem se tornar regiões recomendadas). Elas são designadas na portal do Azure como **outras**. |
| serviço básico | Um serviço principal do Azure que está disponível em todas as regiões quando a região está geralmente disponível. |
| serviço principal | Um serviço do Azure que está disponível em todas as regiões recomendadas dentro de 12 meses da disponibilidade geral de região/serviço ou da disponibilidade orientada por demanda em regiões alternativas. |
| serviço especializado | Um serviço do Azure que é a disponibilidade orientada por demanda entre regiões apoiadas por hardware personalizado/especializado. |
| serviço regional | Um serviço do Azure que é implantado regionalmente e permite que o cliente especifique a região na qual o serviço será implantado. Para obter uma lista completa, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| serviço não regional | Um serviço do Azure para o qual não há nenhuma dependência em uma região específica do Azure. Os serviços não regionais são implantados em duas ou mais regiões e, se houver uma falha regional, a instância do serviço em outra região continuará atendendo aos clientes. Para obter uma lista completa, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regiões

Uma região é um conjunto de data centers implantados em um perímetro definido por latência e conectados por meio de uma rede regional de baixa latência. O Azure oferece a você a flexibilidade de implantar aplicativos onde você precisa, incluindo em várias regiões para fornecer resiliência entre regiões. Para obter mais informações, consulte [visão geral do pilar de resiliência](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Zonas de Disponibilidade

Uma zona de disponibilidade é uma oferta de alta disponibilidade que protege seus aplicativos e dados de falhas do datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Uma Zona de Disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, as VMs serão efetivamente distribuídas em três domínios de falha e três domínios de atualização. A plataforma Azure reconhece essa distribuição entre domínios de atualização para garantir que as VMs em diferentes zonas não estejam agendadas para serem atualizadas ao mesmo tempo.

Compila alta disponibilidade na arquitetura do aplicativo, colocalizando os recursos de computação, armazenamento, rede e dados em uma zona e replicando em outras zonas. Os serviços do Azure que dão suporte a Zonas de Disponibilidade enquadram-se em duas categorias:

- **Serviços zonais** – onde um recurso é fixado em uma zona específica (por exemplo, máquinas virtuais, discos gerenciados, endereços IP padrão) ou
- **Serviços com redundância de zona** – quando a plataforma do Azure é replicada automaticamente entre zonas (por exemplo, armazenamento com redundância de zona, banco de dados SQL).

Para obter uma continuidade de negócios abrangente no Azure, compile a arquitetura do aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. É possível replicar os aplicativos e dados de maneira síncrona usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastre.
 
![exibição conceitual de uma zona reduzindo-se a uma região](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Os identificadores de zona de disponibilidade (os números 1, 2 e 3 na imagem acima) são mapeados logicamente para as zonas físicas reais para cada assinatura de forma independente. Isso significa que a disponibilidade Zona 1 em uma determinada assinatura pode se referir a uma zona física diferente da Zona 1 de disponibilidade em uma assinatura diferente. Como consequência, é recomendável não confiar nas IDs de zona de disponibilidade em assinaturas diferentes para o posicionamento da máquina virtual.

## <a name="region-and-service-categories"></a>Categorias de região e serviço

A abordagem do Azure sobre a disponibilidade de serviços do Azure entre regiões é melhor descrita pela expressa de serviços disponibilizados em regiões recomendadas e regiões alternativas.

- **Região recomendada** -uma região que fornece a mais ampla variedade de recursos de serviço e é projetada para dar suporte a zonas de disponibilidade agora ou no futuro. Elas são designadas na portal do Azure conforme **recomendado**.
- **Região alternativa (outro)** – uma região que estende a superfície do Azure dentro de um limite de residência de dados em que também existe uma região recomendada. As regiões alternativas ajudam a otimizar a latência e a fornecer uma segunda região para as necessidades de recuperação de desastre. Eles não são projetados para dar suporte a Zonas de Disponibilidade (embora o Azure realize a avaliação regular dessas regiões para determinar se elas devem se tornar regiões recomendadas). Elas são designadas na portal do Azure como **outras**.

### <a name="comparing-region-types"></a>Comparando tipos de região

Os serviços do Azure são agrupados em três categorias: serviços básicos, básicos e especializados. A política geral do Azure sobre a implantação de serviços em qualquer região específica é controlada principalmente por tipo de região, categorias de serviço e demanda do cliente:

- **Fundamental** – disponível em todas as regiões recomendadas e alternativas quando a região está disponível para o público geral ou dentro de 12 meses de um novo serviço básico ficando disponível para o público geral.
- **Mainstream** – disponível em todas as regiões recomendadas dentro de 12 meses da disponibilidade geral de região/serviço; orientado por demanda em regiões alternativas (muitas já estão implantadas em um grande subconjunto de regiões alternativas).
- Ofertas de serviços direcionadas **especializadas** , geralmente focadas no setor ou com suporte por hardware personalizado/especializado. Disponibilidade orientada por demanda entre regiões (muitas já estão implantadas em um grande subconjunto de regiões recomendadas).

Para ver quais serviços são implantados em uma determinada região, bem como o roteiro futuro para visualização ou disponibilidade geral dos serviços em uma região, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Se uma oferta de serviço não estiver disponível em uma região específica, você poderá compartilhar seu interesse entrando em contato com seu representante de vendas da Microsoft.

| Tipo de região | Não regional | Foundational | Base | Especializada | Zonas de Disponibilidade | Residência de dadosResidência de dados |
| --- | --- | --- | --- | --- | --- | --- |
| Recomendado | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Controlado por demanda | :heavy_check_mark: | :heavy_check_mark: |
| Alternativo | :heavy_check_mark: | :heavy_check_mark: | Controlado por demanda | Controlado por demanda | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Serviços por categoria

Conforme mencionado anteriormente, o Azure classifica os serviços em três categorias: fundamental, predominante e especializado. As categorias de serviço são atribuídas em disponibilidade geral. Geralmente, os serviços iniciam seu ciclo de vida como um serviço especializado e, à medida que a demanda e a utilização aumentam, podem ser promovidas ao básico ou básico. A tabela a seguir lista a categoria de serviços como básico, principal ou especializado. Você deve observar o seguinte sobre a tabela:

- Alguns serviços não são regionais. Para obter informações e uma lista de serviços não regionais, consulte [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).
- As máquinas virtuais de geração mais antigas não são listadas. Para obter mais informações, consulte a documentação em [gerações anteriores de tamanhos de máquina virtual](../virtual-machines/sizes-previous-gen.md).

> [!div class="mx-tableFixed"]
> | Foundational | Base | Especializada |
> | --- | --- | --- |
> | Armazenamento de conta | Gerenciamento de API | API do Azure para FHIR |
> | Gateway de Aplicativo | Configuração de Aplicativos | Azure Analysis Services |
> | Serviço de Backup do Azure | Serviço de Aplicativo | Serviço do Azure Blockchain |
> | Azure Cosmos DB | Automação | Azure Blueprints |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Banco de Dados do Azure para MariaDB |
> | Azure ExpressRoute | Azure Bastion | HSM Dedicado do Azure |
> | Banco de Dados SQL do Azure | Cache Redis do Azure | Azure Dev Spaces |
> | Serviços de Nuvem | Pesquisa Cognitiva do Azure | Gêmeos Digitais do Azure |
> | Serviços de nuvem: Av2-Series | Azure Data Explorer | Azure Lab Services |
> | Serviços de nuvem: Dv2-Series | Azure Data Share | Azure NetApp Files |
> | Serviços de nuvem: Dv3-Series | Banco de Dados do Azure para MySQL | Quantum do Azure |
> | Serviços de nuvem: Ev3-Series | Banco de Dados do Azure para PostgreSQL | Serviço de nuvem do Azure Spring |
> | Serviços de nuvem: IPs de nível de instância | Serviço de Migração de Banco de Dados do Azure | Azure Time Series Insights |
> | Serviços de nuvem: IP Reservado | Azure Databricks | Solução VMware no Azure pela CloudSimple |
> | Armazenamento em Disco | Proteção contra DDoS do Azure | Serviços de nuvem: série G |
> | Hubs de Eventos | Azure DevTest Labs | Serviços de nuvem: série H |
> | Key Vault | Firewall do Azure | Serviços cognitivas: Visão Personalizada |
> | Balanceador de carga | Gerenciador de Firewall do Azure | Serviços cognitivas: Reconhecimento do Locutor |
> | Barramento de Serviço | Funções do Azure | Data Box Heavy |
> | Service Fabric | Azure HPC Cache | Catálogo de Dados |
> | Conjuntos de Dimensionamento de Máquinas Virtuais | Hub IoT do Azure | Data Factory: Data Factory v1 |
> | Máquinas Virtuais | AKS (Serviço de Kubernetes do Azure) | Data Lake Analytics |
> | Máquinas virtuais: Av2-Series | Azure Machine Learning | Azure Machine Learning Studio (clássico)|
> | Máquinas virtuais: Bs-Series | Link Privado do Azure | Microsoft Genomics |
> | Máquinas virtuais: DSv2-Series | Red Hat OpenShift no Azure | Remote Rendering |
> | Máquinas virtuais: DSv3-Series | Serviço Azure SignalR | Âncoras Espaciais |
> | Máquinas virtuais: Dv2-Series | Azure Site Recovery | StorSimple |
> | Máquinas virtuais: Dv3-Series | Azure Stack Hub | Video Indexer |
> | Máquinas virtuais: ESv3-Series | Stream Analytics do Azure | Máquinas virtuais: DASv4-Series |
> | Máquinas virtuais: Ev3-Series | Azure Synapse Analytics | Máquinas virtuais: DAv4-Series |
> | Máquinas virtuais: série F | Lote | Máquinas virtuais: DCsv2-Series |
> | Máquinas virtuais: FS-Series | Serviços de nuvem: série M | Máquinas virtuais: EASv4-Series |
> | Máquinas virtuais: IPs de nível de instância | Serviços Cognitivos | Máquinas virtuais: EAv4-Series |
> | Máquinas virtuais: IP Reservado | Serviços cognitivas: Pesquisa Visual Computacional | Máquinas virtuais: série G |
> | Rede Virtual | Serviços cognitivas: Content Moderator | Máquinas virtuais: GS-Series |
> | Gateway de VPN | Serviços cognitivas: face | Máquinas virtuais: HBv1-Series |
> |  | Serviços cognitivas: reconhecedor de formulário | Máquinas virtuais: HBv2-Series |
> |  | Serviços cognitivas: Reconhecimento vocal | Máquinas virtuais: HCv1-Series |
> |  | Serviços Cognitivos: QnA Maker | Máquinas virtuais: série H |
> |  | Serviços cognitivas: serviços de fala | Máquinas virtuais: LS-Series |
> |  | Instâncias de Contêiner | Máquinas virtuais: LSv2-Series |
> |  | Registro de Contêiner | Máquinas virtuais: Mv2-Series |
> |  | Data Factory | Máquinas virtuais: NC-Series |
> |  | Grade de Eventos | Máquinas virtuais: NCv2-Series |
> |  | HDInsight | Máquinas virtuais: NCv3-Series |
> |  | Aplicativos Lógicos | Máquinas virtuais: NDs-Series |
> |  | Serviços de Mídia | Máquinas virtuais: NDv2-Series |
> |  | Observador de Rede | Máquinas virtuais: NV-Series |
> |  | Hubs de Notificação | Máquinas virtuais: NVv3-Series |
> |  | Power BI Embedded | Máquinas virtuais: NVv4-Series |
> |  | Armazenamento de blob Premium | Máquinas virtuais: SAP HANA em Instâncias Grandes do Azure |
> |  | Armazenamento de arquivos Premium | Visual Studio App Center |
> |  | Armazenamento: Armazenamento de Arquivos |  |
> |  | Ultra Armazenamento em Disco |  |
> |  | Máquinas virtuais: Ddsv4-Series |  |
> |  | Máquinas virtuais: Ddv4-Series |  |
> |  | Máquinas virtuais: Dsv4-Series |  |
> |  | Máquinas virtuais: Dv4-Series |  |
> |  | Máquinas virtuais: Edsv4-Series |  |
> |  | Máquinas virtuais: Edv4-Series |  |
> |  | Máquinas virtuais: Esv4-Series |  |
> |  | Máquinas virtuais: Ev4-Series |  |
> |  | Máquinas virtuais: Fsv2-Series |  |
> |  | Máquinas virtuais: série M |  |
> |  | WAN Virtual |  |

###  <a name="services-resiliency"></a>Resiliência de serviços

Todos os serviços de gerenciamento do Azure são arquitetados para serem resilientes de falhas no nível de região. No espectro de falhas, uma ou mais falhas de zona de disponibilidade em uma região têm um raio de falha menor em comparação a uma falha de região inteira. O Azure pode se recuperar de uma falha no nível de zona dos serviços de gerenciamento dentro da região ou de outra região do Azure. O Azure realiza uma manutenção crítica em uma zona por vez dentro de uma região, para evitar falhas que afetem os recursos do cliente implantados em Zonas de Disponibilidade dentro de uma região.

### <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs no Zonas de Disponibilidade

Não há custo adicional para máquinas virtuais implantadas em uma Zona de Disponibilidade. O SLA de 99,99% de tempo de atividade da VM é oferecido quando duas ou mais VMs são implantadas em duas ou mais Zonas de Disponibilidade dentro de uma região do Azure. Haverá encargos de transferência de dados adicionais de VM para VM entre Zona de Disponibilidade. Para obter mais informações, consulte a página [Preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="get-started-with-availability-zones"></a>Introdução às Zonas de Disponibilidade

- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um Disco Gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Armazenamento com redundância de zona](../storage/common/storage-redundancy.md)
- [Camada de uso geral do banco de dados SQL](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Recuperação de desastre geográfico dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação de desastre geográfico do Barramento de Serviço](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância de zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar região com redundância de zona para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introdução o cache do Azure para Redis Zonas de Disponibilidade](https://aka.ms/redis/az/getstarted)
- [Criar uma instância do Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Criar um cluster AKS (serviço de kubernetes do Azure) que usa Zonas de Disponibilidade](../aks/availability-zones.md)

## <a name="next-steps"></a>Próximas etapas

- [Regiões que dão suporte a Zonas de Disponibilidade no Azure](az-region.md)
- [Modelos de início rápido](https://aka.ms/azqs)