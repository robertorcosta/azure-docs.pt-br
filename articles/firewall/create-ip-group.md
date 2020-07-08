---
title: Criar grupos de IP no firewall do Azure
description: Os grupos de IPS permitem que você agrupe e gerencie endereços IP para regras de firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602526"
---
# <a name="create-ip-groups"></a>Criar grupos de IPs

Os grupos de IPS permitem que você agrupe e gerencie endereços IP para regras de firewall do Azure. Eles podem ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereços IP.

## <a name="create-an-ip-group"></a>Criar um grupo de IPS

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Digite **grupos de IPS** na caixa de texto de pesquisa e, em seguida, selecione **grupos de IPS**.
3. Selecione **Criar**.
4. Selecione sua assinatura.
5. Selecione um grupo de recursos ou crie um novo.
6. Digite um nome exclusivo para o grupo de IPS e selecione uma região.

6. Selecione **Avançar: endereços IP**.
7. Digite um endereço IP, vários endereços IP ou intervalos de endereços IP.

   Há duas maneiras de inserir endereços IP:
   - Você pode inseri-los manualmente
   - Você pode importá-los de um arquivo

   Para importar de um arquivo, selecione **importar de um arquivo**. Você pode arrastar o arquivo para a caixa ou selecionar **procurar arquivos**. Se necessário, você pode revisar e editar seus endereços IP carregados.

   Quando você digita um endereço IP, o portal o valida para verificar se há sobreposição, duplicações e problemas de formatação.

5. Quando terminar, selecione **revisar + criar**.
6. Selecione **Criar**.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre grupos de IP](ip-groups.md)