---
title: Configurar a conectividade ponto a site usando o SSMS
titleSuffix: Azure SQL Managed Instance
description: Conecte-se ao Azure SQL Instância Gerenciada usando o SQL Server Management Studio (SSMS) usando uma conexão ponto a site de um computador cliente local.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 7b9c9fc6259656af77bf1ba1b95ccf190cbd85da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708613"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Início rápido: configurar uma conexão ponto a site com o Azure SQL Instância Gerenciada local
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este guia de início rápido demonstra como se conectar ao Azure SQL Instância Gerenciada usando o SSMS ( [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) ) de um computador cliente local em uma conexão ponto a site. Para obter informações sobre conexões ponto a site, consulte [sobre VPN ponto a site](../../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido:

- Usa os recursos criados em [criar uma instância gerenciada](instance-create-quickstart.md) como ponto de partida.
- Requer o PowerShell 5,1 e Azure PowerShell 1.4.0 ou posterior no computador cliente local. Se necessário, consulte as instruções para [Instalando o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Requer a versão mais recente do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) no computador cliente local.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Anexar um gateway de VPN a uma rede virtual

1. Abra o PowerShell no computador cliente local.

2. Copie este script do PowerShell. Esse script anexa um gateway de VPN à rede virtual do SQL Instância Gerenciada que você criou no guia de início rápido [criar uma instância gerenciada](instance-create-quickstart.md) . Esse script usa o módulo Azure PowerShell AZ e faz o seguinte para hosts baseados em Windows ou Linux:

   - Cria e instala certificados em um computador cliente
   - Calcula o intervalo de IP de sub-rede futuro do gateway de VPN
   - Cria a sub-rede de gateway
   - Implanta o modelo de Azure Resource Manager que anexa o gateway de VPN à sub-rede VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Cole o script na janela do PowerShell e forneça os parâmetros necessários. Os valores para `<subscriptionId>` , `<resourceGroup>` e `<virtualNetworkName>` devem corresponder àqueles que você usou para o início rápido [criar uma instância gerenciada](instance-create-quickstart.md) . O valor de `<certificateNamePrefix>` pode ser uma cadeia de caracteres de sua escolha.

4. Execute o script do PowerShell.

> [!IMPORTANT]
> Não continue até que o script do PowerShell seja concluído.

## <a name="create-a-vpn-connection"></a>Criar uma conexão VPN

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Abra o grupo de recursos no qual você criou o gateway de rede virtual e, em seguida, abra o recurso de gateway de rede virtual.
3. Clique em **Configuração Ponto a Site** e, em seguida, clique em **Fazer o download do cliente VPN**.

    ![Baixar cliente VPN](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. No computador cliente local, extraia os arquivos do arquivo zip e, em seguida, abra a pasta com os arquivos extraídos.
5. Abra a pasta **WindowsAmd64** e abra o arquivo **VpnClientSetupAmd64.exe** .
6. Se você receber uma mensagem **O Windows protegeu seu computador**, clique em **Mais informações** e, em seguida, em **Executar mesmo assim**.

    ![Instalar o cliente VPN](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. Na caixa de diálogo Controle de Conta de Usuário, clique em **Sim** para continuar.
8. Na caixa de diálogo que faz referência à sua rede virtual, selecione **Sim** para instalar o cliente VPN para sua rede virtual.

## <a name="connect-to-the-vpn-connection"></a>Conectar-se à conexão VPN

1. Acesse **VPN** na **rede & Internet** no computador cliente local e selecione sua rede virtual SQL instância gerenciada para estabelecer uma conexão com essa VNet. Na imagem a seguir, a rede virtual é chamada **MyNewVNet**.

    ![Conexão VPN](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Selecione **Conectar**.
3. Na caixa de diálogo, selecione **Sim**.

    ![Conexão VPN](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Quando for solicitado que o Gerenciador de conexões precise de privilégios elevados para atualizar sua tabela de rotas, escolha **continuar**.
5. Clique em **Sim** na caixa de diálogo Controle de Conta de Usuário para continuar.

   Você estabeleceu uma conexão VPN com sua VNet do SQL Instância Gerenciada.

    ![Conexão VPN](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Conectar-se ao SSMS

1. No computador cliente local, abra SQL Server Management Studio.
2. Na caixa de diálogo **conectar ao servidor** , insira o nome de **host** totalmente qualificado para sua instância gerenciada na caixa **nome do servidor** .
3. Selecione **Autenticação do SQL Server**, forneça seu nome de usuário e senha e, em seguida, selecione **Conectar**.

    ![Conexão do SSMS](./media/point-to-site-p2s-configure/ssms-connect.png)  

Depois de se conectar, visualize seus bancos de dados do sistema e do usuário no bancos de dados de nós. Exiba também vários objetos em segurança, objetos de servidor, replicação, gerenciamento, SQL Server Agent e nós do XEvent Profiler.

## <a name="next-steps"></a>Próximas etapas

- Para obter um guia de início rápido mostrando como se conectar de uma máquina virtual do Azure, consulte [Configurar uma conexão ponto a site](point-to-site-p2s-configure.md).
- Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos à Instância Gerenciada de SQL](connect-application-instance.md).
- Para restaurar um banco de dados de SQL Server existente do local para uma instância gerenciada, você pode usar o [serviço de migração de banco de dados do Azure para migração](../../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando T-SQL RESTORE](restore-sample-database-quickstart.md) para restaurar de um arquivo de backup de banco de dados.
