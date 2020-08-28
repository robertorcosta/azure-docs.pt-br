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
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001613"
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

