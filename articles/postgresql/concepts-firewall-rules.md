---
title: Regras de firewall - Banco de dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como usar as regras de firewall para se conectar ao Banco de Dados Do Azure para PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157263"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Regras de firewall no Banco de Dados Azure para PostgreSQL - Servidor Único
O banco de dados Do Zure para firewall do servidor PostgreSQL impede todo o acesso ao servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.
Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

**Regras de firewall:** essas regras permitem que os clientes acessem todo o Banco de Dados do Azure para servidor PostgreSQL, ou seja, todos os bancos dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas por meio do Portal do Azure ou usando comandos da CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Visão geral do firewall
Todo acesso de banco de dados ao seu Banco de Dados do Azure para servidor PostgreSQL é bloqueado por padrão pelo firewall. Para começar a usar o servidor de outro computador, especifique uma ou mais regras de firewall no nível do servidor para permitir o acesso ao seu servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso em si ao site do Portal do Azure não é afetado pelas regras de firewall.
As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Banco de Dados PostgreSQL, conforme exibido no diagrama a seguir:

![Fluxo de exemplo de como funciona o firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Conectando pela Internet
As regras de firewall no nível do servidor se aplicam a todos os bancos de dados no mesmo servidor do Banco de Dados do Azure para PostgreSQL. Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, a conexão será concedida.
Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em uma das regras de firewall no nível do servidor, a solicitação de conexão falhará.
Por exemplo, se o seu aplicativo se conectar ao driver JDBC para PostgreSQL, você poderá encontrar esse erro ao tentar se conectar quando o firewall estiver bloqueando a conexão.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Conexão pelo Azure
Recomenda-se que você encontre o endereço IP de saída de qualquer aplicativo ou serviço e permita explicitamente o acesso a esses endereços OU intervalos IP individuais. Por exemplo, você pode encontrar o endereço IP de saída de um Serviço de Aplicativo Do Azure ou usar um IP público vinculado a uma máquina virtual ou outro recurso (veja abaixo informações sobre como se conectar com o IP privado de uma máquina virtual sobre os pontos finais de serviço). 

Se um endereço IP de saída fixa não estiver disponível para o serviço Do Azure, você poderá considerar a habilitação de conexões de todos os endereços IP do data center do Azure. Essa configuração pode ser ativada a partir do portal Azure, definindo a opção **Permitir acesso aos serviços do Azure** para **ON** a partir do painel de **segurança Conexão** e bater **em Salvar**. A partir do Azure CLI, uma configuração de regra de firewall com endereço de partida e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor do Banco de Dados do Azure para PostgreSQL.

> [!IMPORTANT]
> A opção **Permitir acesso aos serviços do Azure** configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

![Configurar Permitir o acesso aos serviços do Azure](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Conectando-se a partir de um VNet
Para se conectar com segurança ao seu banco de dados Azure para servidor PostgreSQL a partir de um VNet, considere usar [os pontos finais do serviço VNet](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gerenciando programaticamente as regras de firewall
Além do Portal do Azure, as regras de firewall podem ser gerenciadas por meio de programação usando a CLI do Azure.
Confira também [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Problemas de firewall para solução de problemas
Considere os seguintes pontos quando o acesso ao Banco de Dados do Microsoft Azure para o serviço de servidor PostgreSQL não se comportar conforme o esperado:

* **As alterações à lista de permissões ainda não entraram em vigor:** pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados do Azure para servidor PostgreSQL entrem em vigor.

* **O logon não está autorizado ou uma senha incorreta foi usada:** se um logon não tiver permissões no servidor do Banco de Dados do Azure para servidor PostgreSQL, ou se a senha usada estiver incorreta, a conexão com o Banco de Dados do Azure para servidor PostgreSQL será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente ainda deverá fornecer as credenciais de segurança necessárias.

   Por exemplo, usando um cliente JDBC, o seguinte erro pode aparecer.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: falha na autenticação da senha para o usuário "seunomedeusuário"

* **Endereço IP dinâmico:** se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:

   * Peça ao seu Provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores clientes que acessarão o servidor de Banco de Dados do Azure para servidor PostgreSQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

   * Obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP estáticos como uma regra de firewall.

* **O IP do servidor parece ser público:** As conexões com o banco de dados Azure para servidor PostgreSQL são roteadas através de um gateway Azure acessível ao público. No entanto, o IP do servidor real é protegido pelo firewall. Para obter mais informações, visite o [artigo de arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Próximas etapas
Para ver artigos sobre como criar regras de firewall no nível de servidor e de banco de dados, consulte:
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-firewall-using-cli.md)
- [Pontos finais do serviço VNet no Banco de Dados Azure para PostgreSQL](./concepts-data-access-and-security-vnet.md)
