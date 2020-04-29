---
title: Matriz de suporte de backup do SAP HANA
description: Neste artigo, saiba mais sobre os cenários e limitações com suporte ao usar o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252435"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte para backup de bancos de dados do SAP HANA em VMs do Azure

O backup do Azure dá suporte ao backup de bancos de dados SAP HANA no Azure. Este artigo resume os cenários com suporte e as limitações presentes quando você usa o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.

> [!NOTE]
> A frequência do backup de log agora pode ser definida para um mínimo de 15 minutos. Os backups de log só começam a fluir após a conclusão de um backup completo bem-sucedido para o banco de dados.

## <a name="scenario-support"></a>Suporte ao cenário

| **Cenário**               | **Configurações com suporte**                                | **Configurações sem suporte**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA em execução somente em VMs Linux do Azure                    | HLI (HANA Large instances)                                   |
| **Áreas geográficas**                   | **3º**<br> **Américas** – EUA Central, leste dos EUA 2, leste dos EUA, norte EUA Central, Sul EUA Central, oeste dos EUA 2, Oeste EUA Central, oeste dos EUA, Canadá central, leste do Canadá, sul do Brasil <br> **Pacífico Asiático** – Austrália Central, Austrália Central 2, leste da Austrália, sudeste da Austrália, leste do Japão, oeste do Japão, Coreia central, Coreia do sul, Ásia Oriental, Sudeste Asiático, Índia central, sul da Índia, Índia ocidental, Leste da China, norte da China, China 2, norte da China 2 <br> **Europa** – Europa Ocidental, Europa setentrional, França Central, Sul do Reino Unido, Oeste do Reino Unido, Norte da Alemanha, Centro-oeste da Alemanha, Norte da Suíça, oeste da Suíça, central norte da Suíça <br> **África/me** -norte da África do Sul, oeste da África do sul, Norte dos EAU, EAU central  <BR>  **Regiões do Azure Governamental** | Sul da França, Alemanha central, Alemanha nordeste, US Gov IOWA |
| **Versões do sistema operacional**            | SLES 12 com SP2, SP3 ou SP4; SLES 15 com SP1                              | RHEL                                                |
| **Versões do HANA**          | SDC no HANA 1. x, MDC no HANA 2. x <= SPS04 Rev 46       | -                                                            |
| **Implantações do HANA**       | SAP HANA em uma única VM do Azure-escalar somente. <br><br> Para implantações de alta disponibilidade, ambos os nós em duas máquinas diferentes são tratados como nós individuais com cadeias de dados separadas.               | Escalabilidade <br><br> Em implantações de alta disponibilidade, o backup não faz failover para o nó secundário automaticamente. Configurar o backup deve ser feito separadamente para cada nó.                                           |
| **Instâncias do HANA**         | Uma única instância de SAP HANA em uma única VM do Azure – escalar verticalmente somente | Várias instâncias de SAP HANA em uma única VM                  |
| **Tipos de banco de dados HANA**    | Contêiner de Banco de Dados Individual (SDC) em 1. x, contêiner de vários bancos de dados (MDC) em 2. x | MDC no HANA 1. x                                              |
| **Tamanho do banco de dados HANA**     | tamanho de backup completo de 2 TB conforme relatado pelo HANA)                   |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais e de log                          | Incrementais, instantâneos                                       |
| **Tipos de restauração**          | Consulte a SAP HANA nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauração com suporte |                                                              |
| **Limites do Backup**          | Até 2 TB de tamanho de backup completo por instância de SAP HANA         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + camadas dinâmicas <br>  Clonagem por meio de LaMa        |

------

> [!NOTE]
> Atualmente, não há suporte para operações de backup e restauração de SAP HANA clientes nativos (SAP HANA Studio/cockpit/DBA cockpit).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [fazer backup de bancos de dados SAP Hana em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como [restaurar SAP Hana bancos de dados em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
