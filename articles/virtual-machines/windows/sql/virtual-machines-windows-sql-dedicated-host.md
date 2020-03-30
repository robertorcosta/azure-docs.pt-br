---
title: VM do servidor SQL em um host dedicado ao Azure
description: Conheça os detalhes para executar um VM do Servidor SQL em um Host dedicado do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: edb2d3fa670475d9b08fe05494035949181a9240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834344"
---
# <a name="sql-server-vm-on-an-azure-dedicated-host"></a>VM do servidor SQL em um host dedicado ao Azure 

Este artigo detalha as especificidades do uso de uma VM do Servidor SQL com um [Host Dedicado azure](/azure/virtual-machines/windows/dedicated-hosts). Podem ser encontradas informações adicionais sobre o host dedicado ao azure no blog [Introducing Azure Dedicated Host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Visão geral
[O Azure Dedicated Host](/azure/virtual-machines/windows/dedicated-hosts) é um serviço que fornece servidores físicos - capazes de hospedar uma ou mais máquinas virtuais - dedicados a uma assinatura do Azure. Hosts dedicados são os mesmos servidores físicos usados nos data centers da Microsoft, fornecidos como recurso. Você pode provisionar hosts dedicados dentro de uma região, região de disponibilidade e domínio de falha. Em seguida, você pode colocar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.

## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em hosts dedicados.
- As seguintes séries vm são suportadas: DSv3 e ESv3. 

## <a name="licensing"></a>Licenciamento

Você pode escolher entre duas opções diferentes de licenciamento ao adicionar seu VM sql server a um Host dedicado do Azure. 

  - **SQL VM licenciamento**: Esta é a opção de licenciamento existente, onde você paga por cada licença VM do SQL Server individualmente. 
  - **Licenciamento de host dedicado**: O novo modelo de licenciamento disponível para o Host Dedicado do Azure, onde as licenças do SQL Server são empacotadas e pagas no nível do host. 


Opções em nível de host para usar as licenças existentes do SQL Server: 
  - SQL Server Enterprise Edition Azure Hybrid Benefit
    - Disponível para clientes com SA ou assinatura.
    - Licencie todos os núcleos físicos disponíveis e desfrute de virtualização ilimitada (até os vCPUs máximos suportados pelo host).
        - Para obter mais informações sobre a aplicação do Azure Hybrid Benefit para o host Dedicado ao Azure, consulte o FAQ de benefício híbrido do [Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licenças de servidor SQL adquiridas antes de 1º de outubro
      - A edição SQL Server Enterprise tem opções de licença de nível de host e by-VM. 
      - SQL Server A edição padrão tem apenas a opção de licença by-VM disponível. 
          - Para obter detalhes, consulte os [Termos do Produto da Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se nenhuma opção de nível de host dedicada do SQL Server for selecionada, o SQL Server AHB poderá ser selecionado no nível de VMs individuais, assim como acontece com VMs de vários locatários.



## <a name="provisioning"></a>Provisionamento  
O provisionamento de um VM do Servidor SQL para o host dedicado não é diferente de qualquer outra Máquina Virtual do Azure. Você pode fazê-lo usando [o Azure PowerShell](../dedicated-hosts-powershell.md), o [portal Azure](../dedicated-hosts-portal.md)e [o Azure CLI](../../linux/dedicated-hosts-cli.md).

O processo de adicionar um VM SQL Server existente ao host dedicado requer tempo de inatividade, mas não afetará os dados e não terá perda de dados. No entanto, todos os bancos de dados, incluindo bancos de dados do sistema, devem ser copiados antes da mudança.

## <a name="virtualization"></a>Virtualização 

Um dos benefícios de um host dedicado é a virtualização ilimitada. Por exemplo, você pode ter licenças para 64 vCores, mas você pode configurar o host para ter 128 vCores, para que você obtenha o dobro dos vCores, mas apenas pagando metade das licenças do SQL Server. 

Uma vez que é o seu host, você é elegível para definir a virtualização com uma proporção 1:2. 

## <a name="faq"></a>Perguntas frequentes

**P: Como funciona o Azure Hybrid Benefit para licenças do Windows Server/SQL Server no Host Dedicado do Azure?**

R: Os clientes podem usar o valor de suas licenças existentes do Windows Server e SQL Server com garantia de software, ou licenças de assinatura qualificadas, para pagar uma taxa reduzida no Host Dedicado do Azure usando o Azure Hybrid Benefit. Os clientes do Windows Server Datacenter e do SQL Server Enterprise Edition recebem virtualização ilimitada (implantam o maior número possível de máquinas virtuais do Windows Server no host, sujeitas à capacidade física do servidor subjacente) quando licenciam todo o host e use o Azure Hybrid Benefit.  Todas as cargas de trabalho do Windows Server e do SQL Server no Host Dedicado do Azure também são elegíveis para atualizações de segurança estendidas para o Windows Server e o SQL Server 2008/R2 sem nenhum custo adicional. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FaQ para SQL Server em um VM Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para SQL Server em um VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em um VM windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


