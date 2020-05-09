---
title: O que fazer no caso de uma interrupção de serviço do Azure afetar o Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Saiba o que fazer no caso de uma interrupção de serviço do Azure afetar o Cofre de Chaves do Azure.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: sudbalas
ms.openlocfilehash: 4796e6c555ca67794409fb1476f3c4fd0d760719
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780446"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Redundância e disponibilidade de Cofre de Chaves do Azure

O Cofre de Chaves do Azure tem várias camadas de redundância, a fim de garantir que seus segredos e chaves permaneçam disponíveis para seu aplicativo até mesmo se os componentes individuais do serviço falharem.

O conteúdo de seu cofre de chaves é replicado na região e em uma região secundária a pelo menos 150 milhas de distância, mas na mesma região geográfica. Isso mantém a alta durabilidade de seus segredos e chaves. Consulte o documento [Regiões emparelhadas do Azure](../../best-practices-availability-paired-regions.md) para obter detalhes sobre pares de regiões específicos.

Se os componentes individuais dentro do serviço de cofre de chaves falharem, os componentes alternativos dentro da região interferirão para atender à sua solicitação, de modo a garantir que não haja degradação da funcionalidade. Você não precisa executar qualquer ação para disparar esse recurso. Ele ocorre automaticamente de modo transparente para você.

No eventual caso de uma região inteira do Azure ficar indisponível, as solicitações que você faz do Cofre de Chaves do Azure nessa região são roteadas automaticamente (*failover*) para uma região secundária. Quando a região primária estiver disponível novamente, as solicitações serão roteadas de volta (*failback*) para a região primária. Novamente, não é necessário executar nenhuma ação, pois isso acontecerá de modo automático.

Com esse design de alta disponibilidade, o Azure Key Vault não requer nenhum tempo de inatividade para atividades de manutenção.

Há algumas advertências que você deve conhecer:

* No caso de um failover de região, pode levar alguns minutos para o serviço executar failover. Solicitações feitas durante esse período podem falhar até que o failover seja concluído.
* Após a conclusão de um failover, o cofre de chaves estará no modo somente leitura. As solicitações permitidas nesse modo são:
  * Listar cofres de chave
  * Obter propriedades de cofres de chave
   * Listar certificados
  * Obter certificados
  * Listar segredos
  * Obter segredos
  * Listar chaves
  * Obter (propriedades das) chaves
  * Encrypt
  * Descriptografar
  * Encapsular
  * Desencapsular
  * Verificar
  * Assinar
  * Backup
* Após o failback de um failover, todos os tipos de solicitação (ou seja, solicitações de leitura *e* de gravação) são disponibilizados.

