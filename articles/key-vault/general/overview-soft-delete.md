---
title: Exclusão reversível do Azure Key Vault | Microsoft Docs
description: A exclusão suave no Azure Key Vault permite recuperar cofres de chaves excluídos e objetos do cofre chave, como chaves, segredos e certificados.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 6185f0d84f27b6be89e797fc7cfb22940d8c6401
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432093"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Visão geral de exclusão reversível do Azure Key Vault

O recurso de exclusão reversível do Azure Key Vault permite a recuperação de cofres e objetos de cofre excluídos, o que é conhecido como exclusão reversível. Especificamente, abordamos os seguintes cenários:

- Suporte à exclusão reversível de cofres de chaves
- Suporte à exclusão recuperável de objetos do cofre de chaves (por exemplo, chaves, segredos, certificados)

## <a name="supporting-interfaces"></a>Interfaces de suporte

O recurso de exclusão suave está inicialmente disponível através das interfaces [REST,](/rest/api/keyvault/) [CLI,](soft-delete-cli.md) [PowerShell](soft-delete-powershell.md) e [.NET/C#.](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)

## <a name="scenarios"></a>Cenários

Os Azure Key Vaults são recursos controlados, gerenciados pelo Azure Resource Manager. O Azure Resource Manager também especifica um comportamento bem definido para a exclusão, que exige que uma operação DELETE bem-sucedida faça com que esse recurso não esteja mais acessível. O recurso de exclusão reversível aborda a recuperação do objeto excluído, independentemente se a exclusão foi acidental ou intencional.

1. No cenário típico, um usuário pode ter acidentalmente excluído um cofre de chaves ou um objeto do cofre de chaves; se esse cofre de chaves ou objeto do cofre de chaves precisar ser recuperável por um período predeterminado, o usuário poderá desfazer a exclusão e recuperar seus dados.

2. Em um cenário diferente, um usuário mal-intencionado pode tentar excluir um cofre de chaves ou um objeto do cofre de chaves, como uma chave dentro de um cofre, para causar uma interrupção dos negócios. A separação da exclusão do cofre de chaves ou do objeto do cofre de chaves da exclusão real dos dados subjacentes pode ser usada como uma medida de segurança, por exemplo, restringindo as permissões de exclusão de dados a outra função confiável. Essa abordagem efetivamente exige quorum para uma operação que, de outro modo, poderá resultar em uma perda de dados imediata.

### <a name="soft-delete-behavior"></a>Comportamento de exclusão reversível

Quando a exclusão suave é ativada, os recursos marcados como recursos excluídos são retidos por um período especificado (90 dias por padrão). Além disso, o serviço fornece um mecanismo para recuperar o objeto excluído, basicamente, desfazendo a exclusão.

Ao criar um novo cofre de chaves, a exclusão suave está ateada por padrão. Você pode criar um cofre de chaves sem exclusão suave através do [Azure CLI](soft-delete-cli.md) ou [Azure Powershell](soft-delete-powershell.md). Uma vez que a exclusão suave esteja ativada em um cofre de chaves, ela não pode ser desativada

O período de retenção padrão é de 90 dias, mas, durante a criação do cofre-chave, é possível definir o intervalo da diretiva de retenção para um valor de 7 a 90 dias através do portal Azure. A política de retenção de proteção de purga usa o mesmo intervalo. Uma vez definido, o intervalo da política de retenção não pode ser alterado.

Não é possível reutilizar o nome de um cofre de chaves que tenha sido excluído até que o período de retenção tenha passado.

### <a name="purge-protection"></a>Proteção contra expurgo 

A proteção contra expurgo é um comportamento opcional do Key Vault e não está **habilitada por padrão**. Pode ser ligado via [CLI](soft-delete-cli.md#enabling-purge-protection) ou [Powershell](soft-delete-powershell.md#enabling-purge-protection).

Quando a proteção de purga estiver em ação, um cofre ou um objeto no estado excluído não pode ser eliminado até que o período de retenção tenha passado. Cofres e objetos excluídos suavemente ainda podem ser recuperados, garantindo que a política de retenção seja seguida. 

O período de retenção padrão é de 90 dias, mas é possível definir o intervalo da política de retenção para um valor de 7 a 90 dias através do portal Azure. Uma vez que o intervalo da diretiva de retenção esteja definido e salvo, ele não pode ser alterado para esse cofre. 

### <a name="permitted-purge"></a>Limpeza permitida

A exclusão permanente, limpeza, de um cofre de chaves é possível por meio de uma operação POST no recurso de proxy e exige privilégios especiais. Geralmente, apenas o proprietário da assinatura poderá limpar um cofre de chaves. A operação POST dispara a exclusão imediata e irrecuperável desse cofre. 

Exceções são:
- Quando a assinatura do Azure foi marcada como *indeletável*. Neste caso, apenas o serviço pode executar a exclusão real e ele o fará como um processo agendado. 
- Quando o sinalizador de proteção --enable-purge-protection estiver ativado no próprio cofre. Nesse caso, o Key Vault aguardará 90 dias desde quando o objeto secreto original foi marcado para exclusão para então excluí-lo permanentemente.

### <a name="key-vault-recovery"></a>Recuperação do cofre de chaves

Ao excluir um cofre de chaves, o serviço cria um recurso de proxy na assinatura, adicionando metadados suficientes para recuperação. O recurso de proxy é um objeto armazenado, disponível na mesma localização do cofre de chaves excluído. 

### <a name="key-vault-object-recovery"></a>Recuperação de objetos do cofre de chaves

Ao excluir um objeto de cofre chave, como uma chave, o serviço colocará o objeto em um estado excluído, tornando-o inacessível a quaisquer operações de recuperação. Nesse estado, o objeto do cofre de chaves só poderá ser listado, recuperado ou excluído permanentemente/de maneira forçada. 

Ao mesmo tempo, o Key Vault agendará a exclusão dos dados subjacentes correspondentes ao cofre de chaves ou objeto do cofre de chaves excluído para execução após um intervalo de retenção predeterminado. O registro DNS correspondente ao cofre também é mantido durante o intervalo de retenção.

### <a name="soft-delete-retention-period"></a>Período de retenção da exclusão reversível

Os recursos excluídos de maneira reversível são mantidos por um período definido de tempo, 90 dias. Durante o intervalo de retenção da exclusão reversível, o seguinte se aplica:

- É possível listar todos os cofres de chaves e objetos do cofre de chaves no estado de exclusão reversível de sua assinatura, bem como informações de exclusão e recuperação de acesso sobre eles.
    - Somente usuários com permissões especiais podem listar os cofres excluídos. Recomendamos que nossos usuários criem uma função personalizada com essas permissões especiais para a manipulação dos cofres excluídos.
- Não é possível criar um cofre de chaves com o mesmo nome na mesma localização; do mesmo modo, não é possível criar um objeto do cofre de chaves em determinado cofre se esse cofre de chaves contém um objeto com o mesmo nome e que está no estado excluído 
- Somente um usuário com privilégios específicos pode restaurar um cofre de chaves ou objeto do cofre de chaves emitindo um comando de recuperação no recurso de proxy correspondente.
    - O usuário, membro da função personalizada, que tem o privilégio para criar um cofre de chaves no grupo de recursos pode restaurar o cofre.
- Somente um usuário com privilégios específicos pode excluir por imposição um cofre de chaves ou objeto do cofre de chaves emitindo um comando de exclusão no recurso de proxy correspondente.

A menos que um cofre de chaves ou objeto do cofre de chaves seja recuperado, ao final do intervalo de retenção, o serviço realizará uma limpeza do cofre de chaves ou do objeto do cofre de chaves de excluídos de maneira reversível e de seu conteúdo. A exclusão de recursos não pode ser reagendada.

### <a name="billing-implications"></a>Implicações de cobrança

Em geral, quando um objeto (um cofre de chaves ou uma chave ou um segredo) está no estado excluído, há apenas duas operações possíveis: “limpar” e “recuperar”. Todas as outras operações falharão. Portanto, mesmo que o objeto exista, nenhuma operação pode ser executada e, portanto, nenhum uso ocorrerá, portanto, não haverá cobrança. No entanto, há as exceções a seguir:

- Ações “limpar' e “recuperar” contarão até as operações do cofre de chaves normal e serão cobradas.
- Se o objeto for uma chave de HSM, o encargo”chave Protegida HSM” por versão de chave por encargo mensal será aplicada se uma versão de chave tiver sido usada nos últimos 30 dias. Depois disso, uma vez que o objeto está no estado excluído, nenhuma operação pode ser executada em relação a ela, portanto nenhum encargo será aplicado.

## <a name="next-steps"></a>Próximas etapas

As duas guias a seguir oferecem os cenários de uso primário para usar a exclusão reversível.

- [Como usar a exclusão reversível do Key Vault com o PowerShell](soft-delete-powershell.md) 
- [Como usar a exclusão reversível do Key Vault com a CLI](soft-delete-cli.md)

