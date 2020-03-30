---
title: Matriz de suporte de backup do SAP HANA
description: Neste artigo, conheça os cenários e limitações suportadas quando você usa o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252435"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte para backup de bancos de dados do SAP HANA em VMs do Azure

O Azure Backup suporta o backup de bancos de dados SAP HANA para o Azure. Este artigo resume os cenários suportados e as limitações presentes quando você usa o Azure Backup para fazer backup de bancos de dados SAP HANA em VMs do Azure.

> [!NOTE]
> A freqüência de backup de log agora pode ser definida para um mínimo de 15 minutos. Os backups de log só começam a fluir depois que um backup completo bem-sucedido para o banco de dados tiver sido concluído.

## <a name="scenario-support"></a>Suporte ao cenário

| **Cenário**               | **Configurações suportadas**                                | **Configurações sem suporte**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA em execução apenas em VMs Azure Linux                    | HANA Grandes Instâncias (HLI)                                   |
| **Geos**                   | **Ga:**<br> **Américas** – Américas – Eua Central, Leste dos EUA 2, Leste dos EUA, Norte Central dos EUA, Centro-Sul dos EUA, Oeste dos EUA 2, Oeste dos EUA, Canadá Central, Canadá Leste, Brasil Sul <br> **Ásia-Pacífico** – Austrália Central, Austrália Central 2, Austrália Leste, Austrália Sudeste, Japão Leste, Japão Oeste, Coréia Central, Coréia do Sul, Leste Asiático, Sudeste Asiático, Índia Central, Índia do Sul, Índia Ocidental, China Leste, China Norte, China East2, China North 2 <br> **Europa** – Europa Ocidental, Europa Norte, França Central, Reino Unido Sul, Reino Unido Oeste, Alemanha Norte, Alemanha Centro-Oeste, Suíça Norte, Suíça Oeste, Suíça Norte, Suíça Norte <br> **África / ME** - África do Sul Norte, África do Sul Oeste, Norte dos Emirados Americanos, Centro dos Emirados Diretos  <BR>  **Regiões do Azure Governamental** | França Sul, Alemanha Central, Alemanha Nordeste, EUA Gov IOWA |
| **Versões do Sistema Operacional**            | Less 12 com SP2, SP3 ou SP4; SLES 15 com SP1                              | RHEL                                                |
| **Versões HANA**          | SDC em HANA 1.x, MDC em HANA 2.x <= SPS04 Rev 46       | -                                                            |
| **Implantações hana**       | SAP HANA em um único Azure VM - Dimensione apenas. <br><br> Para implantações de alta disponibilidade, ambos os nós nas duas máquinas diferentes são tratados como nós individuais com cadeias de dados separadas.               | Escalabilidade <br><br> Em implantações de alta disponibilidade, o backup não falha automaticamente no nó secundário. A configuração do backup deve ser feita separadamente para cada nó.                                           |
| **Hana Instâncias**         | Uma única instância SAP HANA em uma única VM Azure – dimensione apenas | Várias instâncias sap HANA em um único VM                  |
| **Tipos de banco de dados HANA**    | Contêiner de banco de dados único (SDC) ON 1.x, Contêiner multibanco (MDC) em 2.x | MDC em HANA 1.x                                              |
| **Tamanho do banco de dados HANA**     | Tamanho de backup completo de 2 TB, conforme relatado pelo HANA)                   |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais e log                          | Incremental, Snapshots                                       |
| **Tipos de restauração**          | Consulte o SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauração suportados |                                                              |
| **Limites do Backup**          | Até 2 TB de tamanho de backup completo por instância SAP HANA         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + Hierarquidamento Dinâmico <br>  Clonagem através do LaMa        |

------

> [!NOTE]
> As operações de backup e restauração de clientes nativos do SAP HANA (SAP HANA Studio/ Cockpit/ DBA Cockpit) não são suportadas no momento.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [fazer backup de bancos de dados SAP HANA em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como [restaurar bancos de dados SAP HANA em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
