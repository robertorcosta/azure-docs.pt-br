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
ms.openlocfilehash: 93f01b3c23e08e7f432841d8a77cbe3602bff1c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81482146"
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

Ao provisionar uma VM do Azure usando uma imagem da Galeria de SQL Server, selecione **Alterar configuração** na guia **configurações de SQL Server** para abrir a página de configuração de armazenamento otimizado para desempenho. Você pode deixar os valores em padrão ou modificar o tipo de configuração de disco que melhor atenda às suas necessidades com base em sua carga de trabalho. 

![Configuração de armazenamento da VM do SQL Server durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecione o tipo de carga de trabalho que você está implantando seu SQL Server para a **otimização de armazenamento**. Com a opção de otimização **geral** , por padrão, você terá um disco de dados com IOPS máximo de 5000 e usará essa mesma unidade para os dados, o log de transações e o armazenamento de tempdb. Selecionar o OLTP ( **processamento transacional** ) ou o **data warehousing** criará um disco separado para os dados, um disco separado para o log de transações e usará o SSD local para tempdb. Não há nenhuma diferença de armazenamento entre o **processamento transacional** e o **data warehousing**, mas ele altera a [configuração do stripe e os sinalizadores de rastreamento](#workload-optimization-settings). Escolher armazenamento Premium define o cache para *ReadOnly* para a unidade de dados e *nenhum* para a unidade de log de acordo com [SQL Server práticas recomendadas de desempenho da VM](virtual-machines-windows-sql-performance.md). 

![Configuração de armazenamento da VM do SQL Server durante o provisionamento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

A configuração de disco é totalmente personalizável para que você possa configurar a topologia de armazenamento, o tipo de disco e o IOPs necessários para sua carga de trabalho de VM SQL Server. Você também terá a capacidade de usar UltraSSD (visualização) como uma opção para o **tipo de disco** se sua VM SQL Server estiver em uma das regiões com suporte (leste dos EUA 2, sudeste asiático e Europa setentrional) e se tiver habilitado [ultra disks para sua assinatura](/azure/virtual-machines/windows/disks-enable-ultra-ssd).  

Além disso, você tem a capacidade de definir o cache para os discos. As VMs do Azure têm uma tecnologia de cache de várias camadas chamada [cache de BLOBs](/azure/virtual-machines/windows/premium-storage-performance#disk-caching) quando usadas com [discos Premium](/azure/virtual-machines/windows/disks-types#premium-ssd). O cache de BLOBs usa uma combinação da RAM da máquina virtual e do SSD local para cache. 

O cache de disco para SSD Premium pode ser *ReadOnly*, *ReadWrite* ou *None*. 

- O cache *ReadOnly* é altamente benéfico para SQL Server arquivos de dados armazenados no armazenamento Premium. O cache *ReadOnly* traz baixa latência de leitura, IOPS de leitura alta e taxa de transferência como, as leituras são realizadas do cache, que está dentro da memória da VM e do SSD local. Essas leituras são muito mais rápidas do que as leituras do disco de dados, que é do armazenamento de BLOBs do Azure. O armazenamento Premium não conta as leituras servidas do cache para a IOPS e a taxa de transferência do disco. Portanto, o aplicável é capaz de alcançar um total maior de IOPS e taxa de transferência. 
- *Nenhuma* configuração de cache deve ser usada para os discos que hospedam SQL Server arquivo de log, pois o arquivo de log é gravado sequencialmente e não se beneficia do cache *ReadOnly* . 
- O cache *ReadWrite* não deve ser usado para hospedar SQL Server arquivos, pois SQL Server não oferece suporte à consistência de dados com o cache *ReadWrite* . Grava a capacidade de desperdício do cache de blob *ReadOnly* e as latências são ligeiramente aumentadas se as gravações passarem por camadas de cache de blob *ReadOnly* . 


   > [!TIP]
   > Certifique-se de que sua configuração de armazenamento corresponda às limitações impostas pelo tamanho da VM selecionada. Escolher parâmetros de armazenamento que excedem o limite de desempenho do tamanho da VM resultará `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`em erro:. Diminua o IOPs alterando o tipo de disco ou aumente a limitação do limite de desempenho aumentando o tamanho da VM. 


Com base em suas opções, o Azure realiza as seguintes tarefas de configuração de armazenamento depois de criar a VM:

* Cria e anexa os SSDs premium para a máquina virtual.
* Configura os discos de dados para serem acessíveis ao SQL Server.
* Configura os discos de dados em um pool de armazenamento com base nos requisitos de tamanho e desempenho (IOPS e taxa de transferência) especificados.
* Associa o pool de armazenamento a uma nova unidade na máquina virtual.
* Otimiza essa nova unidade com base em seu tipo de carga de trabalho especificado (Data warehouse, Processamento transacional ou Geral).

Para obter mais detalhes sobre como o Azure define as configurações de armazenamento, confira a [Seção de configuração de armazenamento](#storage-configuration). Para obter uma explicação completa de como criar uma VM SQL Server no portal do Azure, consulte [o tutorial de provisionamento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modelos do Resource Manager

Se você usar os modelos do Resource Manager a seguir, dois discos de dados premium são conectados por padrão, sem nenhuma configuração de pool de armazenamento. No entanto, você pode personalizar esses modelos para alterar o número de discos de dados premium anexados à máquina virtual.

* [Criar VM com backup automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Criar VM com aplicação de patch automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Criar VM com integração de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modelo de Início Rápido

Você pode usar o modelo de início rápido a seguir para implantar uma VM SQL Server usando a otimização de armazenamento. 

* [Criar VM com otimização de armazenamento](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Criar VM usando UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para VMs do SQL Server existente, você pode modificar algumas configurações de armazenamento no Portal do Azure. Abra o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)e selecione **visão geral**. A página Visão geral do SQL Server mostra o uso de armazenamento atual de sua VM. Todas as unidades que existem na sua VM são exibidas neste gráfico. Para cada unidade, o espaço de armazenamento é exibido em quatro seções:

* Dados SQL
* Log do SQL
* Outros (armazenamento não SQL)
* Disponível

Para modificar as configurações de armazenamento, selecione **definir** em **configurações**. 

![Configurar o armazenamento para a VM do SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Você pode modificar as configurações de disco para as unidades que foram configuradas durante o processo de criação de VM SQL Server. A seleção de **estender unidade** abre a página de modificação da unidade, permitindo que você altere o tipo de disco, bem como adicione discos adicionais. 

![Configurar o armazenamento para a VM do SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>Configuração de armazenamento

Esta seção fornece uma referência para as alterações de configuração de armazenamento que o Azure executa automaticamente durante o provisionamento ou configuração de VM do SQL no portal do Azure.

* O Azure configura um pool de armazenamento do armazenamento selecionado de sua VM. A próxima seção deste tópico fornece detalhes sobre a configuração do pool de armazenamento.
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
| Número de Colunas |Número de discos de dados até 8<sup>1</sup> |


<sup>1</sup>Após a criação do pool de armazenamento, você não pode alterar o número de colunas no pool de armazenamento.


## <a name="workload-optimization-settings"></a>Configurações de otimização da carga de trabalho

A tabela a seguir descreve as três opções de tipo de carga de trabalho disponíveis e suas otimizações correspondentes:

| Tipo de carga de trabalho | Descrição | Otimizações |
| --- | --- | --- |
| **Geral** |Configuração padrão que oferece suporte à maioria das cargas de trabalho |Nenhum |
| **Processamento transacional** |Otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados |Sinalizador de Rastreamento 1117<br/>Sinalizador de Rastreamento 1118 |
| **Data Warehousing** |Otimiza o armazenamento para as cargas de trabalho de análise e emissão de relatórios |Sinalizador de Rastreamento 610<br/>Sinalizador de Rastreamento 1117 |

> [!NOTE]
> Você só pode especificar o tipo de carga de trabalho quando provisiona uma máquina virtual do SQL, selecionando-o na etapa de configuração de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos relacionados à execução do SQL Server em VMs do Azure, consulte [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
