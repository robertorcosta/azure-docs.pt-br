---
title: Proteja seu cluster no Azure Data Explorer
description: Este artigo descreve como proteger seu cluster no Azure Data Explorer dentro do portal Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720337"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Proteja seu cluster no Azure Data Explorer - Portal Azure

[A Criptografia de Disco Do Azure](/azure/security/azure-security-disk-encryption-overview) ajuda a proteger e proteger seus dados para atender aos seus compromissos de segurança organizacional e conformidade. Ele fornece criptografia de volume para o Sistema Operacional e discos de dados de suas máquinas virtuais de cluster. Ele também se integra ao [Azure Key Vault](/azure/key-vault/), que nos permite controlar e gerenciar as chaves e segredos de criptografia de disco, e garantir que todos os dados nos discos VM sejam criptografados. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Habilite a criptografia em repouso no portal Azure
  
As configurações de segurança do cluster permitem que você habilite a criptografia de disco em seu cluster. A ativação da [criptografia em repouso](/azure/security/fundamentals/encryption-atrest) no cluster fornece proteção de dados para dados armazenados (em repouso). 

1. No portal Azure, acesse o recurso de cluster do Azure Data Explorer. No título **Configurações,** selecione **Segurança**. 

    ![Ligue a criptografia em repouso](media/manage-cluster-security/security-encryption-at-rest.png)

1. Na janela **Segurança,** selecione **'Ligado'** para a configuração de segurança **de criptografia de disco.** 

1. Selecione **Salvar**.
 
> [!NOTE]
> Selecione **Desativar** para desativar a criptografia depois de habilitada.

## <a name="next-steps"></a>Próximas etapas

[Verificar integridade do cluster](/azure/data-explorer/check-cluster-health)
