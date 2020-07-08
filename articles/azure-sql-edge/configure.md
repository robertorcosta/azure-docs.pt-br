---
title: Configurar SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre como configurar o Azure SQL Edge (versão prévia).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636233"
---
# <a name="configure-azure-sql-edge-preview"></a>Configurar SQL do Azure no Edge (versão prévia)

O SQL do Azure no Edge dá suporte para a configuração por meio de uma destas duas opções:

- Variáveis de ambiente
- Um arquivo MSSQL. conf colocado na pasta/var/opt/MSSQL

> [!NOTE]
> A configuração de variáveis de ambiente substitui as configurações especificadas no arquivo MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Configurar usando variáveis de ambiente

O SQL do Azure no Edge expõe várias variáveis de ambiente diferentes que podem ser usadas para configurar o contêiner do SQL no Edge. Essas variáveis de ambiente são um subconjunto dos itens disponíveis para SQL Server em Linux. Para obter mais informações sobre variáveis de ambiente SQL Server em Linux, consulte [variáveis de ambiente](/sql/linux/sql-server-linux-configure-environment-variables/).

A variável de ambiente SQL Server em Linux a seguir não tem suporte para o Azure SQL Edge. Se definido, essa variável de ambiente será ignorada durante a inicialização do contêiner.

| Variável de ambiente | Descrição |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Habilitar grupo de disponibilidade. Por exemplo, **1** está habilitado e **0** está desabilitado. |

> [!IMPORTANT]
> A variável de ambiente **MSSQL_PID** do SQL no Edge só aceita **Premium** e **Developer** como valores válidos. O Azure SQL Edge não dá suporte à inicialização usando uma chave do produto.

> [!NOTE]
> Baixe os [termos de licença para software Microsoft para o](https://go.microsoft.com/fwlink/?linkid=2128283) Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>Especificar as variáveis de ambiente

Especifique as variáveis de ambiente para o SQL Edge ao implantar o serviço por meio do [portal do Azure](deploy-portal.md). Você pode adicioná-los na seção de **variáveis de ambiente** da implantação do módulo ou como parte das **Opções de criação do contêiner**.

Adicione valores em **variáveis de ambiente**.

![Definir usando a lista de variáveis de ambiente](media/configure/set-environment-variables.png)

Adicione valores nas **Opções de criação de contêiner**.

![Definir usando opções de criação de contêiner](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Configurar usando um arquivo MSSQL. conf

O Azure SQL Edge não inclui o [Utilitário de configuração MSSQL-conf,](/sql/linux/sql-server-linux-configure-mssql-conf/) como SQL Server em Linux. Você precisa configurar manualmente o arquivo MSSQL. conf e colocá-lo na unidade de armazenamento persistente que é mapeada para a pasta/var/opt/MSSQL/no módulo SQL Edge. Quando você estiver implantando o SQL Edge do Azure Marketplace, esse mapeamento será especificado como a opção **montagens** nas **Opções de criação do contêiner**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

As opções MSSQL. conf a seguir não são aplicáveis ao SQL Edge:

|Opção|Descrição|
|:---|:---|
|**Comentários do cliente** | Escolha se SQL Server envia comentários à Microsoft. |
|**Perfil do Database Mail** | Definir o perfil padrão do Database Mail para o SQL Server em Linux. |
|**Alta disponibilidade** | Habilitar grupos de disponibilidade. |
|**Coordenador de Transações Distribuídas da Microsoft** | Configurar e solucionar problemas do MSDTC no Linux. Não há suporte para as opções de configuração relacionadas à transação distribuída adicional para o SQL Edge. Para obter mais informações sobre essas opções de configuração adicionais, consulte [Configurar o MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**EULAs para Serviços de Machine Learning** | Aceite os EULAs do R e do Python para pacotes Azure Machine Learning. Aplica-se somente ao SQL Server 2019.|
|**outboundnetworkaccess** |Habilitar o acesso à rede para extensões Java, R e Python dos [Serviços de Machine Learning](/sql/linux/sql-server-linux-setup-machine-learning/).|

O arquivo MSSQL. conf de exemplo a seguir funciona para o SQL Edge. Para obter mais informações sobre o formato de um arquivo MSSQL. conf, consulte o [formato MSSQL. conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="next-steps"></a>Próximas etapas

- [Conectar-se ao SQL do Azure no Edge](connect.md)
- [Criar uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)
