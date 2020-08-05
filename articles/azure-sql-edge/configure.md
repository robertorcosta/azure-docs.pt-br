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
ms.date: 07/28/2020
ms.openlocfilehash: 0cb2eed0895c10f649facaa184a5f9f9ea158aa5
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87551975"
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
|**EULAs de serviços ML** | Aceite os EULAs do R e do Python para pacotes Azure Machine Learning. Aplica-se somente ao SQL Server 2019.|
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

## <a name="run-azure-sql-edge-as-non-root-user"></a>Executar o Azure SQL Edge como um usuário não raiz

A partir do Azure SQL Edge CTP 2.2, os contêineres do SQL Edge podem ser executados com um usuário/grupo não raiz. Quando implantado por meio do Azure Marketplace, a menos que um usuário/grupo diferente seja especificado, contêineres do SQL Edge são iniciados como o usuário MSSQL (não raiz). Para especificar um usuário não raiz diferente durante a implantação, adicione o `*"User": "<name|uid>[:<group|gid>]"*` par chave-valor em opções de criação de contêiner. No exemplo abaixo, o SQL Edge está configurado para iniciar como o usuário `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Para permitir que o usuário não raiz Acesse arquivos de BD que estão em volumes montados, verifique se o usuário/grupo no qual você executa o contêiner tem permissões de leitura & gravação no armazenamento de arquivos persistente. No exemplo abaixo, definimos o usuário não raiz com user_id 10001 como o proprietário dos arquivos. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Atualizando de versões anteriores do CTP

O CTP anterior do Azure SQL Edge foi configurado para ser executado como os usuários raiz. As opções a seguir estão disponíveis ao atualizar do CTP anterior

- Continuar a usar o usuário raiz – para continuar usando o usuário raiz, adicione o `*"User": "0:0"*` par chave-valor em opções de criação de contêiner.
- Usar o usuário MSSQL padrão-para usar o usuário MSSQL padrão, siga as etapas abaixo
  - Adicione um usuário chamado MSSQL no host do Docker. No exemplo a seguir, adicionamos um usuário MSSQL com a ID 10001. Esse usuário também é adicionado ao grupo raiz.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Alterar a permissão no volume de montagem/diretório no qual reside o arquivo de banco de dados 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Usar uma conta diferente de usuário não raiz-para usar uma conta de usuário diferente da raiz
  - Atualize as opções de criação do contêiner para especificar o `*"User": "user_name | user_id*` par adicionar chave-valor em opções de criação de contêiner. Substitua user_name ou user_id por um user_name ou user_id real do host do Docker. 
  - Altere as permissões no volume de montagem/diretório.



## <a name="next-steps"></a>Próximas etapas

- [Conectar-se ao SQL do Azure no Edge](connect.md)
- [Criar uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)
