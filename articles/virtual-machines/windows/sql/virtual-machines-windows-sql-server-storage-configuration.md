---
title: Configuração de armazenamento para VMs do SQL Server | Microsoft Docs
description: Este tópico descreve como o Azure configura o armazenamento para VMs do SQL Server durante o provisionamento (modelo de implantação do Resource Manager). Também explica como você pode configurar o armazenamento para suas VMs existentes do SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 9d8fce0772f13c6e009b2441ecd85779a7622c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243192"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuração de armazenamento para VMs do SQL Server

Quando você configurar uma imagem de máquina virtual do SQL Server no Azure, o Portal ajuda você a automatizar a configuração de armazenamento. Isso inclui a anexação de armazenamento para a VM, tornando o armazenamento acessível para o SQL Server e configurando-o para otimizar seus requisitos específicos de desempenho.

Este tópico explica como o Azure configura o armazenamento para suas VMs do SQL Server durante o provisionamento e para VMs existentes. Essa configuração tem base nas [práticas recomendadas de desempenho](virtual-machines-windows-sql-performance.md) para VMs do Azure executando o SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para usar as definições automatizadas de configuração de armazenamento, sua máquina virtual exige as seguintes características:

* Deve ser provisionada com uma [imagem da galeria do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Deve usar o [Modelo de implantação do Resource Manager](../../../azure-resource-manager/management/deployment-models.md).
* Deve usar os [SSDs premium](../disks-types.md).

## <a name="new-vms"></a>Novas VMs

As seções a seguir descrevem como configurar o armazenamento para novas máquinas virtuais do SQL Server.

### <a name="azure-portal"></a>Portal do Azure

Ao provisionar uma VM do Azure usando uma imagem de galeria do SQL Server, selecione **Alterar configuração** na guia Configurações do **servidor SQL** para abrir a página Configuração de armazenamento otimizado de desempenho. Você pode deixar os valores no padrão ou modificar o tipo de configuração de disco que melhor se adequa às suas necessidades com base na sua carga de trabalho. 

![Configuração de armazenamento da VM do SQL Server durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecione o tipo de carga de trabalho que você está implantando seu SQL Server para **otimização de armazenamento**. Com a opção de otimização **geral,** por padrão você terá um disco de dados com IOPS 5000 max, e você usará essa mesma unidade para seus dados, log de transações e armazenamento TempDB. A seleção do **processamento transacional** (OLTP) ou **do armazenamento de dados** criará um disco separado para dados, um disco separado para o registro de transações e usará SSD local para TempDB. Não há diferenças de armazenamento entre **processamento transacional** e **armazenamento de dados,** mas ele altera sua [configuração de listra e sinalizadores de rastreamento](#workload-optimization-settings). A escolha do armazenamento premium define o cache para *ReadOnly* para a unidade de dados e *Nenhum* para a unidade de log de acordo com as [práticas recomendadas de desempenho do SQL Server VM](virtual-machines-windows-sql-performance.md). 

![Configuração de armazenamento da VM do SQL Server durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

A configuração do disco é completamente personalizável para que você possa configurar a topologia de armazenamento, tipo de disco e IOPs que você precisa para a carga de trabalho do SQL Server VM. Você também tem a capacidade de usar o UltraSSD (preview) como uma opção para o **tipo disk** se o seu VM do SQL Server estiver em uma das regiões suportadas (Leste dos EUA 2, Sudeste Asiático e Norte da Europa) e você habilitou [discos ultra para sua assinatura](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Além disso, você tem a capacidade de definir o cache para os discos. As VMs do Azure possuem uma tecnologia de cache de vários níveis chamada [Blob Cache](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) quando usada com [Discos Premium](/azure/virtual-machines/windows/disks-types#premium-ssd). O Blob Cache usa uma combinação da RAM da máquina virtual e do SSD local para cache. 

O cache de disco para SSD Premium pode ser *ReadOnly,* *ReadWrite* ou *None*. 

- O cache *ReadOnly* é altamente benéfico para arquivos de dados do SQL Server que são armazenados no Armazenamento Premium. *ReadOnly* cache traz latência de leitura baixa, IOPS de leitura alta e throughput as, leituras são realizadas a partir de cache, que os dentro da memória VM e SSD local. Essas leituras são muito mais rápidas do que as leituras do disco de dados, que é do armazenamento blob do Azure. O armazenamento premium não conta as leituras servidas do cache para o disco IOPS e throughput. Portanto, o seu aplicável é capaz de alcançar maior rendimento total de formigas IOPS. 
- *Nenhuma* configuração de cache deve ser usada para os discos que hospedam o arquivo SQL Server Log, pois o arquivo de log é gravado sequencialmente e não se beneficia do cache *ReadOnly.* 
- O cache *ReadWrite* não deve ser usado para hospedar arquivos do SQL Server, pois o SQL Server não suporta a consistência dos dados com o cache *ReadWrite.* Grava capacidade de desperdício do cache de bolhas *ReadOnly* e as latências aumentam ligeiramente se as gravações passarem por camadas de cache de bolha *readOnly.* 


   > [!TIP]
   > Certifique-se de que sua configuração de armazenamento corresponda às limitações impostas pelo tamanho vm selecionado. Escolher parâmetros de armazenamento que excedam a tampa `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`de desempenho do tamanho da VM resultará em erro: . Diminua os IOPs alterando o tipo de disco ou aumente a limitação da tampa de desempenho aumentando o tamanho da VM. 


Com base em suas opções, o Azure realiza as seguintes tarefas de configuração de armazenamento depois de criar a VM:

* Cria e anexa os SSDs premium para a máquina virtual.
* Configura os discos de dados para serem acessíveis ao SQL Server.
* Configura os discos de dados em um pool de armazenamento com base nos requisitos de tamanho e desempenho (IOPS e taxa de transferência) especificados.
* Associa o pool de armazenamento a uma nova unidade na máquina virtual.
* Otimiza essa nova unidade com base em seu tipo de carga de trabalho especificado (Data warehouse, Processamento transacional ou Geral).

Para obter mais detalhes sobre como o Azure define as configurações de armazenamento, confira a [Seção de configuração de armazenamento](#storage-configuration). Para obter um passo a passo completo de como criar uma VM do SQL Server no portal Azure, consulte [o tutorial de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos do Resource Manager

Se você usar os modelos do Resource Manager a seguir, dois discos de dados premium são conectados por padrão, sem nenhuma configuração de pool de armazenamento. No entanto, você pode personalizar esses modelos para alterar o número de discos de dados premium anexados à máquina virtual.

* [Criar VM com backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com aplicação de patch automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com integração de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modelo de Início Rápido

Você pode usar o seguinte modelo quickstart para implantar um VM do SQL Server usando otimização de armazenamento. 

* [Criar VM com otimização de armazenamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Criar VM usando UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para VMs do SQL Server existente, você pode modificar algumas configurações de armazenamento no Portal do Azure. Abra o [recurso de máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)e selecione **Visão geral**. A página Visão Geral do Servidor SQL mostra o uso atual do armazenamento de sua VM. Todas as unidades que existem na sua VM são exibidas neste gráfico. Para cada unidade, o espaço de armazenamento é exibido em quatro seções:

* Dados SQL
* Log do SQL
* Outros (armazenamento não SQL)
* Disponível

Para modificar as configurações de armazenamento, **selecione Configurar** em **Configurações**. 

![Configurar o armazenamento para a VM do SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Você pode modificar as configurações de disco para as unidades que foram configuradas durante o processo de criação do VM do SQL Server. A seleção **extend drive** abre a página de modificação da unidade, permitindo que você altere o tipo de disco, bem como adicione discos adicionais. 

![Configurar o armazenamento para a VM do SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Configuração de armazenamento

Esta seção fornece uma referência para as alterações de configuração de armazenamento que o Azure executa automaticamente durante o provisionamento ou configuração do SQL VM no portal Azure.

* O Azure configura um pool de armazenamento a partir do armazenamento selecionado da VM. A próxima seção deste tópico fornece detalhes sobre a configuração do pool de armazenamento.
* A configuração de armazenamento automática sempre usa discos de dados P30 dos [SSDs premium](../disks-types.md). Consequentemente, há um mapeamento 1:1 entre o número selecionado de Terabytes e o número de discos de dados anexados à sua VM.

Para saber mais sobre preços, consulte a página [Preços de armazenamento](https://azure.microsoft.com/pricing/details/storage) na guia **Armazenamento em disco** .

### <a name="creation-of-the-storage-pool"></a>Criação do pool de armazenamento

O Azure usa as configurações a seguir para criar o pool de armazenamento em VMs do SQL Server.

| Configuração | Valor |
| --- | --- |
| Tamanho da distribuição |256 KB (Data warehouse); 64 KB (Transacional) |
| Tamanhos do disco |1 TB cada |
| Cache |Ler |
| Tamanho da alocação |Tamanho da unidade de alocação de NTFS de 64 KB |
| Recuperação | Recuperação simples (sem resiliência) |
| Número de Colunas |Número de discos de dados até<sup>81</sup> |


<sup>1</sup>Após a criação do pool de armazenamento, você não pode alterar o número de colunas no pool de armazenamento.


## <a name="workload-optimization-settings"></a>Configurações de otimização da carga de trabalho

A tabela a seguir descreve as três opções de tipo de carga de trabalho disponíveis e suas otimizações correspondentes:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Configuração padrão que oferece suporte à maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados |Sinalizador de Rastreamento 1117<br/>Sinalizador de Rastreamento 1118 |
| **Data warehousing** |Otimiza o armazenamento para as cargas de trabalho de análise e emissão de relatórios |Sinalizador de Rastreamento 610<br/>Sinalizador de Rastreamento 1117 |

> [!NOTE]
> Você só pode especificar o tipo de carga de trabalho quando provisiona uma máquina virtual do SQL, selecionando-o na etapa de configuração de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
