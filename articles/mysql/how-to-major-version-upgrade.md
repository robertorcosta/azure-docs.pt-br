---
title: Atualização de versão principal no banco de dados do Azure para MySQL-servidor único
description: Este artigo descreve como você pode atualizar a versão principal para o banco de dados do Azure para MySQL-servidor único
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630193"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Atualização de versão principal no banco de dados do Azure para MySQL servidor único

> [!IMPORTANT]
> A atualização de versão principal para o banco de dados do Azure para MySQL servidor único está em visualização pública.

Este artigo descreve como você pode atualizar sua versão principal do MySQL in-loco no banco de dados do Azure para MySQL servidor único.

Esse recurso permitirá que os clientes realizem atualizações in-loco de seus servidores MySQL 5,6 para o MySQL 5,7 com um clique no botão sem nenhuma movimentação de dados ou a necessidade de qualquer alteração na cadeia de conexão do aplicativo.

> [!Note]
> * A atualização de versão principal só está disponível para atualização de versão principal do MySQL 5,6 para MySQL 5,7<br>
> * A atualização da versão principal ainda não tem suporte no servidor de réplica.
> * O servidor não estará disponível durante a operação de atualização. Portanto, é recomendável realizar atualizações durante a janela de manutenção planejada.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Executar a atualização da versão principal do MySQL 5,6 para o MySQL 5,7 usando portal do Azure

Siga estas etapas para executar a atualização de versão principal para seu banco de dados do Azure do servidor MySQL 5,6 usando portal do Azure

> [!IMPORTANT]
> É recomendável executar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Consulte [como executar uma restauração pontual](howto-restore-server-portal.md#point-in-time-restore).

1. Na [portal do Azure](https://portal.azure.com/), selecione o servidor de banco de dados do Azure para MySQL 5,6 existente.

2. Na página **visão geral** , clique no botão **Atualizar** na barra de ferramentas.

3. Na seção **atualização** , selecione **OK** para atualizar o servidor do banco de dados do Azure para MySQL 5,6 para o servidor 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Banco de dados do Azure para MySQL-visão geral-atualização":::

4. Uma notificação confirmará que a atualização foi bem-sucedida.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Executar a atualização da versão principal do MySQL 5,6 para o MySQL 5,7 usando CLI do Azure

Siga estas etapas para executar a atualização de versão principal para seu banco de dados do Azure do servidor MySQL 5,6 usando CLI do Azure

> [!IMPORTANT]
> É recomendável executar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Consulte [como executar uma restauração pontual](howto-restore-server-cli.md#server-point-in-time-restore).

1. Instale o [CLI do Azure para Windows](/cli/azure/install-azure-cli) ou use CLI do Azure no [Azure cloud Shell](../cloud-shell/overview.md) para executar os comandos de atualização. 
 
   Essa atualização requer a versão 2.16.0 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. Execute az version para localizar a versão e as bibliotecas dependentes que estão instaladas. Para fazer a atualização para a versão mais recente, execute az upgrade.

2. Depois de entrar, execute o comando [AZ MySQL Server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) :
    
   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   O prompt de comando mostra a mensagem "-Running". Depois que essa mensagem não for mais exibida, a atualização da versão será concluída.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Quando esse recurso de atualização será GA, pois temos o MySQL v 5.6 em nosso ambiente de produção que precisamos atualizar?

A GA desse recurso é planejada antes da desativação do MySQL v 5.6. No entanto, o recurso está pronto para produção e totalmente suportado pelo Azure, portanto, você deve executá-lo com confiança em seu ambiente. Como prática recomendada, sugerimos fortemente que você execute e teste-o primeiro em uma cópia restaurada do servidor para que você possa estimar o tempo de inatividade durante a atualização e executar o teste de compatibilidade de aplicativos antes de executá-lo na produção. Para obter mais informações, consulte [como executar a restauração pontual](howto-restore-server-portal.md#point-in-time-restore) para criar uma cópia pontual do seu servidor. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Isso causará tempo de inatividade do servidor e, nesse caso, quanto tempo?

Sim, o servidor não estará disponível durante o processo de atualização, portanto, recomendamos que você execute essa operação durante a janela de manutenção planejada. O tempo de inatividade estimado depende do tamanho do banco de dados, do tamanho do armazenamento provisionado (IOPs provisionado) e do número de tabelas no banco de dados. O tempo de atualização é diretamente proporcional ao número de tabelas no servidor. Espera-se que as atualizações dos servidores SKU básicos demorem mais tempo como na plataforma de armazenamento padrão. Para estimar o tempo de inatividade do seu ambiente de servidor, é recomendável primeiro executar a atualização na cópia restaurada do servidor.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Observe que ainda não há suporte para ele no servidor de réplica. O que isso significa concreto?

Atualmente, a atualização da versão principal não tem suporte para o servidor de réplica, o que significa que você não deve executá-lo para servidores envolvidos na replicação (ou seja, o servidor de origem ou de réplica). Se você quiser testar a atualização dos servidores envolvidos na replicação antes de adicionarmos o suporte de réplica para o recurso de atualização, recomendamos as seguintes etapas:

1. Durante a manutenção planejada, [pare a replicação e exclua o servidor de réplica](howto-read-replicas-portal.md) depois de capturar seu nome e todas as informações de configuração (configurações de firewall, configuração de parâmetro de servidor se ele for diferente do servidor de origem).
2. Execute a atualização do servidor de origem.
3. Provisione um novo servidor de réplica de leitura com o mesmo nome e definições de configuração capturadas na etapa 1. O novo servidor de réplica estará no v 5.7 automaticamente depois que o servidor de origem for atualizado para o v 5.7.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>O que acontecerá se não optarmos por atualizar nosso servidor MySQL v 5.6 antes de 5 de fevereiro de 2021?

Você ainda pode continuar executando o servidor MySQL v 5.6 como antes. O Azure **nunca** executará a atualização forçada no servidor. No entanto, as restrições documentadas na [política de versão do banco de dados do Azure para MySQL](concepts-version-policy.md) serão aplicadas.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [a política de controle de versão do banco de dados do Azure para MySQL](concepts-version-policy.md).
