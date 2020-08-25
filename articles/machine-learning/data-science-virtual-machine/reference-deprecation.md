---
title: 'Referência: descontinuações de imagem de Máquina Virtual de Ciência de Dados'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre aposentadorias que afetam o Máquina Virtual de Ciência de Dados do Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816313"
---
# <a name="reference-retirements-of-dsvm-images"></a>Referência: aposentadorias de imagens DSVM

Abaixo, discutimos a desativação (substituição) de imagens e sugestões para lidar com as próximas aposentadorias na Máquina Virtual de Ciência de Dados do Azure.

## <a name="why-we-retire-dsvm-images"></a>Por que desativamos imagens DSVMs

Atualmente, a Máquina Virtual de Ciência de Dados tem duas edições:

* Ubuntu
* Windows Server

A imagem DSVM para essas edições é criada em uma versão específica do sistema operacional, por exemplo, Ubuntu 18, 4 LTS. Ao longo do tempo, a janela de manutenção para a _versão_ do sistema operacional será encerrada e/ou as ferramentas de ciência de dados não oferecerão mais suporte a versões mais antigas do sistema operacional. Para manter a imagem DSVM atualizada com os sistemas operacionais e as ferramentas de ciência de dados mais recentes, lançamos uma nova imagem do DSVM com base nas versões mais recentes do sistema operacional.

## <a name="how-we-retire-dsvm-images"></a>Como desativamos imagens DSVMs

Emitimos um _anúncio de aposentadoria_ em que os usuários existentes do DSVM são notificados (por email) de uma próxima desativação da imagem do DSVM. O anúncio de aposentadoria detalhará a _Data_ de desativação (após essa data, a imagem não estará disponível) e as recomendações de mitigação (por exemplo, atualizando para uma imagem DSVM mais recente).

Na data do _comunicado_ , ocultaremos a imagem no Marketplace para que:

1. novos usuários são impedidos de provisionar inadvertidamente uma imagem DSVM desativada.
2. os usuários existentes podem usar implantações do ARM até a data de desativação.

A imagem oculta receberá patches do sistema operacional até a _Data_ de desativação, mas __não__ receberá atualizações para as ferramentas e estruturas de ciência de dados. Na _Data_de desativação, a imagem não estará disponível para implantações de ARM.

Qualquer imagem provisionada do DSVM em sua assinatura continuará a operar após a data de desativação. No entanto, é recomendável atualizar para a imagem DSVM mais recente para que você tenha os sistemas operacionais e as ferramentas de ciência de dados mais recentes.

## <a name="impact"></a>Impacto

As imagens do DSVM provisionadas existentes em sua assinatura continuarão a operar após a data de desativação. No entanto, recomendamos que os usuários atualizem sua imagem DSVM para a versão mais recente usando o modelo portal do Azure ou ARM.

> [!WARNING]
> Imagens de DSVM desativadas provisionadas usando conjuntos de dimensionamento de máquinas virtuais falharão ao escalar verticalmente após a data de desativação.
>
> Os modelos de ARM que não foram atualizados com os novos detalhes da imagem DSVM falharão ao implantar após a data de desativação.

## <a name="mitigating-upcoming-retirements"></a>Reduzindo as próximas aposentadorias

Nesta seção, discutiremos a mitigação para futuras aposentadorias.

### <a name="upgrade-windows-2016-dsvm"></a>Atualizar o Windows 2016 DSVM

Para migrar um disco de dados de seu DSVM do Windows 2016 existente para um DSVM do Windows 2019, execute as seguintes etapas:

1. Crie um novo DSVM do Windows 2019, seguindo as instruções mostradas [aqui](./provision-vm.md#create-your-dsvm).
1. Desanexe discos de dados existentes da imagem do Windows 2016 usando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Anexe o disco da etapa anterior à sua imagem do Windows 2019 usando [estas instruções](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

### <a name="upgrade-ubuntu-1604-dsvm"></a>Atualizar o Ubuntu 16, 4 DSVM

É recomendável atualizar o Ubuntu 16, 4 DSVMs existente para a [edição ubuntu 18, 4 DSVM](./dsvm-ubuntu-intro.md).
