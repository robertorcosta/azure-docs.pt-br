---
title: Configurar SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre como configurar o SQL do Azure no Edge (versão prévia)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594005"
---
# <a name="configure-azure-sql-edge-preview"></a>Configurar SQL do Azure no Edge (versão prévia)

O SQL do Azure no Edge dá suporte para a configuração por meio de uma destas duas opções:

- Usando variáveis de ambiente.
- Usando o arquivo mssql.conf colocado na pasta /var/opt/mssql.

> [!NOTE]
> A definição de variáveis de ambiente substitui as configurações especificadas no arquivo mssql.conf.

## <a name="configure-using-environment-variables"></a>Configurar usando variáveis de ambiente

O SQL do Azure no Edge expõe várias variáveis de ambiente diferentes que podem ser usadas para configurar o contêiner do SQL no Edge. Essas variáveis de ambiente são um subconjunto das variáveis de ambiente disponíveis para SQL Server em Linux. Para obter mais informações sobre variáveis de ambiente do SQL Server em Linux, confira [Variáveis de ambiente](/sql/linux/sql-server-linux-configure-environment-variables/).

As variáveis de ambiente do SQL Server em Linux a seguir não são compatíveis com o SQL do Azure no Edge. Se forem definidas, essas variáveis de ambiente serão ignoradas durante a inicialização do contêiner.

| Variável de ambiente | Descrição |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Habilite o Grupo de Disponibilidade. Por exemplo, '1' é habilitado e '0' ´é desabilitado |

> [!IMPORTANT]
> A variável de ambiente *MSSQL_PID* do SQL no Edge só aceita **Premium** e **Developer** como valores válidos. O SQL do Azure no Edge não é compatível com inicialização usando uma chave do produto.

> [!NOTE]
> Para baixar o contrato de licença de usuário final do SQL do Azure no Edge, consulte [Contrato de licença de usuário final](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Especificando variáveis de ambiente

As variáveis de ambiente do SQL no Edge podem ser especificadas ao implantar o SQL do Azure no Edge por meio do [portal do Azure](deploy-portal.md). Elas podem ser adicionadas na seção "Variáveis de ambiente" da implantação do módulo ou como parte da opção de criação de contêiner, conforme descrito abaixo.

*Definir usando opções de variáveis de ambiente*

![definir usando lista de variáveis de ambiente](media/configure/set-environment-variables.png)

*Definir usando opções de criação de contêiner*

![definir usando opções de criação de contêiner](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Configurar usando o arquivo mssql.conf

O SQL do Azure no Edge não inclui o [utilitário de configuração mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) como o SQL Server em Linux. Por esse motivo, o arquivo mssql.conf precisa ser configurado manualmente e colocado na unidade de armazenamento persistente, que é mapeada até a pasta /var/opt/mssql/ no módulo do SQL no Edge. Ao implantar o SQL no Edge pelo Azure Marketplace, esse mapeamento é especificado como a opção **Mounts” na opção de criação de contêiner.

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

As opções de mssql.conf a seguir não se aplicam ao SQL no Edge:
</br></br>
|Opção|Descrição|
|:---|:---|
|**Comentários do cliente** | Escolher se o SQL Server envia ou não comentários à Microsoft. |
|**Perfil do Database Mail** | Definir o perfil padrão do Database Mail para o SQL Server em Linux. |
|**Alta disponibilidade** | Habilitar grupos de disponibilidade. |
|**Coordenador de Transações Distribuídas da Microsoft** | Configurar e solucionar problemas do MSDTC no Linux. As opções de configuração relacionadas a transações distribuídas adicionais também não são compatíveis com o SQL no Edge. Para obter mais informações sobre essas opções de configuração adicionais, consulte [Configurar o MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**EULAs para Serviços de Machine Learning** | Aceitar os EULAs de R e Python para pacotes de Serviços de Machine Learning. Aplica-se somente ao SQL Server 2019.|
|**outboundnetworkaccess** |Habilitar o acesso à rede para extensões Java, R e Python dos [Serviços de Machine Learning](/sql/linux/sql-server-linux-setup-machine-learning/).|

Um arquivo mssql.conf de exemplo, que funciona no SQL no Edge, é fornecido abaixo. Para obter mais informações sobre o formato do arquivo mssql.conf, consulte [Formato do mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-step"></a>Próxima etapa

- [Conectar-se ao SQL do Azure no Edge](connect.md)
- [Compilar uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)
