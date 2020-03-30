---
title: Criptografia de dados com chave gerenciada pelo cliente - Banco de dados Azure para MySQL
description: O Banco de Dados Do Azure para criptografia de dados MySQL com uma chave gerenciada pelo cliente permite que você traga sua própria chave (BYOK) para proteção de dados em repouso. Também permite que as organizações implementem a separação de deveres na gestão de chaves e dados.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a97fee619858aa024ff208b72d3b2594c30d2fd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299117"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Banco de dados Azure para criptografia de dados MySQL com uma chave gerenciada pelo cliente

> [!NOTE]
> Neste momento, você deve solicitar acesso para usar esse recurso. Para isso, entre AskAzureDBforMySQL@service.microsoft.comem contato.

A criptografia de dados com chaves gerenciadas pelo cliente para o Azure Database for MySQL permite que você traga sua própria chave (BYOK) para proteção de dados em repouso. Também permite que as organizações implementem a separação de deveres na gestão de chaves e dados. Com a criptografia gerenciada pelo cliente, você é responsável e em um controle total do ciclo de vida de uma chave, das principais permissões de uso e da auditoria das operações nas chaves.

A criptografia de dados com chaves gerenciadas pelo cliente para o Azure Database for MySQL é definida no nível do servidor. Para um determinado servidor, uma chave gerenciada pelo cliente, chamada chave de criptografia de chave (KEK), é usada para criptografar a chave de criptografia de dados (DEK) usada pelo serviço. O KEK é uma chave assimétrica armazenada em uma instância [do Azure Key Vault,](../key-vault/key-Vault-secure-your-key-Vault.md) de propriedade do cliente e gerenciado pelo cliente. A chave de criptografia de chave (KEK) e a chave de criptografia de dados (DEK) são descritas com mais detalhes mais tarde neste artigo.

Key Vault é um sistema de gerenciamento de chaves externo baseado em nuvem. É altamente disponível e fornece armazenamento escalável e seguro para chaves criptográficas RSA, opcionalmente apoiado por fips 140-2 Módulos de segurança de hardware validados (HSMs). Ele não permite acesso direto a uma chave armazenada, mas fornece serviços de criptografia e descriptografia para entidades autorizadas. O Key Vault pode gerar a chave, importá-la ou [transferi-la a partir de um dispositivo HSM no local.](../key-vault/key-Vault-hsm-protected-keys.md)

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure, onde o Azure Database for MySQL suporta níveis de preços "Propósito Geral" e "Otimização da Memória".

## <a name="benefits"></a>Benefícios

A criptografia de dados do Banco de Dados Azure para MySQL fornece os seguintes benefícios:

* O acesso a dados é totalmente controlado por você pela capacidade de remover a chave e tornar o banco de dados inacessível 
* Controle total sobre o ciclo de vida-chave, incluindo a rotação da chave para se alinhar com as políticas corporativas
* Gerenciamento central e organização de chaves no Azure Key Vault
* Capacidade de implementar a separação de funções entre os oficiais de segurança e os administradores da DBA e do sistema


## <a name="terminology-and-description"></a>Descrição e terminologia

**Chave de criptografia de dados (DEK)**: Uma chave AES256 simétrica usada para criptografar uma partição ou bloco de dados. Criptografar cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso a DEKs é necessário pelo provedor de recursos ou instância do aplicativo que criptografa e descriptografa um bloco específico. Quando você substitui um DEK por uma nova chave, apenas os dados em seu bloco associado devem ser recriptografados com a nova chave.

**Chave de criptografia de chave (KEK)**: Uma chave de criptografia usada para criptografar os DEKs. Um KEK que nunca sai do Key Vault permite que os DEKs sejam criptografados e controlados. A entidade que tem acesso ao KEK pode ser diferente da entidade que exige o DEK. Uma vez que o KEK é necessário para descriptografar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente excluídos pela exclusão do KEK.

