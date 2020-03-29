---
title: Regras de firewall - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Este artigo descreve as regras de firewall para o Banco de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975560"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Regras de firewall no Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)
O banco de dados Azure para firewall do servidor PostgreSQL impede todo o acesso ao nó de coordenador do Hyperscale (Citus) até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.
Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

**Regras do firewall:** Essas regras permitem que os clientes acessem seu nó coordenador de Hyperscale (Citus), ou seja, todos os bancos de dados dentro do mesmo servidor lógico. As regras de firewall em nível de servidor podem ser configuradas usando o portal Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Visão geral do firewall
Todo o acesso do banco de dados ao nó do coordenador é bloqueado pelo firewall por padrão. Para começar a usar o servidor de outro computador, especifique uma ou mais regras de firewall no nível do servidor para permitir o acesso ao seu servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso em si ao site do Portal do Azure não é afetado pelas regras de firewall.
As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Banco de Dados PostgreSQL, conforme exibido no diagrama a seguir:

![Fluxo de exemplo de como funciona o firewall](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Conectando-se da Internet e do Azure

Um firewall de grupo de servidor Hyperscale (Citus) que pode se conectar ao nó coordenador do grupo. O firewall determina o acesso consultando uma lista configurável de regras. Cada regra é um endereço IP, ou intervalo de endereços, que são permitidos dentro.

Quando o firewall bloqueia conexões, pode causar erros de aplicativo. O uso do driver PostgreSQL JDBC, por exemplo, levanta um erro como este:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entrada para o host "123.45.67.890", usuário "citus", banco de dados "citus", SSL

Consulte [Criar e gerenciar regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para saber como as regras são definidas.

## <a name="troubleshooting-the-database-server-firewall"></a>Solução de problemas do firewall do servidor de banco de dados
Quando o acesso ao banco de dados do Microsoft Azure para o serviço PostgreSQL - Hyperscale (Citus) não se comportar como você espera, considere esses pontos:

* **As alterações na lista de desporto ainda não entraram em vigor:** Pode haver até um atraso de cinco minutos para que as alterações na configuração de firewall Hyperscale (Citus) entrem em vigor.

* **O usuário não está autorizado ou uma senha incorreta foi usada:** Se um usuário não tiver permissões no servidor ou a senha usada estiver incorreta, a conexão com o servidor será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente ainda deverá fornecer as credenciais de segurança necessárias.

Por exemplo, usando um cliente JDBC, o seguinte erro pode aparecer.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: falha na autenticação da senha para o usuário "seunomedeusuário"

* **Endereço IP dinâmico:** se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:

* Solicite ao provedor de serviços da Internet (ISP) a faixa de endereçoIP atribuída aos computadores clientes que acessam o nó de coordenador do Hyperscale (Citus) e, em seguida, adicione o intervalo de endereçoIP como regra de firewall.

* Obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP estáticos como uma regra de firewall.

## <a name="next-steps"></a>Próximas etapas
Para ver artigos sobre como criar regras de firewall no nível de servidor e de banco de dados, consulte:
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-hyperscale-manage-firewall-using-portal.md)
