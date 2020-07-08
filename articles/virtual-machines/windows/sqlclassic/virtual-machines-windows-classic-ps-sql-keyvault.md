---
title: Integrar Key Vault com uma VM clássica do SQL Server no Azure
description: Saiba como automatizar a configuração da criptografia do SQL Server para uso com o cofre de chave do Azure. Este tópico explica como usar a integração do Cofre de Chaves do Azure com máquinas virtuais do SQL Server para criação no modelo de implantação clássico.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 7439aa360395490f31a638ac690ed7e5cad1054b
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195829"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurar a Integração do Azure Key Vault para SQL Server em Máquinas Virtuais do Azure (Clássicas)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/azure-key-vault-integration-configure.md)
> * [Clássico](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Visão geral
Há vários recursos de criptografia do SQL Server, como [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE (criptografia de nível de coluna)](https://msdn.microsoft.com/library/ms173744.aspx) e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem o gerenciamento e armazenamento de chaves criptográficas usadas para a criptografia. O serviço Cofre da Chave do Azure (AKV) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server use essas chaves do Cofre da Chave do Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e Clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Se você estiver executando o SQL Server em máquinas locais, exitem [etapas a serem seguidas para acessar o Azure Key Vault do computador local com SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para o SQL Server em VMs do Azure, você pode economizar tempo usando o recurso *Integração do Cofre da Chave do Azure* . Com alguns cmdlets do Azure PowerShell para habilitar esse recurso, você poderá automatizar a configuração necessária para que uma VM do SQL acesse seu cofre da chave.

Quando esse recurso está habilitado, ele instala automaticamente o SQL Server Connector, configura o provedor de EKM a fim de acessar o Cofre da Chave do Azure e cria a credencial para permitir que você acesse seu cofre. Ao examinar as etapas da documentação local mencionada anteriormente, é possível ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisará fazer manualmente é criar o cofre da chave e as chaves. A partir daí, toda a configuração de sua VM do SQL será automatizada. Quando esse recurso concluir a configuração, você poderá executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como faria normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configurar a integração de AKV
Use o PowerShell para configurar a integração do Cofre da Chave do Azure. As seções a seguir oferecem uma visão geral dos parâmetros necessários e um exemplo de script do PowerShell.

### <a name="install-the-sql-server-iaas-extension"></a>Instalar a Extensão IaaS do SQL Server
Primeiro, [instale a extensão IaaS do SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Compreender os parâmetros de entrada
A tabela a seguir lista os parâmetros necessários para executar o script do PowerShell na próxima seção.

| Parâmetro | Descrição | Exemplo |
| --- | --- | --- |
| **$akvURL** |**A URL do cofre da chave** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Nome da Entidade de Serviço** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Segredo da Entidade de Serviço** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nome da credencial**: A Integração AKV cria uma credencial no SQL Server, permitindo que a VM tenha acesso ao cofre da chave. Escolha um nome para essa credencial. |"mycred1" |
| **$vmName** |**Nome da máquina virtual**: O nome de uma VM do SQL criada anteriormente. |"myvmname" |
| **$serviceName** |**Nome do serviço**: O nome do Serviço de Nuvem associado à VM do SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Habilitar a integração de AKV com o PowerShell
O cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** cria um objeto de configuração para o recurso de integração do Cofre da Chave do Azure. O cmdlet **Set-AzureVMSqlServerExtension** configura essa integração com o parâmetro **KeyVaultCredentialSettings**. As etapas a seguir mostram como usar esses comandos.

1. No Azure PowerShell, primeiro configure os parâmetros de entrada com valores específicos conforme descrito nas seções anteriores deste tópico. O seguinte script é um exemplo.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Em seguida, use o script abaixo para configurar e habilitar a integração de AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

A extensão do Agente IaaS do SQL atualizará a VM do SQL com essa nova configuração.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

