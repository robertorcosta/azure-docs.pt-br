---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82562110"
---
Seu dispositivo está associado a uma conta de armazenamento que é usada como um destino para seus dados no Azure. O acesso à conta de armazenamento é controlado pela assinatura e chaves de acesso de armazenamento de 2 512 bits associadas a essa conta de armazenamento.

Uma das chaves é usada para autenticação quando o dispositivo de borda Azure Stack acessa a conta de armazenamento. A outra chave é mantida em reserva, para que você possa girar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Proteja cuidadosamente sua chave de conta. Não distribua a senha para outros usuários, codifique-a ou salve-a em qualquer lugar em texto sem formatação acessível a outras pessoas.
- Gere novamente sua chave de conta por meio do portal do Azure se você considerar que ela pode estar comprometida. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).
- O administrador do Azure deve alterar periodicamente ou regenerar a chave primária ou secundária usando a seção de armazenamento do portal do Azure para acessar a conta de armazenamento diretamente.