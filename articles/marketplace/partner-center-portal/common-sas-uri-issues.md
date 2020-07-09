---
title: Correções e problemas comuns de URI do SAS - Azure Marketplace
description: Problemas comuns encontrados e resoluções sugeridas ao trabalhar com assinaturas de acesso compartilhado.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: anbene
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: 95ce37d92adc3d09c5a09944b094df7971831198
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110718"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Correções e problemas comuns de URI do SAS

A seguir, estão alguns dos problemas comuns encontrados ao trabalhar com assinaturas de acesso compartilhado (que são usadas para identificar e compartilhar os VHDs enviados para sua solução), juntamente com as resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Correção** |
| --------- | ------------------- | ------- |
| *Falha ao copiar imagens* |  |  |
| "?" não é encontrado no URI do SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI do SAS usando as ferramentas recomendadas. |
| Os parâmetros "st" e "se" não estão no URI do SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI do SAS com os valores de **Data de Início** e **Data de Término** corretos. |
| "sp=rl" não está no URI do SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI do SAS com permissões definidas como `Read` e `List`. |
| O URI do SAS tem espaços em branco no nome do VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI do SAS para remover espaços em branco. |
| Erro de autorização do URI do SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Revise e corrija o formato URI do SAS. Regenere se necessário. |
| Os parâmetros "st" e "se" do URI do SAS não têm especificação completa de data e hora | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | O parâmetros **Data de Início** e **Data de Término** do URI do SAS (subcadeias `st` e `se`) devem ter um formato de data e hora completo, como `11-02-2017T00:00:00Z`. Algumas versões abreviadas são inválidas (alguns comandos na CLI do Azure podem gerar valores abreviados por padrão). |
|  |  |  |

Para obter detalhes, consulte [Usar assinaturas de acesso compartilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
