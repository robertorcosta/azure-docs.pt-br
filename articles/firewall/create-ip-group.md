---
title: Criar grupos IP no Firewall Do Azure
description: Os Grupos IP permitem que você agrupe e gerencie endereços IP para as regras do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444531"
---
# <a name="create-ip-groups-preview"></a>Criar grupos IP (visualização)

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

Os Grupos IP permitem que você agrupe e gerencie endereços IP para as regras do Firewall do Azure. Eles podem ter um único endereço IP, vários endereços IP ou uma ou mais faixas de endereçoIP.

## <a name="create-an-ip-group"></a>Criar um grupo IP

1. Na página inicial do portal do Azure, selecione **Criar um recurso**.
2. Digite **grupos IP** na caixa de texto de pesquisa e, em seguida, selecione **Grupos IP**.
3. Selecione **Criar**.
4. Selecione sua assinatura.
5. Selecione um grupo de recursos ou crie um novo.
6. Digite um nome exclusivo para você GRUPO IP e selecione uma região.

6. Selecione **A seguir: endereços IP**.
7. Digite um endereço IP, vários endereços IP ou faixas de endereçoIP.

   Existem duas maneiras de inserir endereços IP:
   - Você pode inseri-los manualmente
   - Você pode importá-los a partir de um arquivo

   Para importar de um arquivo, **selecione Importar a partir de um arquivo**. Você pode arrastar seu arquivo para a caixa ou selecionar **Procurar arquivos**. Se necessário, você pode rever e editar seus endereços IP enviados.

   Quando você digita um endereço IP, o portal valida-o para verificar se há problemas de sobreposição, duplicatas e formatação.

5. Quando terminar, selecione **'Revisar + Criar '''Revisar'.**
6. Selecione **Criar**.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre grupos DE IP](ip-groups.md)