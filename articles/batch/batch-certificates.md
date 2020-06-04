---
title: Uso de certificados - Azure Batch
description: Usar certificados para habilitar a autenticação de aplicativos
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 4da5fad63b148fa054eefb7f13424b46dc43bf29
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83764315"
---
# <a name="using-certificates-with-batch"></a>Usando certificados com o Lote

No momento, o principal motivo para usar certificados com o Lote é se tiver aplicativos em execução em pools que precisam ser autenticados com um ponto de extremidade. 

Se você ainda não tiver um certificado, crie um certificado autoassinado usando a ferramenta da linha de comando `makecert`.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Carregue os certificados manualmente por meio do portal do Azure

1. Na conta do Lote para a qual você deseja carregar um certificado, selecione **Certificados** e, em seguida, selecione **Adicionar**. 

2. Carregue o certificado com uma extensão .pfx ou .cer. 

Uma vez carregado, o certificado é adicionado a uma lista de certificados e você pode verificar a impressão digital.

Agora, quando você cria um pool do Lote, é possível navegar até Certificados dentro do pool e atribuir o certificado criado nesse pool.

## <a name="next-steps"></a>Próximas etapas

O Lote tem uma API de certificado, [Criação de certificado em lote do AZ](https://docs.microsoft.com/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create)

Para obter informações sobre como usar Key Vault, consulte [Acessar o Key Vault com segurança com o Lote](credential-access-key-vault.md).
