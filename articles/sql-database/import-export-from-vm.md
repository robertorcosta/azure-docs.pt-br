---
title: Importar ou exportar um banco de dados SQL
description: Importe ou exporte um banco de dados Azure SQL sem permitir que os serviços do Azure acessem o servidor.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 61a85b2554bbd69541b3081f72525d2b7deed625
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529225"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importe ou exporte um banco de dados SQL do Azure sem permitir que os serviços do Azure acessem o servidor

Este artigo mostra como importar ou exportar um banco de dados Azure SQL quando *o Allow Azure Services* é definido como *OFF* no servidor SQL do Azure. O fluxo de trabalho usa uma máquina virtual do Azure para executar o SqlPackage para executar a operação de importação ou exportação.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Crie a máquina virtual do Azure

Crie uma máquina virtual Do Azure selecionando o botão **Implantar para o Azure.**

Este modelo permite que você implante uma máquina virtual simples do Windows usando algumas opções diferentes para a versão do Windows, usando a versão corrigida mais recente. Isso implantará uma VM tamanho A2 na localização do grupo de recursos e devolverá o nome de domínio totalmente qualificado da VM.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Para obter mais informações, consulte [Implantação muito simples de uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Conecte-se à máquina virtual

As etapas a seguir mostram como se conectar à sua máquina virtual usando uma conexão remota de desktop.

1. Após a conclusão da implantação, vá para o recurso da máquina virtual.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Selecione **Conectar**.

   Um formulário de Protocolo RDP (arquivo .rdp) é exibido com o IP endereço IP público e o número da porta para a máquina virtual.

   ![Formulário do RDP](./media/import-export-from-vm/rdp.png)  

3. Selecione **Baixar arquivo RDP**.

   > [!NOTE]
   > Você também pode usa SSH para se conectar à VM.

4. Feche o formulário **Conectar-se à máquina virtual**.
5. Para se conectar à sua VM, abra o arquivo RDP baixado.
6. Quando solicitado, selecione **Conectar**. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) da Mac App Store.

7. Insira o nome de usuário e a senha especificados na criação da máquina virtual e escolha **OK**.

8. Você pode receber um aviso de certificado durante o processo de entrada. Escolha **Sim** ou **Continuar** para prosseguir com a conexão.



## <a name="install-sqlpackage"></a>Instalar SqlPackage

