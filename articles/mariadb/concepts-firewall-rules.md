---
title: Regras de firewall-banco de dados do Azure para MariaDB
description: Saiba mais sobre como usar regras de firewall para habilitar conexões com o banco de dados do Azure para o servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 743e3f50d747993250399493d97fc2becab19319
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79532035"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Regras de firewall de servidor do Banco de Dados do Azure para MariaDB
Os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.

Para configurar seu firewall, crie regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

**Regras de firewall:** estas regras permitem que os clientes acessem todo o servidor de Banco de Dados do Azure para MariaDB, ou seja, todos os bancos dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas por meio do Portal do Azure ou dos comandos da CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Visão geral do firewall
Todo acesso de banco de dados ao seu servidor de Banco de Dados do Azure para MariaDB é bloqueado por padrão pelo firewall. Para começar a usar o servidor de outro computador, especifique uma ou mais regras de firewall no nível do servidor para permitir o acesso ao seu servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso em si ao site do Portal do Azure não é afetado pelas regras de firewall.

As tentativas de conexão da Internet e do Azure devem passar primeiramente pelo firewall antes de poderem acessar seu Banco de Dados do Azure para MariaDB, conforme exibido no diagrama a seguir:

![Fluxo de exemplo de como funciona o firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Conectando pela Internet
Regras de firewall de nível de servidor se aplicam a todos os bancos de dados no servidor de Banco de Dados do Azure para MariaDB.

Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível do servidor, a conexão será concedida.

Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em uma das regras de firewall no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

## <a name="connecting-from-azure"></a>Conexão pelo Azure
É recomendável que você localize o endereço IP de saída de qualquer aplicativo ou serviço e permita explicitamente o acesso a esses endereços IP ou intervalos individuais. Por exemplo, você pode encontrar o endereço IP de saída de um serviço de Azure App ou usar um IP público vinculado a uma máquina virtual ou outro recurso (consulte abaixo para obter informações sobre como se conectar com o IP privado de uma máquina virtual nos pontos de extremidade de serviço). 

Se um endereço IP de saída fixo não estiver disponível para seu serviço do Azure, você poderá considerar a possibilidade de habilitar conexões de todos os endereços IP do datacenter do Azure. Essa configuração pode ser habilitada no portal do Azure definindo a opção **permitir acesso aos serviços do Azure** como **ativado** no painel **segurança de conexão** e ao pressionar **salvar**. No CLI do Azure, uma configuração de regra de firewall com endereço inicial e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor de Banco de Dados do Azure para MariaDB.

> [!IMPORTANT]
> A opção **permitir acesso aos serviços do Azure** configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.
> 

![Configurar Permitir o acesso aos serviços do Azure](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Conectando de uma VNet
Para se conectar com segurança ao banco de dados do Azure para MariaDB Server de uma VNet, considere o uso de [pontos de extremidade de serviço de VNet](./concepts-data-access-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gerenciando programaticamente as regras de firewall
Além do Portal do Azure, as regras de firewall podem ser gerenciadas por meio de programação usando a CLI do Azure. 

Consulte também [criar e gerenciar as regras de firewall do banco de dados do Azure para MariaDB usando CLI do Azure](./howto-manage-firewall-cli.md).

## <a name="troubleshooting-firewall-issues"></a>Solucionando problemas de firewall
Considere os seguintes pontos quando o acesso ao serviço do servidor de Banco de Dados do Microsoft Azure para MariaDB não se comportar conforme o esperado:

* **As alterações à lista de permissões ainda não entraram em vigor:** pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do servidor de Banco de Dados do Azure para MariaDB entrem em vigor.

* **O logon não está autorizado ou uma senha incorreta foi usada:** se um logon não tiver permissões no servidor de Banco de Dados do Azure para MariaDB, ou se a senha usada estiver incorreta, a conexão com o servidor de Banco de Dados do Azure para MariaDB será negada. Criar uma configuração de firewall apenas oferece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente deve fornecer as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver com dificuldades para atravessar o firewall, tente uma das seguintes soluções:

   * Peça ao seu ISP (Provedor de serviços de Internet) o intervalo de endereços IP atribuído aos computadores cliente que acessarão o servidor de Banco de Dados do Azure para MariaDB e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

   * Obtenha o endereçamento IP estático para os computadores cliente e, em seguida, adicione os endereços IP como regras de firewall.

* O **IP do servidor parece ser público:** As conexões com o banco de dados do Azure para o servidor MariaDB são roteadas por meio de um gateway do Azure acessível publicamente. No entanto, o IP do servidor real é protegido pelo firewall. Para obter mais informações, consulte o [artigo sobre arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Próximas etapas
- [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando o portal do Azure](./howto-manage-firewall-portal.md)
- [Criar e gerenciar regras de firewall do Banco de Dados do Azure para MariaDB usando a CLI do Azure](./howto-manage-firewall-cli.md)
- [Pontos de extremidade de serviço de VNet no banco de dados do Azure para MariaDB](./concepts-data-access-security-vnet.md)
