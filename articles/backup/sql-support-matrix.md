---
title: Matriz de suporte ao backup do Azure para backup do servidor SQL em VMs do Azure
description: Fornece um resumo das configurações e limitações de suporte ao fazer backup do SQL Server em VMs Azure com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409993"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matriz de suporte para backup do servidor SQL em VMs do Azure

Você pode usar o Backup do Azure para fazer backup de bancos de dados SQL Server em VMs azure hospedados na plataforma de nuvem Microsoft Azure. Este artigo resume as configurações e limitações gerais de suporte para cenários e implantações do SQL Server Backup em VMs Azure.

## <a name="scenario-support"></a>Suporte ao cenário

**Suporte** | **Detalhes**
--- | ---
**Implantações com suporte** | Há suporte para VMs do Azure no Marketplace do SQL e VMs que não são do Marketplace (do SQL Server instaladas manualmente).
**Áreas geográficas com suporte** | ASE (Sudeste da Austrália), AE (Leste da Austrália), Leste da Austrália, AC (Austrália Central), AC (Austrália Central 2) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Leste do Canadá (CE)<br> Sudeste da Ásia (SEA), Leste da Ásia (EA) <br> Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Centro-Oeste dos EUA (WCUS), Oeste dos EUA (WUS); Oeste dos EUA 2 (WUS 2) Centro-Norte dos EUA (NCUS) EUA Central (CUS) Centro-Sul dos EUA (SCUS) <br> INC (Índia Central), INS (Sul da Índia), Oeste da Índia <br> Oeste do Japão (JPE), Leste do Japão (JPW) <br> Coreia Central (KRC), Sul da Coreia (KRS) <br> Norte da Europa (NE), Oeste da Europa <br> Sul do Reino Unido (UKS), Oeste do Reino Unido (UKW) <br> US Gov – Arizona, US Gov – Virgínia, US Gov – Texas, Região Central do US DoD, Leste do US DoD <br> Norte da Alemanha, Centro-oeste da Alemanha <br> Norte da Suíça, Oeste da Suíça <br> França Central <br> Leste da China, Leste da China 2, Norte da China, Norte da China 2
**Sistemas operacionais suportados** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Não há suporte para Linux no momento.
**Versões do SQL Server com suporte** | SQL Server 2019, SQL Server 2017, conforme detalhado na [página Pesquisar ciclo de vida de produto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SPs conforme detalhado na [página Pesquisar ciclo de vida do produto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer e Express.
**Versões do .NET com suporte** | .NET Framework 4.5.2 ou posterior instalado na VM

## <a name="feature-consideration-and-limitations"></a>Considerações e limitações de recurso

* O Backup do SQL Server pode ser configurado no portal do Azure ou **PowerShell**. Nós não apoiamos a CLI.
* A solução é compatível em ambos os tipos de [implantações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – VMs do Azure Resource Manager e VMs clássicas.
* VM que executa o SQL Server exige conectividade com a Internet para acessar os endereços IP públicos do Azure.
* A FCI (SQL Server Failover Cluster Instance, instância de cluster de failover do servidor **sql)** não é suportada.
* Não há suporte para as operações de backup e de restauração para bancos de dados de espelho e instantâneos do banco de dados.
* Usar mais de uma solução de backup para fazer backup de sua instância do SQL Server autônoma ou do grupo de disponibilidade SQL Always On pode levar à falha de backup. Evite fazer isso.
* Fazer backup de dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também poderá levar à falha de backup.
* Backup do Azure dá suporte a apenas tipos de backup Completo e Somente Cópia para banco de dados **Somente leitura**
* Bancos de dados com um grande número de arquivos não podem ser protegidos. O número máximo de arquivos com suporte não é **~1000**.  
* Você pode fazer backup de **até ~2000** bancos de dados SQL Server em um cofre. Caso você tenha um número maior de bancos de dados, poderá criar vários cofres.
* Você pode configurar o backup de até **50** bancos de dados de uma vez; essa restrição ajuda a otimizar cargas de backup.
* Apoiamos bancos de dados de até **2 TB** de tamanho; para tamanhos maiores do que os problemas de desempenho podem surgir.
* Para ter uma noção de quantos bancos de dados podem ser protegidos por servidor, considere fatores como largura de banda, tamanho de VM, frequência de backup, tamanho do banco de dados e assim por diante. [Baixe](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) o planejador de recursos para calcular o número aproximado de bancos de dados que você pode ter por servidor com base nos recursos da VM e na política de backup.
* Quando os grupos de disponibilidade são configurados, os backups são retirados dos diferentes nós com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade está resumido abaixo.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Faça backup do comportamento com always on availability groups

Recomendamos que o backup esteja configurado em apenas um nó de um grupo de disponibilidade (AG). Configure sempre o backup na mesma região do nó principal. Em outras palavras, você sempre precisa do nó principal para estar presente na região onde você está configurando o backup. Se todos os nós da AG estiverem na mesma região onde o backup está configurado, não há nenhuma preocupação.

#### <a name="for-cross-region-ag"></a>Para grupos de disponibilidade entre regiões

* Independentemente da preferência de backup, os backups só serão executados a partir dos nós que estão na mesma região onde o backup está configurado. Isso porque os backups entre regiões não são suportados. Se você tiver apenas dois nós e o nó secundário estiver na outra região, os backups continuarão a funcionar a partir do nó principal (a menos que sua preferência de backup seja 'apenas secundária').
* Se um nó falhar em uma região diferente da que o backup está configurado, os backups falharão nos nós da região com falha.

Dependendo da preferência de backup e os tipos de backups (completo/diferencial/log/somente cópia completos), os backups serão feitos em um nó específico (primário/secundário).

#### <a name="backup-preference-primary"></a>Preferência de backup: Principal

**Tipo de backup** | **Nó**
--- | ---
Completo | Primária
Diferencial | Primária
Log |  Primária
Completo somente de cópia |  Primária

#### <a name="backup-preference-secondary-only"></a>Preferência de backup: Somente secundário

**Tipo de backup** | **Nó**
--- | ---
Completo | Primária
Diferencial | Primária
Log |  Secundário
Completo somente de cópia |  Secundário

#### <a name="backup-preference-secondary"></a>Preferência de backup: Secundária

**Tipo de backup** | **Nó**
--- | ---
Completo | Primária
Diferencial | Primária
Log |  Secundário
Completo somente de cópia |  Secundário

#### <a name="no-backup-preference"></a>Nenhuma preferência de Backup

**Tipo de backup** | **Nó**
--- | ---
Completo | Primária
Diferencial | Primária
Log |  Secundário
Completo somente de cópia |  Secundário

## <a name="next-steps"></a>Próximas etapas

Aprenda a fazer backup de [um banco de dados SQL Server](backup-azure-sql-database.md) que está sendo executado em uma VM Azure.
