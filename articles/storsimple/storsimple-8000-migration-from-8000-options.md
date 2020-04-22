---
title: Opções de migração de dados de dispositivos da série StorSimple 8000
description: Fornece uma visão geral das opções para migrar dados da série StorSimple 8000.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: 974bcc657b811a10e28b41150439e83d26a208d2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767044"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opções para migrar dados da série StorSimple 8000

> [!IMPORTANT]
> Em 31 de dezembro de 2022, a série StorSimple 8000 chegará ao status de fim de suporte (EOS). Recomendamos que os clientes da série StorSimple 8000 migrem para uma das alternativas descritas no documento.

A série StorSimple 8000 está chegando ao [fim do Suporte](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) em dezembro de 2022. Os clientes que estão executando a série StorSimple 8000 têm a opção de atualizar para outros serviços híbridos de primeira parte do Azure. Este artigo descreve as opções híbridas do Azure disponíveis para migrar dados.

## <a name="migration-options"></a>Opções de migração

Os clientes que usam a série StorSimple 8000 têm opções de Azure ou de terceiros.

### <a name="azure-options"></a>Opções do Azure

#### <a name="migrate-to-azure-file-sync"></a>Migrar para Sincronização de Arquivos do Azure

Essa nova opção de migração permite que os clientes armazenem os compartilhamentos de arquivos de sua organização nos Arquivos Do Azure. Esses compartilhamentos de arquivos são então centralizados para acesso local usando AFS (Sincronização de Arquivos do Azure). A AFS pode ser implantada em um host do Windows Server. A migração de dados real é então executada como uma cópia do host ou usando a ferramenta de migração.

Para obter mais informações sobre como migrar dados para o Azure File Sync, vá para a [migração do StorSimple 8100 e 8600 para o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000).

### <a name="third-party-options"></a>Opções de terceiros

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrar para o NAS do Panzura Freedom

Os clientes das séries StorSimple 5000-7000 e StorSimple 8000 podem optar por migrar para o Panzura Freedom NAS para manter seus dados no Azure. A solução Panzura Freedom fornece uma solução NAS que abrange data centers, escritórios, nuvens públicas e privadas. A solução habilita fluxos de trabalho de dados na nuvem, híbridos e locais para clientes móveis, SMB e NFS.

Há suporte para essa migração no Panzura e os clientes podem começar solicitando o suporte à migração do [site do Panzura](https://panzura.com/migrate-storsimple-panzura/).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migração - Perguntas frequentes

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Q. Quando os dispositivos da série StorSimple 8000 chegam ao fim do serviço?

a. A série StorSimple 8000 chega ao [fim do suporte](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) em dezembro de 2022. O fim do suporte implica que a Microsoft não será mais capaz de fornecer suporte para hardware e software desses dispositivos após dezembro de 2022. É altamente recomendável que você comece a formular um plano para migrar os dados dos dispositivos agora.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. O que acontecerá com os dados armazenados no Azure?  

a. Será possível continuar usando os dados no Azure após migrá-los para um serviço mais recente.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. O que acontecerá com os dados que tenho armazenados localmente no dispositivo do StorSimple?

a. Os dados que estão no dispositivo local poderão ser copiados para o serviço mais recente, conforme descrito nos documentos de migração.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. O que acontece se eu quiser manter o meu aparelho da série StorSimple 8000?

a. Embora os serviços poderão continuar funcionando, a Microsoft não poderá mais dar suporte de hardware e software. A migração é altamente recomendável para continuidade de negócios.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. Quais opções estão disponíveis para migrar dados de dispositivos da série StorSimple 8000?

a. Dependendo do cenário, os usuários da série StorSimple 8000 têm as seguintes opções de migração:

* **Migrar para Sincronização de Arquivos do Azure**: use essa opção quando quiser alternar para o formato nativo do Azure. É possível usar a Sincronização de Arquivos do Azure para gerenciamento centralizado de compartilhamentos de arquivos.

* **Outras opções**: Você pode entrar em contato com o Suporte da Microsoft para discutir opções de migração não listadas aqui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. Há suporte de migração para outras soluções de armazenamento?

a. Sim. Há suporte para migração de outras soluções de armazenamento usando a cópia do host dos dados.

### <a name="q-is-migration-supported-by-microsoft"></a>Q. A Microsoft dá suporte à migração?

a. Migrar da série 8000 é uma operação totalmente apoiada. Na verdade, a Microsoft recomenda contatar o Suporte antes de iniciar a migração. A migração atualmente é uma operação assistida. Se você pretende migrar dados do seu dispositivo da série StorSimple 8000, [entre em contato com o suporte do StorSimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. Qual é o modelo de preços para migração para o Azure File Sync?

a. Ao usar o Azure File Sync, as taxas de assinatura do serviço podem ser aplicadas. Os clientes também terão que pagar custos contínuos de armazenamento. Consulte os [preços da AFS]( https://azure.microsoft.com/pricing/details/storage/files/) para obter uma estimativa.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. Quanto tempo demora para concluir uma migração?

a. O tempo para migrar dados depende da quantidade de dados e da opção de atualização selecionada.

## <a name="next-steps"></a>Próximas etapas

* [Migrar dados de uma série StorSimple 8000 para o Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
