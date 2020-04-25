---
title: Problemas de URL de SAS comuns e correções para o Azure Marketplace
description: Lista problemas comuns sobre o uso de URIs de assinatura de acesso compartilhado e possíveis soluções.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147039"
---
# <a name="common-sas-url-issues-and-fixes"></a>Problemas comuns de URL do SAS e correções

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de máquina virtual do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [problemas comuns de URL de SAS e correções](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) para gerenciar suas ofertas migradas.

A tabela a seguir lista alguns dos problemas comuns encontrados ao trabalhar com assinaturas de acesso compartilhado (que são usadas para identificar e compartilhar os VHDs enviados para sua solução), juntamente com as resoluções sugeridas.

| **Problema** | **Mensagem de falha** | **Soluciona** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Falha ao copiar imagens* |  |  |
| "?" não é encontrado na URL do SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL do SAS usando as ferramentas recomendadas. |
| parâmetros "St" e "se" não estão na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize a URL do SAS com as **Data de início** e **Data de término** corretas. | 
| "SP = RL" não está na URL SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Atualize o URL do SAS com permissões definidas como `Read` e `List`. | 
| O URL do SAS tem espaços em branco no nome do VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Atualize o URL do SAS para remover espaços em branco. |
| Erro de autorização de URL do SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Revise e corrija o formato URI do SAS. Regenere se necessário. |
| Os parâmetros "st" e "se" da URL do SAS não têm especificação completa de data e hora | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | Os parâmetros de data de **início** e data de`st` **término** da URL SAS (e `se` subcadeias de caracteres) devem ter o `11-02-2017T00:00:00Z`formato DateTime completo, como. Versões abreviadas não são válidas. (Alguns comandos na CLI do Azure podem gerar valores abreviados por padrão). | 
|  |  |  |

Para saber mais, confira [Usar SAS (Assinaturas de Acesso Compartilhado)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) para saber mais.
