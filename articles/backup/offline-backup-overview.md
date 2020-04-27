---
title: Visão geral do backup offline
description: Saiba mais sobre os componentes do backup offline. Eles incluem backup offline baseado em Azure Data Box e backup offline com base no serviço de importação/exportação do Azure.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: a33810c633d8c7ca79c559355935d3c11853bc0a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160914"
---
# <a name="overview-of-offline-backup"></a>Visão geral do backup offline

Este artigo fornece uma visão geral do backup offline.

Os backups completos iniciais para o Azure normalmente transferem grandes quantidades de dados online e exigem mais largura de banda de rede em comparação com os backups subsequentes que transferem apenas alterações incrementais. Escritórios ou data centers remotos em determinadas geografias nem sempre têm largura de banda de rede suficiente. Por esse motivo, esses backups iniciais levam vários dias. Durante esse tempo, os backups usam continuamente a mesma rede que foi provisionada para aplicativos em execução no datacenter local.

O backup do Azure dá suporte ao backup offline, que transfere os dados de backup iniciais offline, sem o uso da largura de banda da rede. Ele fornece um mecanismo para copiar dados de backup em dispositivos de armazenamento físico. Os dispositivos são então enviados para um datacenter do Azure próximo e carregados em um cofre dos serviços de recuperação. Esse processo garante a transferência robusta de dados de backup sem usar nenhuma largura de banda de rede.

## <a name="offline-backup-options"></a>Opções de backup offline

O backup offline é oferecido em dois modos com base na propriedade dos dispositivos de armazenamento:

- Backup offline baseado em Azure Data Box (versão prévia)
- Backup offline com base no serviço de importação/exportação do Azure

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Backup offline baseado em Azure Data Box (versão prévia)

Atualmente, esse modo é compatível com o agente de Serviços de Recuperação do Microsoft Azure (MARS), em versão prévia. Essa opção aproveita a [Azure data Box](https://azure.microsoft.com/services/databox/) de enviar dispositivos de transferência proprietários, seguros e de adulteração da Microsoft com conectores USB para seu datacenter ou escritório remoto. Os dados de backup são gravados diretamente nesses dispositivos. Essa opção economiza o esforço necessário para adquirir seus próprios discos e conectores compatíveis com o Azure ou para provisionar o armazenamento temporário como um local de preparo. A Microsoft também lida com a logística de transferência de ponta a ponta, que pode ser acompanhada pela portal do Azure.

Uma arquitetura que descreve a movimentação de dados de backup com essa opção é mostrada aqui.

![Arquitetura de Data Box de backup do Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Aqui está um resumo da arquitetura:

1. O backup do Azure copia diretamente os dados de backup para esses dispositivos pré-configurados.
2. Em seguida, você pode enviar esses dispositivos de volta para um datacenter do Azure.
3. Azure Data Box copia os dados para uma conta de armazenamento de Propriedade do cliente.
4. O backup do Azure copia automaticamente os dados de backup da conta de armazenamento para o cofre de serviços de recuperação designado. Os backups online incrementais são agendados.

Para usar o backup offline com base em Azure Data Box, consulte [backup offline usando Azure data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Backup offline com base no serviço de importação/exportação do Azure

Essa opção tem suporte do Backup do Microsoft Azure Server (MABS), do System Center Data Protection Manager (DPM) DPM-A e do agente MARS. Ele usa o [serviço de importação/exportação do Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service). Você pode transferir os dados de backup iniciais para o Azure usando seus próprios discos e conectores compatíveis com o Azure. Essa abordagem exige que você provisione o armazenamento temporário conhecido como o local de preparo e use utilitários predefinidos para formatar e copiar os dados de backup em discos de Propriedade do cliente.

Uma arquitetura que descreve a movimentação de dados de backup com essa opção é mostrada aqui.

![Arquitetura do serviço de importação/exportação do backup do Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Aqui está um resumo da arquitetura:

1. Em vez de enviar os dados de backup pela rede, o backup do Azure grava os dados de backup em um local de preparo.
2. Os dados no local de preparo são gravados em um ou mais discos SATA usando um utilitário personalizado.
3. Como parte do trabalho preparatória, o utilitário cria um trabalho de importação do Azure. As unidades SATA são enviadas para o datacenter do Azure mais próximo e fazem referência ao trabalho de importação para conectar as atividades.
4. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
5. O backup do Azure copia os dados de backup da conta de armazenamento para o cofre dos serviços de recuperação. Os backups incrementais são agendados.

Para usar o backup offline com base no serviço de importação/exportação do Azure com o agente MARS, consulte [fluxo de trabalho de backup offline no backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Para usar o mesmo junto com o MABS ou o DPM-A, consulte [fluxo de trabalho de backup offline para o DPM e servidor de backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Resumo de suporte de backup offline

A tabela a seguir compara as duas opções disponíveis para que você possa fazer as escolhas apropriadas com base em seu cenário.

| **Consideração**                                            | **Backup offline baseado em Azure Data Box**                     | **Backup offline com base no serviço de importação/exportação do Azure**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modelos de implantação de backup do Azure                              | Agente MARS (versão prévia)                                              | Agente MARS, MABS, DPM-A                                           |
| Máximo de dados de backup por servidor (MARS) ou por grupo de proteção (MABS, DPM-A) | [Disco Azure data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7,2 TB <br> [Azure data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (até 10 discos de 8 TB cada)                          |
| Segurança (dados, dispositivo e serviço)                           | [Dados](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES 256-bit criptografado <br> Interface de maiúsculas e minúsculas com base em [dispositivo](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) , proprietária e baseada em credencial para copiar dados <br> Recursos de segurança protegidos pelo [serviço](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) do Azure | Dados-BitLocker criptografado                                 |
| Provisionamento temporário do local de preparo                     | Não obrigatório                                                | Maior ou igual ao tamanho estimado dos dados de backup        |
| Regiões com suporte                                           | [Regiões de disco Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Regiões de Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Regiões do serviço de importação/exportação do Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Envio entre países                                     | Sem suporte  <br>    O endereço de origem e o datacenter do Azure de destino devem estar no mesmo país * | Suportado                                                    |
| Logística de transferência (entrega, transporte, retirada)           | Totalmente gerenciado pela Microsoft                                     | Gerenciado pelo cliente                                            |
| Preços                                                      | [Preços de Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Preço do disco Azure Data Box](https://azure.microsoft.com/pricing/details/databox/disk/) | [Preço do serviço de importação/exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Se o seu país não tiver um datacenter do Azure, você precisará enviar seus discos para um datacenter do Azure em outro país.

## <a name="next-steps"></a>Próximas etapas

- [Backup offline do backup do Azure usando Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Fluxo de trabalho de backup offline no backup do Azure](backup-azure-backup-import-export.md)
- [Fluxo de trabalho de backup offline para o DPM e Servidor de Backup do Azure](backup-azure-backup-server-import-export-.md)
