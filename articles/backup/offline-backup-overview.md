---
title: Visão geral do backup off-line
description: Conheça os componentes do backup off-line. Eles incluem backup off-line baseado na Caixa de Dados Do Azure e backup off-line com base no serviço Azure Import/Export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196139"
---
# <a name="overview-of-offline-backup"></a>Visão geral do backup off-line

Este artigo dá uma visão geral do backup off-line.

Os backups completos iniciais para o Azure normalmente transferem grandes quantidades de dados on-line e exigem mais largura de banda da rede quando comparados aos backups subsequentes que transferem apenas alterações incrementais. Escritórios remotos ou data centers em determinadas geografias nem sempre têm largura de banda de rede suficiente. Por essa razão, esses backups iniciais levam vários dias. Durante esse tempo, os backups usam continuamente a mesma rede que foi provisionada para aplicativos executados no datacenter local.

O Azure Backup suporta backup off-line, que transfere dados iniciais de backup off-line, sem o uso da largura de banda da rede. Ele fornece um mecanismo para copiar dados de backup em dispositivos de armazenamento físico. Os dispositivos são então enviados para um data center azure próximo e carregados em um cofre dos Serviços de Recuperação. Esse processo garante a transferência robusta de dados de backup sem o uso de largura de banda de rede.

## <a name="offline-backup-options"></a>Opções de backup offline

O backup off-line é oferecido em dois modos com base na propriedade dos dispositivos de armazenamento:

- Backup off-line baseado na Caixa de Dados Do Azure (visualização)
- Backup off-line baseado no serviço de importação/exportação do Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup off-line baseado na Caixa de Dados Do Azure (visualização)

Este modo é atualmente suportado com o Microsoft Azure Recovery Services (MARS) Agent, em visualização. Esta opção aproveita a Caixa de [Dados Do Azure](https://azure.microsoft.com/services/databox/) para enviar dispositivos de transferência proprietários, seguros e resistentes à adulteração da Microsoft com conectores USB para o seu data center ou escritório remoto. Os dados de backup são diretamente gravados nesses dispositivos. Essa opção economiza o esforço necessário para adquirir seus próprios discos e conectores compatíveis com o Azure ou para prover armazenamento temporário como local de preparação. A Microsoft também lida com a logística de transferência de ponta a ponta, que você pode acompanhar através do portal Azure. 

Uma arquitetura que descreve o movimento de dados de backup com essa opção é mostrada aqui.

![Arquitetura azure Data Box de backup](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Aqui está um resumo da arquitetura:

1. O Azure Backup copia diretamente os dados de backup para esses dispositivos pré-configurados.
2. Em seguida, você pode enviar esses dispositivos de volta para um data center do Azure.
3. O Azure Data Box copia os dados em uma conta de armazenamento de propriedade do cliente.
4. O Azure Backup copia automaticamente os dados de backup da conta de armazenamento para o cofre designado Recovery Services. Backups on-line incrementais são agendados.

Para usar backup off-line com base na Caixa de Dados Do Azure, consulte [backup offline usando a Caixa de Dados Do Azure](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup off-line baseado no serviço de importação/exportação do Azure

Essa opção é suportada pelo Microsoft Azure Backup Server (MABS), Pelo DPM-A (System Center Data Protection Manager) DPM-A e pelo Agente MARS. Ele usa o [serviço De importação/exportação do Azure.](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) Você pode transferir dados de backup iniciais para o Azure usando seus próprios discos e conectores compatíveis com o Azure. Essa abordagem exige que você provisione o armazenamento temporário conhecido como local de preparação e use utilitários pré-construídos para formatar e copiar os dados de backup em discos de propriedade do cliente. 

Uma arquitetura que descreve o movimento de dados de backup com essa opção é mostrada aqui.

![Arquitetura de serviços de importação/exportação de backup do Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Aqui está um resumo da arquitetura:

1. Em vez de enviar os dados de backup pela rede, o Azure Backup grava os dados de backup para um local de preparação.
2. Os dados no local de preparação são gravados em um ou mais discos SATA usando um utilitário personalizado.
3. Como parte do trabalho preparatório, a concessionária cria um trabalho de importação do Azure. As unidades SATA são enviadas para o data center Azure mais próximo e fazem referência ao trabalho de importação para conectar as atividades.
4. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
5. O Azure Backup copia os dados de backup da conta de armazenamento para o cofre dos Serviços de Recuperação. Backups incrementais são agendados.

Para usar backup off-line com base no serviço Azure Import/Export com o Agente MARS, consulte [Fluxo de trabalho de backup offline no Backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Para usar o mesmo junto com o MABS ou o DPM-A, consulte [fluxo de trabalho de backup offline para DPM e Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Resumo do suporte de backup off-line

A tabela a seguir compara as duas opções disponíveis para que você possa fazer as escolhas apropriadas com base no seu cenário.

| **Consideração**                                            | **Backup off-line baseado na Caixa de Dados Do Azure**                     | **Backup off-line baseado no serviço de importação/exportação do Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelos de implantação do Azure Backup                              | Agente MARS (visualização)                                              | Agente MARS, MABS, DPM-A                                           |
| Máximo de dados de backup por servidor (MARS) ou por grupo de proteção (MABS, DPM-A) | [Disco azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7.2 TB <br> [Caixa de dados Azure](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (até 10 discos de 8 TB cada)                          |
| Segurança (dados, dispositivo e serviço)                           | [Dados](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256 bits criptografados <br> [Dispositivo](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - Caixa robusta, interface proprietária, baseada em credenciais para copiar dados <br> [Serviço](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Protegido por recursos de segurança do Azure | Dados - BitLocker criptografado                                 |
| Provisionamento temporário de locais de encenação                     | Não obrigatório                                                | Mais ou igual ao tamanho estimado dos dados de backup        |
| Regiões com suporte                                           | [Regiões de disco do Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Regiões da Caixa de Dados Do Azul](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Regiões de serviços de importação/exportação do Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Transporte cross-country                                     | Sem suporte  <br>    Endereço de origem e destino O datacenter do Azure deve estar no mesmo país* | Com suporte                                                    |
| Logística de transferência (entrega, transporte, pickup)           | Totalmente gerenciado pela Microsoft                                     | Gerenciado pelo cliente                                            |
| Preços                                                      | [Preços da Caixa de Dados Do Azure](https://azure.microsoft.com/pricing/details/databox/) <br> [Preços do disco azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Preços dos serviços de importação/exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*Se o seu país não tiver um data center do Azure, você precisa enviar seus discos para um data center do Azure em outro país.

## <a name="next-steps"></a>Próximas etapas

* [Backup off-line do Azure usando a caixa de dados do Azure](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Fluxo de trabalho de backup off-line no Backup do Azure](backup-azure-backup-import-export.md) 
* [Fluxo de trabalho de backup off-line para dpm e azure backup server](backup-azure-backup-server-import-export-.md)
