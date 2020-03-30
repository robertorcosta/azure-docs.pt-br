---
title: Conexões de solução de problemas - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Saiba como solucionar problemas de conexão no Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)
keywords: conexão do postgresql, cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977498"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Solucionando problemas de conexão no Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)

Problemas de conexão podem ser causados por várias coisas, tais como:

* Configurações de firewall
* Tempo limite da conexão
* Sinal incorreto nas informações
* Limite de conexão alcançado para o grupo de servidores
* Problemas com a infraestrutura do serviço
* Manutenção de serviços
* O nó coordenador falhando em novo hardware

Geralmente, os problemas de conexão com a Hyperscale podem ser classificados da seguinte forma:

* Erros transitórios (de curta duração ou intermitentes)
* Erros persistentes ou não transitórios (erros regularmente recorrentes)

## <a name="troubleshoot-transient-errors"></a>Solucionar problemas de erros transitórios

Erros transitórios ocorrem por uma série de razões. As mais comuns incluem manutenção do sistema, erro com hardware ou software e upgrades de nó coordenador vCore.

Permitir alta disponibilidade para nódulos de grupo de servidor Hyperscale pode mitigar esses tipos de problemas automaticamente. No entanto, sua aplicação ainda deve estar preparada para perder sua conexão brevemente. Além disso, outros eventos podem levar mais tempo para mitigar, como quando uma grande transação causa uma recuperação de longa duração.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade temporários

1. Verifique no Painel de Serviços do [Microsoft Azure](https://azure.microsoft.com/status) se há paralisações conhecidas que ocorreram durante o período em que o aplicativo estava relatando erros.
2. Aplicativos que se conectam a um serviço de nuvem como o Hyperscale (Citus) devem esperar erros transitórios e reagir graciosamente. Por exemplo, os aplicativos devem implementar a lógica de repetição para lidar com esses erros em vez de superá-los como erros de aplicativos para os usuários.
3. À medida que o grupo de servidores se aproxima de seus limites de recursos, erros podem parecer problemas de conectividade transitórios. Aumentar a RAM do nó ou adicionar nódulos de trabalhadores e reequilibrar dados pode ajudar.
4. Se os problemas de conectividade continuarem ou durarem mais de 60 segundos ou acontecerem mais de uma vez por dia, faça uma solicitação de suporte ao Azure selecionando **Obter suporte** no site de suporte do [Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Solucionar erros persistentes

Se o aplicativo não se conectar persistentemente ao Hyperscale (Citus), as causas mais comuns são a configuração errada do firewall ou o erro do usuário.

* Configuração de firewall nó coordenador: Certifique-se de que o firewall do servidor Hyperscale esteja configurado para permitir conexões do seu cliente, incluindo servidores proxy e gateways.
* Configuração de firewall do cliente: O firewall em seu cliente deve permitir conexões ao servidor de banco de dados. Alguns firewalls exigem permitir não apenas o aplicativo pelo nome, mas permitir que os endereços IP e portas do servidor.
* Erro do usuário: Verifique duas vezes a seqüência de conexão. Você pode ter parâmetros mal digitados, como o nome do servidor. Você pode encontrar strings de conexão para várias estruturas de idioma e psql no portal Azure. Vá para a página **Conexão strings** no grupo de servidor Hyperscale (Citus). Também tenha em mente que os clusters Hyperscale (Citus) têm apenas um banco de dados e seu nome predefinido é **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Etapas para resolver os problemas de conectividade temporários

1. Configure [regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para permitir o endereço IP do cliente. Para fins de testes temporários, configure uma regra de firewall usando 0.0.0.0 como o endereço IP inicial e usando 255.255.255.255 como o endereço IP final. Essa regra abre o servidor para todos os endereços IP. Se a regra resolver seu problema de conectividade, remova-o e crie uma regra de firewall para um endereço IP ou faixa de endereço apropriadamente limitado.
2. Em todos os firewalls entre o cliente e a internet, certifique-se de que a porta 5432 esteja aberta para conexões de saída.
3. Verifique a cadeia de conexão e outras configurações de conexão.
4. Verifique a integridade do serviço no painel.

## <a name="next-steps"></a>Próximas etapas

* Aprenda os conceitos de regras de Firewall no Banco de [Dados Azure para PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md)
* Veja como [gerenciar regras de firewall para o Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)](howto-hyperscale-manage-firewall-using-portal.md)
