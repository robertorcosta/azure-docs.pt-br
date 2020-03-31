---
title: Migrar dados do lago de dados e data warehouse para o Azure
description: Use a Fábrica de Dados do Azure para migrar dados do seu lago de dados e data warehouse para o Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: aaf1593cc049e8b23f8ebe36fea022b3029ccd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930792"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Use a Fábrica de Dados do Azure para migrar dados do seu lago de dados ou data warehouse para o Azure

Se você quiser migrar seu data lake ou enterprise data warehouse (EDW) para o Microsoft Azure, considere usar o Azure Data Factory. A Fábrica de Dados Do Azure é adequada aos seguintes cenários:

- Migração de carga de trabalho de big data do Amazon Simple Storage Service (Amazon S3) ou de um Sistema de Arquivos Distribuídos Hadoop (HDFS) no local para o Azure
- Migração edw da Oracle Exadata, Netezza, Teradata ou Amazon Redshift para Azure

A Azure Data Factory pode mover petabytes (PB) de dados para migração de lagos de dados e dezenas de terabytes (TB) de dados para migração de data warehouse.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Por que a Fábrica de Dados do Azure pode ser usada para migração de dados

- A Fábrica de Dados Do Azure pode facilmente aumentar a quantidade de poder de processamento para mover dados de forma sem servidor, com alto desempenho, resiliência e escalabilidade. E você paga apenas pelo que usa. Também observe o seguinte: 
  - A Fábrica de Dados do Azure não tem limitações no volume de dados ou no número de arquivos.
  - A Fábrica de Dados do Azure pode usar totalmente sua largura de banda de rede e armazenamento para alcançar o maior volume de throughput de movimentação de movimentação de dados em seu ambiente.
  - A Azure Data Factory usa um método pay-as-you-go, de modo que você pague apenas pelo tempo que você realmente usa para executar a migração de dados para o Azure.  
- A Fábrica de Dados Do Azure pode executar uma carga histórica única e cargas incrementais programadas.
- A Azure Data Factory usa o tempo de execução de integração do Azure (IR) para mover dados entre os pontos finais do lago de dados e do armazém acessíveis ao público. Ele também pode usar o RI auto-hospedado para mover dados para pontos finais de lago de dados e armazém dentro da Rede Virtual Azure (VNet) ou atrás de um firewall.
- A Azure Data Factory tem segurança de nível corporativo: você pode usar o MSI (Windows Installer) ou a Service Identity para integração de serviço seguro ou usar o Azure Key Vault para gerenciamento de credenciais.
- A Azure Data Factory oferece uma experiência de autoria sem código e um rico painel de monitoramento integrado.  

## <a name="online-vs-offline-data-migration"></a>Migração de dados on-line vs. off-line

O Azure Data Factory é uma ferramenta padrão de migração de dados on-line para transferir dados através de uma rede (internet, ER ou VPN). Considerando que, com a migração de dados off-line, os usuários enviam fisicamente dispositivos de transferência de dados de sua organização para um Data Center do Azure.  

Existem três considerações-chave quando você escolhe entre uma abordagem de migração on-line e off-line:  

- Tamanho dos dados a serem migrados
- Largura de banda de rede
- Janela de migração

Por exemplo, suponha que você planeja usar a Fábrica de Dados do Azure para concluir sua migração de dados dentro de duas semanas (sua *janela de migração).* Observe a linha de corte rosa/azul na tabela a seguir. A célula rosa mais baixa para uma determinada coluna mostra o tamanho dos dados/o emparelhamento de largura de banda da rede cuja janela de migração está mais próxima, mas menos de duas semanas. (Qualquer emparelhamento de tamanho/largura de banda em uma célula azul tem uma janela de migração on-line de mais de duas semanas.) 

![on vs.](media/data-migration-guidance-overview/online-offline.png) offline Esta tabela ajuda você a determinar se você pode atender à janela de migração pretendida através da migração on-line (Azure Data Factory) com base no tamanho de seus dados e na largura de banda da rede disponível. Se a janela de migração on-line for superior a duas semanas, você deverá usar a migração offline.

> [!NOTE]
> Ao usar a migração on-line, você pode obter tanto o carregamento de dados históricos quanto os feeds incrementais de ponta a ponta através de uma única ferramenta.  Através dessa abordagem, seus dados podem ser mantidos sincronizados entre a loja existente e a nova loja durante toda a janela de migração. Isso significa que você pode reconstruir sua lógica ETL na nova loja com dados atualizados.


## <a name="next-steps"></a>Próximas etapas

- [Migrar dados do AWS S3 para o Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrar dados do cluster hadoop local para o Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrar dados do servidor Netezza local para o Azure](data-migration-guidance-netezza-azure-sqldw.md)
