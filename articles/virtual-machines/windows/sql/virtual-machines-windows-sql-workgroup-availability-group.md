---
title: Configurar um grupo de disponibilidade de grupos de trabalho independente de domínio
description: Saiba como configurar um grupo de trabalho de Always On de Domínio do Active Directory independente de grupos de disponibilidade em uma máquina virtual SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77122669"
---
# <a name="configure-a-workgroup-availability-group"></a>Configurar um grupo de disponibilidade de grupos de trabalho 

Este artigo explica as etapas necessárias para criar um Active Directory cluster independente de domínio com um grupo de disponibilidade Always On; Isso também é conhecido como um cluster de grupo de trabalho. Este artigo se concentra nas etapas que são relevantes para preparar e configurar o grupo de trabalho e grupos de disponibilidade e os glossários sobre as etapas abordadas em outros artigos, como criar o cluster ou implantar o grupo de disponibilidade. 


## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade de grupos de trabalho, você precisará do seguinte:
- Pelo menos duas máquinas virtuais do Windows Server 2016 (ou superior) que executam o SQL Server 2016 (ou superior), implantadas no mesmo conjunto de disponibilidade ou zonas de disponibilidade diferentes, usando endereços IP estáticos. 
- Uma rede local com um mínimo de 4 endereços IP livres na sub-rede. 
- Uma conta em cada computador no grupo de administradores que também tem direitos de sysadmin dentro de SQL Server. 
- Portas abertas: TCP 1433, TCP 5022, TCP 59999. 

Para referência, os seguintes parâmetros são usados neste artigo, mas podem ser modificados conforme necessário: 

| **Nome** | **Parâmetro** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **NODE2**   | AGNode2 (10.0.0.5) |
| **Nome do cluster** | AGWGAG (10.0.0.6) |
| **Ouvinte** | AGListener (10.0.0.7) | 
| **Sufixo DNS** | ag.wgcluster.example.com | 
| **Nome do grupo de trabalho** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Definir sufixo DNS 

Nesta etapa, configure o sufixo DNS para ambos os servidores. Por exemplo, `ag.wgcluster.example.com`. Isso permite que você use o nome do objeto ao qual você deseja se conectar como um endereço totalmente qualificado em sua rede, como `AGNode1.ag.wgcluster.example.com`. 

Para configurar o sufixo DNS, siga estas etapas:

