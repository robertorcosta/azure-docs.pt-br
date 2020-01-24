---
title: Matriz de suporte de backup do SAP HANA
description: Neste artigo, saiba mais sobre os cenários e limitações com suporte ao usar o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 2063da4c5210cace41454d8bdc5b12e636ba76cd
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705642"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Matriz de suporte para backup de bancos de dados do SAP HANA em VMs do Azure

O backup do Azure dá suporte ao backup de bancos de dados SAP HANA no Azure. Este artigo resume os cenários com suporte e as limitações presentes quando você usa o backup do Azure para fazer backup de bancos de dados SAP HANA em VMs do Azure.

## <a name="onboard-to-the-public-preview"></a>Integrar-se à versão prévia pública

Integre à versão prévia pública da seguinte maneira:

* No portal, registre sua ID da assinatura para o provedor de serviço dos Serviços de Recuperação [seguindo este artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* Para o PowerShell, execute este cmdlet. Ele deve ser concluído como "Registrado".

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> A frequência do backup de log agora pode ser definida para um mínimo de 15 minutos. Os backups de log só começam a fluir após a conclusão de um backup completo bem-sucedido para o banco de dados.

## <a name="scenario-support"></a>Suporte ao cenário

| **Cenário**               | **Configurações com suporte**                                | **Configurações sem suporte**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologia**               | SAP HANA em execução somente em VMs Linux do Azure                    | HLI (HANA Large instances)                                   |
| **Áreas geográficas**                   | **3º**<br />**Europa** – Europa Ocidental, Europa setentrional, França central, sul da frança, Sul do Reino Unido, Oeste do Reino Unido, norte da Alemanha, centro-oeste da Alemanha, Norte da Suíça, oeste da Suíça<br />**Pacífico Asiático** – Austrália Central, Austrália Central 2, leste da Austrália, sudeste da Austrália, leste do Japão, oeste do Japão, Coreia central, sul da Coreia<br /><br>**Visualizar:**<br />**Américas** – EUA Central, leste dos EUA 2, leste dos EUA, norte EUA Central, Sul EUA Central, oeste dos EUA 2, Oeste EUA Central, oeste dos EUA, Canadá central, leste do Canadá, sul do Brasil<br />**Pacífico Asiático** – Ásia Oriental, Sudeste Asiático, Índia central, sul da Índia | Leste da China, Norte da China, China 2, Norte da China 2, Índia ocidental, Norte da Suíça Central, norte da África do Sul, oeste da África do Sul, Norte dos EAU, EAU Central, regiões do Azure governamental |
| **Versões do sistema operacional**            | SLES 12 com SP2, SP3 ou SP4                                | SLES 15, RHEL                                                |
| **Versões do HANA**          | SDC no HANA 1. x, MDC no HANA 2. x < = SPS04 Rev 44            | -                                                            |
| **Implantações do HANA**       | SAP HANA em uma única VM do Azure-escalar somente verticalmente               | Expansão                                                    |
| **Instâncias do HANA**         | Uma única instância de SAP HANA em uma única VM do Azure – escalar verticalmente somente | Várias instâncias de SAP HANA em uma única VM                  |
| **Tipos de banco de dados HANA**    | Contêiner de Banco de Dados Individual (SDC) em 1. x, contêiner de vários bancos de dados (MDC) em 2. x | MDC no HANA 1. x                                              |
| **Tamanho do banco de dados HANA**     | tamanho de backup completo de 2 TB conforme relatado pelo HANA)                   |                                                              |
| **Tipos de backup**           | Backups completos, diferenciais e de log                          | Incrementais, instantâneos                                       |
| **Tipos de restauração**          | Consulte a SAP HANA nota [1642148](https://launchpad.support.sap.com/#/notes/1642148) para saber mais sobre os tipos de restauração com suporte |                                                              |
| **Limites de backup**          | Até 2 TB de tamanho de backup completo por instância de SAP HANA         |                                                              |
| **Configurações especiais** |                                                              | SAP HANA + camadas dinâmicas <br>  Clonagem por meio de LaMa        |

------

> [!NOTE]
> Atualmente, não há suporte para operações de backup e restauração de SAP HANA clientes nativos (SAP HANA Studio/cockpit/DBA cockpit).



## <a name="next-steps"></a>Próximos passos

* Saiba como [fazer backup de bancos de dados SAP Hana em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* Saiba como [restaurar SAP Hana bancos de dados em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar bancos de dados do SAP HANA cujo backup é feito usando o Backup do Azure](sap-hana-db-manage.md)
* Saiba como [solucionar problemas comuns ao fazer backup de bancos de dados do SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
