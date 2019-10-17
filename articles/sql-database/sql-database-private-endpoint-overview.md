---
title: Link Privado do Banco de Dados SQL do Azure e do Data Warehouse | Microsoft Docs
description: Visão geral do recurso do ponto de extremidade privado
author: rohitnayakmsft
ms.author: rohitna
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 09/17/2019
ms.openlocfilehash: 3adcccde89823f865535c9e1350ac4c3e541eef6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "71066249"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse-preview"></a>Link Privado do Banco de Dados SQL do Azure e do Data Warehouse (versão prévia)

O Link Privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um **ponto de extremidade privado**. Para obter uma lista dos serviços de PaaS que dão suporte à funcionalidade de Link Privado, acesse a página [Documentação do Link Privado](../private-link/index.yml). Um ponto de extremidade privado é um endereço IP privado em uma [VNET](../virtual-network/virtual-networks-overview.md) e sub-rede específicas. 

> [!IMPORTANT]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse. Este artigo *não* se aplica à implantação de uma **instância gerenciada** no Banco de Dados SQL do Azure.

## <a name="data-exfiltration-prevention"></a>Prevenção contra exportação de dados

A exportação de dados no Banco de Dados SQL do Azure ocorre quando um usuário autorizado, como um administrador de banco de dados, tem a capacidade de extrair dados de um sistema e movê-los para outra localização ou outro sistema fora da organização. Por exemplo, o usuário move os dados para uma conta de armazenamento pertencente a terceiros.

Considere um cenário com um usuário que executa o SSMS (SQL Server Management Studio) em uma VM do Azure que se conecta a um Banco de Dados SQL. Esse Banco de Dados SQL está no data center do Oeste dos EUA. O exemplo abaixo mostra como limitar o acesso com pontos de extremidade públicos no Banco de Dados SQL usando controles de acesso à rede.

1. Desabilite todo o tráfego de serviço do Azure para o Banco de Dados SQL por meio do ponto de extremidade público configurando Permitir Serviços do Azure como **DESATIVADO**. Verifique se nenhum endereço IP é permitido nas regras de firewall no nível de servidor e de banco de dados. Para obter mais informações, confira [Controles de acesso à rede do Data Warehouse e do Banco de Dados SQL do Azure](sql-database-networkaccess-overview.md).
1. Só permita o tráfego para o Banco de Dados SQL usando o endereço IP privado da VM. Para obter mais informações, confira os artigos sobre o [ponto de extremidade de serviço](sql-database-vnet-service-endpoint-rule-overview.md) e as [regras de firewall da VNET](sql-database-firewall-configure.md).
1. Na VM do Azure, restrinja o escopo da conexão de saída usando [NSGs (grupos de segurança de rede)](../virtual-network/manage-network-security-group.md) e Marcas de Serviço, conforme mostrado a seguir
    - Especifique uma regra NSG para permitir o tráfego para a Tag de Serviço = SQL.WestUs – só permitindo a conexão com o Banco de Dados SQL no Oeste dos EUA
    - Especifique uma regra NSG (com uma **prioridade mais alta**) para negar o tráfego para a Tag de Serviço = SQL – negando as conexões com o Banco de Dados SQL em todas as regiões

Ao final desta configuração, a VM do Azure só poderá se conectar aos Bancos de Dados SQL na região Oeste dos EUA. No entanto, a conectividade não é restrita a um único Banco de Dados SQL. A VM ainda pode se conectar a qualquer Banco de Dados SQL na região Oeste dos EUA, incluindo os bancos de dados que não fazem parte da assinatura. Embora tenhamos reduzido o escopo da exportação de dados no cenário acima a uma região específica, não o eliminamos por completo.

Com o Link Privado, os clientes agora podem configurar controles de acesso à rede como NSGs para restringir o acesso ao ponto de extremidade privado. Os recursos de PaaS individuais do Azure são então mapeados para pontos de extremidade privados específicos. Um usuário interno mal-intencionado só pode acessar o recurso de PaaS mapeado (por exemplo, um Banco de Dados SQL) e nenhum outro recurso. 

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade local no emparelhamento privado

Quando os clientes se conectam ao ponto de extremidade público em computadores locais, seus endereços IP precisam ser adicionados ao firewall baseado em IP usando uma [Regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md). Embora esse modelo funcione bem para permitir o acesso a computadores individuais para cargas de trabalho de desenvolvimento ou teste, é difícil gerenciá-lo em um ambiente de produção.

Com o Link Privado, os clientes podem habilitar o acesso entre instalações ao ponto de extremidade privado usando o [ExpressRoute](../expressroute/expressroute-introduction.md), o emparelhamento privado ou o túnel de VPN. Os clientes podem então desabilitar todo o acesso por meio do ponto de extremidade público e não usar o firewall baseado em IP para permitir qualquer endereço IP.

Com o Link Privado, os clientes podem habilitar o acesso entre instalações ao ponto de extremidade privado usando o túnel de VPN ou o emparelhamento privado do ER (ExpressRoute). Em seguida, eles podem desabilitar todo o acesso por meio do ponto de extremidade público e não usar o firewall baseado em IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Como configurar o Link Privado para o Banco de Dados SQL do Azure 

