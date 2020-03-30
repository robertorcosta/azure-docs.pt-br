---
title: Segurança - Banco de Dados Azure para MariaDB
description: Uma visão geral dos recursos de segurança no Banco de Dados Do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527819"
---
# <a name="security-in-azure-database-for-mariadb"></a>Segurança no Banco de Dados do Azure para MariaDB

Existem várias camadas de segurança disponíveis para proteger os dados do seu banco de dados Azure para o servidor MariaDB. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção e criptografia de informações

### <a name="in-transit"></a>Em trânsito
O Banco de Dados Do Azure para MariaDB protege seus dados criptografando dados em trânsito com a Security Transport Layer. A criptografia (SSL/TLS) é aplicada por padrão.

### <a name="at-rest"></a>Em repouso
O serviço Banco de Dados do Azure para MariaDB usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados em disco, com exceção de arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.


## <a name="network-security"></a>Segurança de rede
As conexões a um banco de dados Azure para servidor MariaDB são primeiramente roteadas através de um gateway regional. O gateway tem um IP acessível ao público, enquanto os endereços IP do servidor estão protegidos. Para obter mais informações sobre o gateway, visite o [artigo de arquitetura de conectividade](concepts-connectivity-architecture.md).  

Um banco de dados Azure recém-criado para servidor MariaDB tem um firewall que bloqueia todas as conexões externas. Embora eles cheguem ao gateway, eles não têm permissão para se conectar ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP de origem de cada solicitação. Consulte a visão geral das [regras do firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos finais de serviço de rede virtual ampliam sua conectividade de rede virtual sobre o backbone do Azure. Usando regras de rede virtuais, você pode habilitar seu banco de dados Azure para servidor MariaDB para permitir conexões de sub-redes selecionadas em uma rede virtual. Para obter mais informações, consulte a visão geral do [ponto final do serviço](concepts-data-access-security-vnet.md)de rede virtual .


## <a name="access-management"></a>Gerenciamento de acesso

Ao criar o Banco de Dados Azure para o servidor MariaDB, você fornece credenciais para um usuário administrador. Este administrador pode ser usado para criar usuários adicionais do MariaDB.


## <a name="threat-protection"></a>Proteção contra ameaças

Você pode optar pelo [Advanced Threat Protection,](concepts-data-access-and-security-threat-protection.md) que detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores.

[O registro de auditoria](concepts-audit-logs.md) está disponível para rastrear a atividade em seus bancos de dados. 


## <a name="next-steps"></a>Próximas etapas
- Habilite regras de firewall para [IPs](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-security-vnet.md)