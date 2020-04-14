---
title: Configure a VM hospedada no Microsoft Azure para o Azure Marketplace
description: Explica como dimensionar, atualizar e generalizar uma VM hospedada no Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: f20beced7d977668d12c06375ceb8a2554c6d335
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273419"
---
# <a name="configure-the-azure-hosted-vm"></a>Configurar a VM hospedada no Azure

> [!IMPORTANT]
> A partir de 13 de abril de 2020, iniciaremos a gestão móvel das ofertas da Sua Máquina Virtual Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Criar seus ativos técnicos da Máquina Virtual do Azure](https://aka.ms/AzureVMTechAsset) para gerenciar suas ofertas migradas.

Este artigo explica como dimensionar, atualizar e generalizar uma VM (máquina virtual) hospedada no Azure.  Essas etapas são necessárias para preparar a VM para ser implantada no Azure Marketplace.

## <a name="sizing-the-vhds"></a>Dimensionar os VHDs

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Se você selecionou uma das VMs pré-configuradas com um sistema operacional (e, opcionalmente, serviços adicionais), então você já escolheu um tamanho padrão de VM do Azure, conforme descrito na [guia SKUs](./cpp-skus-tab.md)da máquina virtual .  Iniciar sua solução com um sistema operacional pré-configurado é a abordagem recomendada.  No entanto, se você estiver instalando um sistema operacional manualmente, será necessário dimensionar o VHD principal na imagem da VM:

- Para Windows, o VHD do sistema operacional deve ser criado como um VHD de formato fixo de 127-128 GB. 
- Para Linux, esse VHD deve ser criado como um VHD de formato fixo de 30 a 50 GB.

Se o tamanho físico for menor que 127-128 GB, o VHD deverá ser esparso. As imagens básicas do Windows e SQL Server fornecidas já atenderam a esses requisitos; portanto, não altere o formato nem o tamanho do VHD obtido. 

Discos de dados podem ser de até 1 TB. Ao decidir sobre o tamanho, lembre-se de que os clientes não podem redimensionar VHDs dentro de uma imagem no momento da implantação. Os VHDs de disco de dados devem ser criados como VHDs de formato fixo. Eles também devem ser esparsos. Os discos de dados podem inicialmente estar vazios ou conter dados.


## <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

As imagens de base das VMs do sistema operacional contêm as atualizações mais recentes até a data publicada. Antes de publicar o VHD do sistema operacional que você criou, assegure-se de atualizar o sistema operacional e todos os serviços instalados com os patches de segurança e manutenção mais recentes.

Para Windows Server 2016, execute o comando **Verificar se há atualizações**.  Caso contrário, para versões mais antigas do Windows, consulte [Como obter uma atualização através do Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  A atualização do Windows instalará automaticamente as atualizações de segurança críticas e importantes mais recentes.

Para distribuições Linux, as atualizações geralmente são baixadas e instaladas por meio de uma ferramenta de linha de comando ou de um utilitário gráfico.  Por exemplo, o Ubuntu Linux fornece o comando [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) e a ferramenta [Gerenciador de Atualização](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) para atualizar o SO.


## <a name="perform-additional-security-checks"></a>Realizar verificações de segurança adicionais

Você deve manter um alto nível de segurança para as imagens de solução no Azure Marketplace.  O artigo a seguir fornece uma lista de verificação de configurações e procedimentos de segurança para ajudá-lo nesse objetivo: [Recomendações de segurança para as imagens do Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Algumas dessas recomendações são específicas para imagens baseadas em Linux, mas a maioria aplica-se a qualquer imagem de VM. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

Se uma configuração adicional for necessária, a abordagem recomendada é usar uma tarefa agendada que seja executada na inicialização para fazer qualquer alteração final na VM após a implantação.  Considere também as seguintes recomendações:
- Se for uma tarefa de execução única, é recomendável que a tarefa seja excluída após a conclusão ser realizada com êxito.
- As configurações não devem depender de unidades diferentes de C ou D, porque somente essas duas unidades sempre terão a garantia de existir. A unidade C é o disco do sistema operacional e a unidade D é o disco local temporário.

Para obter mais informações sobre personalizações do Linux, consulte [Recursos e extensões da máquina virtual para Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para obter essa reutilização, o VHD do sistema operacional deve ser *generalizado*, uma operação que remove todos os identificadores específicos de instância e drivers de software de uma VM.

### <a name="windows"></a>Windows

Os discos do sistema operacional do Windows são generalizados com a [ferramenta sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Se você atualizar ou reconfigurar o sistema operacional subsequentemente, deverá executar novamente o sysprep. 

> [!WARNING]
>  Como as atualizações poderão ser executadas automaticamente após executar o sysprep, você deverá desativar a VM até que ela seja implantada.  Esse desligamento evitará atualizações subsequentes de alterações específicas da instância no sistema operacional do VHD ou serviços instalados.

Para obter mais informações sobre como executar o sysprep, consulte [Passos para generalizar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

O processo de duas etapas a seguir generaliza uma VM Linux e a reimplanta como uma VM separada. Essas duas etapas são apenas o essencial do processo. Para obter mais informações sobre essas duas etapas e por que elas devem ser feitas, consulte [Como criar uma imagem de uma máquina virtual ou VHD](../../../virtual-machines/linux/capture-image.md). Com o propósito de criar o VHD para sua oferta do Azure Marketplace, você pode parar quando chegar à seção "Criar uma VM a partir da imagem capturada".

#### <a name="remove-the-azure-linux-agent"></a>Remover o agente Linux do Azure
1.  Conecte-se à VM do Linux usando um cliente SSH.
2.  Na janela SSH, digite o seguinte comando: <br/>
    `sudo waagent -deprovision+user`
3.  Digite `y` para continuar. (Você pode adicionar o parâmetro `-force` ao comando anterior para evitar essa etapa de confirmação.)
4.  Depois que o comando for concluído, digite `exit` para fechar o cliente SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Capturar a imagem
1.  Acesse o portal do Azure, selecione seu RG (grupo de recursos) e desaloque a VM.
2.  O VHD é generalizado e você poderá criar uma nova VM usando esse VHD.


## <a name="create-one-or-more-copies"></a>Criar uma ou mais cópias

A criação de cópias da VM geralmente é útil para backup, teste, failover personalizado ou balanceamento de carga para oferecer diferentes configurações de uma solução, e assim por diante. Para obter informações sobre como duplicar e baixar um VHD primário, para criar um clone não gerenciado, consulte:

- VM do Linux: [Baixar um VHD do Linux pelo Azure](../../../virtual-machines/linux/download-vhd.md)
- VM do Windows: [Baixar um VHD do Windows pelo Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Próximas etapas

Depois que sua VM é generalizada, foi desalocada e você criou uma imagem da VM, você está pronto para [implantar uma máquina virtual a partir de um disco rígido virtual](./cpp-deploy-vm-vhd.md).
