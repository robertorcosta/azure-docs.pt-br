---
title: Recuperar de uma falha de dispositivo do Azure Stack Edge pro
description: Descreve como recuperar de um dispositivo com falha do Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: ef99ff874376e0d76e45e4b0b2c36a78d938130b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843731"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Recuperar de um dispositivo de GPU pro de Azure Stack de borda com falha 

Este artigo descreve como se recuperar de uma falha não tolerável em seu dispositivo de GPU pro Azure Stack Edge. Uma falha não tolerável no dispositivo de GPU pro Azure Stack Edge requer uma substituição de dispositivo.

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que você tenha:

- Suporte da Microsoft contatado em relação à falha do dispositivo e eles recomendavam a substituição do dispositivo. 
- Backup da configuração do dispositivo, conforme descrito em [preparar para uma falha do dispositivo](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Configurar o dispositivo de substituição

Quando o dispositivo encontra uma falha não tolerável, você precisa solicitar um dispositivo de substituição. As etapas de configuração para o dispositivo de substituição permanecem as mesmas. 

Recupere as informações de configuração do dispositivo cujo backup foi feito do dispositivo que falhou. Você usará essas informações para configurar o dispositivo de substituição.  

Siga estas etapas para configurar o dispositivo de substituição:

1. Reúna as informações necessárias na [lista de verificação de implantação](azure-stack-edge-gpu-deploy-checklist.md). Você deve usar as informações que salvou da configuração anterior do dispositivo. 
1. Solicite um novo dispositivo da mesma configuração que o que falhou.  Para fazer um pedido, [crie um novo recurso do Edge Azure Stack](azure-stack-edge-gpu-deploy-prep.md#) no portal do Azure.
1. [Desembalar](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [montar em rack](azure-stack-edge-gpu-deploy-install.md#rack-the-device) e [conectar seu dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Conecte-se à interface do usuário local do dispositivo](azure-stack-edge-gpu-deploy-connect.md).
1. Configure a rede usando os mesmos endereços IP que você usou para o dispositivo antigo. Isso minimiza o impacto em qualquer computador cliente usado em seu ambiente. Consulte como [definir as configurações de rede](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Atribua o mesmo nome de dispositivo e domínio DNS que o dispositivo antigo. Isso garante que os clientes possam usar o mesmo nome de dispositivo para se comunicar com o novo dispositivo. Consulte como [definir a configuração do dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configure certificados no novo dispositivo da mesma maneira como você fez para o dispositivo antigo. Tenha em mente que o novo dispositivo tem um novo número de série do nó. Se você usou seus próprios certificados no dispositivo antigo, será necessário obter um novo certificado de nó. Consulte como [configurar certificados](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Obtenha a chave de ativação do portal do Azure e ative o novo dispositivo. Consulte como [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).

Agora você está pronto para implantar as cargas de trabalho que estavam executando no dispositivo antigo.

## <a name="restore-edge-cloud-shares"></a>Restaurar compartilhamentos de nuvem de borda

Siga estas etapas para restaurar os dados nos compartilhamentos de nuvem do Edge em seu dispositivo:

1. [Adicione compartilhamentos](azure-stack-edge-j-series-manage-shares.md#add-a-share) com os mesmos nomes de compartilhamento criados anteriormente no dispositivo com falha. Certifique-se de que, ao criar compartilhamentos, **Selecione o contêiner de blob** esteja definido para usar a opção **existente** e, em seguida, selecione o contêiner que foi usado com o dispositivo anterior.
1. [Adicione usuários](azure-stack-edge-j-series-manage-users.md#add-a-user) que tinham acesso ao dispositivo anterior.
1. [Adicione contas de armazenamento](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account) associadas aos compartilhamentos anteriormente no dispositivo. Ao criar contas de armazenamento de borda, selecione um contêiner existente e aponte para o contêiner que foi mapeado para a conta de armazenamento do Azure mapeada no dispositivo anterior. Todos os dados do dispositivo que foram gravados na conta de armazenamento de borda no dispositivo anterior foram carregados para o contêiner de armazenamento selecionado na conta de armazenamento do Azure mapeada.
1. [Atualize os](azure-stack-edge-j-series-manage-shares.md#refresh-shares) dados de compartilhamento do Azure. Isso extrairia todos os dados de nuvem do contêiner existente para os compartilhamentos.

## <a name="restore-edge-local-shares"></a>Restaurar compartilhamentos locais de borda

Para se preparar para uma falha potencial do dispositivo, você pode ter implantado uma das seguintes soluções de backup para proteger os dados de compartilhamentos locais de suas cargas de trabalho kubernetes ou IoT:

| Software de terceiros           | Referência à solução                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para obter detalhes, entre em contato com Cohesity.          |
| Veritas                        | Para obter detalhes, entre em contato com Veritas.   |

Depois que o dispositivo de substituição estiver totalmente configurado, habilite o dispositivo para o armazenamento local. 

Siga estas etapas para recuperar os dados de compartilhamentos locais: 

1. [Configure a computação no dispositivo](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Adicione um compartilhamento local de](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) volta.
1. Execute o procedimento de recuperação fornecido pela solução de proteção de dados escolhida. Consulte referências da tabela anterior.

## <a name="restore-vm-files-and-folders"></a>Restaurar arquivos e pastas da VM

Para se preparar para uma falha potencial do dispositivo, você pode ter implantado uma das seguintes soluções de backup para proteger os dados em VMs:



| Soluções de backup        | SO com suporte   | Referência                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agente de Serviços de Recuperação do Microsoft Azure (MARS) para o backup do Azure | Windows        | [Sobre o agente do MARS](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows, Linux | [Resumo da solução de integração, backup e recuperação do Microsoft Azure](https://www.cohesity.com/solution/cloud/azure) <br>Para obter detalhes, entre em contato com Cohesity.                          |
| Veritas                 | Windows, Linux | Para obter detalhes, entre em contato com Veritas.                    |

Depois que o dispositivo de substituição estiver totalmente configurado, você poderá reimplantar as VMs com a imagem de VM usada anteriormente. 

Siga estas etapas para recuperar os dados nas VMs:
 
1. [Implante uma VM de uma imagem de VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) no dispositivo. 
1. Instale a solução de proteção de dados escolhida na VM.
1. Execute o procedimento de recuperação fornecido pela solução de proteção de dados escolhida. Consulte referências da tabela anterior.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [retornar um dispositivo Azure Stack Edge pro](azure-stack-edge-return-device.md).
