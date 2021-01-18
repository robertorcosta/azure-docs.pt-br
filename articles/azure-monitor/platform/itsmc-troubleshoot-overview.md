---
title: Solução de problemas no Conector ITSM
description: Solucionando problemas no Conector de Gerenciamento de Serviços de TI
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 14f1056bf761eb7b591d04db34610468058bc255
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562837"
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

- Se Log Analytics alertas forem acionados, mas os itens de trabalho não forem criados no produto de ITSM, se os itens de configuração não forem criados/vinculados a itens de trabalho ou para outras informações, consulte estes recursos:
   -  ITSMC: a solução mostra um resumo de conexões, itens de trabalho, computadores e muito mais. Selecione o bloco que tem o rótulo **status do conector** . Isso o levará para a **pesquisa de logs** com a consulta relevante. Examine os registros de log com um `LogType_S` de `ERROR` para obter mais informações.
   - Página **pesquisa de logs** : exiba os erros e as informações relacionadas diretamente usando a consulta `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Solucionar problemas Service Manager implantação de aplicativo Web

-   Se você tiver problemas com a implantação do aplicativo Web, verifique se você tem permissões para criar/implantar recursos na assinatura.
-   Se você receber uma **referência de objeto não definida como uma instância de um erro de objeto** ao executar o [script](itsmc-service-manager-script.md), certifique-se de que você inseriu valores válidos na seção **configuração do usuário** .
-   Se você não conseguir criar o namespace de retransmissão do barramento de serviço, verifique se o provedor de recursos necessário está registrado na assinatura. Se não estiver registrado, crie manualmente o namespace de retransmissão do barramento de serviço do portal do Azure. Você também pode criá-lo ao [criar a conexão híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection) no portal do Azure.