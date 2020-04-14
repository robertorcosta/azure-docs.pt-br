---
title: Problemas e correções comuns do SAS URI - Azure Marketplace
description: Problemas comuns encontrados e resoluções sugeridas ao trabalhar com assinaturas de acesso compartilhadas.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266234"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemas e correções comuns do SAS URI

> [!IMPORTANT]
> Estamos mudando o gerenciamento das ofertas da Sua Máquina Virtual Do Portal de Parceiros em Nuvem para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [problemas e correções comuns do Uri SAS](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) para gerenciar suas ofertas.

A seguir, problemas comuns encontrados ao trabalhar com assinaturas de acesso compartilhado (que são usadas para identificar e compartilhar os VHDs enviados para sua solução), juntamente com resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Corrigir** |
| --------- | ------------------- | ------- |
| *Falha ao copiar imagens* |  |  |
| "?" não é encontrado no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o Uri SAS usando ferramentas recomendadas. |
| Parâmetros "st" e "se" não estão no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI SAS com os valores adequados **de Data de Início** e Data de **Término.** |
| "sp=rl" não está no SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o Uri SAS `Read` com `List`permissões definidas como e . |
| SAS URI tem espaços brancos no nome VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o Uri SAS para remover espaços brancos. |
| Erro de autorização do SAS URI | `Failure: Copying Images. Not able to download blob due to authorization error.` | Revise e corrija o formato URI do SAS. Regenere se necessário. |
| Os parâmetros SAS URI "st" e "se" não possuem especificação de data-hora completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Os parâmetros de **data de início** e prazo de término **do** SAS URI (e`st` `se` substrings) devem ter formato completo de data-hora, tais como `11-02-2017T00:00:00Z`. As versões encurtadas são inválidas (alguns comandos no Azure CLI podem gerar valores encurtados por padrão). |
|  |  |  |

Para obter detalhes, [consulte Usando assinaturas de acesso compartilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