Os DEKs, criptografados com os KEKs, são armazenados separadamente. Apenas uma entidade com acesso ao KEK pode descriptografar esses DEKs. Para obter mais informações, consulte [Segurança na criptografia em repouso](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Como funciona a criptografia de dados com uma chave gerenciada pelo cliente

![Diagrama que mostra uma visão geral de Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Para que um servidor MySQL use chaves gerenciadas pelo cliente armazenadas no Key Vault para criptografia do DEK, um administrador do Key Vault fornece os seguintes direitos de acesso ao servidor:

* **obter**: Para recuperar a parte pública e propriedades da chave no cofre da chave.
* **wrapKey**: Para ser capaz de criptografar o DEK.
* **desembrulheKey**: Para poder descriptografar o DEK.

O administrador do cofre chave também pode [habilitar o registro de eventos de auditoria do Key Vault,](../azure-monitor/insights/azure-key-vault.md)para que eles possam ser auditados posteriormente.

Quando o servidor é configurado para usar a chave gerenciada pelo cliente armazenada no cofre de chaves, o servidor envia o DEK para o cofre de chaves para criptografia. O Key Vault retorna o DEK criptografado, que é armazenado no banco de dados do usuário. Da mesma forma, quando necessário, o servidor envia o DEK protegido para o cofre de chaves para descriptografia. Os auditores podem usar o Azure Monitor para revisar os registros de eventos de auditoria do Key Vault, se o registro estiver ativado.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Requisitos para configurar a criptografia de dados para o Banco de Dados Azure para MySQL

A seguir, os requisitos para configurar o Key Vault:

* O Key Vault e o Azure Database for MySQL devem pertencer ao mesmo inquilino do Azure Active Directory (Azure AD). As interações entre o Cofre de Chaves e o servidor não são suportadas. A movimentação de recursos depois requer que você reconfigure a criptografia de dados.
* Você deve habilitar o recurso de exclusão suave no cofre da chave, para proteger contra perda de dados se uma exclusão acidental de chave (ou Key Vault) acontecer. Os recursos excluídos são retidos por 90 dias, a menos que o usuário os recupere ou expurgue enquanto isso. As ações de recuperação e expurgo têm suas próprias permissões associadas em uma política de acesso do Key Vault. O recurso de exclusão suave está desligado por padrão, mas você pode habilitá-lo através do PowerShell ou do Azure CLI (observe que você não pode habilitá-lo através do portal Azure).
* Conceda ao Banco de Dados Do Azure acesso ao cofre principal com as permissões get, wrapKey e unwrapKey usando sua identidade gerenciada exclusiva. No portal Azure, a identidade única é criada automaticamente quando a criptografia de dados é ativada no MySQL. Consulte [Configurar a criptografia de dados para MySQL](howto-data-encryption-portal.md) para instruções detalhadas e passo a passo quando estiver usando o portal Azure.

* Quando você estiver usando um firewall com o Key Vault, você deve habilitar a opção **Permitir que serviços confiáveis da Microsoft contornem o firewall**.

A seguir, os requisitos para configurar a chave gerenciada pelo cliente:

* A chave gerenciada pelo cliente a ser usada para criptografar o DEK pode ser apenas assimétrica, RSA 2028.
* A data de ativação da chave (se definida) deve ser uma data e hora no passado. A data de validade (se definida) deve ser uma data e hora futuras.
* A chave deve estar no estado *habilitado.*
* Se você estiver importando uma chave existente no cofre da chave, certifique-se`.pfx`de `.byok` `.backup`fornecê-la nos formatos de arquivo suportados (, ).

## <a name="recommendations"></a>Recomendações

Quando você está usando criptografia de dados usando uma chave gerenciada pelo cliente, aqui estão recomendações para configurar o Key Vault:

* Defina um bloqueio de recursos no Key Vault para controlar quem pode excluir esse recurso crítico e evitar a exclusão acidental ou não autorizada.
* Habilite a auditoria e o relatório em todas as chaves de criptografia. O Key Vault fornece registros fáceis de injetar em outras ferramentas de gerenciamento de informações de segurança e eventos. O Azure Monitor Log Analytics é um exemplo de um serviço que já está integrado.

* Certifique-se de que o Key Vault e o Azure Database para MySQL residam na mesma região, para garantir um acesso mais rápido às operações de envoltório e desembrulho do DEK.

Aqui estão as recomendações para configurar uma chave gerenciada pelo cliente:

* Mantenha uma cópia da chave gerenciada pelo cliente em um local seguro ou deposite-a no serviço de custódia.

* Se o Key Vault gerar a chave, crie um backup de chave antes de usar a chave pela primeira vez. Você só pode restaurar o backup no Key Vault. Para obter mais informações sobre o comando backup, consulte [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condição-chave gerenciada pelo cliente inacessível

Quando você configura a criptografia de dados com uma chave gerenciada pelo cliente no Key Vault, o acesso contínuo a essa chave é necessário para que o servidor permaneça on-line. Se o servidor perder o acesso à chave gerenciada pelo cliente no Key Vault, o servidor começa a negar todas as conexões em 10 minutos. O servidor emite uma mensagem de erro correspondente e altera o estado do servidor para *Inacessível*. A única ação permitida em um banco de dados neste estado é excluí-lo.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revogação acidental de acesso à chave do Key Vault

Pode acontecer de alguém com direitos de acesso suficientes ao Key Vault acidentalmente desativar o acesso do servidor à chave por:

* Revogando as permissões get, wrapKey e desembrulheKey do servidor.
* Excluindo a chave.
* Excluindo o cofre da chave.
* Mudando as regras de firewall do cofre chave.

* Excluindo a identidade gerenciada do servidor no Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitore a chave gerenciada pelo cliente no Key Vault

Para monitorar o estado do banco de dados e para permitir o alerta para a perda de acesso transparente ao protetor de criptografia de dados, configure os seguintes recursos do Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): Um banco de dados inacessível que perdeu o acesso à chave do cliente mostra como "Inacessível" após a primeira conexão ao banco de dados ter sido negada.
* [Registro de atividades](../service-health/alerts-activity-log-service-notifications.md): Quando o acesso à chave do cliente no Cofre de Chaves gerenciado pelo cliente falha, as entradas são adicionadas ao registro de atividades. Você pode restabelecer o acesso o mais rápido possível, se você criar alertas para esses eventos.

* [Grupos de ação](../azure-monitor/platform/action-groups.md): Defina estes para enviar notificações e alertas com base em suas preferências.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Restaurar e replicar com a chave gerenciada de um cliente no Key Vault

Depois que o Banco de Dados Do Azure para MySQL é criptografado com a chave gerenciada do cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer esta nova cópia através de uma operação local ou de georestauração, ou através de réplicas de leitura. No entanto, a cópia pode ser alterada para refletir a chave gerenciada de um novo cliente para criptografia. Quando a chave gerenciada pelo cliente é alterada, backups antigos do servidor começam a usar a chave mais recente.

Para evitar problemas ao configurar a criptografia de dados gerenciada pelo cliente durante a restauração ou a criação de réplicas de leitura, é importante seguir essas etapas nos servidores mestre e restaurado/replica:

* Inicie o processo de restauração ou leitura de réplicas do master Azure Database for MySQL.
* Mantenha o servidor recém-criado (restaurado/réplica) em um estado inacessível, porque sua identidade única ainda não recebeu permissões ao Key Vault.
* No servidor restaurado/replica, revalide a chave gerenciada pelo cliente nas configurações de criptografia de dados. Isso garante que o servidor recém-criado receba permissões de envoltório e desembrulho para a chave armazenada no Key Vault.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar a [criptografia de dados com uma chave gerenciada pelo cliente para o banco de dados Do Azure para MySQL usando o portal Azure](howto-data-encryption-portal.md).
