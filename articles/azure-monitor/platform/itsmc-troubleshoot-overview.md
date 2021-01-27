---
title: Solução de problemas no Conector ITSM
description: Solucionando problemas no Conector de Gerenciamento de Serviços de TI
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: fce20626d5e000c08b8a057671c06a3084534187
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896029"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Solução de problemas no Conector ITSM

Este artigo aborda problemas comuns no Conector ITSM e como solucioná-los.

Azure Monitor Alertas proativamente notificam você quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os percebam.
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

## <a name="common-symptoms---how-should-it-be-resolved"></a>Sintomas comuns-como eles devem ser resolvidos?

A lista abaixo contém sintomas comuns e como ele deve ser resolvido:

* **Sintoma**: se uma conexão não conseguir se conectar ao sistema ITSM, você receberá um **erro ao salvar a mensagem de conexão** .

    **Causa**: a causa pode ser uma das opções:
    * Credenciais incorretas
     * Privilégios insuficientes
     * O aplicativo Web deve ser implantado corretamente

    **Resolução**:
    * Para as conexões ServiceNow, Cherwell e Provance:
        * Certifique-se de que você inseriu corretamente o nome de usuário, a senha, a ID do cliente e o segredo do cliente para cada uma das conexões.  
        * Para o ServiceNow: Verifique se você tem privilégios suficientes no produto de ITSM correspondente para fazer a conexão, conforme [especificado](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).
  * Para conexões Service Manager:  
      * Verifique se o aplicativo Web foi implantado com êxito e se a conexão híbrida foi criada. Para verificar se a conexão foi estabelecida com êxito com o computador Service Manager local, vá para a URL do aplicativo Web, conforme descrito na documentação para fazer a [conexão híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  
* **Sintoma**: itens de trabalho duplicados são criados

    **Causa**: a causa pode ser uma das duas opções:
    * Mais de uma ação de ITSM está definida para o alerta.
    * O alerta foi resolvido.

    **Resolução**: pode haver duas soluções:
    * Verifique se você tem um único grupo de ações de ITSM por alerta.
    * Conector ITSM não dá suporte à atualização de status de itens de trabalho de correspondência quando um alerta é resolvido. Um novo item de trabalho resolvido é criado.
* **Sintoma**: os itens de trabalho não são criados

    **Causa**: pode haver alguns motivos para esse sintoma:
    * Modificação de código no lado do ServiceNow
    * Configurações incorretas de permissões
    * Os limites de taxa do ServiceNow são muito altos/baixos
    * O token de atualização expirou
    * Conector ITSM foi excluído

    **Resolução**: você pode verificar o [painel](itsmc-dashboard.md) e examinar os erros na seção status do conector. Examine os [erros comuns](itsmc-dashboard-errors.md) e descubra como resolver o erro.

* **Sintoma**: não é possível criar a ação de ITSM para o grupo de ações

    **Causa**: a conector ITSM recém-criada ainda não concluiu a sincronização inicial.

    **Resolução**: você pode examinar os [erros comuns da interface do usuário](itsmc-dashboard-errors.md#ui-common-errors) e descobrir como resolver o erro.