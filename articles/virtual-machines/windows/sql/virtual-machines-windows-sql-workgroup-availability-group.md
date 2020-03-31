---
title: Configure um grupo de disponibilidade de grupo de trabalho independente de domínio
description: Saiba como configurar um grupo de trabalho independente do domínio do active directory Always On em uma máquina virtual do SQL Server no Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122669"
---
# <a name="configure-a-workgroup-availability-group"></a>Configure um grupo de disponibilidade de grupo de trabalho 

Este artigo explica as etapas necessárias para criar um cluster independente de domínio do Active Directory com um grupo de disponibilidade Always On; isso também é conhecido como um cluster de grupo de trabalho. Este artigo foca nas etapas relevantes para a preparação e configuração do grupo de trabalho e do grupo de disponibilidade, e gloss sobre etapas que são abordadas em outros artigos, como como criar o cluster ou implantar o grupo de disponibilidade. 


## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade de grupo de trabalho, você precisa do seguinte:
- Pelo menos duas máquinas virtuais do Windows Server 2016 (ou superior) executando o SQL Server 2016 (ou superior), implantadas no mesmo conjunto de disponibilidade, ou diferentes zonas de disponibilidade, usando endereços IP estáticos. 
- Uma rede local com um mínimo de 4 endereços IP gratuitos na sub-rede. 
- Uma conta em cada máquina do grupo administrador que também tem direitos de sysadmin dentro do SQL Server. 
- Portas abertas: TCP 1433, TCP 5022, TCP 59999. 

Para referência, os seguintes parâmetros são utilizados neste artigo, mas podem ser modificados conforme necessário: 

| **Nome** | **Parâmetro** |
| :------ | :---------------------------------- |
| **Nó1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **Nome do cluster** | AGWGAG (10.0.0.6) |
| **Ouvinte** | AGListener (10.0.0.7) | 
| **Sufixo DNS** | ag.wgcluster.example.com | 
| **Nome do grupo de trabalho** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Definir sufixo DNS 

Nesta etapa, configure o sufixo DNS para ambos os servidores. Por exemplo, `ag.wgcluster.example.com`. Isso permite que você use o nome do objeto ao qual deseja se `AGNode1.ag.wgcluster.example.com`conectar como um endereço totalmente qualificado dentro de sua rede, como . 

Para configurar o sufixo DNS, siga estas etapas:

