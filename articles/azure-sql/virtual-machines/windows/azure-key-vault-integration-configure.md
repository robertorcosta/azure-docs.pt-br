---
title: Integrar o Key Vault ao SQL Server em VMs do Windows no Azure (Resource Manager) | Microsoft Docs
description: Saiba como automatizar a configuração da criptografia do SQL Server para uso com o cofre de chave do Azure. Este tópico explica como usar a integração do Azure Key Vault com máquinas virtuais do SQL criadas com o Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 966daa52652846004d163e230fab227a78c20f7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669243"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurar a integração do Azure Key Vault para SQL Server em VMs do Azure (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Resource Manager](azure-key-vault-integration-configure.md)
> * [Clássico](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

Há vários recursos de criptografia do SQL Server, como [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE (criptografia de nível de coluna)](https://msdn.microsoft.com/library/ms173744.aspx) e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem o gerenciamento e armazenamento de chaves criptográficas usadas para a criptografia. O serviço AKV (Azure Key Vault) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server use essas chaves do Cofre da Chave do Azure.

Se você estiver executando o SQL Server localmente, existem etapas a serem seguidas para [acessar o Azure Key Vault pela instância do SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para o SQL Server em VMs do Azure, você pode economizar tempo usando o recurso de *Integração do Azure Key Vault*.

Quando esse recurso está habilitado, ele instala automaticamente o SQL Server Connector, configura o provedor de EKM a fim de acessar o Cofre da Chave do Azure e cria a credencial para permitir que você acesse seu cofre. Ao examinar as etapas da documentação local mencionada anteriormente, é possível ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisará fazer manualmente é criar o cofre da chave e as chaves. A partir daí, toda a configuração de sua VM do SQL Server será automatizada. Quando esse recurso concluir a configuração, você poderá executar instruções T-SQL (Transact-SQL) para começar a criptografar seus bancos de dados ou backups como faria normalmente.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > O provedor de EKM (gerenciamento de chaves extensível) versão 1.0.4.0 é instalado na VM do SQL Server por meio da [extensão de IaaS (infraestrutura como serviço) do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Atualizar a extensão de IaaS do SQL não atualizará a versão do provedor. Considere a possibilidade de atualizar manualmente a versão do provedor EKM se necessário (por exemplo, ao migrar para uma Instância Gerenciada do SQL).


## <a name="enabling-and-configuring-key-vault-integration"></a>Habilitar e configurar a integração do Key Vault
Você pode habilitar a integração do Key Vault durante o provisionamento ou configurá-la para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Se você estiver provisionando uma nova máquina de virtual do SQL com o Gerenciador de Recursos, o Portal do Azure fornecerá uma forma para habilitar a integração do Azure Key Vault. O recurso de Cofre de Chaves do Azure está disponível somente para as edições Enterprise, Developer e Evaluation do SQL Server.

![Integração do Cofre da Chave do SQL Azure](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Para obter uma explicação detalhada sobre o provisionamento, confira [Provisionar uma máquina virtual do SQL no portal do Azure](create-sql-vm-portal.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para máquinas virtuais SQL existentes, abra o [recurso de máquinas virtuais do SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selecione **Segurança** em **Configurações**. Selecione **Habilitar** para habilitar a integração do Azure Key Vault. 

![Integração do SQL Key Vault para VMs existentes](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Quando tiver terminado, selecione o botão **Aplicar** na parte inferior da página **Segurança** para salvar as alterações.

> [!NOTE]
> O nome da credencial que criamos aqui será mapeada para um logon do SQL posteriormente. Isso permite que o logon do SQL acesse o cofre de chaves. 


> [!NOTE]
> Você também pode configurar a integração de Key Vault usando um modelo. Para saber mais, confira [Azure quickstart template for Azure Key Vault integration (Modelo de início rápido do Azure para integração com o Cofre de Chaves do Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