### <a name="creation-process"></a>Processo de criação
Os pontos de extremidade privados podem ser criados usando o portal, o PowerShell ou a CLI do Azure:
- [Portal](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Processo de aprovação
Depois que o administrador de rede cria o PE (ponto de extremidade privado), o administrador do SQL pode gerenciar a PEC (conexão de ponto de extremidade privado) com o Banco de Dados SQL.

1. Navegue até o recurso do SQL Server no portal do Azure.

    - (1) Selecione as conexões de ponto de extremidade privado no painel esquerdo
    - (2) Mostra uma lista de todas as PECs (conexões de ponto de extremidade privado)
    - (3) O PE (ponto de extremidade privado) correspondente criou a ![captura de tela de todas as PECs][3]

1. Selecione uma PEC individual na lista selecionando-a.
![Captura de tela selecionou a PEC][6]

1. O administrador do SQL pode optar por aprovar ou rejeitar uma PEC e, opcionalmente, adicionar uma resposta de texto curto.
![Captura de tela da aprovação da PEC][4]

1. Após a aprovação ou a rejeição, a lista refletirá o estado apropriado junto com o texto de resposta.
![Captura de tela de todas as PECs após a aprovação][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Casos de uso do Link Privado para o Banco de Dados SQL do Azure 

Os clientes podem se conectar ao ponto de extremidade privado na mesma VNET, VNET emparelhada na mesma região ou por meio da conexão VNET a VNET entre regiões. Além disso, os clientes podem se conectar localmente usando o ExpressRoute, o emparelhamento privado ou o túnel de VPN. Veja abaixo um diagrama simplificado que mostra os casos de uso comuns.

 ![Diagrama das opções de conectividade][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testar a conectividade com o Banco de Dados SQL em uma VM do Azure na mesma VNET (Rede Virtual)

Para este cenário, suponha que você tenha criado uma VM (Máquina Virtual) do Azure que executa o Windows Server 2016. 

1. [Inicie uma sessão RDP (Área de Trabalho Remota) e conecte-se à máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. Em seguida, você pode fazer algumas verificações de conectividade básicas para garantir que a VM esteja se conectando ao Banco de Dados SQL por meio do ponto de extremidade privado usando as seguintes ferramentas:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Verificar a conectividade usando o Telnet

O [Cliente Telnet](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) é um recurso do Windows que pode ser usado para testar a conectividade. Dependendo da versão do sistema operacional Windows, talvez seja necessário habilitar esse recurso explicitamente. 

Abra uma janela do prompt de comando depois de instalar o Telnet. Execute o comando do Telnet e especifique o endereço IP e o ponto de extremidade privado do Banco de Dados SQL.

```
>telnet 10.1.1.5 1433
```

Quando o Telnet se conectar com êxito, você verá uma tela em branco na janela Comando, como a imagem abaixo:

 ![Diagrama do Telnet][2]

### <a name="check-connectivity-using-psping"></a>Verificar a conectividade usando o Psping

O [Psping](/sysinternals/downloads/psping) pode ser usado como mostrado a seguir para verificar se a PEC (conexão de ponto de extremidade privado) está escutando conexões na porta 1433.

Execute o Psping da seguinte maneira, fornecendo o FQDN do servidor do Banco de Dados SQL e a porta 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

A saída mostra que o Psping pode executar ping no endereço IP privado associado à PEC.

### <a name="check-connectivity-using-nmap"></a>Verificar a conectividade usando o Nmap

O Nmap (Mapeador de Rede) é uma ferramenta gratuita e open-source usada para a descoberta de rede e auditoria de segurança. Para obter mais informações e o link de download, visite https://nmap.org. Use essa ferramenta para garantir que o ponto de extremidade privado esteja escutando conexões na porta 1433.

Execute o Nmap como mostrado a seguir, fornecendo o intervalo de endereços da sub-rede que hospeda o ponto de extremidade privado.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

O resultado mostra que um endereço IP está ativo, que corresponde ao endereço IP do ponto de extremidade privado.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Verificar a conectividade usando o SSMS (SQL Server Management Studio)

A última etapa é usar [o SSMS para se conectar ao Banco de Dados SQL](sql-database-connect-query-ssms.md). Depois de se conectar ao Banco de Dados SQL usando o SSMS, verifique se você está se conectando no endereço IP privado da VM do Azure executando a seguinte consulta:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````
> [!NOTE]
> Na versão prévia, as conexões com o ponto de extremidade privado só dão suporte ao **Proxy** como a [política de conexão](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Como se conectar por meio de uma VM do Azure na VNET (Rede Virtual) emparelhada 

Configure o [Emparelhamento VNET](../virtual-network/tutorial-connect-virtual-networks-powershell.md) para estabelecer a conectividade com o Banco de Dados SQL por meio de uma VM do Azure em uma VNET emparelhada.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Como se conectar por meio de uma VM do Azure no ambiente VNET a VNET

Configure a [conexão de gateway de VPN VNET a VNET](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para estabelecer a conectividade com um Banco de Dados SQL por meio de uma VM do Azure em uma região ou uma assinatura diferente.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Como se conectar por meio de um ambiente local pela VPN

Para estabelecer a conectividade de um ambiente local com o Banco de Dados SQL, escolha e implemente uma das opções:
- [Conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Conexão VPN site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Como se conectar de um SQL Data Warehouse do Azure ao Armazenamento do Azure usando o PolyBase

O PolyBase é comumente usado para carregar dados no SQL Data Warehouse do Azure de contas do Armazenamento do Azure. Se a conta de Armazenamento do Azure da qual você está carregando dados limita o acesso somente a um conjunto de sub-redes da VNET por meio de pontos de extremidade privados, pontos de extremidade de serviço ou firewalls baseados em IP, a conectividade do PolyBase com a conta será interrompida. Para habilitar os cenários de importação e exportação do PolyBase com o SQL Data Warehouse do Azure que se conectam ao Armazenamento do Azure protegido com uma VNET, siga as etapas fornecidas [aqui](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). 



## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral de segurança do Banco de Dados SQL do Azure, consulte [Protegendo seu banco de dados](sql-database-security-overview.md)
- Para obter uma visão geral da conectividade do Banco de Dados SQL do Azure, confira [Arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png
