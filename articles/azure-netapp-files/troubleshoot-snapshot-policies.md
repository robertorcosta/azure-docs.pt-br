---
title: Solucionar problemas de políticas de instantâneo para Azure NetApp Files | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a solucionar problemas de gerenciamento de política de instantâneo para Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91340866"
---
# <a name="troubleshoot-snapshot-policies"></a>Solucionar problemas de políticas de instantâneo

Este artigo descreve os cenários de erro que você pode encontrar ao gerenciar Azure NetApp Files políticas de instantâneo. Ele também fornece soluções que podem ajudá-lo a resolver os problemas.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>Falha na criação da política de instantâneo com nome de política de instantâneo inválido

Ocorrerá um erro durante a criação da política de instantâneo se o nome da política de instantâneo for inválido. As diretrizes a seguir se aplicam a nomes de política de instantâneo:  

* O nome da política de instantâneo não pode conter caracteres não ASCII ou especiais.
* O nome da política de instantâneo deve começar com uma letra ou um número e pode conter letras, números, sublinhado (' _ ') e hifens ('-') somente.
* O nome da política de instantâneo deve ter entre 1 e 64 caracteres.  

Você deve revisar o nome da política de instantâneo de acordo com as diretrizes.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>Falha na criação da política de instantâneo com valores inválidos 

Azure NetApp Files falha ao criar uma política de instantâneo se você inserir um valor inválido para um campo, como `Number of snapshots to keep` ou `Minute on the hour to take snapshot` .  
 
Os valores válidos são os seguintes:   

* O valor deve ser um número válido.
* O valor deve estar entre 0 e 59.

Certifique-se de que um valor válido seja fornecido para os campos.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>Falha na criação da política de instantâneo com o erro "o número total de instantâneos a serem mantidos excede 255" 

Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). O máximo inclui a soma de todos os instantâneos por hora, diários, semanais e mensais. Você deve diminuir o `Snapshots to keep` valor e tentar novamente.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>A atribuição de política a um volume com falha com "a política de instantâneo total está acima do máximo ' 255 ' erro

Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). Quando a soma de todos os instantâneos sob demanda, por hora, diariamente, semanais e mensais excede o máximo, ocorre um erro. Diminua o `snapshots to keep` valor ou exclua alguns instantâneos sob demanda e tente novamente.

## <a name="next-steps"></a>Próximas etapas  

* [Gerenciar políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
