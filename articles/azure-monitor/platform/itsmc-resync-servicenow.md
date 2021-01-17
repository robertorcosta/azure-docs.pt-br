---
title: Como corrigir manualmente problemas de sincronização do ServiceNow
description: Redefina a conexão com o ServiceNow para que os alertas no Microsoft Azure possam chamar o ServiceNow novamente
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2f0889732ac756e16465659ee2e5b52335d1148f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539261"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Solução de problemas no Conector ITSM

Este artigo aborda problemas comuns no Conector ITSM e como solucioná-los.

Azure Monitor Alertas proativamente notificam você quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam. Para obter mais informações sobre alertas, consulte Visão geral de alertas no Microsoft Azure.
O cliente pode selecionar como deseja ser notificado sobre o alerta se ele é por email, SMS, webhook ou até mesmo para automatizar uma solução. Outra opção a ser notificada é usar o ITSM.
O ITSM oferece a opção de enviar os alertas para o sistema de tíquetes externos, como o ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizar e analisar os dados de incidente e solicitação de alteração

Dependendo de sua configuração quando você configurar uma conexão, o ITSMC poderá sincronizar até 120 dias de incidentes e dados de solicitação de alteração. O esquema de registro de log para esses dados é fornecido na [seção informações adicionais](./itsmc-synced-data.md) deste artigo.

Você pode visualizar o incidente e alterar os dados de solicitação usando o painel do ITSMC:

![Captura de tela que mostra o painel do ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

O painel também fornece informações sobre o status do conector, que você pode usar como ponto de partida para analisar problemas com as conexões.

Para obter mais informações sobre a investigação do painel, consulte [investigação de erro usando o painel](./itsmc-dashboard.md).

### <a name="service-map"></a>Mapa do serviço

Você também pode visualizar os incidentes sincronizados em relação aos computadores afetados no Mapa do Serviço.

O Mapa do Serviço descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Ele permite que você exiba seus servidores da maneira como você os considera: como sistemas interconectados que fornecem serviços críticos. Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP. Além da instalação de um agente, nenhuma configuração é necessária. Para obter mais informações, consulte [usando mapa do serviço](../insights/service-map.md).

Se você estiver usando Mapa do Serviço, poderá exibir os itens da central de serviços criados em soluções de ITSM, conforme mostrado aqui:

![Captura de tela que mostra a exibição de Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>Solução de problemas de conexões de ITSM

- Se uma conexão falhar ao se conectar ao sistema ITSM e você receber um **erro ao salvar** a mensagem de conexão, execute as seguintes etapas:
   - Para as conexões ServiceNow, Cherwell e Provance:  
     - Certifique-se de que você inseriu corretamente o nome de usuário, a senha, a ID do cliente e o segredo do cliente para cada uma das conexões.  
     - Verifique se você tem privilégios suficientes no produto de ITSM correspondente para fazer a conexão.  
   - Para conexões Service Manager:  
     - Verifique se o aplicativo Web foi implantado com êxito e se a conexão híbrida foi criada. Para verificar se a conexão foi estabelecida com êxito com o computador Service Manager local, vá para a URL do aplicativo Web, conforme descrito na documentação para fazer a [conexão híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

- Se os dados do ServiceNow não estiverem sendo sincronizados com o Log Analytics, verifique se a instância do ServiceNow não está em suspensão. As instâncias de desenvolvimento do ServiceNow às vezes entram em suspensão quando estão ociosas por muito tempo. Se isso não for o que está acontecendo, relate o problema.
- Se Log Analytics alertas forem acionados, mas os itens de trabalho não forem criados no produto de ITSM, se os itens de configuração não forem criados/vinculados a itens de trabalho ou para outras informações, consulte estes recursos:
   -  ITSMC: a solução mostra um resumo de conexões, itens de trabalho, computadores e muito mais. Selecione o bloco que tem o rótulo **status do conector** . Isso o levará para a **pesquisa de logs** com a consulta relevante. Examine os registros de log com um `LogType_S` de `ERROR` para obter mais informações.
   - Página **pesquisa de logs** : exiba os erros e as informações relacionadas diretamente usando a consulta `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas Service Manager implantação de aplicativo Web

-   Se você tiver problemas com a implantação do aplicativo Web, verifique se você tem permissões para criar/implantar recursos na assinatura.
-   Se você receber uma **referência de objeto não definida como uma instância de um erro de objeto** ao executar o [script](itsmc-service-manager-script.md), certifique-se de que você inseriu valores válidos na seção **configuração do usuário** .
-   Se você não conseguir criar o namespace de retransmissão do barramento de serviço, verifique se o provedor de recursos necessário está registrado na assinatura. Se não estiver registrado, crie manualmente o namespace de retransmissão do barramento de serviço do portal do Azure. Você também pode criá-lo ao [criar a conexão híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection) no portal do Azure.

### <a name="how-to-manually-fix-sync-problems"></a>Como corrigir manualmente problemas de sincronização

Azure Monitor pode se conectar a provedores de gerenciamento de serviços de ti (ITSM) de terceiros. O ServiceNow é um desses provedores.

Por motivos de segurança, talvez seja necessário atualizar o token de autenticação usado para sua conexão com o ServiceNow.
Use o seguinte processo de sincronização para reativar a conexão e atualizar o token:


1. Pesquise a solução na faixa de pesquisa superior e, em seguida, selecione as soluções relevantes

    ![Captura de tela que mostra a faixa de pesquisa superior e onde selecionar as soluções relevantes.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na tela de solução, escolha "selecionar tudo" no filtro de assinatura e, em seguida, filtre por "assistência técnica"

    ![Captura de tela que mostra onde escolher Selecionar tudo e onde filtrar pela assistência técnica.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecione a solução de sua conexão de ITSM.
1. Selecione conexão de ITSM na faixa à esquerda.

    ![Captura de tela que mostra onde selecionar as conexões de ITSM.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecione cada conector na lista. 
    1. Clique no nome do conector para configurá-lo
    1. Excluir todos os conectores que não estão mais em uso

    1. Atualizar os campos de acordo com [essas definições](./itsmc-connections.md) com base em seu tipo de parceiro

    1. Clique em sincronizar

       ![Captura de tela que realça o botão Sincronizar.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Clique em salvar

        ![Nova conexão](media/itsmc-resync-servicenow/save-8bit.png)

f.    Examine as notificações para ver se o processo foi iniciado.
