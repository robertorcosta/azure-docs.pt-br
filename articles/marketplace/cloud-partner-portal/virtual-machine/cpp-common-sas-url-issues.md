---
title: Problemas e correções comuns de URL SAS para o Azure Marketplace
description: Lista problemas comuns sobre o uso de URIs de assinatura de acesso compartilhado e possíveis soluções.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 47702959474a352a8e13710ec850f789dee4d517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278154"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemas comuns de URL do SAS e correções

A tabela a seguir lista alguns dos problemas comuns encontrados ao trabalhar com assinaturas de acesso compartilhado (que são usadas para identificar e compartilhar os VHDs enviados para sua solução), juntamente com as resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Corrigir** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Falha ao copiar imagens* |  |  |
| "?" não é encontrado na URL do SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL do SAS usando as ferramentas recomendadas. |
| Parâmetros "st" e "se" não estão na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize a URL do SAS com as **Data de início** e **Data de término** corretas. | 
| "sp=rl" não está na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Atualize o URL do SAS com permissões definidas como `Read` e `List`. | 
| O URL do SAS tem espaços em branco no nome do VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL do SAS para remover espaços em branco. |
| Erro de autorização de URL do SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Revise e corrija o formato URI do SAS. Regenere se necessário. |
| Os parâmetros "st" e "se" da URL do SAS não têm especificação completa de data e hora | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Os parâmetros de data de`st` `se` **início** e data de término **do** URL SAS (e substrings) são necessários para ter o formato de data-hora completa, tais como `11-02-2017T00:00:00Z`. Versões abreviadas não são válidas. (Alguns comandos na CLI do Azure podem gerar valores abreviados por padrão). | 
|  |  |  |

Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) para saber mais.
