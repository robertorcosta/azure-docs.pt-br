---
title: Tutorial sobre como devolver o Azure Data Box na ordem de exportação | Microsoft Docs
description: Saiba como enviar o Azure Data Box para a Microsoft após a conclusão da ordem de exportação
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7023d29bcb559f4edf11b374b9bfb959e968f626
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208734"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Tutorial: Devolução do Azure Data Box Disk (versão prévia)


Este tutorial descreve como retornar o Azure Data Box e os dados são apagados depois que o dispositivo é recebido nos dados do Azure.

Neste tutorial, você aprenderá sobre tópicos como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Eliminar dados do Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

- Você concluiu o [Tutorial: Copiar dados do Azure Data Box](data-box-deploy-export-copy-data.md). 
- Trabalhos de cópia estão concluídos. A Preparação para o envio não poderá ser executada se os trabalhos de cópia estiverem em andamento.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

As próximas etapas são determinadas pelo local em você está devolvendo o dispositivo.

## <a name="ship-data-box-back"></a>Devolver o Data Box

Verifique se a cópia de dados do dispositivo foi concluída e se a execução de **Preparar para o envio** foi bem-sucedida. De acordo com a região para a qual você está enviando o dispositivo, o procedimento é diferente.

## <a name="in-us-canada-europe"></a>[Nos EUA, no Canadá e na Europa](#tab/in-us-canada-europe)

Execute as etapas a seguir se estiver devolvendo o dispositivo nos EUA, no Canadá ou na Europa.

1. Verifique se o dispositivo está desligado e se os cabos foram removidos. 
2. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte posterior do dispositivo.
3. Certifique-se de que a etiqueta de remessa esteja aparecendo no papel eletrônico e agende uma retirada com a operadora. Se a etiqueta for danificada ou perdida ou não for exibida na tela do E-ink, contate o Suporte da Microsoft. Se o Suporte sugerir, acesse **Visão Geral > Baixar etiqueta de remessa** no portal do Azure. Baixe a etiqueta de remessa e afixe-a ao dispositivo. 
4. Agende uma retirada com a UPS se estiver devolvendo o dispositivo. Para agendar uma retirada:

    - Ligue para a UPS local (linha gratuita específica do país/região).
    - Em sua chamada, mencione a remessa inversa, conforme mostrado na exibição E-ink ou sua etiqueta impressa de número de controle.
    - Se o número de controle não está entre aspas, o serviço de no-break exigirá que você pague um encargo adicional durante a retirada.

    Em vez de agendar a retirada, você também pode descartar o Data Box no local mais próximo de redistribuição.
4. Após a coleta e registro do Data Box por sua operadora, o status do pedido no portal será atualizado para **Recolhido**. Uma ID de rastreamento também é exibida.


## <a name="in-australia"></a>[Na Austrália](#tab/in-australia)

Datacenters do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as etapas a seguir para o envio na Austrália.


1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Verifique se a cópia de dados no dispositivo foi concluída e se a execução de **Preparação para o envio** foi bem-sucedida.
3. Desligue o dispositivo e remova os cabos.
4. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
5. Agendar uma coleta online no [Link da DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="in-japan"></a>[No Japão](#tab/in-japan) 

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Desligue o dispositivo e remova os cabos.
3. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo.
4. Escreva as informações de nome e endereço da sua empresa na nota de consignação como suas informações de remetente.
5. Envie um email para a Quantium Solutions usando o modelo de email a seguir.

    - Se a nota de consignação do Japan Post Chakubarai não tiver sido incluída ou estiver ausente, indique isso nesse email. A Quantium Solutions Japan solicitará ao Japan Post que traga a nota de consignação após a retirada.
    - Caso você tenha vários pedidos, envie um email para garantir a retirada individual.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. Receba um email de confirmação da Quantium Solutions depois de agendar uma retirada. O email de confirmação também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você poderá contatar o Suporte da Quantium Solutions (em japonês) com as seguintes informações: 

- Email：Customerservice.JP@quantiumsolutions.com 
- Telefone：03-5755-0150 


## <a name="in-singapore"></a>[Em Singapura](#tab/in-singapore) 

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de devolução.
2. Anote o número de acompanhamento (exibido como um número de referência na página Preparar para o Envio da interface de usuário da Web local do Data Box). Isso estará disponível depois que a etapa preparar para o envio for concluída. Baixe a etiqueta de remessa desta página e cole na caixa de embalagem. 
3. Desligue o dispositivo e remova os cabos.
4. Enrole e coloque o cabo de alimentação fornecido com o dispositivo com segurança na parte traseira do dispositivo. 
5. Envie um email para o Serviço de atendimento ao consumidor da SingPost usando este modelo de email com o número de acompanhamento.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Para solicitações de reserva recebidas em um dia útil:
   > - Antes das 15:00, a retirada ocorrerá no dia útil a seguir, entre 9:00 e 13:00.
   > - Após às 15:00, a retirada ocorrerá no dia útil a seguir, entre 14:00 e 18:00.  


## <a name="self-managed"></a>[Autogerenciado](#tab/in-selfmanaged) 

Se estiver usando o Data Box no Japão, em Singapura, na Coreia do Sul e no Oeste da Europa e tiver selecionado a opção de remessa autogerenciada durante a criação do pedido, siga estas instruções. 

1. Após a conclusão dessa etapa, anote o código de Autorização mostrado na página Preparar para o Envio da IU da Web local do Data Box.
2. Desligue o dispositivo e remova os cabos. Enrole o cabo de alimentação fornecido com o dispositivo e coloque-o com segurança na parte traseira do dispositivo.
3. Envie um email para a equipe de Operações do Azure Data Box usando o modelo abaixo quando estiver pronto para devolver o dispositivo.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
---

## <a name="erasure-of-data-from-data-box"></a>Eliminar dados do Data Box
 
Após o dispositivo chegar ao datacenter do Azure, o Data Box apaga os dados nos respectivos discos de acordo com as [diretrizes do NIST SP 800-88 Revisão 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para o envio
> * Enviar o Data Box para a Microsoft
> * Eliminar dados do Data Box

Avance para o próximo artigo para saber como gerenciar seu Data Box.

> [!div class="nextstepaction"]
> [Gerenciar Data Box via portal do Azure](./data-box-portal-admin.md)


