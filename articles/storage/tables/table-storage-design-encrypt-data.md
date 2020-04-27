---
title: Criptografar dados de tabela de armazenamento do Azure | Microsoft Docs
description: Saiba mais sobre a criptografia de dados de tabela no armazenamento do Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "60325996"
---
# <a name="encrypt-table-data"></a>Criptografar dados de tabela
A Biblioteca de Cliente do Armazenamento do Azure para .NET dá suporte à criptografia de propriedades de entidade para as operações de inserção e substituição. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias, e são convertidas novamente em cadeias de caracteres após a descriptografia.    

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades que devem ser criptografadas. Isso pode ser feito especificando o atributo [EncryptProperty] \(para entidades POCO que derivam de TableEntity) ou um resolvedor de criptografia nas opções de solicitação. Um resolvedor de criptografia é um delegado que usa uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de clientes usará essas informações para decidir se uma propriedade deve ser criptografada durante a gravação para a transmissão. O representante também oferece a possibilidade de lógica em torno de como as propriedades são criptografadas. (Por exemplo, se X, em seguida, criptografe A propriedade A; caso contrário, criptografe as propriedades A e B.) Não é necessário fornecer essas informações durante a leitura ou consulta de entidades.

## <a name="merge-support"></a>Suporte de mesclagem

Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. Mesclar requer fazer chamadas de serviço extra para ler a entidade já existente no serviço ou usar uma nova chave por propriedade, os quais não são ambos adequados por motivos de desempenho.     

Para obter informações sobre como criptografar dados de tabela, confira [Criptografia do lado do cliente e Cofre da Chave do Azure para Armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Próximas etapas

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Relações de modelagem](table-storage-design-modeling.md)
- [Relações de modelagem](table-storage-design-modeling.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
