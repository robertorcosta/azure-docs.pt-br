---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75467141"
---
Seu dispositivo está associado a uma conta de armazenamento que é usada como destino para seus dados no Azure. O acesso à conta de armazenamento é controlado pela assinatura e duas chaves de acesso de armazenamento de 512 bits associadas a essa conta de armazenamento.

Uma das chaves é usada para autenticação quando o dispositivo Data Box Edge acessa a conta de armazenamento. A outra chave é mantida em reserva, para que você possa girar as chaves periodicamente.

Por razões de segurança, muitos data centers requerem rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Proteja cuidadosamente a chave da sua conta. Não distribua a senha para outros usuários, codifique-a ou salve-a em qualquer lugar em texto simples que seja acessível a outros.
- Regenere a chave da sua conta através do portal Azure se você acha que ela pode ser comprometida. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).
- O administrador do Azure deve alterar ou regenerar periodicamente a chave primária ou secundária usando a seção Armazenamento do portal Azure para acessar diretamente a conta de armazenamento.