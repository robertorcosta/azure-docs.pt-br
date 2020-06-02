---
title: Tutorial para pedido do Azure Data Box | Microsoft Docs
description: Conheça os pré-requisitos de implantação e como solicitar um Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: bc21ba73ef7e8f5879af2b15787449315f36a3f8
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745316"
---
# <a name="tutorial-order-azure-data-box"></a>Tutorial: Solicitar o Azure Data Box

O Azure Data Box é uma solução híbrida que permite importar dados do local no Azure de maneira rápida, fácil e confiável. Você transfere os dados para um dispositivo de armazenamento de 80 TB fornecido pela Microsoft (capacidade utilizável) e, depois, devolve o dispositivo. Após, esses dados são carregados no Azure.

Este tutorial descreve como você pode solicitar um Azure Data Box. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos para implantar o Data Box
> * Solicitar um Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

## <a name="prerequisites"></a>Pré-requisitos

Preencha os seguintes pré-requisitos de configuração para o serviço e dispositivo do Data Box antes de implantar o dispositivo.

### <a name="for-service"></a>Para o serviço

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Para dispositivo

Antes de começar, verifique se:

* Você deve ter um computador host conectado à rede do datacenter. O Data Box copiará os dados deste computador. Seu computador host deve executar um sistema operacional com suporte, conforme descrito em [Requisitos de sistema do Azure Data Box](data-box-system-requirements.md).
* Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de GbE 10 não estiver disponível, um link de dados de 1 GbE poderá ser usado, mas as velocidades de cópia serão afetadas.

## <a name="order-data-box"></a>Solicitar Data Box

Execute as etapas a seguir no portal do Azure para solicitar um dispositivo.

1. Use suas credenciais do Microsoft Azure para entrar nesta URL: [https://portal.azure.com](https://portal.azure.com).
2. Clique em **+ Criar um recurso** e pesquise *Azure Data Box*. Clique em **Azure Data Box**.

   [![Pesquisar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Clique em **Criar**.

4. Verifique se o serviço Data Box está disponível em sua região. Insira ou selecione as seguintes informações e clique em **Aplicar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Subscription     | Selecione uma assinatura do EA, do CSP ou do Azure Sponsorship para o serviço Data Box. <br> A assinatura está vinculada à sua conta de cobrança.       |
    |Tipo de transferência     | Selecione **Importar no Azure**.        |
    |País/região de origem    |    Selecione o país/região em que os dados residem no momento.         |
    |Região do Azure de destino     |     Selecione a região do Azure para onde você deseja transferir dados.        |

5. Selecione **Data Box**. A capacidade máxima utilizável de um único pedido é de 80 TB. É possível criar vários pedidos para tamanhos maiores de dados.

      [![Selecione Data Box opção 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. No **Pedido**, especifique os **Detalhes do pedido**. Insira ou selecione as seguintes informações e clique em **Avançar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Nome     |  Forneça um nome amigável para acompanhar o pedido. <br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número.      |
    |Resource group     |    Use um grupo existente ou crie um novo. <br> Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos.         |
    |Região do Azure de destino     | Selecione uma região para sua conta de armazenamento. <br> Para saber mais, acesse [disponibilidade por região](data-box-overview.md#region-availability).        |
    |Destino de armazenamento     | Escolha entre conta de armazenamento, discos gerenciados ou ambos. <br> Com base na região especificada do Azure, selecione uma ou mais contas de armazenamento existente na lista filtrada de uma conta de armazenamento existente. O Data Box pode ser vinculada a até 10 contas de armazenamento. <br> Você também pode criar uma nova conta de **Uso geral v1**, **Uso geral v2** ou de **Armazenamento de Blobs**. <br>Contas de armazenamento com redes virtuais são compatíveis. Para permitir que o serviço do Data Box trabalhe com contas de armazenamento protegido, habilite os serviços confiáveis em definições de firewall de rede da conta de armazenamento. Para obter mais informações, confira como [Adicionar o Azure Data Box como um serviço confiável](../storage/common/storage-network-security.md#exceptions).|

    Se usar a conta de armazenamento como o destino de armazenamento, você verá a seguinte captura de tela:

    ![Ordem do Data Box para a conta de armazenamento](media/data-box-deploy-ordered/order-storage-account.png)

    Se usar o Data Box para criar discos gerenciados dos VHDs locais, você também precisará fornecer as seguintes informações:

    |Configuração  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Se você pretende criar discos gerenciados de VHDs locais, crie novos grupos de recursos. Ao criar um pedido do Data Box para o disco gerenciado pelo serviço Data Box, você poderá usar um grupo de recursos somente se o grupo de recursos tiver sido criado anteriormente. <br> Especifique vários grupos de recursos separados por ponto e vírgula. Há suporte para um limite máximo de dez grupos de recursos.|

    ![Ordem do Data Box para o disco gerenciado](media/data-box-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para os discos gerenciados é usada como uma conta de armazenamento de preparo. O serviço do Data Box carrega os VHDs como blobs de páginas na conta de armazenamento de preparo antes de convertê-los em discos gerenciados e movê-los para os grupos de recursos. Para obter mais informações, confira [Verificar o upload de dados para o Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. No **Endereço para remessa**, forneça seu nome e sobrenome, nome e endereço da empresa e um número de telefone válido. Clique em **Validar endereço**. O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito.

   Após o pedido ser feito com êxito, se a remessa autogerenciada tiver sido selecionado, você receberá uma notificação por email. Para saber mais sobre a remessa autogerenciada, confira [Usar a remessa autogerenciada](data-box-portal-customer-managed-shipping.md).

8. Clique em **Avançar** após os detalhes da remessa tiverem sido validados com êxito.

9. Nos **Detalhes da notificação**, especifique endereços de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados.

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

10. Examine o **Resumo** de informações relacionadas com o pedido, o contato, a notificação e os termos de privacidade. Marque a caixa correspondente ao contrato de termos de privacidade.

11. Clique em **Pedido**. A criação do pedido demora alguns minutos.

## <a name="track-the-order"></a>Acompanhar o pedido

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal do Azure. Acesse seu pedido do Data Box e, em seguida, acesse **Visão Geral** para exibir o status. O portal mostra o pedido sob o estado **Pedido**.

Se o dispositivo não estiver disponível, você receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identificará o dispositivo para envio e o preparará. Durante a preparação do dispositivo, ocorrerão as seguintes ações:

* Os compartilhamentos SMB são criados para cada conta de armazenamento associada ao dispositivo.
* Para cada compartilhamento, as credenciais de acesso, como nome de usuário e senha, são geradas.
* Também é gerada uma senha do dispositivo que ajuda a desbloqueá-lo.
* O Data Box é bloqueado para impedir o acesso não autorizado ao dispositivo.

Após a conclusão da preparação do dispositivo, o portal mostra o pedido no estado **Processado**.

![O pedido do Data Box é processado](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft preparará e enviará seu dispositivo por meio de uma operadora regional. Após o envio do dispositivo, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.

![Pedido do Data Box expedido](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Cancelar o pedido

Para cancelar esse pedido, no portal do Azure, vá para **Visão geral** e clique em **Cancelar** na barra de comandos.

Depois de fazer um pedido, você poderá cancelá-lo a qualquer momento antes do status ser marcado como processado.

Para excluir um pedido cancelado, vá para **Visão geral** e clique em **Excluir** na barra de comandos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos para implantar o Data Box
> * Solicitar Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

Avance para o próximo tutorial para saber como proteger seu Data Box.

> [!div class="nextstepaction"]
> [Configurar seu Azure Data Box](./data-box-deploy-set-up.md)
