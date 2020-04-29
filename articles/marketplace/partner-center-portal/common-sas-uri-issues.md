---
title: Correções e problemas comuns de URI de SAS-Azure Marketplace
description: Problemas comuns encontrados e resoluções sugeridas ao trabalhar com assinaturas de acesso compartilhado.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266234"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Problemas e correções de URI de SAS comuns

> [!IMPORTANT]
> Estamos movendo o gerenciamento de suas ofertas de máquina virtual do Azure de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, siga as instruções em [problemas de URI de SAS comuns e correções](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) para Portal do Cloud Partner gerenciar suas ofertas.

Veja a seguir os problemas comuns encontrados ao trabalhar com assinaturas de acesso compartilhado (que são usadas para identificar e compartilhar os VHDs carregados para sua solução), juntamente com as resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Soluciona** |
| --------- | ------------------- | ------- |
| *Falha ao copiar imagens* |  |  |
| "?" não foi encontrado no URI de SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI de SAS usando as ferramentas recomendadas. |
| parâmetros "St" e "se" não estão no URI de SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI de SAS com os valores de **data de início** e **data de término** adequados. |
| "SP = RL" não está no URI de SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI de SAS com permissões definidas `Read` como `List`e. |
| O URI de SAS tem espaços em branco no nome do VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URI de SAS para remover espaços em branco. |
| Erro de autorização de URI SAS | `Failure: Copying Images. Not able to download blob due to authorization error.` | Revise e corrija o formato URI do SAS. Regenere se necessário. |
| Os parâmetros "St" e "se" do URI de SAS não têm especificação de data e hora completa | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | Os `11-02-2017T00:00:00Z`parâmetros de data de **início** e de data`st` de `se` **término** da SAS (e subcadeias de caracteres) devem ter formato de data/hora completo, como. As versões reduzidas são inválidas (alguns comandos no CLI do Azure podem gerar valores reduzidos por padrão). |
|  |  |  |

Para obter detalhes, consulte [usando assinaturas de acesso compartilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
