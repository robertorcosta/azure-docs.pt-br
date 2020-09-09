---
title: Tutorial para preparar o portal do Azure e o ambiente de datacenter para implantar a GPU do Azure Stack Edge | Microsoft Docs
description: O primeiro tutorial sobre como implantar a GPU do Azure Stack Edge envolve a preparação do portal do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c02af8983489651d8b5b83d5d3107a534c51f067
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254671"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-with-gpu"></a>Tutorial: Preparar a implantação do Azure Stack Edge com GPU 

Este é o primeiro tutorial da série de tutoriais de implantação necessários para implantar completamente o Azure Stack Edge com GPU. Este tutorial descreve como preparar o portal do Azure para implantar um recurso do Azure Stack Edge.

Você precisará de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal leva menos de 10 minutos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

### <a name="get-started"></a>Introdução

Para a implantação do Azure Stack Edge, você precisa primeiro preparar o ambiente. Quando o ambiente estiver pronto, siga as etapas exigidas e, se necessário, as etapas e os procedimentos opcionais para implantar totalmente o dispositivo. As instruções passo a passo de implantação indicam quando você deve executar cada uma das etapas necessárias e opcionais.

| Etapa | Descrição |
| --- | --- |
| **Preparação** |Essas etapas precisam ser concluídas na preparação para a próxima implantação. |
| **[Lista de verificação da configuração da implantação](#deployment-configuration-checklist)** |Use essa lista de verificação para coletar e registrar informações antes e durante a implantação. |
| **[Pré-requisitos de implantação](#prerequisites)** |Eles validam se o ambiente está pronto para implantação. |
|  | |
|**Tutoriais de implantação** |Esses tutoriais são necessários para implantar seu dispositivo do Azure Stack Edge em produção. |
|**[1. Preparar o portal do Azure para o Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)** |Crie e configure seu recurso do Azure Stack Edge antes de instalar um dispositivo físico do Azure Stack Edge. |
|**[2. Instalar o Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md)**|Desembalar, colocar em rack e conectar os cabos do dispositivo físico do Azure Stack Edge.  |
|**[3. Conectar-se ao Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md)** |Depois que o dispositivo for instalado, conecte-se à IU da Web local do dispositivo.  |
|**[4. Definir configurações de rede para o Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Configure a rede, incluindo as configurações de rede de computação e do proxy Web para seu dispositivo.   |
|**[5. Definir configurações do dispositivo para o Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Atribua um nome de dispositivo e domínio DNS, configure o servidor de atualização e a hora do dispositivo. |
|**[6. Definir configurações de segurança para o Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Configure certificados para seu dispositivo. Use certificados gerados pelo dispositivo ou traga seus certificados.   |
|**[7. Ativar o Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md)** |Use a chave de ativação do serviço para ativar o dispositivo. O dispositivo está pronto para configurar compartilhamentos NFS ou SMB ou conectar por meio de REST. |
|**[8. Configurar a computação](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configure a função de computação em seu dispositivo. Isso também criará um cluster Kubernetes. |
|**[9A. Transferir dados com compartilhamentos do Edge](azure-stack-edge-j-series-deploy-add-shares.md)** |Adicione compartilhamentos e conecte-se a ele por meio de SMB ou NFS. |
|**[9B. Transferir dados com contas de armazenamento do Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Adicione contas de armazenamento e conecte-se ao Armazenamento de Blobs por meio de APIs REST. |


Agora você pode começar a reunir informações sobre a configuração do software para seu dispositivo do Azure Stack Edge.

## <a name="deployment-configuration-checklist"></a>Lista de verificação da configuração da implantação

Antes de implantar o dispositivo, você precisará reunir as informações para configurar o software em seu dispositivo do Azure Stack Edge. Preparar essas informações antecipadamente ajudará a simplificar o processo de implantação do dispositivo no seu ambiente. Use a [lista de verificação de configuração de implantação do Azure Stack Edge](azure-stack-edge-gpu-deploy-checklist.md) para anotar os detalhes de configuração enquanto você implanta o dispositivo.


## <a name="prerequisites"></a>Pré-requisitos

A seguir estão os pré-requisitos de configuração para o recurso do Azure Stack Edge, o dispositivo Azure Stack Edge e a rede do datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso do Azure Stack Edge

Antes de começar, verifique se:

- Sua assinatura do Microsoft Azure está habilitada para um recurso do Azure Stack Edge. Use uma assinatura compatível, como [EA (Contrato Enterprise) da Microsoft](https://azure.microsoft.com/overview/sales-number/), [Programa do CSP (Provedor de Soluções na Nuvem)](https://docs.microsoft.com/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Não há suporte para as assinaturas pagas conforme o uso.
- Você tem acesso de proprietário ou colaborador no nível do grupo de recursos aos recursos do Azure Stack Edge/Data Box Gateway, do Hub IoT e do Armazenamento do Azure.

    - Para criar qualquer recurso do Azure Stack Edge/Data Box Gateway, você deverá ter permissões como um colaborador (ou superior) com escopo no nível do grupo de recursos. Você também precisará verificar se o provedor `Microsoft.DataBoxEdge` está registrado. Para obter informações sobre como realizar o registro, acesse [Registrar o provedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar qualquer recurso do Hub IoT, verifique se o provedor Microsoft.Devices está registrado. Para obter informações sobre como realizar o registro, acesse [Registrar o provedor de recursos](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Para criar um recurso da conta de armazenamento, novamente, você precisará ter acesso de colaborador ou superior com escopo no nível do grupo de recursos. O Armazenamento do Azure é, por padrão, um provedor de recursos registrado.
- Você tem acesso de usuário ou administrador à API do Azure Active Directory Graph. Para obter mais informações, confira [API do Azure Active Directory Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
- Você tem sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

### <a name="for-the-azure-stack-edge-device"></a>Para o dispositivo Azure Stack Edge

Antes de implantar um dispositivo físico, verifique se:

- Você examinou as informações de segurança que foram incluídas no pacote de remessa.
- Você tem um slot de 1 U disponível em um rack padrão de 19" no seu datacenter para montar o dispositivo em rack.
- Você tem acesso a uma superfície de trabalho plana, estável e nivelada na qual o dispositivo pode apoiar-se com segurança.
- O local em que você pretende instalar o dispositivo tem alimentação CA padrão de uma fonte independente ou uma PDU (unidade de distribuição de energia) no rack com um no-break.
- Você tem acesso a um dispositivo físico.


### <a name="for-the-datacenter-network"></a>Para a rede de datacenter

Antes de começar, verifique se:

- A rede no seu datacenter é configurada de acordo com os requisitos de rede do seu dispositivo Azure Stack Edge. Para obter mais informações, confira os requisitos do sistema do [Azure Stack Edge](azure-stack-edge-system-requirements.md).

- Para as condições normais de operação do Azure Stack Edge, você tem:

    - Uma largura de banda de download de, no mínimo, 10 Mbps para garantir que o dispositivo permaneça atualizado.
    - Uma largura de banda dedicada de download e upload de, no mínimo, 20 Mbps para transferir arquivos.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se você tiver um recurso do Azure Stack Edge existente para gerenciar seu dispositivo físico, ignore esta etapa e vá para [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso do Azure Stack Edge, execute as etapas a seguir no portal do Azure.

1. Use suas credenciais do Microsoft Azure para entrar no portal do Azure nesta URL: [https://portal.azure.com](https://portal.azure.com).

2. No painel esquerdo, selecione **+ Criar um recurso**. Procure e selecione **Azure Stack Edge/Data Box Gateway**. Selecione **Criar**. Se você encontrar problemas, acesse [Solucionar problemas de pedido](azure-stack-edge-troubleshoot-ordering.md).

3. Escolha a assinatura que deseja usar para o dispositivo Azure Stack Edge. Selecione o país para onde deseja enviar este dispositivo físico. Selecione **Mostrar dispositivos**.

    ![Criar um recurso 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Selecione o tipo de dispositivo. Em **Azure Stack Edge Commercial**, escolha **Azure Stack Edge com GPU** e selecione **Criar conta**. 

    ![Criar um recurso 2](media/azure-stack-edge-gpu-deploy-prep/create-resource-2.png)

5. É exibida uma forma curta. Preencha o formulário e selecione **Enviar**. A Microsoft habilitará sua assinatura.

    ![Criar um recurso 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Depois que a assinatura estiver habilitada, você poderá continuar com a criação do recurso. Na folha **Selecionar tipo de dispositivo**, escolha **Selecionar**.

    ![Criar um recurso 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)


6. Na guia **Informações Básicas**, insira ou selecione os **Detalhes do projeto** a seguir.
    
    |Configuração  |Valor  |
    |---------|---------|
    |Subscription    |Isso é preenchido automaticamente de acordo com a seleção anterior. A assinatura está vinculada à sua conta de faturamento. |
    |Resource group  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).     |

4. Insira ou selecione os **Detalhes da instância** a seguir.

    |Configuração  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 caracteres contendo letras, números e hifens.<br> Nome começa e termina com uma letra ou um número.        |
    |Região     |Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Se você estiver usando o Azure Governamental, todas as regiões do governo estarão disponíveis, conforme mostrado nas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Escolha um local mais próximo da região geográfica em que você deseja implantar seu dispositivo.|

    ![Criar um recurso 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


5. Selecione **Avançar: Endereço para entrega**.

    - Caso já tenha um dispositivo, marque a caixa de combinação **Tenho um dispositivo Azure Stack Edge**.

        ![Criar um recurso 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Se esse for o novo dispositivo solicitado, insira o nome de contato, a empresa, o endereço para entrega do dispositivo e informações de contato.

        ![Criar um recurso 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

6. Selecione **Avançar: Revisar + criar**.

7. Na guia **Examinar + criar**, examine os **Detalhes de preço**, os **Termos de uso** e os detalhes do recurso. Marque a caixa de combinação **Li os termos de privacidade**.

    ![Criar um recurso 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

8. Selecione **Criar**.

A criação do recurso leva alguns minutos. Depois que o recurso for criado com êxito e implantado, você será notificado. Selecione **Ir para o recurso**.

![Acesse o recurso do Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Depois que o pedido for feito, a Microsoft analisará o pedido e o contatará (por email) com os detalhes da entrega.

![Notificação para revisão do pedido do Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)

Se você tiver problemas durante o processo de pedido, confira [Solucionar problemas de pedidos](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois que o recurso do Azure Stack Edge estiver em execução, será necessário obter a chave de ativação. Essa chave é usada para ativar e conectar seu dispositivo Azure Stack Edge ao recurso. Você pode obter essa chave agora enquanto estiver no portal do Azure.

1. Selecione o recurso criado. Selecione **Visão geral** e, em seguida, **Configuração do dispositivo**.

    ![Selecionar Configuração do dispositivo](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. No bloco **Ativar**, selecione **Gerar chave** para criar uma chave de ativação. Selecione o ícone de copiar para copiar a chave e salvá-la para uso posterior.

    ![Obter a chave de ativação](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias depois de ser gerada.
> - Se a chave expirou, gere uma nova. A chave antiga não é válida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Stack Edge, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Prossiga para o próximo tutorial para saber como instalar o Azure Stack Edge.

> [!div class="nextstepaction"]
> [Instalar o Azure Stack Edge](./azure-stack-edge-gpu-deploy-install.md)



