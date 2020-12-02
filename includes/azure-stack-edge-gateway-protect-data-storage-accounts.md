---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466146"
---
O dispositivo está associado a uma conta de armazenamento usada como destino dos dados no Azure. O acesso a uma conta de armazenamento é controlado pela assinatura e pelas duas chaves de acesso de armazenamento de 512 bits associadas àquela conta de armazenamento.

Uma das chaves é usada para autenticação quando o dispositivo Data Box Edge acessa a conta de armazenamento. A outra chave fica de reserva para que você possa alternar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Proteja a chave de conta com cuidado. Não distribua a senha para outros usuários, nem codifique ou salve em qualquer lugar em texto sem formatação acessível a outras pessoas.
- [Gere novamente sua chave de conta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) por meio do portal do Azure se você considerar que ela pode estar comprometida.
- O administrador do Azure deve alterar periodicamente ou regenerar a chave primária ou secundária usando a seção de armazenamento do portal do Azure para acessar a conta de armazenamento diretamente.