1. RDP em seu primeiro nó e abra Gerenciador do Servidor. 
1. Selecione **servidor local** e, em seguida, selecione o nome da sua máquina virtual em **nome do computador**. 
1. Selecione **alterar...** em **para renomear este computador...**. 
1. Altere o nome do nome do grupo de trabalho para que seja algo significativo, `AGWORKGROUP`como: 

   ![Alterar nome do grupo de trabalho](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Selecione **mais...** para abrir o **sufixo DNS e a caixa de diálogo nome do computador NetBIOS** . 
1. Digite o nome do sufixo DNS em **sufixo DNS primário deste computador**, como `ag.wgcluster.example.com` e, em seguida, selecione **OK**: 

   ![Adicionar sufixo DNS](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Confirme se o **nome completo do computador** agora está mostrando o sufixo DNS e, em seguida, selecione **OK** para salvar as alterações: 

   ![Adicionar sufixo DNS](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Reinicialize o servidor quando solicitado a fazê-lo. 
1. Repita essas etapas em outros nós a serem usados para o grupo de disponibilidade. 

## <a name="edit-host-file"></a>Editar arquivo de host

Como não há nenhum Active Directory, não há como autenticar conexões do Windows. Dessa forma, atribua confiança editando o arquivo de host com um editor de texto. 

Para editar o arquivo de host, siga estas etapas:

1. RDP em sua máquina virtual. 
1. Use o **Explorador de arquivos** para `c:\windows\system32\drivers\etc`ir para. 
1. Clique com o botão direito do mouse no arquivo de **hosts** e abra o arquivo com o **bloco de notas** (ou qualquer outro editor de texto).
1. No final do arquivo, adicione uma entrada para cada nó, o grupo de disponibilidade e o ouvinte no formato `IP Address, DNS Suffix #comment` como: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Adicionar entradas para o endereço IP, o cluster e o ouvinte ao arquivo de host](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Configurar permissões

Como não há Active Directory para gerenciar permissões, você precisa permitir manualmente uma conta de administrador local não Builtin para criar o cluster. 

Para fazer isso, execute o seguinte cmdlet do PowerShell em uma sessão administrativa do PowerShell em cada nó: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Nesta etapa, você criará o cluster de failover. Se você não estiver familiarizado com essas etapas, poderá segui-las no [tutorial do cluster de failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Diferenças notáveis entre o tutorial e o que deve ser feito para um cluster de grupo de trabalho:
- Desmarque **armazenamento**e **espaços de armazenamento diretos** ao executar a validação do cluster. 
- Ao adicionar os nós ao cluster, adicione o nome totalmente qualificado, como:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Desmarque **Adicionar todo o armazenamento elegível ao cluster**. 

Depois que o cluster tiver sido criado, atribua um endereço IP de cluster estático. Para fazer isso, siga estas etapas:

1. Em um dos nós, abra **Gerenciador de cluster de failover**, selecione o cluster, clique com o botão direito do mouse no **nome \<: ClusterNam>** em **recursos principais do cluster** e selecione **Propriedades**. 

   ![Propriedades de inicialização para o nome do cluster](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Selecione o endereço IP em **endereços IP** e selecione **Editar**. 
1. Selecione **usar estático**, forneça o endereço IP do cluster e, em seguida, selecione **OK**: 

   ![Forneça um endereço IP estático para o cluster](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Verifique se as configurações parecem corretas e selecione **OK** para salvá-las:

   ![Verificar as propriedades do cluster](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem 

Nesta etapa, configure uma testemunha de compartilhamento de nuvem. Se você não estiver familiarizado com as etapas, consulte o [tutorial cluster de failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Habilitar recurso de grupo de disponibilidade 

Nesta etapa, habilite o recurso de grupo de disponibilidade. Se você não estiver familiarizado com as etapas, consulte o [tutorial do grupo de disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Criar chaves e certificado

Nesta etapa, crie certificados que um logon do SQL usa no ponto de extremidade criptografado. Crie uma pasta em cada nó para manter os backups de certificado, `c:\certs`como. 

Para configurar o primeiro nó, siga estas etapas: 

1. Abra **SQL Server Management Studio** e conecte-se ao seu primeiro nó, `AGNode1`como. 
1. Abra uma **nova** janela de consulta e execute a seguinte instrução TRANSACT-SQL (T-SQL) após atualizar para uma senha complexa e segura:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Em seguida, crie o ponto de extremidade HADR e use o certificado para autenticação executando esta instrução Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Use o **Explorador de arquivos** para ir até o local do arquivo onde está o certificado `c:\certs`, como. 
1. Faça uma cópia manual do certificado, como `AGNode1Cert.crt`, do primeiro nó e transfira-o para o mesmo local no segundo nó. 

Para configurar o segundo nó, siga estas etapas: 

1. Conecte-se ao segundo nó com **SQL Server Management Studio**, como `AGNode2`. 
1. Em uma **nova** janela de consulta, execute a seguinte instrução TRANSACT-SQL (T-SQL) após atualizar para uma senha complexa e segura: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Em seguida, crie o ponto de extremidade HADR e use o certificado para autenticação executando esta instrução Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Use o **Explorador de arquivos** para ir até o local do arquivo onde está o certificado `c:\certs`, como. 
1. Faça uma cópia manual do certificado, como `AGNode2Cert.crt`, do segundo nó e transfira-o para o mesmo local no primeiro nó. 

Se houver outros nós no cluster, repita essas etapas também, modificando os respectivos nomes de certificado. 

## <a name="create-logins"></a>Criar logons

A autenticação de certificado é usada para sincronizar dados entre nós. Para permitir isso, crie um logon para o outro nó, crie um usuário para o logon, crie um certificado para o logon para usar o certificado de backup e, em seguida, conceda a conexão no ponto de extremidade de espelhamento. 

Para fazer isso, primeiro execute a seguinte consulta Transact-SQL (T-SQL) no primeiro nó, como `AGNode1`: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Em seguida, execute a seguinte consulta Transact-SQL (T-SQL) no segundo nó, como `AGNode2`: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Se houver outros nós no cluster, repita essas etapas também, modificando os respectivos nomes de certificado e de usuário. 

## <a name="configure-availability-group"></a>Configurar o grupo de disponibilidade

Nesta etapa, configure seu grupo de disponibilidade e adicione seus bancos de dados a ele. Não crie um ouvinte neste momento. Se você não estiver familiarizado com as etapas, consulte o [tutorial do grupo de disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Certifique-se de iniciar um failover e um failback para verificar se tudo está funcionando como deveria ser. 

   > [!NOTE]
   > Se houver uma falha durante o processo de sincronização, talvez seja necessário conceder `NT AUTHORITY\SYSTEM` direitos sysadmin para criar recursos de cluster no primeiro nó, como `AGNode1` temporariamente. 

## <a name="configure-load-balancer"></a>Configurar o balanceador de carga

Nesta etapa final, configure o balanceador de carga usando o [portal do Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md) ou o [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Próximas etapas

Você também pode usar [AZ SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) para configurar um grupo de disponibilidade. 


