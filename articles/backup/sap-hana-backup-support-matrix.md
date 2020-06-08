---
title: Matriz de suporte de backup do SAP HANA
description: Neste artigo, saiba mais sobre os cenários e limitações compatíveis ao usar o backup do Azure para fazer backup de bancos de dados do SAP HANA em VMs do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 5dcbfa67ce69a3dad1c263427ea3a0e34e8cef18
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747349"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte para backup de bancos de dados do SAP HANA em VMs do Azure

O Backup do Azure oferece suporte ao backup de bancos de dados SAP HANA no Azure. Este artigo resume os cenários com suporte e as limitações presentes quando você usa o Backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.

> [!NOTE]
> Agora a frequência do backup de log pode ser definida para 15 minutos, no mínimo. Os backups de log só começam a fluir depois que um backup completo bem-sucedido do banco de dados é concluído.

## <a name="scenario-support"></a>Suporte ao cenário

| **Cenário**               | **Configurações compatíveis**                                | **Configurações não compatíveis**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA em execução apenas em VMs Linux do Azure                    | Instâncias grandes do HANA (HLI)                                   |
| **Áreas geográficas**                   | **GA:**<br> **Américas**  - EUA Central, Leste dos EUA 2, Leste dos EUA, Centro-Norte dos EUA, Centro-Sul dos EUA, Oeste dos EUA 2, Centro-Oeste dos EUA, Oeste dos EUA, Canadá Central, Leste do Canadá, Sul do Brasil <br> **Ásia-Pacífico**  - Austrália Central, Austrália Central 2, Leste da Austrália, Sudeste da Austrália, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia, Leste da Ásia, Sudeste da Ásia, Índia Central, Sul da Índia, Oeste da Índia, Leste da China, Norte da China, Oeste da China 2, Norte da China 2 <br> **Europa** – Oeste da Europa, Norte da Europa, França Central, Sul do Reino Unido, Oeste do Reino Unido, Norte da Alemanha, Centro-Oeste da Alemanha, Norte da Suíça, Oeste da Suíça, Centro-Norte da Suíça <br> **África/Oriente Médio** - Norte da África do Sul, Oeste da África do Sul, Norte dos EAU, EAU Central  <BR>  **Regiões do Azure Governamental** | Sul da França, Alemanha Central, Nordeste da Alemanha, IOWA do US Gov |
| **Versões do sistema operacional**            | SLES 12 com SP2, SP3 ou SP4; SLES 15 com SP1                              | RHEL                                                |
| **Versões do HANA**          | SDC no HANA 1.x, MDC no HANA 2.x < = SPS04 Rev 46       | -                                                            |
| **Implantações do HANA**       | SAP HANA em uma única VM do Azure - Apenas escalar verticalmente. <br><br> Para implantações de alta disponibilidade, ambos os nós em dois computadores diferentes são tratados como nós individuais com cadeias de dados separadas.               | Escalabilidade <br><br> O backup não faz failover para o nó secundário automaticamente em implantações de alta disponibilidade. A configuração do backup deve ser feita separadamente para cada nó.                                           |
| **Instâncias do HANA**         | Uma única instância do SAP HANA em uma única VM do Azure - Apenas escalar verticalmente | Várias instâncias do SAP HANA em uma única VM                  |
| **Tipos de banco de dados do HANA**    | Contêiner do Banco de Dados Individual (SDC) em 1.x, contêiner de vários bancos de dados (MDC) em 2.x | MDC no HANA 1.x                                              |
| **Tamanho do banco de dados do HANA**     | Tamanho de backup completo de 2 TB conforme relatado pelo HANA)                   |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais e de log                          | Incremental, instantâneos                                       |
| **Restaurar tipos**          | Consulte a observação [1642148](https://launchpad.support.sap.com/#/notes/1642148) do SAP HANA para saber mais sobre os tipos de restauração compatíveis |                                                              |
| **Limites do backup**          | Até 2 TB de tamanho de backup completo por instância do SAP HANA         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + Camadas dinâmicas <br>  Clonagem por meio do LaMa        |

------

>[!NOTE]
>O Backup do Azure não é ajustado automaticamente para alterações do horário de verão ao fazer backup de um banco de dados SAP HANA em execução em uma VM do Azure.
>
>Modifique a política manualmente, se necessário.


> [!NOTE]
> Agora você pode [monitorar os trabalhos de backup e restauração](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#monitor-manual-backup-jobs-in-the-portal) (para o mesmo computador) acionados de clientes nativos do HANA (estúdio do SAP HANA/cockpit/cockpit do DBA) no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Fazer backup dos bancos de dados do SAP HANA em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como [Restaurar bancos de dados do SAP HANA em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
