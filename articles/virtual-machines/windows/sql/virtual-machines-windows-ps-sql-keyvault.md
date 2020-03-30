---
title: Integrar o Key Vault ao SQL Server em VMs do Windows no Azure (Resource Manager) | Microsoft Docs
description: Saiba como automatizar a configuração da criptografia do SQL Server para uso com o cofre de chave do Azure. Este tópico explica como usar a integração do Cofre da Chave do Azure com máquinas virtuais do SQL Server criadas com o Gerenciador de Recursos.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
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
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102142"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Configurar a Integração do Azure Key Vault para SQL Server em Máquinas Virtuais do Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Gerenciador de recursos](virtual-machines-windows-ps-sql-keyvault.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Visão geral
Há vários recursos de criptografia do SQL Server, como [TDE (Transparent Data Encryption)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE (criptografia de nível de coluna)](https://msdn.microsoft.com/library/ms173744.aspx) e [criptografia de backup](https://msdn.microsoft.com/library/dn449489.aspx). Essas formas de criptografia exigem o gerenciamento e armazenamento de chaves criptográficas usadas para a criptografia. O serviço Cofre da Chave do Azure (AKV) foi criado para melhorar a segurança e o gerenciamento dessas chaves em um local seguro e altamente disponível. O [conector sql server](https://www.microsoft.com/download/details.aspx?id=45344) permite que o SQL Server use essas chaves do Azure Key Vault.

Se você estiver executando o SQL Server com máquinas locais, [existem etapas que você pode seguir para acessar o Azure Key Vault a partir de sua máquina SQL Server no local.](https://msdn.microsoft.com/library/dn198405.aspx) Mas para o SQL Server em VMs Azure, você pode economizar tempo usando o recurso *Azure Key Vault Integration.*

Quando esse recurso está habilitado, ele instala automaticamente o SQL Server Connector, configura o provedor de EKM a fim de acessar o Cofre da Chave do Azure e cria a credencial para permitir que você acesse seu cofre. Ao examinar as etapas da documentação local mencionada anteriormente, é possível ver que esse recurso automatiza as etapas 2 e 3. A única coisa que você ainda precisará fazer manualmente é criar o cofre da chave e as chaves. A partir daí, toda a configuração de sua VM do SQL será automatizada. Quando esse recurso concluir a configuração, você poderá executar instruções T-SQL para começar a criptografar seus bancos de dados ou backups como faria normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > O provedor de EKM versão 1.0.4.0 está instalado na VM do SQL Server por meio da [extensão IaaS do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Atualizar a extensão IaaS do SQL não atualizará a versão do provedor. Considere a possibilidade de atualizar manualmente a versão do provedor EKM se necessário (por exemplo, ao migrar para uma Instância Gerenciada do SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Habilitando e configurando a integração de AKV
Você pode habilitar a integração de AKV durante o provisionamento ou configurá-la para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Se você estiver provisionando uma nova máquina de virtual do SQL Server com o Gerenciador de Recursos, o Portal do Azure fornecerá uma forma para habilitar a integração do Cofre de Chaves do Azure. O recurso de Cofre de Chaves do Azure está disponível somente para as edições Enterprise, Developer e Evaluation do SQL Server.

![Integração do Cofre da Chave do SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obter um passo a passo detalhado do provisionamento, consulte [Provision a SQL Server virtual machine in the Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais SQL Server existentes, abra o recurso de [máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e selecione **Segurança** em **Configurações**. Selecione **Ativar** para ativar a integração do Azure Key Vault. 

![Integração de AKV do SQL para VMs existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Quando terminar, selecione o botão **Aplicar** na parte inferior da página **Segurança** para salvar suas alterações.

> [!NOTE]
> O nome de credencial que criamos aqui será mapeado para um login SQL mais tarde. Isso permite que o logon do SQL acesse o cofre de chaves. 


> [!NOTE]
> Você também pode configurar a integração de AKV usando um modelo. Para saber mais, confira [Azure quickstart template for Azure Key Vault integration (Modelo de início rápido do Azure para integração com o Cofre de Chaves do Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
