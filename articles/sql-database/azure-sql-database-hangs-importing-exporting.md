---
title: O serviço de importação/exportação leva muito tempo
description: O serviço de importação/exportação de banco de dados Azure SQL leva muito tempo para importar ou exportar um banco de dados
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535758"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>O serviço de importação/exportação de banco de dados Azure SQL leva muito tempo para importar ou exportar um banco de dados

Quando você usa o serviço de importação/exportação de banco de dados Azure SQL, o processo pode levar mais tempo do que o esperado. Este artigo descreve as possíveis causas para esse atraso e métodos alternativos de solução.

## <a name="azure-sql-database-importexport-service"></a>Serviço de importação/exportação de banco de dados Azure SQL

O serviço de importação/exportação de banco de dados Azure SQL é um serviço web baseado em REST que é executado em todos os data centers do Azure. Esse serviço é chamado quando você usa o [banco de dados Importação](sql-database-import.md#using-azure-portal) ou a opção [Exportar](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) para mover seu banco de dados SQL no portal Azure. O serviço fornece serviços gratuitos de fila de solicitação e computação para realizar importações e exportações entre um banco de dados Azure SQL e o armazenamento Azure Blob.

As operações de importação e exportação não representam um backup de banco de dados físico tradicional, mas sim um backup lógico do banco de dados que usa um formato BACPAC especial. O formato BACPAC permite que você evite ter que usar um formato físico que pode variar entre versões do Microsoft SQL Server e do Azure SQL Database. Portanto, você pode usá-lo para restaurar o banco de dados com segurança em um banco de dados SQL Server e em um banco de dados SQL.

## <a name="what-causes-delays-in-the-process"></a>O que causa atrasos no processo?

O serviço de importação/exportação de banco de dados Azure SQL fornece um número limitado de máquinas virtuais de computação (VMs) por região para processar operações de importação e exportação. As VMs de computação são hospedadas por região para garantir que a importação ou exportação evite atrasos e encargos de largura de banda entre as regiões. Se muitas solicitações forem feitas ao mesmo tempo na mesma região, podem ocorrer atrasos significativos no processamento das operações. O tempo necessário para completar as solicitações pode variar de alguns segundos a muitas horas.

> [!NOTE]
> Se uma solicitação não for processada dentro de quatro dias, o serviço cancela automaticamente a solicitação.

## <a name="recommended-solutions"></a>Soluções recomendadas

Se as exportações do banco de dados forem usadas apenas para recuperação da exclusão acidental de dados, todas as edições do Banco de Dados SQL do Azure fornecem recursos de restauração de autoatendimento a partir de backups gerados pelo sistema. Mas se você precisar dessas exportações por outras razões, e se você precisar de desempenho de importação/exportação consistentemente mais rápido ou mais previsível, considere as seguintes opções:

* [Exportar para um arquivo BACPAC usando o utilitário SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Exportar para um arquivo BACPAC usando O SSMS (SSMS) (SSMS) .](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Execute a importação ou exportação do BACPAC diretamente em seu código usando a API da Microsoft SQL Server Data-Tier Application Framework (DacFx). Para obter informações adicionais, consulte:
  * [Exportar um aplicativo de nível de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac Namespace](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Baixar DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Coisas a considerar quando você exporta ou importa um banco de dados SQL do Azure

* Todos os métodos discutidos neste artigo utilizam a cota da Unidade de Transação de Banco de Dados (DTU), que causa o estrangulamento pelo serviço de banco de dados Azure SQL. Você pode [visualizar as estatísticas do DTU para o banco de dados no portal Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Se o banco de dados atingiu seus limites de recursos, [atualize o nível de serviço](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) para adicionar mais recursos.
* Idealmente, você deve executar aplicativos clientes (como o utilitário sqlpackage ou seu aplicativo DAC personalizado) a partir de uma VM na mesma região que seu banco de dados SQL. Caso contrário, você pode experimentar problemas de desempenho relacionados à latência da rede.
* Exportar mesas grandes sem índices agrupados pode ser muito lento ou até mesmo causar falha. Esse comportamento ocorre porque a tabela não pode ser dividida e exportada em paralelo. Em vez disso, deve ser exportado em uma única transação, e isso causa um desempenho lento e uma possível falha durante a exportação, especialmente para mesas grandes.


## <a name="related-documents"></a>Documentos relacionados

[Considerações ao exportar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
