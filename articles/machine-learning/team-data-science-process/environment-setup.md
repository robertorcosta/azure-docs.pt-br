---
title: Configurar ambientes de ciência de dados no Azure -Processo de Ciência de Dados de Equipe
description: Configurar ambientes de ciência de dados no Azure para uso no Processo de Ciência de Dados de Equipe.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 63a77a4f715402951ba8af70755196d52e3b742d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492388"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configurar ambientes de ciência de dados para uso no Processo de Ciência de Dados de Equipe
O Processo de Ciência de Dados de Equipe usa uma variedade de ambientes de ciência de dados para armazenamento, processamento e análise de dados. Inclui o Armazenamento de Blobs do Azure, vários tipos de máquinas virtuais do Azure, clusters de HDInsight (Hadoop) e Workspace de Azure Machine Learning. A decisão sobre qual tipo de ambiente usar depende do tipo e da quantidade de dados que serão modelados e do destino de tais dados na nuvem. 

* Para obter diretrizes sobre as perguntas a serem consideradas ao tomar essa decisão, veja [Planejar seu ambiente de ciência de dados do Machine Learning do Azure](plan-your-environment.md). 
* Para obter um catálogo de alguns dos cenários que você pode encontrar ao fazer análises avançadas, confira [Scenarios for the Team Data Science Process (Cenários para o Processo de Ciência de Dados de Equipe)](plan-sample-scenarios.md)

Os artigos a seguir descrevem como configurar os vários ambientes de ciência de dados usados pelo Team Data Science Process.

* [Azure storage-account](../../storage/common/storage-quickstart-create-account.md)
* [Cluster do HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Espaço de trabalho Azure Machine Learning Studio (clássico)](../studio/create-workspace.md)

O **DSVM (Máquina Virtual de ciência de dados da Microsoft)** também está disponível como uma imagem de VM (máquina virtual) do Azure. Essa VM é pré-instalada e configurada com diversas ferramentas populares que são usadas para a análise de dados e o aprendizado de máquina. O DSVM está disponível no Windows e no Linux. Para obter mais informações, consulte [Introdução à Máquina Virtual de Ciência de Dados baseada em nuvem para Linux e Windows](../data-science-virtual-machine/overview.md).

Saiba como criar:

- [DSVM do Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM do Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM do CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