1. RDP em seu primeiro nó e abra o Server Manager. 
1. Selecione **Servidor Local** e selecione o nome da sua máquina virtual com **o nome do computador**. 
1. Selecione **Alterar...** para **renomear este computador...**. 
1. Alterar o nome do grupo de trabalho para `AGWORKGROUP`ser algo significativo, como: 

   ![Alterar o nome do grupo de trabalho](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Selecione **Mais...** para abrir a caixa de diálogo **Sufixo DNS e Nome do Computador NetBIOS.** 
1. Digite o nome do seu sufixo DNS no **sufixo DNS primário deste computador,** como e, em `ag.wgcluster.example.com` seguida, selecione **OK**: 

   ![Adicionar sufixo DNS](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Confirme se o **nome completo** do computador está mostrando agora o sufixo DNS e, em seguida, selecione **OK** para salvar suas alterações: 

   ![Adicionar sufixo DNS](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Reinicie o servidor quando for solicitado a fazê-lo. 
1. Repita essas etapas em quaisquer outros nós a serem usados para o grupo de disponibilidade. 

## <a name="edit-host-file"></a>Editar arquivo host

Como não há diretório ativo, não há como autenticar conexões do Windows. Como tal, atribua confiança editando o arquivo host com um editor de texto. 

Para editar o arquivo host, siga estas etapas:

1. RDP em sua máquina virtual. 
1. Use **o File** `c:\windows\system32\drivers\etc`Explorer para ir para . 
1. Clique com o botão direito do mouse no arquivo **hosts** e abra o arquivo com **o Bloco de Notas** (ou qualquer outro editor de texto).
1. No final do arquivo, adicione uma entrada para cada nó, o grupo de `IP Address, DNS Suffix #comment` disponibilidade e o ouvinte na forma de: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Adicionar entradas para o endereço IP, cluster e ouvinte para o arquivo host](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Definir permissões

Como não há diretório ativo para gerenciar permissões, você precisa permitir manualmente que uma conta de administrador local não incorporada crie o cluster. 

Para isso, execute o seguinte cmdlet PowerShell em uma sessão administrativa do PowerShell em cada nó: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Nesta etapa, você criará o cluster failover. Se você não estiver familiarizado com essas etapas, você pode segui-las a partir do tutorial de [cluster failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Diferenças notáveis entre o tutorial e o que deve ser feito para um cluster de grupo de trabalho:
- Desmarcar **armazenamento**e **espaços de armazenamento direto ao** executar a validação do cluster. 
- Ao adicionar os nós ao cluster, adicione o nome totalmente qualificado, como:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Desmarcar **Adicionar todo o armazenamento elegível ao cluster**. 

Uma vez criado o cluster, atribua um endereço IP de cluster estático. Para fazer isso, siga estas etapas:

1. Em um dos nós, abra **failover cluster manager,** selecione o cluster, clique com o botão direito do mouse no ** \<Nome: ClusterNam>** em **Cluster Core Resources** e, em seguida, selecione **Propriedades**. 

   ![Propriedades de lançamento para o nome do cluster](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Selecione o endereço IP em **Endereços IP** e selecione **Editar**. 
1. Selecione **Usar Estático,** forneça o endereço IP do cluster e selecione **OK**: 

   ![Forneça um endereço IP estático para o cluster](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Verifique se suas configurações estão corretas e selecione **OK** para salvá-las:

   ![Verificar propriedades do cluster](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem 

Nesta etapa, configure uma testemunha de compartilhamento de nuvem. Se você não estiver familiarizado com as etapas, consulte o tutorial de [cluster failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Habilitar o recurso de grupo de disponibilidade 

Nesta etapa, habilite o recurso grupo de disponibilidade. Se você não estiver familiarizado com as etapas, consulte o tutorial do [grupo de disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Criar chaves e certificado

Nesta etapa, crie certificados que um login SQL usa no ponto final criptografado. Crie uma pasta em cada nó para conter `c:\certs`os backups do certificado, tais como . 

Para configurar o primeiro nó, siga estas etapas: 

1. Abra **o SQL Server Management Studio** e conecte-se ao seu primeiro nó, como `AGNode1`. 
1. Abra uma nova janela **de consulta** e execute a seguinte declaração Transact-SQL (T-SQL) após a atualização para uma senha complexa e segura:

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

1. Em seguida, crie o ponto final HADR e use o certificado de autenticação executando esta declaração Transact-SQL (T-SQL):

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

1. Use **o File Explorer** para ir ao local `c:\certs`do arquivo onde seu certificado está, como . 
1. Faça manualmente uma cópia do `AGNode1Cert.crt`certificado, como, do primeiro nó, e transfira-o para o mesmo local no segundo nó. 

Para configurar o segundo nó, siga estas etapas: 

1. Conecte-se ao segundo nó com **o SQL Server Management Studio,** como `AGNode2`. 
1. Em uma nova janela **de consulta,** execute a seguinte declaração Transact-SQL (T-SQL) após a atualização para uma senha complexa e segura: 

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

1. Em seguida, crie o ponto final HADR e use o certificado de autenticação executando esta declaração Transact-SQL (T-SQL):

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

1. Use **o File Explorer** para ir ao local `c:\certs`do arquivo onde seu certificado está, como . 
1. Faça manualmente uma cópia do `AGNode2Cert.crt`certificado, como, a partir do segundo nó, e transfira-o para o mesmo local no primeiro nó. 

Se houver outros nós no cluster, repita essas etapas também, modificando os respectivos nomes de certificados. 

## <a name="create-logins"></a>Criar logins

A autenticação do certificado é usada para sincronizar dados entre os nós. Para permitir isso, crie um login para o outro nó, crie um usuário para o login, crie um certificado para o login para usar o certificado de backup e, em seguida, conceda conexão no ponto final do espelhamento. 

Para isso, primeiro execute a seguinte consulta Transact-SQL (T-SQL) no `AGNode1`primeiro nó, tais como: 

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

Em seguida, execute a seguinte consulta Transact-SQL (T-SQL) `AGNode2`no segundo nó, tais como: 

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

Se houver outros nós no cluster, repita essas etapas também, modificando o respectivo certificado e os nomes de usuário. 

## <a name="configure-availability-group"></a>Configurar o grupo de disponibilidade

Nesta etapa, configure seu grupo de disponibilidade e adicione seus bancos de dados a ele. Não crie um ouvinte neste momento. Se você não estiver familiarizado com as etapas, consulte o tutorial do [grupo de disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Certifique-se de iniciar um failover e failback para verificar se tudo está funcionando como deveria. 

   > [!NOTE]
   > Se houver uma falha durante o processo de `NT AUTHORITY\SYSTEM` sincronização, talvez seja necessário conceder direitos sysadmin para criar recursos de cluster no primeiro nó, como `AGNode1` temporariamente. 

## <a name="configure-load-balancer"></a>Configurar balanceador de carga

Nesta etapa final, configure o balanceador de carga usando o [portal Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md) ou [o PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Próximas etapas

Você também pode usar [a Cli Az SQL VM](virtual-machines-windows-sql-availability-group-cli.md) para configurar um grupo de disponibilidade. 


