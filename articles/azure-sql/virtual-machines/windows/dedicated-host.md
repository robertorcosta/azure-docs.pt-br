---
title: Executar VM do SQL Server em um Host Dedicado do Azure
description: Saiba como executar uma VM do SQL Server em um Host Dedicado do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 40c851e5ff5fc83ccf6b6d67e319bb97bd860bd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84669096"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Executar VM do SQL Server em um Host Dedicado do Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo tem detalhes específicos sobre o uso de uma VM (máquina virtual) do SQL Server com o [Host Dedicado do Azure](/azure/virtual-machines/windows/dedicated-hosts). Informações adicionais sobre o Host Dedicado do Azure podem ser encontradas na postagem no blog [Apresentando Host Dedicado do Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Visão geral
[O Host Dedicado do Azure](/azure/virtual-machines/windows/dedicated-hosts) é um serviço que fornece servidores físicos - que podem hospedar uma ou mais máquinas virtuais - dedicados a uma assinatura do Azure. Os hosts dedicados são os mesmos servidores físicos usados nos data centers da Microsoft, fornecidos como um recurso. Você pode provisionar hosts dedicados em uma região, uma zona de disponibilidade e em um domínio de falha. Em seguida, você pode posicionar VMs diretamente em seus hosts provisionados, em qualquer configuração que melhor atenda às suas necessidades.

## <a name="limitations"></a>Limitações

- Os Conjuntos de Dimensionamento de Máquinas Virtuais não são compatíveis com hosts dedicados atualmente.
- Há suporte para a seguinte série de VMs: DSv3 e ESv3. 

## <a name="licensing"></a>Licenciamento

Escolha entre duas opções de licenciamento diferentes ao posicionar sua VM do SQL Server em um Host Dedicado do Azure. 

  - **Licenciamento de VM do SQL**: Essa é a opção de licenciamento existente, em que você paga individualmente por licença de VM do SQL Server. 
  - **Licenciamento de host dedicado**: O novo modelo de licenciamento disponível para o Host Dedicado do Azure, no qual as licenças do SQL Server são agrupadas em um pacote e pagas no nível do host. 


Opções de nível de host para usar licenças de SQL Server existentes: 
  - AHB (Benefício Híbrido do Azure) do SQL Server Enterprise Edition
    - Disponível para clientes com SA ou assinatura.
    - Licenciamento de todos os núcleos físicos disponíveis para desfrutar de virtualização ilimitada (até o máximo de vCPUs compatíveis com o host).
        - Para obter mais informações sobre como aplicar o AHB ao Host Dedicado do Azure, confira [Perguntas frequentes sobre o Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licenças do SQL Server adquiridas antes de 1º de outubro
      - O SQL Server Enterprise Edition tem opções de licença de nível de host e de VM. 
      - O SQL Server Standard Edition tem apenas a opção de licença por VM disponível. 
          - Para obter detalhes, confira os [Termos de Produto da Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Se não selecionar nenhuma opção de nível de host dedicado do SQL Server, você poderá selecionar o AHB do SQL Server no nível de VMs individuais, assim como faria com VMs multilocatário.



## <a name="provisioning"></a>Provisionamento  
O provisionamento de uma VM do SQL Server para o host dedicado não é diferente de nenhuma outra máquina virtual do Azure. Você pode fazer isso usando o [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), o [portal do Azure](../../../virtual-machines/windows/dedicated-hosts-portal.md) e a [CLI do Azure](../../../virtual-machines/linux/dedicated-hosts-cli.md).

O processo de adição de uma VM do SQL Server existente ao host dedicado requer tempo de inatividade, mas não afetará os dados e não haverá perda de dados. No entanto, é necessário fazer backup de todos os bancos de dados, incluindo bancos de dados do sistema, antes da migração.

## <a name="virtualization"></a>Virtualização 

Um dos benefícios de um host dedicado é a virtualização ilimitada. Por exemplo, você pode ter licenças para 64 vCores, mas pode configurar o host para ter 128 vCores, portanto, você obtém o dobro de vCores, mas paga apenas metade do que você pagaria pelas licenças de SQL Server. 

Como é o seu host, você está qualificado para definir a virtualização com uma proporção de 1:2. 

## <a name="faq"></a>Perguntas frequentes

**P: Como o Benefício Híbrido do Azure funciona para licenças do Windows Server/SQL Server no Host Dedicado do Azure?**

A: Os clientes podem usar o valor de suas licenças existentes do Windows Server e do SQL Server com o Software Assurance ou licenças de assinatura qualificadas, para pagar uma taxa reduzida no Host Dedicado do Azure usando o Benefício Híbrido do Azure. Os clientes do Windows Server Datacenter e do SQL Server Enterprise Edition obtêm virtualização ilimitada (podem implantar tantas máquinas virtuais do Windows Server quantas forem possíveis no host, sujeito à capacidade física do servidor subjacente) quando licenciam todo o host e usam o Benefício Híbrido do Azure.  Todas as cargas de trabalho do Windows Server e do SQL Server no Host Dedicado do Azure também estão qualificadas para atualizações de segurança estendidas para o Windows Server e o SQL Server 2008/R2 sem custo adicional. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](doc-changes-updates-release-notes.md)


