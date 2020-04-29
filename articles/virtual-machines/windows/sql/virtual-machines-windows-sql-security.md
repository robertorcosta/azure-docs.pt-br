---
title: Considerações de segurança para o SQL Server no Azure | Microsoft Docs
description: Este tópico fornece diretrizes gerais para proteger o SQL Server em execução em uma Máquina Virtual do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f5ea0ddff38532b119d8d984f2dabd6d898b44a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77031349"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações sobre Segurança para SQL Server em Máquinas Virtuais do Azure

Este tópico inclui diretrizes gerais de segurança que ajudam a estabelecer o acesso seguro a instâncias do SQL Server em uma VM (máquina virtual) do Azure.

O Azure está em conformidade com vários padrões e regulamentações do setor que podem permitir o build de uma solução em conformidade com o SQL Server em execução em uma máquina virtual. Para obter informações sobre conformidade regulamentar com o Azure, consulte a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Controlar o acesso à VM do SQL

Ao criar sua máquina de virtual do SQL Server, considere como controlar cuidadosamente quem tem acesso ao computador e ao SQL Server. Em geral, você deve fazer o seguinte:

- Restrinja o acesso ao SQL Server somente aos aplicativos e clientes que precisam dele.
- Siga as melhores práticas de gerenciamento de contas de usuário e senhas.

As próximas seções fornecem sugestões sobre como considerar esses pontos.

## <a name="secure-connections"></a>Conexões seguras

Quando você cria uma máquina virtual do SQL Server com uma imagem da galeria, a opção **Conectividade do SQL Server** fornece as opções **Local (dentro da VM)**, **Privada (dentro da Rede Virtual)** ou **Pública (Internet)**.

![Conectividade do SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Para obter a melhor segurança, escolha a opção mais restritiva para seu cenário. Por exemplo, se você estiver executando um aplicativo que acessa o SQL Server na mesma VM, **Local** será a opção mais segura. Se você estiver executando um aplicativo do Azure que exige o acesso ao SQL Server, a opção **Privada** protegerá a comunicação com o SQL Server apenas na [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) especificada. Se precisar de acesso **Público** (Internet) à VM do SQL Server, siga as outras melhores práticas deste tópico para reduzir a área da superfície de ataque.

As opções selecionadas no portal usam regras de segurança de entrada no [NSG](../../../virtual-network/security-overview.md) (Grupo de Segurança de Rede) das VMs para permitir ou negar o tráfego de rede à máquina virtual. Modifique ou crie novas regras do NSG de entrada para permitir o tráfego para a porta do SQL Server (1433 padrão). Também especifique endereços IP específicos que têm permissão para se comunicar nessa porta.

![Regras de grupo de segurança de rede](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Além das regras do NSG para restringir o tráfego de rede, você também pode usar o Firewall do Windows na máquina virtual.

Se estiver usando pontos de extremidade com o modelo de implantação clássico, remova todos os pontos de extremidade da máquina virtual, caso não estejam sendo usados. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Gerenciar a ACL em um ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Isso não é necessário para VMs que usam o Resource Manager.

Por fim, considere a possibilidade de habilitar as conexões criptografadas na instância do Mecanismo de Banco de Dados do SQL Server na máquina virtual do Azure. Configure a instância do SQL Server com um certificado assinado. Para saber mais, veja [Enable Encrypted Connections to the Database Engine](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) (Habilitar conexões criptografadas para o mecanismo de banco de dados) e [Sintaxe da cadeia de conexão](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="encryption"></a>Criptografia

O Managed disks oferece criptografia do lado do servidor e Azure Disk Encryption. A [criptografia do lado do servidor](/azure/virtual-machines/windows/disk-encryption) fornece criptografia em repouso e protege seus dados para atender aos compromissos de conformidade e segurança da organização. O [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) usa a tecnologia BITLOCKER ou DM-cript e integra-se com Azure Key Vault para criptografar o sistema operacional e os discos de dados. 

## <a name="use-a-non-default-port"></a>Usar uma porta não padrão

Por padrão, o SQL Server escuta uma porta conhecida, 1433. Para uma maior segurança, configure o SQL Server para escutar uma porta não padrão, como 1401. Se você provisionar uma imagem da galeria do SQL Server no portal do Azure, poderá especificar essa porta na folha **Configurações do SQL Server**.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para configurar isso após o provisionamento, você tem duas opções:

- Para VMs do Gerenciador de recursos, você pode selecionar **segurança** no [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). Isso fornece uma opção para alterar a porta.

  ![Alteração da porta TCP no portal](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Para VMs Clássicas ou para VMs do SQL Server que não foram provisionadas com o portal, é possível configurar a porta manualmente durante a conexão remota à VM. Para obter as etapas de configuração, consulte [Configurar um servidor para escutar uma porta TCP específica](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Se você usar essa técnica manual, também precisará adicionar uma regra do Firewall do Windows para permitir o tráfego de entrada nessa porta TCP.

> [!IMPORTANT]
> A especificação de uma porta não padrão é uma boa ideia se a porta do SQL Server é aberta para conexões com a Internet pública.

Quando o SQL Server está escutando uma porta não padrão, você deve especificar a porta ao se conectar. Por exemplo, considere um cenário em que o endereço IP do servidor é 13.55.255.255 e o SQL Server está escutando a porta 1401. Para se conectar ao SQL Server, você especificará `13.55.255.255,1401` na cadeia de conexão.

## <a name="manage-accounts"></a>Gerenciar Contas

Você não deseja que os invasores adivinhem nomes de contas ou senhas facilmente. Use as seguintes dicas para ajudar:

- Crie uma conta de administrador local exclusiva que não esteja nomeada como **Administrador**.

- Use senhas fortes e complexas para todas as suas contas. Para obter mais informações sobre como criar uma senha forte, consulte o artigo [Criar uma senha forte](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password).

- Por padrão, o Azure seleciona a autenticação do Windows durante a instalação da máquina Virtual do SQL Server. Portanto, o logon **SA** é desabilitado e uma senha é atribuída pela instalação. Recomendamos que o logon **SA** não seja usado nem habilitado. Se você precisar ter um logon do SQL, use uma das seguintes estratégias:

  - Crie uma conta do SQL com um nome exclusivo que tem a associação **sysadmin**. Faça isso no portal habilitando a **Autenticação SQL** durante o provisionamento.

    > [!TIP] 
    > Se você não habilitar a Autenticação SQL durante o provisionamento, deverá alterar manualmente o modo de autenticação para **Modo de Autenticação do SQL Server e do Windows**. Para obter mais informações, consulte [alterar o modo de autenticação do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se precisar usar o logon **SA**, habilite o logon depois de provisionar e atribuir uma nova senha forte.

## <a name="additional-best-practices"></a>Melhores práticas adicionais

Além das práticas descritas neste tópico, recomendamos que você examine e implemente as práticas recomendadas de segurança de práticas de segurança locais tradicionais, bem como as práticas recomendadas de segurança de máquina virtual. 

Para obter mais informações sobre práticas de segurança locais, consulte [considerações de segurança para uma instalação de SQL Server](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) e a [central de segurança](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Para obter mais informações sobre a segurança da máquina virtual, consulte a [visão geral de segurança de máquinas virtuais](/azure/security/fundamentals/virtual-machines-overview).


## <a name="next-steps"></a>Próximas etapas

Se você também estiver interessado em práticas recomendadas sobre desempenho, veja [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para ver outros tópicos relacionados à execução do SQL Server em VMs do Azure, confira [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

