---
title: Tutorial sobre como devolver o Microsoft Azure Data Box Disk | Microsoft Docs
description: Usar este tutorial para aprender como enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: eb9231a84295240c20e34bfad56f406317c107da
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845489"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Devolução do Azure Data Box Disk 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Tutorial: Devolução do Azure Data Box Disk 

Este tutorial descreve como agendar uma retirada para devolução do Azure Data Box Disk. As instruções de retirada dependem da localização em que o dispositivo é devolvido. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Retirada do Data Box Disk em regiões diferentes

## <a name="prerequisites"></a>Prerequisites

Antes de começar, verifique se você concluiu o [Tutorial: copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Devolver o Data Box Disk

::: zone-end

1. Após a conclusão da validação de dados, desconecte os discos. Remova os cabos de conexão.
2. Encapsule todos os discos e os cabos de conexão com plástico bolha e coloque-os na caixa de remessa. Poderá haver encargos se os acessórios estiverem ausentes.
    - Reutilize a embalagem da remessa inicial.  
    - É recomendável que empacotar os discos usando um plástico bolha bem ajustado.
    - Verifique se que o ajuste é firme para reduzir qualquer movimentos dentro da caixa.

As próximas etapas são determinadas pelo local em você está devolvendo o dispositivo. As instruções são diferentes para EUA/Canadá, UE (União Europeia), Austrália ou países da Ásia.

### <a name="in-us-or-canadatabin-us-or-canada"></a>[Nos EUA ou no Canadá](#tab/in-us-or-canada)

Execute as seguintes etapas se estiver devolvendo o dispositivo nos EUA ou Canadá.

1. Use a etiqueta de remessa de devolução localizada na capa de plástico transparente que está afixada à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Acesse **Visão Geral > Baixar etiqueta de remessa** e baixe uma etiqueta de remessa de devolução.
    - Afixe o rótulo ao dispositivo.

2. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.
3. Agende uma retirada com a UPS. Para agendar uma retirada:

    - Ligue para a UPS local (linha gratuita específica do país/região).
    - Em sua chamada, mencione a remessa inversa, conforme mostrado na etiqueta impressa de número de controle.
    - Se o número de controle não está entre aspas, o serviço de no-break exigirá que você pague um encargo adicional durante a retirada.
    - Em vez de agendar a retirada, você também pode descartar o Data Box Disk no local mais próximo de redistribuição.

### <a name="in-europetabin-europe"></a>[Na Europa](#tab/in-europe)

Execute as seguintes etapas se estiver devolvendo o dispositivo na Europa.

1. Use a etiqueta de remessa de devolução localizada na capa de plástico transparente que está afixada à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Acesse **Visão Geral > Baixar etiqueta de remessa** e baixe uma etiqueta de remessa de devolução.
    - Afixe o rótulo ao dispositivo.

2. Lacre a caixa de envio e verifique se a etiqueta de remessa de devolução está visível.
3. Se você estiver devolvendo o dispositivo na Europa com a DHL, solicite a retirada da DHL visitando o site e especificando o número da carta de porte aéreo.
4. Acesse o site da DHL Express do país/região e escolha **Agendar a recolha pelo serviço de correio > Remessa de eReturn**.    
3. Especifique o número da carta de porte e clique em **Agendar retirada** para organizar a retirada.

### <a name="in-australiatabin-australia"></a>[Na Austrália](#tab/in-australia)

Datacenters do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as seguintes etapas para retirada na Austrália.

1. Use a etiqueta de remessa de devolução fornecida e verifique se o código TAU (número de referência) está escrito nela. Caso não tenha mais a etiqueta de remessa fornecida ou tenha qualquer outro problema, envie um email para [Operações do Data Box na Ásia](mailto:adbo@microsoft.com). Forneça o nome do pedido no assunto do cabeçalho e os detalhes do problema que você está enfrentando.
3. Afixe a etiqueta à caixa. 
4. Agende online no link https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference uma retirada. 

### <a name="in-japantabin-japan"></a>[No Japão](#tab/in-japan)

1. Escreva as informações de nome e endereço da sua empresa na nota de consignação como suas informações de remetente.
2. Envie um email para a Quantium Solutions usando o modelo de email a seguir.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **Se estiver retirando em Osaka**, modifique o assunto no modelo de email para: `Pickup request for Microsoft Azure OSA`.
    - Se a nota de consignação do Japan Post Chakubarai não tiver sido incluída ou estiver ausente, indique isso nesse email. A Quantium Solutions Japan solicitará ao Japan Post que traga a nota de consignação após a retirada.
    - Caso você tenha vários pedidos, envie um email para garantir a retirada individual.

3. Receba um email de confirmação da Quantium Solutions depois de agendar uma retirada. O email de confirmação também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você poderá contatar o Suporte da Quantium Solutions (em japonês) com as seguintes informações: 

- Email：Customerservice.JP@quantiumsolutions.com 
- Telefone：03-5755-0150 

### <a name="in-koreatabin-korea"></a>[Na Coreia do Sul](#tab/in-korea)

1. Inclua a nota de consignação de devolução.
2. Para solicitar a retirada quando houver nota de consignação:
    1. Ligue para a linha direta da *Quantium Solutions International* em 070-8231 1418 durante o horário de expediente (das 10h às 17h, de segunda a sexta-feira). Mencione *Retirada do Microsoft Azure* e o número da solicitação de serviço para providenciar uma retirada.  
    2. Se a linha direta estiver ocupada, envie um email para `microsoft@rocketparcel.com` com o assunto *Retirada do Microsoft Azure* e o número da solicitação de serviço como referência.
    3. Se a transportadora não chegar para a coleta, ligue para a linha direta da *Quantium Solutions International* para providências alternativas. 
    4. Você receberá um email de confirmação para o agendamento da retirada.
3. Siga esta etapa somente se não houver nota de consignação. Para solicitar retirada:
    1. Ligue para a linha direta da *Quantium Solutions International* em 070-8231 1418 durante o horário de expediente (das 10h às 17h, de segunda a sexta-feira). Mencione *Retirada do Microsoft Azure* e o número da solicitação de serviço para providenciar uma retirada. Especifique que você precisa de uma nova nota de consignação para providenciar uma retirada. Forneça o remetente (cliente), as informações de destinatário (datacenter do Azure) e o número de referência (número da solicitação de serviço). 
    2. Se a linha direta estiver ocupada, envie um email para `microsoft@rocketparcel.com` com o assunto *Retirada do Microsoft Azure* e o número da solicitação de serviço como referência.
    3. Se a transportadora não chegar para a coleta, ligue para a linha direta da *Quantium Solutions International* para providências alternativas. 
    4. Você recebe uma confirmação verbal se a solicitação for feita por telefone.


### <a name="in-singaporetabin-singapore"></a>[Em Singapura](#tab/in-singapore)

1. Imprima a etiqueta de remessa e anexe-a à caixa. Se o rótulo estiver danificado ou tiver sido perdido:
    - Acesse **Visão Geral > Baixar etiqueta de remessa** e obtenha uma etiqueta de remessa de devolução.
    - Afixe o rótulo ao dispositivo. Certifique-se que a etiqueta está visível.

2. Para solicitar a retirada, envie um email para o Atendimento ao Cliente do SingPost usando o modelo a seguir com o número de controle (esse número pode ser encontrado no rótulo de retorno fornecido no pacote entregue).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - XZ00001234567 
    Body: 
     a. Requestor name
     b. Requestor contact number
     c. Requestor collection address
     d. Preferred collection date
    ```

   > [!NOTE]
   > Para solicitações de reserva recebidas em um dia útil:
   > - Antes das 15:00, a retirada ocorrerá no dia útil a seguir, entre 9:00 e 13:00.
   > - Após às 15:00, a retirada ocorrerá no dia útil a seguir, entre 14:00 e 18:00.

   Se tiver problemas, entre em contato com as Operações do Data Box na Ásia pelo email adbo@microsoft.com. Forneça o nome do trabalho no cabeçalho de assunto e o problema encontrado.

3. Passe para a transportadora.

### <a name="in-self-managedtabin-selfmanaged"></a>[Remessa autogerenciada](#tab/in-selfmanaged)

Se estiver usando o Data Box Disk no Japão, em Singapura, na Coreia do Sul e no Oeste da Europa e tiver selecionado a opção de remessa autogerenciada durante a criação do pedido, siga estas instruções. 

1. Vá para a folha **Visão geral** de seu pedido no portal do Azure. Examine as instruções que são exibidas ao selecionar **Agendar retirada**. Você deverá ver um código de Autorização, que será usado no momento de entrega do pedido.

2. Envie um email para a equipe de Operações do Azure Data Box usando o modelo a seguir quando estiver pronto para devolver o dispositivo.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: ‘orderName’
    Body: 
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
3. A equipe de Operações do Azure Data Box trabalhará com você para organizar a devolução para o Datacenter do Azure.

::: zone target="docs"

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Retirada do Data Box Disk em regiões diferentes

Avance para as próximas instruções para saber como verificar o upload de dados do Data Box Disk para a conta do Armazenamento do Azure.

> [!div class="nextstepaction"]
> [Verificar o upload de dados do Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end




