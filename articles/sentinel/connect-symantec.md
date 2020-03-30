---
title: Conecte os dados do Symantec ICDx ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar os dados do Symantec ICDx ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588086"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Conecte seu aparelho Symantec ICDx 



O conector Symantec ICDx permite conectar facilmente todos os registros de soluções de segurança da Symantec com o Azure Sentinel, para visualizar dashboards, criar alertas personalizados e melhorar a investigação. Isso lhe dá mais informações sobre a rede da sua organização e melhora os recursos de operação de segurança. A integração entre o Symantec ICDx e o Azure Sentinel faz uso da API REST.


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que você está executando o Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configure e conecte o Symantec ICDx 

O Symantec ICDx pode integrar e exportar logs diretamente para o Azure Sentinel.

1. Abra o console de gerenciamento icdx para adicionar os reencaminhadores do Microsoft Azure Sentinel (Log Analytics).
2. Na barra de navegação do ICDx, clique **em Configuração**. 
3. Na parte superior da tela **Configuração,** clique **em Encaminhados**.
4. Em **Forwarders,** ao lado do Microsoft Azure Sentinel (Log Analytics), clique em **Adicionar**. 
4. Na janela **Microsoft Azure Sentinel (Log Analytics),** clique em **Mostrar Avançado**. 
5. No topo da janela expandida para o Microsoft Azure Sentinel (Log Analytics), faça o seguinte:
    -   **Nome**: Digite um nome para o encaminhador que não tenha mais de 30 caracteres. Escolha um nome único e significativo. Este nome aparece na lista de encaminhadores na tela **Configuração** e nos painéis na tela **do Painel.** Por exemplo: Microsoft Azure Log Analytics East. Esse campo é obrigatório.
    -   **Descrição**: Digite uma descrição para o encaminhador. Esta descrição também aparece na lista de encaminhadores na tela **Configuração.** Inclua detalhes como o tipo de evento que está sendo encaminhado e o grupo que precisa inspecionar os dados.
    -   **Tipo de inicialização**: Selecione o método de inicialização para a configuração do encaminhador. Suas opções são manuais e automáticas.<br>O padrão é Automático. 
6. Em **Eventos,** faça o seguinte: 
    - **Fonte**: Selecione um ou mais arquivos a partir dos quais encaminhar eventos. Você pode selecionar arquivos de coletores ativos (incluindo o Arquivo Comum), arquivos de coletores órfãos (ou seja, arquivos para os coletores que você excluiu), arquivos receptores ICDx ou o Arquivo do Sistema. <br>O padrão é O Arquivo Comum.
      > [!NOTE]
      > Os arquivos do receptor ICDx são listados separadamente, pelo nome. 
 
    - **Filtro**: Adicione um filtro que especifica o subconjunto de eventos para a frente. Realize um dos seguintes procedimentos:
        - Para selecionar uma condição de filtro, clique em um tipo, atributo, operador e valor. 
        - No campo Filtro, revise a condição do filtro. Você pode editá-lo diretamente no campo ou excluí-lo conforme necessário.
        - Clique em E ou OU para adicionar à sua condição de filtro.
        - Você também pode clicar em Consultas salvas para aplicar uma consulta salva.
    - **Atributos incluídos**: Digite a lista de atributos delimitados por vírgula a incluir nos dados encaminhados. Os atributos incluídos têm precedência sobre atributos excluídos.
    - **Atributos excluídos**: Digite a lista de atributos delimitados por vírgula para excluir dos dados encaminhados.
    - **Tamanho do lote**: Selecione o número de eventos a serem enviados por lote. Suas opções são 10, 50, 100, 500 e 1000.<br>O padrão é 100. 
    - **Limite de taxa**: Selecione a taxa na qual os eventos são encaminhados, expressos em eventos por segundo. Suas opções são Ilimitadas, 500, 1000, 5000, 10000. <br> O padrão é 5000. 
7. Em **Destino Azure,** faça o seguinte: 
    - **ID do espaço**de trabalho : Cole o ID do espaço de trabalho a partir de baixo. Esse campo é obrigatório.
    - **Tecla primária**: Cole a tecla principal abaixo. Esse campo é obrigatório.
    - **Nome de log personalizado**: Digite o nome de log personalizado no espaço de trabalho log analytics do portal Microsoft Azure para o qual você está indo para a frente de eventos. O padrão é SymantecICDx. Esse campo é obrigatório.
8. Clique *em Salvar* para concluir a configuração do encaminhador. 
9. Para iniciar o forwarder, em **Opções,** clique **em Mais** e, em seguida, **Iniciar**.
10. Para usar o esquema relevante no Log Analytics para os eventos symantec ICDx, procure **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar mais de 20 minutos até que seus registros comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Symantec ICDx ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.


