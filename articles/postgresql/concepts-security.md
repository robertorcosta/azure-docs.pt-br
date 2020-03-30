---
title: Segurança no banco de dados Do Zure para PostgreSQL - Servidor Único
description: Uma visão geral dos recursos de segurança no Banco de Dados Azure para PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972597"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Segurança no banco de dados Do Zure para PostgreSQL - Servidor Único

Existem várias camadas de segurança disponíveis para proteger os dados do banco de dados Do Azure para o servidor PostgreSQL. Este artigo descreve essas opções de segurança.

## <a name="information-protection-and-encryption"></a>Proteção e criptografia de informações

### <a name="in-transit"></a>Em trânsito
O Banco de Dados Do Azure para PostgreSQL protege seus dados criptografando dados em trânsito com a Security Transport Layer. A criptografia (SSL/TLS) é aplicada por padrão.

### <a name="at-rest"></a>Em repouso
O serviço Banco de Dados do Azure para PostgreSQL usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados em disco, com exceção de arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure e as chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.


## <a name="network-security"></a>Segurança de rede
As conexões a um banco de dados Azure para servidor PostgreSQL são primeiramente roteadas através de um gateway regional. O gateway tem um IP acessível ao público, enquanto os endereços IP do servidor estão protegidos. Para obter mais informações sobre o gateway, visite o [artigo de arquitetura de conectividade](concepts-connectivity-architecture.md).  

Um banco de dados Azure recém-criado para servidor PostgreSQL tem um firewall que bloqueia todas as conexões externas. Embora eles cheguem ao gateway, eles não têm permissão para se conectar ao servidor. 

### <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP concedem acesso a servidores com base no endereço IP de origem de cada solicitação. Consulte a visão geral das [regras do firewall](concepts-firewall-rules.md) para obter mais informações.

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual
Os pontos finais de serviço de rede virtual ampliam sua conectividade de rede virtual sobre o backbone do Azure. Usando regras de rede virtuais, você pode habilitar seu banco de dados Azure para servidor PostgreSQL para permitir conexões de sub-redes selecionadas em uma rede virtual. Para obter mais informações, consulte a visão geral do [ponto final do serviço](concepts-data-access-and-security-vnet.md)de rede virtual .

### <a name="private-ip"></a>IP Privado
O Private Link permite que você se conecte ao seu banco de dados Azure para servidor PostgreSQL Single no Azure através de um ponto final privado. O Azure Private Link essencialmente traz os serviços do Azure dentro da sua Rede Virtual privada (VNet). Os recursos PaaS podem ser acessados usando o endereço IP privado, assim como qualquer outro recurso no VNet. Para obter mais informações, consulte a visão geral do [link privado](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Gerenciamento de acesso

Ao criar o banco de dados Azure para servidor PostgreSQL, você fornece credenciais para uma função de administrador. Essa função de administrador pode ser usada para criar [funções adicionais do PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html).

Você também pode se conectar ao servidor usando a [autenticação AaD (AAD) do Azure Active Directory ( AAD).](concepts-aad-authentication.md)


## <a name="threat-protection"></a>Proteção contra ameaças

Você pode optar pelo [Advanced Threat Protection,](concepts-data-access-and-security-threat-protection.md) que detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar servidores.

[O registro de auditoria](concepts-audit.md) está disponível para rastrear a atividade em seus bancos de dados. 


## <a name="next-steps"></a>Próximas etapas
- Habilite regras de firewall para [IPs](concepts-firewall-rules.md) ou [redes virtuais](concepts-data-access-and-security-vnet.md)
- Saiba mais sobre [a autenticação do Azure Active Directory](concepts-aad-authentication.md) no Banco de Dados Azure para PostgreSQL
