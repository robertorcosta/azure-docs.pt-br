---
title: Visão geral do backup offline
description: Saiba mais sobre os vários componentes do backup offline, incluindo o backup offline baseado em Azure Data Box e o backup offline baseado em importação/exportação do Azure.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027011"
---
# <a name="overview-of-offline-backup"></a>Visão geral do backup offline

Este artigo fornece uma visão geral do backup offline.

Os backups completos iniciais para o Azure normalmente transferem grandes quantidades de dados online e exigem mais largura de banda de rede em comparação com os backups subsequentes que transferem apenas alterações incrementais. Escritórios ou data centers remotos em determinadas geografias nem sempre têm largura de banda de rede suficiente. Portanto, esses backups iniciais levam vários dias e, durante esse tempo, usam continuamente a mesma rede que foi provisionada para aplicativos em execução no datacenter local.

O backup do Azure dá suporte ao "backup offline", que permite transferir dados de backup iniciais offline, sem o uso da largura de banda da rede. Ele fornece um mecanismo para copiar dados de backup no armazenamento físico – dispositivos que são enviados para um datacenter do Azure próximo e carregados em um cofre de serviços de recuperação. Esse processo garante a transferência robusta de dados de backup sem usar nenhuma largura de banda de rede.

## <a name="offline-backup-options"></a>Opções de backup offline

O backup offline é oferecido em dois modos com base na propriedade dos dispositivos de armazenamento.

1. Backup offline baseado em Azure Data Box (versão prévia)
2. Backup offline baseado em importação/exportação do Azure

## <a name="azure-data-box-based-offline-backup-preview"></a>Backup offline baseado em Azure Data Box (versão prévia)

Atualmente, esse modo tem suporte no agente MARS, em versão prévia. Essa opção tira proveito do [serviço de Azure data Box](https://azure.microsoft.com/services/databox/) para enviar dispositivos de transferência proprietários, seguros e resistentes pela Microsoft com conectores USB para seu datacenter ou escritório remoto e os dados de backup são gravados diretamente nesses dispositivos. **Essa opção economiza o esforço necessário para adquirir seus próprios discos e conectores compatíveis com o Azure ou o armazenamento temporário de provisionamento como um local de preparo.** Além disso, a Microsoft lida com a logística de transferência de ponta a ponta, que pode ser acompanhada pela portal do Azure. Uma arquitetura que descreve a movimentação de dados de backup com essa opção é mostrada abaixo.

![Arquitetura de Data Box de backup do Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Aqui está um resumo da arquitetura:

1. O backup do Azure copia diretamente os dados de backup para esses dispositivos pré-configurados.
2. Em seguida, você pode enviar esses dispositivos de volta para um datacenter do Azure.
3. O serviço de Azure Data Box copia os dados em uma conta de armazenamento de Propriedade do cliente.
4. O backup do Azure copia automaticamente os dados de backup da conta de armazenamento para o cofre de serviços de recuperação designado e os backups online incrementais são agendados.

Para usar o backup offline baseado em Azure Data Box, consulte [Este artigo](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Backup offline baseado em importação/exportação do Azure

Essa opção é suportada pelo agente Servidor de Backup do Azure (MABS)/DPM-A/MARS. Ele aproveita o [serviço de importação/exportação do Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) que permite transferir dados de backup iniciais para o Azure usando seus próprios discos e conectores compatíveis com o Azure. Essa abordagem requer o provisionamento de um armazenamento temporário conhecido como o **local de preparo** e o uso de utilitários pré-criados para formatar e copiar os dados de backup em discos de Propriedade do cliente. Uma arquitetura que descreve a movimentação de dados de backup com essa opção é mostrada abaixo:

![Arquitetura de importação/exportação do backup do Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Aqui está um resumo da arquitetura:

1. Em vez de enviar os dados de backup pela rede, o backup do Azure grava os dados de backup em um local de preparo.
2. Os dados no local de preparo são gravados em um ou mais discos SATA usando um utilitário personalizado.
3. Como parte do trabalho preparatória, o utilitário cria um trabalho de importação do Azure. As unidades SATA são enviadas para o datacenter do Azure mais próximo e fazem referência ao trabalho de importação para conectar as atividades.
4. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
5. O Backup do Azure copia os dados de backup da conta de armazenamento para o cofre de Serviços de Recuperação, e os backups incrementais são agendados.

Para usar o backup offline baseado em importação/exportação do Azure com o agente MARS, consulte [Este artigo](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Para usar o mesmo junto com o MABS/DPM-A, consulte [Este artigo](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Resumo de suporte de backup offline

A tabela a seguir compara as duas opções disponíveis para que você possa fazer as escolhas apropriadas com base em seu cenário.

| **Consideração**                                            | **Backup offline baseado em Azure Data Box**                     | **Backup offline baseado em importação/exportação do Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelos de implantação de backup do Azure                              | Agente MARS (versão prévia)                                              | Agente MARS, Servidor de Backup do Azure (MABS), DPM-A                                           |
| Máximo de dados de backup por servidor (MARS) ou por grupo de proteção (MABS, DPM-A) | [Disco do Azure data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7,2 TB <br> [Azure data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (até 10 discos de 8 TB cada)                          |
| Segurança (dados, dispositivo & serviço)                           | [Dados](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES-256 bits criptografados <br> Caso resistente ao [dispositivo](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) , interface baseada em credencial proprietária para copiar dados <br> Recursos de segurança protegidos pelo [serviço](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) do Azure | Dados-BitLocker criptografado                                 |
| Provisionamento temporário do local de preparo                     | Não obrigatório                                                | Maior ou igual ao tamanho estimado dos dados de backup        |
| Regiões com suporte                                           | [Regiões de Disco do Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Regiões de Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Regiões de importação/exportação do Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Envio entre países *                                     | **Sem suporte**  <br>    *O endereço de origem & Datacenter do Azure de destino deve estar no mesmo país* | Com suporte                                                    |
| Logística de transferência (entrega, transporte, coleta)           | Totalmente gerenciado pela Microsoft                                     | Gerenciado pelo cliente                                            |
| Preços                                                      | [Preços de Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Preços de Disco do Azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Preços de importação/exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *Se o seu país não tiver um datacenter do Azure, você precisará enviar seus discos para um datacenter do Azure em outro país.*

## <a name="next-steps"></a>Próximos passos

* [Backup offline baseado em Azure Data Box para o agente MARS](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Backup offline baseado em importação/exportação do Azure para agente MARS](backup-azure-backup-import-export.md)  
* [Backup offline baseado em importação/exportação do Azure para MABS/DPM-A](backup-azure-backup-server-import-export-.md)
