---
title: Configure GPU para Windows Virtual Desktop - Azure
description: Como ativar a renderização e a codificação aceleradas pela GPU no Windows Virtual Desktop.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767033"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Configure a aceleração da unidade de processamento gráfico (GPU) para o Windows Virtual Desktop

O Windows Virtual Desktop suporta renderização e codificação aceleradas pela GPU para melhorar o desempenho e a escalabilidade do aplicativo. A aceleração da GPU é particularmente crucial para aplicativos com uso intensivo de gráficos.

Siga as instruções deste artigo para criar uma máquina virtual Azure otimizada para GPU, adicioná-la ao seu pool de host e configurá-la para usar a aceleração de GPU para renderização e codificação. Este artigo pressupõe que você já tenha um inquilino do Windows Virtual Desktop configurado.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecione uma GPU otimizada para o tamanho da máquina virtual Azure

O Azure oferece uma série de tamanhos de [máquinas virtuais otimizadas para GPU.](/azure/virtual-machines/windows/sizes-gpu) A escolha certa para o seu pool de host depende de uma série de fatores, incluindo suas cargas de trabalho específicas do aplicativo, qualidade desejada da experiência do usuário e custo. Em geral, GPUs maiores e mais capazes oferecem uma melhor experiência de usuário em uma dada densidade de usuário.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Crie um pool de host, provisione sua máquina virtual e configure um grupo de aplicativos

Crie um novo pool de host usando uma VM do tamanho selecionado. Para obter instruções, consulte [Tutorial: Crie um pool de hosts com o Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

O Windows Virtual Desktop suporta renderização e codificação aceleradas por GPU nos seguintes sistemas operacionais:

* Windows 10 versão 1511 ou mais recente
* Windows Server 2016 ou mais recente

Você também deve configurar um grupo de aplicativos ou usar o grupo de aplicativos de desktop padrão (chamado "Desktop Application Group") que é criado automaticamente quando você cria um novo pool de host. Para obter instruções, consulte [Tutorial: Gerencie grupos de aplicativos para o Windows Virtual Desktop](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instale drivers gráficos suportados em sua máquina virtual

Para aproveitar os recursos de GPU das VMs da série Azure N no Windows Virtual Desktop, você deve instalar os drivers gráficos apropriados. Siga as instruções em [sistemas operacionais e drivers suportados](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) para instalar drivers do fornecedor gráfico apropriado, manualmente ou usando uma extensão Azure VM.

Apenas drivers distribuídos pelo Azure são suportados para o Windows Virtual Desktop. Além disso, para VMs Azure com GPUs NVIDIA, apenas [drivers NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) são suportados para Windows Virtual Desktop.

Após a instalação do driver, é necessária uma reinicialização da VM. Use as etapas de verificação nas instruções acima para confirmar se os drivers gráficos foram instalados com sucesso.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurar renderização de aplicativo acelerado por GPU

Por padrão, aplicativos e desktops em execução em configurações de várias sessões são renderizados com a CPU e não aproveitam GPUs disponíveis para renderização. Configure a Diretiva de Grupo para o host de sessão para habilitar a renderização acelerada pela GPU:

1. Conecte-se à área de trabalho da VM usando uma conta com privilégios de administrador local.
2. Abra o menu Iniciar e digite "gpedit.msc" para abrir o Editor de Políticas do Grupo.
3. Navegue na árvore para **configuração** > de**computador Modelos administrativos** > **Do Windows Componentes Serviços remotos** > da área de**trabalho** > Ambiente remoto da**sessão de sessão da** > área de**trabalho**.
4. Selecione política **Use o adaptador gráfico padrão de hardware para todas as sessões de Serviços de Desktop Remoto** e defina essa diretiva como **Habilitada** para habilitar a renderização de GPU na sessão remota.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurar codificação de quadros acelerados por GPU

O Remote Desktop codifica todos os gráficos renderizados por aplicativos e desktops (seja renderizados com GPU ou com CPU) para transmissão para clientes de Desktop Remoto. Por padrão, a Área de Trabalho Remota não aproveita as GPUs disponíveis para essa codificação. Configure a Diretiva de Grupo para o host de sessão para habilitar a codificação de quadros acelerada pela GPU. Continuando os passos acima:

1. Selecione a política **Priorize o modo GráficoS H.264/AVC 444 para conexões de desktop remota** e defina essa política como **Habilitada** para forçar o codec H.264/AVC 444 na sessão remota.
2. Selecione a política Configure a **codificação de hardware H.264/AVC para conexões de desktop remotas** e defina essa diretiva como **Habilitada** para habilitar a codificação de hardware para AVC/H.264 na sessão remota.

    >[!NOTE]
    >No Windows Server 2016, defina a opção **Prefer AVC Hardware Encoding** to **Always try**.

3. Agora que as políticas de grupo foram editadas, force uma atualização da política de grupo. Abra o prompt de comando e digite:

    ```batch
    gpupdate.exe /force
    ```

4. Faça login a partir da sessão de desktop remoto.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verifique a renderização de aplicativos acelerados pela GPU

Para verificar se os aplicativos estão usando a GPU para renderização, tente qualquer um dos seguintes:

* Para VMs Azure com uma GPU NVIDIA, use o `nvidia-smi` utilitário conforme descrito na instalação do driver [Verify](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) para verificar a utilização da GPU ao executar seus aplicativos.
* Nas versões suportadas do sistema operacional, você pode usar o Gerenciador de Tarefas para verificar a utilização da GPU. Selecione a GPU na guia "Desempenho" para ver se os aplicativos estão utilizando a GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verificar codificação de quadros acelerado sustais de GPU

Para verificar se a área de trabalho remota está usando codificação acelerada pela GPU:

1. Conecte-se à área de trabalho da VM usando o cliente Windows Virtual Desktop.
2. Inicie o Visualizador de Eventos e navegue até o seguinte nó: **Aplicativos e serviços Logs** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operacional**
3. Para determinar se a codificação acelerada pela GPU é usada, procure o ID de evento 170. Se você ver "Codificador de hardware AVC ativado: 1" então a codificação de GPU será usada.
4. Para determinar se o modo AVC 444 é usado, procure o ID de evento 162. Se você ver "AVC Disponível: 1 Perfil Inicial: 2048" então AVC 444 é usado.

## <a name="next-steps"></a>Próximas etapas

Estas instruções devem tê-lo em funcionamento com aceleração de GPU em um host de sessão (uma VM). Algumas considerações adicionais para habilitar a aceleração da GPU em um pool de host maior:

* Considere usar uma [extensão VM](/azure/virtual-machines/extensions/overview) para simplificar a instalação do driver e atualizações em uma série de VMs. Use a extensão do [driver de GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) para VMs com GPUs NVIDIA, e use o AMD GPU Driver Extension (em breve) para VMs com GPUs AMD.
* Considere usar a Active Directory Group Policy para simplificar a configuração da diretiva de grupo em uma série de VMs. Para obter informações sobre a implantação da Política de Grupo no domínio Active Directory, consulte [Trabalhando com objetos de diretiva de grupo](https://go.microsoft.com/fwlink/p/?LinkId=620889).
