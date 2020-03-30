---
title: Mudança no lugar da edição sql server
description: Saiba como alterar a edição da sua máquina virtual SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605451"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Mudança no lugar da edição do SQL Server no Azure VM

Este artigo descreve como alterar a edição do SQL Server em uma máquina virtual do Windows no Azure. 

A edição do SQL Server é determinada pela chave do produto e é especificada durante o processo de instalação usando a mídia de instalação. A edição dita quais [recursos](/sql/sql-server/editions-and-components-of-sql-server-2017) estão disponíveis no produto SQL Server. Você pode alterar a edição do SQL Server com a mídia de instalação e fazer o downgrade para reduzir custos ou atualizar para habilitar mais recursos.

Uma vez que a edição do SQL Server tenha sido alterada internamente para o VM do SQL Server, você deve atualizar a propriedade edition do SQL Server no portal Azure para fins de cobrança. 

## <a name="prerequisites"></a>Pré-requisitos

Para fazer uma alteração no local da edição do SQL Server, você precisa do seguinte: 

- Uma [assinatura do Azure.](https://azure.microsoft.com/free/)
- Um [VM do SQL Server no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrado no provedor de recursos [SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).
- Configurar mídia com a **edição desejada** do SQL Server. Os clientes que possuem [garantia de software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) podem obter suas mídias de instalação no Centro de Licenciamento de [Volume](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Os clientes que não possuem o Software Assurance podem usar a mídia de configuração a partir de uma imagem `C:\SQLServerFull`VM do Azure Marketplace SQL Server que tem a edição desejada (normalmente localizada em ). 


## <a name="upgrade-an-edition"></a>Atualize uma edição

> [!WARNING]
> A atualização da edição do SQL Server reiniciará o serviço para o SQL Server, juntamente com quaisquer serviços associados, como Serviços de Análise e Serviços R. 

Para atualizar a edição do SQL Server, obtenha a mídia de configuração do SQL Server para a edição desejada do SQL Server e faça o seguinte:

1. Abrir setup.exe a partir da mídia de instalação do SQL Server. 
1. Vá para **Manutenção** e escolha a opção **Atualizar** edição. 

   ![Seleção para atualização da edição do SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Selecione **Next** até chegar à página **'Pronto para atualizar'** e, em seguida, selecione **Atualizar**. A janela de configuração pode parar de responder por alguns minutos enquanto a mudança está fazendo efeito. Uma página **completa** confirmará que sua atualização de edição está concluída. 

Depois que a edição do SQL Server for atualizada, modifique a propriedade de edição da máquina virtual do SQL Server no portal Azure. Isso atualizará os metadados e o faturamento associados a esta VM.

## <a name="downgrade-an-edition"></a>Desinaque uma edição

Para fazer o downgrade da edição do SQL Server, você precisa desinstalar completamente o SQL Server e reinstalá-lo novamente com a mídia de configuração de edição desejada. 

> [!WARNING]
> A desinstalação do SQL Server pode incorrer em tempo de inatividade adicional. 

Você pode fazer o downgrade da edição do SQL Server seguindo estas etapas:

1. Fazer backup de todos os bancos de dados, incluindo os bancos de dados do sistema. 
1. Mova os bancos de dados do sistema (mestre, modelo e msdb) para um novo local. 
1. Desinstale completamente o SQL Server e todos os serviços associados. 
1. Reinicie a máquina virtual. 
1. Instale o SQL Server usando a mídia com a edição desejada do SQL Server.
1. Instale os pacotes de serviço mais recentes e atualizações cumulativas.  
1. Substitua os novos bancos de dados do sistema que foram criados durante a instalação pelos bancos de dados do sistema que você anteriormente mudou para um local diferente. 

Depois que a edição do SQL Server for rebaixada, modifique a propriedade de edição da máquina virtual do SQL Server no portal Azure. Isso atualizará os metadados e o faturamento associados a esta VM.

## <a name="change-edition-in-portal"></a>Alterar edição no portal 

Depois de alterar a edição do SQL Server usando a mídia de instalação e registrar seu VM do SQL Server com o [provedor de recursos SQL VM,](virtual-machines-windows-sql-register-with-resource-provider.md)você pode usar o portal Azure para modificar a propriedade Edition do VM do SQL Server para fins de cobrança. Para fazer isso, siga estas etapas: 

1. Faça login no [portal Azure](https://portal.azure.com). 
1. Vá para o recurso da máquina virtual sql server. 
1. Em **Configurações,** **selecione Configurar**. Em seguida, selecione a edição desejada do SQL Server na lista de baixa em **Edição**. 

   ![Metadados de edição de alteração](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Revise o aviso que diz que você deve alterar a edição do SQL Server primeiro, e que a propriedade edition deve corresponder à edição do SQL Server. 
1. Selecione **Aplicar** para aplicar suas alterações de metadados de edição. 


## <a name="remarks"></a>Comentários

- A propriedade edition para o SQL Server VM deve corresponder à edição da instância do SQL Server instalada para todas as máquinas virtuais do SQL Server, incluindo os tipos de licenças pay-as-you-go e bring-your-own-license.
- Se você soltar o recurso VM do SQL Server, você voltará para a configuração de edição codificada da imagem.
- A capacidade de alterar a edição é uma característica do provedor de recursos SQL VM. A implantação de uma imagem do Azure Marketplace através do portal Azure registra automaticamente um VM do SQL Server com o provedor de recursos. No entanto, os clientes que estão auto-instalando o SQL Server precisarão registrar manualmente [seu VM do Servidor SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Adicionar uma VM do SQL Server a um conjunto de disponibilidade requer a recriação da VM. Todas as VMs adicionadas a um conjunto de disponibilidade voltarão à edição padrão, e a edição precisará ser modificada novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FaQ para SQL Server em um VM Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para SQL Server em um VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


