---
title: Azure Key Vault mover um cofre para uma região diferente | Microsoft Docs
description: Orientação sobre como mover um cofre de chaves para uma região diferente.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: e65a723ac9daafdc09896a50e197034104408df2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254142"
---
# <a name="moving-an-azure-key-vault-across-regions"></a>Movendo um Azure Key Vault entre regiões

## <a name="overview"></a>Visão geral

Key Vault não oferece suporte a uma operação de movimentação de recursos que permite mover um cofre de chaves para outra região. Este artigo abordará as soluções alternativas se você tiver uma necessidade comercial de mover um cofre de chaves para outra região. Cada opção tem limitações e é essencial entender as implicações dessas soluções alternativas antes de tentar fazê-las em um ambiente de produção.

Se você precisar mover um cofre de chaves para outra região, a solução será criar um novo cofre de chaves na região desejada e copiar manualmente cada segredo individual do cofre de chaves existente para o novo cofre de chaves. Essa operação pode ser feita em qualquer uma das seguintes maneiras listadas abaixo.

## <a name="design-considerations"></a>Considerações de criação

* Os nomes de Key Vault são globalmente exclusivos. Não será possível reutilizar o mesmo nome de cofre.

* Você precisará reconfigurar políticas de acesso e definições de configuração de rede no novo cofre de chaves.

* Será necessário reconfigurar a exclusão reversível e limpar a proteção no novo cofre de chaves.

* A operação de backup e restauração não preservará as configurações de autorotação, talvez seja necessário reconfigurar essas configurações.

## <a name="option-1---use-the-key-vault-backup-and-restore-commands"></a>Opção 1-usar os comandos de backup e restauração do cofre de chaves

Você pode fazer backup de cada segredo, chave e certificado individual em seu cofre usando o comando backup. Seus segredos serão baixados como um blob criptografado. Em seguida, você pode restaurar o blob em seu novo cofre de chaves. Os comandos são documentados no link abaixo.

[Comandos de Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault)

### <a name="limitations"></a>Limitações

* Não é possível fazer backup de um cofre de chaves em uma geografia e restaurá-lo em outra geografia. Saiba mais sobre as geografias do Azure. [Link](https://azure.microsoft.com/global-infrastructure/geographies/)

* O comando backup faz backup de todas as versões de cada segredo. Se você tiver um segredo com um grande número de versões anteriores (maior que 10), haverá uma chance de que a solicitação exceda o tamanho máximo de solicitação permitido e a operação possa falhar.

## <a name="option-2---manually-download-and-upload-secrets"></a>Opção 2-baixar e carregar segredos manualmente

Determinados tipos de segredo podem ser baixados manualmente. Por exemplo, você pode baixar certificados como um arquivo. pfx. Essa opção elimina as restrições geográficas para alguns tipos secretos, como certificados. Você pode carregar os arquivos. pfx em qualquer cofre de chaves em qualquer região. Seu segredo será baixado em um formato não protegido por senha. Você será responsável por proteger seus segredos quando eles deixarem Key Vault enquanto a movimentação for executada.
