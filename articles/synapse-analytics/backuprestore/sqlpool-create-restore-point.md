---
title: Criar um ponto de restauração definido pelo usuário para um pool SQL dedicado
description: Como criar um ponto de restauração para o pool do SQL dedicado.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332038"
---
# <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Neste artigo, você aprenderá a criar um novo ponto de restauração definido pelo usuário para um pool SQL dedicado no Azure Synapse Analytics usando o portal do Azure.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Criar pontos de restauração definidos pelo usuário por meio do portal do Azure

Os pontos de restauração definidos pelo usuário também podem ser criados por meio de portal do Azure.

1. Entre em sua conta do [portal do Azure](https://portal.azure.com/) .

2. Navegue até o pool SQL dedicado para o qual você deseja criar um ponto de restauração.

3. Selecione **visão geral** no painel esquerdo, selecione **+ novo ponto de restauração**. Se o botão novo ponto de restauração não estiver habilitado, verifique se o pool do SQL dedicado não está pausado.

    ![Novo Ponto de Restauração](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Especifique um nome para o ponto de restauração definido pelo usuário e clique em **aplicar**. Os pontos de restauração definidos pelo usuário têm um período de retenção padrão de sete dias.

    ![Nome do ponto de restauração](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL dedicado existente](restore-sql-pool.md)

