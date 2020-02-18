---
title: Solução de problemas de criptografia de dados para banco de dados do Azure para MySQL
description: Saiba como solucionar problemas de criptografia de dados para o banco de dado do Azure para MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4b517a463ec949d804798787ad4b35b53145a4a8
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371547"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-mysql"></a>Solucionando problemas de criptografia de dados com chaves gerenciadas pelo cliente no banco de dados do Azure para MySQL
Este artigo descreve como identificar e resolver problemas ou erros comuns que ocorrem em um banco de dados do Azure para MySQL configurado com criptografia de dado usando a chave gerenciada pelo cliente.

## <a name="introduction"></a>Introdução
Quando a criptografia de dados é configurada para usar uma chave gerenciada pelo cliente no Azure Key Vault, o acesso contínuo a essa chave é necessário para que o servidor fique disponível. Se o servidor perder o acesso à chave gerenciada pelo cliente no Azure Key Vault, o servidor começará a negar todas as conexões com a mensagem de erro apropriada e alterará seu estado para ***inacessível*** no portal do Azure.

Se um banco de dados do Azure inacessível para o servidor MySQL não for mais necessário, ele poderá ser excluído imediatamente para parar de incorrer em custos. Todas as outras ações no servidor não são permitidas até que o acesso ao cofre de chaves do Azure tenha sido restaurado e o servidor esteja novamente disponível. Alterar a opção de criptografia de dados de ' Sim ' (gerenciado pelo cliente) para ' não ' (gerenciado pelo serviço) em um servidor inacessível também não é possível enquanto um servidor é criptografado com gerenciado pelo cliente. Você deve revalidar a chave manualmente para disponibilizar o servidor novamente. Isso é necessário para proteger os dados contra o acesso não autorizado, enquanto as permissões para a chave gerenciada pelo cliente foram revogadas.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Erros comuns que fazem com que o servidor se torne inacessível

A maioria dos problemas que ocorrem quando você usa a criptografia de dados com Azure Key Vault são causadas por uma das seguintes configurações incorretas-

O cofre de chaves não está disponível ou não existe

* O cofre de chaves foi excluído por engano.
* Um erro de rede intermitente faz com que o cofre de chaves fique indisponível.

Sem permissão para acessar o cofre de chaves ou a chave não existe

* A chave foi excluída, desabilitada acidentalmente ou expirou.
* A identidade gerenciada por instância do banco de dados do Azure para MySQL foi excluída acidentalmente.
* As permissões concedidas ao banco de dados do Azure para servidor MySQL a identidade gerenciada para as chaves não são suficientes (elas não incluem obter, encapsular e desencapsular).
* Permissões para a identidade gerenciada da instância do banco de dados do Azure para servidor MySQL foram revogadas.

## <a name="identify-and-resolve-common-errors"></a>Identificar e resolver erros comuns
### <a name="errors-on-the-key-vault"></a>Erros no cofre de chaves

#### <a name="disabled-key-vault"></a>Cofre de chaves desabilitado
* AzureKeyVaultKeyDisabledMessage
* **Explicação** : a operação não pôde ser concluída no servidor porque a chave de Azure Key Vault está desabilitada.

#### <a name="missing-key-vault-permissions"></a>Permissões do Key Vault ausentes
* AzureKeyVaultMissingPermissionsMessage
* O servidor não tem as permissões obter, encapsular e desencapsular necessárias para as permissões de Azure Key Vault. Conceda quaisquer permissões ausentes à entidade de serviço com ID.

### <a name="mitigation"></a>Atenuação
* Confirme se a chave gerenciada pelo cliente está presente no Key Vault:
* Identifique o cofre de chaves e vá até ele no portal do Azure.
* Certifique-se de que a chave identificada pelo URI da chave esteja presente.


## <a name="next-steps"></a>Próximas etapas
[Configure a criptografia de dados com uma chave gerenciada pelo cliente para o banco de dados do Azure para MySQL usando o portal do Azure](howto-data-encryption-portal.md).