[Baixe e instale a versão mais recente do SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Para obter informações adicionais, consulte [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Crie uma regra de firewall para permitir que a VM acesse o banco de dados

Adicione o endereço IP público da máquina virtual ao firewall do servidor sql database.

As etapas a seguir criam uma regra de firewall IP em nível de servidor para o endereço IP público da sua máquina virtual e permitem a conectividade a partir da máquina virtual.

1. Selecione bancos de **dados SQL** no menu à esquerda e selecione seu banco de dados na página **de bancos de dados SQL.** A página de visão geral do seu banco de dados é aberta, mostrando o nome do servidor totalmente qualificado (como **servername.database.windows.net**) e fornece opções para configuração posterior.

2. Copie este nome de servidor totalmente qualificado para usar ao se conectar ao seu servidor e seus bancos de dados.

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png)

3. Selecione **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor de banco de dados será aberta.

   ![regra de firewall de IP no nível do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Escolha **Adicionar IP cliente** na barra de ferramentas para adicionar o endereço IP público da sua máquina virtual a uma nova regra de firewall IP no nível do servidor. Uma regra de firewall de IP no nível do servidor pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Salvar**. Uma regra de firewall IP em nível de servidor é criada para a porta de abertura pública de endereçoIP da sua máquina virtual 1433 no servidor SQL Database.

6. Feche a página **Configurações do Firewall**.



## <a name="export-a-database-using-sqlpackage"></a>Exportar um banco de dados usando SqlPackage

Para exportar um Banco de Dados SQL usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consulte [Exportar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). O utilitário SqlPackage é fornecido com as versões mais recentes do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [do SQL Server Data Tools,](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)ou você pode baixar a versão mais recente do [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Recomendamos o uso do utilitário SqlPackage para escala e desempenho na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar um banco de dados usando SqlPackage.exe com autenticação universal do Active Directory. Substitua por valores específicos do seu ambiente.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importe um banco de dados usando O SqlPackage

Para importar um Banco de Dados do SQL Server usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), confira [Importar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage possui o mais recente [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [sql Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Você também pode baixar a versão mais recente do [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Para escala e desempenho, recomendamos usar o SqlPackage na maioria dos ambientes de produção em vez de usar no portal do Azure. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos `BACPAC`, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

O comando SqlPackage a seguir importa o banco de dados **AdventureWorks2017** do armazenamento local para um servidor de banco de dados SQL Do Azure SQL. Ele cria um novo banco de dados chamado **myMigratedDatabase** com uma camada de serviço **Premium** e um Objetivo de serviço **P6**. Altere esses valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se conectar a um servidor do Banco de Dados SQL gerenciando um banco de dados individual por trás de um firewall corporativo, o firewall precisa estar com a porta 1433 aberta. Para se conectar a uma instância gerenciada, você deve ter uma [conexão ponto a site](sql-database-managed-instance-configure-p2s.md) ou uma conexão de rota expressa.

Este exemplo mostra como importar um banco de dados usando o SqlPackage com a Autenticação Universal do Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Considerações sobre o desempenho

As velocidades de exportação variam devido a muitos fatores (por exemplo, forma de dados) por isso é impossível prever qual velocidade deve ser esperada. O SqlPackage pode levar um tempo considerável, particularmente para grandes bancos de dados.

Para obter o melhor desempenho, você pode tentar as seguintes estratégias:

1. Certifique-se de que nenhuma outra carga de trabalho está sendo executado no banco de dados. Criar uma cópia antes da exportação pode ser a melhor solução para garantir que nenhuma outra carga de trabalho esteja em execução.
2. Aumente o objetivo de nível de serviço do banco de dados (SLO) para lidar melhor com a carga de trabalho de exportação (leia-se principalmente I/O). Se o banco de dados estiver atualmente GP_Gen5_4, talvez um nível de Business Critical ajude com a carga de trabalho de leitura.
3. Certifique-se de que há índices agrupados especialmente para mesas grandes. 
4. As máquinas virtuais (VMs) devem estar na mesma região do banco de dados para ajudar a evitar restrições de rede.
5. As VMs devem ter SSD com tamanho adequado para gerar artefatos temporários antes de carregar para o armazenamento blob.
6. As VMs devem ter configuração adequada de núcleo e memória para o banco de dados específico.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Armazenar os importados ou exportados . Arquivo BACPAC

O. O arquivo BACPAC pode ser armazenado em [Azure Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)ou [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Para obter o melhor desempenho, use Arquivos Azure. O SqlPackage opera com o sistema de arquivos para que ele possa acessar diretamente os Arquivos Azure.

Para reduzir o custo, use o Azure Blobs, que custa menos do que uma parte de arquivo Premium Azure. No entanto, exigirá que você copie o [. Arquivo BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) entre a bolha e o sistema de arquivos local antes da operação de importação ou exportação. Como resultado, o processo levará mais tempo.

Para carregar ou baixar . Arquivos BACPAC, ver [Transferir dados com armazenamento AzCopy e Blob](../storage/common/storage-use-azcopy-blobs.md)e transferir dados com [AzCopy e armazenamento de arquivos](../storage/common/storage-use-azcopy-files.md).

Dependendo do ambiente, talvez seja necessário [configurar firewalls de armazenamento Azure e redes virtuais.](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Próximas etapas

- Para saber como se conectar e consultar um banco de dados SQL importado, consulte [Quickstart: Azure SQL Database: Use o SQL Server Management Studio para conectar e consultar dados](sql-database-connect-query-ssms.md).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para ver uma discussão sobre todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, consulte [Migração de um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
