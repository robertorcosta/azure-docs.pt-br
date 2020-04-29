---
title: Criptografia de dados com chave gerenciada pelo cliente-banco de dado do Azure para MySQL
description: O banco de dados do Azure para MySQL Data Encryption com uma chave gerenciada pelo cliente permite que você Bring Your Own Key (BYOK) para proteção de dados em repouso. Ele também permite que as organizações implementem a separação de tarefas no gerenciamento de chaves e dados.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a97fee619858aa024ff208b72d3b2594c30d2fd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299117"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Banco de dados do Azure para MySQL Data Encryption com uma chave gerenciada pelo cliente

> [!NOTE]
> Neste momento, você deve solicitar acesso para usar esse recurso. Para fazer isso, entre AskAzureDBforMySQL@service.microsoft.comem contato com.

A criptografia de dados com chaves gerenciadas pelo cliente para o Azure database for MySQL permite que você traga sua própria chave (BYOK) para proteção de dados em repouso. Ele também permite que as organizações implementem a separação de tarefas no gerenciamento de chaves e dados. Com a criptografia gerenciada pelo cliente, você é responsável por, e em um controle total do, o ciclo de vida de uma chave, as permissões de uso de chave e a auditoria de operações em chaves.

A criptografia de dados com chaves gerenciadas pelo cliente do banco de dados do Azure para MySQL é definida no nível do servidor. Para um determinado servidor, uma chave gerenciada pelo cliente, chamada chave de criptografia de chave (KEK), é usada para criptografar a DEK (chave de criptografia de dados) usada pelo serviço. O KEK é uma chave assimétrica armazenada em uma instância de [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) de Propriedade do cliente e gerenciada pelo cliente. A chave de criptografia de chave (KEK) e a chave de criptografia de dados (DEK) são descritas em mais detalhes posteriormente neste artigo.

