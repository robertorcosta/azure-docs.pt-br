---
title: Mover um cofre de chaves para uma região diferente – Azure Key Vault | Microsoft Docs
description: Este artigo apresenta orientações sobre como mover um cofre de chaves para uma região diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 7d58cd26b6e4ca77da98f8c2f82dbdb481ccbb50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88585745"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Mover um cofre de chaves do Azure entre regiões

O Azure Key Vault não é compatível com operações de movimentação de recursos que permitam mover um cofre de chaves de uma região para outra. Este artigo aborda soluções alternativas para organizações que têm uma necessidade de negócios para mover um cofre de chaves para outra região. Cada solução alternativa tem limitações. É fundamental entender as implicações dessas soluções alternativas antes de tentar aplicá-las em um ambiente de produção.

Para mover um cofre de chaves para outra região, crie um cofre de chaves na outra região e, em seguida, copie manualmente cada segredo individual do cofre de chaves atual para o novo cofre de chaves. É possível fazer isso usando uma das duas opções a seguir.

## <a name="design-considerations"></a>Considerações sobre o design

Antes de começar, lembre-se dos seguintes conceitos:

* Os nomes do cofre de chaves são globalmente exclusivos. Não é possível reutilizar um nome de cofre.
* É preciso reconfigurar as políticas de acesso e as definições de configuração de rede no novo cofre de chaves.
* É preciso reconfigurar a exclusão reversível e limpar a proteção no novo cofre de chaves.
* A operação de backup e restauração não preservará as configurações de rotação automática. Talvez seja necessário redefinir as configurações.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Opção 1: Usar os comandos de backup e restauração do cofre de chaves

É possível fazer backup de cada segredo, chave e certificado individual em seu cofre usando o comando de backup. Seus segredos são baixados como um blob criptografado. Em seguida, é possível restaurar o blob em seu novo cofre de chaves. Para obter uma lista de comandos, consulte [Comandos do Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault).

O uso dos comandos de backup e restauração tem duas limitações:

* Não é possível fazer backup de um cofre de chaves em uma região geográfica e restaurá-lo em outra região geográfica. Para obter mais informações, consulte [Geografias do Microsoft Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

* O comando de backup faz backup de todas as versões de cada segredo. Se você tiver um segredo com um grande número de versões anteriores (mais de 10), o tamanho da solicitação poderá exceder o máximo permitido, e a operação poderá falhar.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Opção 2: Baixar e carregar manualmente os segredos do cofre de chaves

É possível baixar certos tipos de segredo manualmente. Por exemplo, é possível baixar certificados como um arquivo PFX. Essa opção elimina as restrições geográficas para alguns tipos de segredos, como certificados. É possível carregar os arquivos PFX em qualquer cofre de chaves em qualquer região. Os segredos são baixados em um formato não protegido por senha. Você é responsável por proteger seus segredos durante a movimentação.