O Key Vault é um sistema de gerenciamento de chaves externas baseado em nuvem. Ele é altamente disponível e fornece armazenamento escalonável e seguro para chaves de criptografia RSA, opcionalmente apoiado por HSMs (módulos de segurança de hardware) validados pelo FIPS 140-2 nível 2. Ele não permite acesso direto a uma chave armazenada, mas fornece serviços de criptografia e descriptografia a entidades autorizadas. Key Vault pode gerar a chave, importá-la ou [transferi-la de um dispositivo HSM local](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure, nas quais o banco de dados do Azure para MySQL dá suporte aos tipos de preço "Uso Geral" e "memória otimizada".

## <a name="benefits"></a>Vantagens

A criptografia de dados para o Azure Database para MySQL oferece os seguintes benefícios:

* O acesso a dados é totalmente controlado por você pela capacidade de remover a chave e torná-lo inacessível 
* Controle total sobre o ciclo de vida da chave, incluindo a rotação da chave para se alinhar com as políticas corporativas
* Gerenciamento central e organização de chaves no Azure Key Vault
* Capacidade de implementar a separação de tarefas entre os gerentes de segurança e DBA e administradores do sistema


## <a name="terminology-and-description"></a>Descrição e terminologia

**DEK (chave de criptografia de dados)**: uma chave de aes256s simétricas usada para criptografar uma partição ou um bloco de dados. Criptografar cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso a DEKs é necessário pelo provedor de recursos ou instância do aplicativo que criptografa e descriptografa um bloco específico. Quando você substitui um DEK por uma nova chave, somente os dados em seu bloco associado devem ser criptografados novamente com a nova chave.

Chave de **criptografia de chave (Kek)**: uma chave de criptografia usada para criptografar o DEKs. Um KEK que nunca deixa Key Vault permite que os DEKs sejam criptografados e controlados. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Uma vez que o KEK é necessário para descriptografar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente excluídos pela exclusão do KEK.

O DEKs, criptografado com o KEKs, são armazenados separadamente. Somente uma entidade com acesso ao KEK pode descriptografar esses DEKs. Para obter mais informações, consulte [segurança em criptografia em repouso](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Como funciona a criptografia de dados com uma chave gerenciada pelo cliente

![Diagrama que mostra uma visão geral de Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Para que um servidor MySQL use chaves gerenciadas pelo cliente armazenadas em Key Vault para criptografia do DEK, um administrador de Key Vault fornece os seguintes direitos de acesso ao servidor:

* **Get**: para recuperar a parte pública e as propriedades da chave no cofre de chaves.
* **wrapKey**: para poder criptografar o DEK.
* **unwrapKey**: para poder descriptografar o DEK.

O administrador do cofre de chaves também pode [habilitar o registro em log de eventos de auditoria de Key Vault](../azure-monitor/insights/azure-key-vault.md), para que eles possam ser auditados mais tarde.

Quando o servidor é configurado para usar a chave gerenciada pelo cliente armazenada no cofre de chaves, o servidor envia o DEK para o cofre de chaves para criptografias. Key Vault retorna o DEK criptografado, que é armazenado no banco de dados do usuário. Da mesma forma, quando necessário, o servidor envia o DEK protegido para o cofre de chaves para descriptografia. Os auditores podem usar Azure Monitor para examinar Key Vault logs de eventos de auditoria, se o registro em log estiver habilitado.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Requisitos para a configuração da criptografia de dados para o Azure Database para MySQL

Veja a seguir os requisitos para configurar o Key Vault:

* Key Vault e o banco de dados do Azure para MySQL devem pertencer ao mesmo locatário do Azure Active Directory (AD do Azure). Não há suporte para interações de servidor e Key Vault entre locatários. A movimentação de recursos posteriormente exige que você reconfigure a criptografia de dados.
* Você deve habilitar o recurso de exclusão reversível no cofre de chaves para proteger contra perda de dados se ocorrer uma exclusão de chave acidental (ou Key Vault). Os recursos excluídos por software são mantidos por 90 dias, a menos que o usuário os recupere ou os limpe enquanto isso. As ações de recuperação e limpeza têm suas próprias permissões associadas em uma política de acesso Key Vault. O recurso de exclusão reversível está desativado por padrão, mas você pode habilitá-lo por meio do PowerShell ou do CLI do Azure (Observe que você não pode habilitá-lo por meio do portal do Azure).
* Conceda ao banco de dados do Azure para MySQL acesso ao cofre de chaves com as permissões Get, wrapKey e unwrapKey usando sua identidade gerenciada exclusiva. No portal do Azure, a identidade exclusiva é criada automaticamente quando a criptografia de dados é habilitada no MySQL. Consulte [Configurar a criptografia de dados para MySQL](howto-data-encryption-portal.md) para obter instruções passo a passo detalhadas quando você estiver usando o portal do Azure.

* Quando você estiver usando um firewall com Key Vault, deverá habilitar a opção **permitir que os serviços confiáveis da Microsoft ignorem o firewall**.

Veja a seguir os requisitos para configurar a chave gerenciada pelo cliente:

* A chave gerenciada pelo cliente a ser usada para criptografar o DEK só pode ser assimétrica, RSA 2028.
* A data de ativação da chave (se definido) deve ser uma data e uma hora no passado. A data de validade (se definido) deve ser uma data e hora futura.
* A chave deve estar no estado *habilitado* .
* Se você estiver importando uma chave existente para o cofre de chaves, certifique-se de fornecê-la nos`.pfx`formatos `.byok`de `.backup`arquivo com suporte (,,).

## <a name="recommendations"></a>Recomendações

Quando você estiver usando a criptografia de dados usando uma chave gerenciada pelo cliente, aqui estão as recomendações para configurar Key Vault:

* Defina um bloqueio de recurso em Key Vault para controlar quem pode excluir esse recurso crítico e impedir a exclusão acidental ou não autorizada.
* Habilite a auditoria e os relatórios em todas as chaves de criptografia. O Key Vault fornece logs que são fáceis de injetar em outras informações de segurança e ferramentas de gerenciamento de eventos. Azure Monitor Log Analytics é um exemplo de um serviço que já está integrado.

* Verifique se Key Vault e o banco de dados do Azure para MySQL residem na mesma região, para garantir um acesso mais rápido para operações de encapsulamento DEK e desencapsulamento.

Aqui estão recomendações para configurar uma chave gerenciada pelo cliente:

* Mantenha uma cópia da chave gerenciada pelo cliente em um local seguro ou a caução para o serviço de caução.

* Se Key Vault gerar a chave, crie um backup de chave antes de usar a chave pela primeira vez. Você só pode restaurar o backup para Key Vault. Para obter mais informações sobre o comando backup, consulte [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey).

## <a name="inaccessible-customer-managed-key-condition"></a>Condição de chave gerenciada pelo cliente inacessível

Quando você configura a criptografia de dados com uma chave gerenciada pelo cliente no Key Vault, o acesso contínuo a essa chave é necessário para que o servidor permaneça online. Se o servidor perder o acesso à chave gerenciada pelo cliente no Key Vault, o servidor começará a negar todas as conexões em 10 minutos. O servidor emite uma mensagem de erro correspondente e altera o estado do servidor para *inacessível*. A única ação permitida em um banco de dados nesse estado é excluí-lo.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Revogação de acesso à chave acidental de Key Vault

Pode acontecer que alguém com direitos de acesso suficientes para Key Vault desabilita acidentalmente o acesso do servidor à chave por:

* Revogar as permissões Get, wrapKey e unwrapKey do cofre de chaves do servidor.
* Excluindo a chave.
* Excluindo o cofre de chaves.
* Alterando as regras de firewall do cofre de chaves.

* Excluindo a identidade gerenciada do servidor no Azure AD.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Monitorar a chave gerenciada pelo cliente no Key Vault

Para monitorar o estado do banco de dados e habilitar o alerta para a perda de acesso ao protetor de Transparent Data Encryption, configure os seguintes recursos do Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md): um banco de dados inacessível que perdeu o acesso à chave do cliente é mostrado como "inacessível" após a negação da primeira conexão com o banco de dados.
* [Log de atividades](../service-health/alerts-activity-log-service-notifications.md): quando o acesso à chave do cliente no Key Vault gerenciado pelo cliente falha, as entradas são adicionadas ao log de atividades. Você pode restabelecer o acesso assim que possível, se você criar alertas para esses eventos.

* [Grupos de ação](../azure-monitor/platform/action-groups.md): defina-os para enviar notificações e alertas com base em suas preferências.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Restaurar e replicar com uma chave gerenciada do cliente no Key Vault

Depois que o banco de dados do Azure para MySQL é criptografado com a chave gerenciada de um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é criptografada. Você pode fazer essa nova cópia por meio de uma operação local ou de restauração geográfica ou por meio de réplicas de leitura. No entanto, a cópia pode ser alterada para refletir a chave gerenciada de um novo cliente para criptografia. Quando a chave gerenciada pelo cliente é alterada, os backups antigos do servidor começam a usar a chave mais recente.

Para evitar problemas durante a configuração da criptografia de dados gerenciada pelo cliente durante a restauração ou a criação da réplica de leitura, é importante seguir estas etapas nos servidores mestre e restaurado/de réplica:

* Inicie o processo de restauração ou leitura de réplica do banco de dados mestre do Azure para MySQL.
* Mantenha o servidor recém-criado (restaurado/replicado) em um estado inacessível, porque sua identidade exclusiva ainda não recebeu permissões para Key Vault.
* No servidor restaurado/de réplica, revalide a chave gerenciada pelo cliente nas configurações de criptografia de dados. Isso garante que o servidor recém-criado receba permissões de encapsulamento e desencapsulamento para a chave armazenada em Key Vault.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar a criptografia de dados com uma chave gerenciada pelo cliente do banco de dados do Azure para MySQL usando o portal do Azure](howto-data-encryption-portal.md).
