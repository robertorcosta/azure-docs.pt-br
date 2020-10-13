---
title: Azure Lab Services-carregar uma imagem personalizada na Galeria de imagens compartilhadas
description: Descreve como carregar uma imagem personalizada na Galeria de imagens compartilhadas. Os departamentos de ti da Universidade encontrarão imagens de importação especialmente úteis.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712383"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Carregar uma imagem personalizada na Galeria de Imagens Compartilhadas

A Galeria de imagens compartilhadas está disponível para você para importar suas próprias imagens personalizadas para a criação de laboratórios no Azure Lab Services. Os departamentos de ti da Universidade encontrarão imagens de importação especialmente benéficos pelos seguintes motivos: 

* Você não precisa criar imagens manualmente usando a VM de modelo de um laboratório.
* Você pode carregar imagens criadas usando outras ferramentas, como SCCM, Endpoint Manager, etc.

Este artigo descreve as etapas que podem ser tomadas para trazer uma imagem personalizada e usá-la em Azure Lab Services. 

> [!IMPORTANT]
> Ao mover suas imagens de um ambiente de laboratório físico para AZ Labs, você precisa reestruturá-las appropriatly. Não basta reutilizar as imagens existentes dos laboratórios físicos. <br/>Para obter detalhes, confira a postagem [de um laboratório físico para Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) blog.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Traga uma imagem personalizada de um ambiente de laboratório físico

As etapas a seguir mostram como importar uma imagem personalizada que inicia de um ambiente de laboratório físico. Um VHD é então criado a partir desse ambiente e importado para a Galeria de imagens compartilhadas no Azure para que possa ser usado dentro do Azure Lab Services.

Existem muitas opções para a criação de um VHD de um ambiente de laboratório físico. As etapas a seguir mostram como criar um VHD de uma VM do Windows Hyper-V:

1. Comece com uma VM do Hyper-V em seu ambiente de laboratório físico que foi criado a partir da sua imagem.
    1. A VM deve ser criada como uma VM de geração 1.
    1. A VM deve usar um tamanho de disco fixo. Você também pode especificar o tamanho do disco nesta janela. O tamanho do disco não deve ser maior que 128 GB.<br/>    
    Não há suporte para imagens com tamanho de disco > 128 GB por Azure Lab Services. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Conectar disco rígido virtual":::   
    1. Imagem da VM normalmente.
1. [Conecte-se à VM e prepare-a para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).
    1. [Definir configurações do Windows para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [Verifique os serviços do Windows que são o mínimo necessário para garantir a conectividade da VM](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [Atualizar configurações do registro da área de trabalho remota](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [Configure as regras do Firewall do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. Instalar atualizações do Windows
    1. [Instalar o agente de VM do Azure e configuração adicional, conforme mostrado aqui](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    As etapas acima criarão uma imagem especializada. Se estiver criando uma imagem generalizada, você também precisará executar o [Sysprep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep). <br/>
        Você deve criar uma imagem especializada se desejar manter o diretório de usuário (que pode conter arquivos, informações de conta de usuário, etc.) necessário para o software incluído na imagem.
1. Como o **Hyper-V** cria um arquivo **VHDX** por padrão, é necessário convertê-lo em um arquivo VHD.
    1. Navegue até a ação do **Gerenciador do Hyper-V**  ->  **Action**  ->  **Editar disco**.
    1. Aqui, você terá a opção de **converter** o disco de um VHDX para um VHD
    1. Ao tentar expandir o tamanho do disco, certifique-se de não exceder 128 GB.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Conectar disco rígido virtual" do portal do Azure para escolher o tamanho do disco. Como mencionado anteriormente, o tamanho precisa ser de no > 128 GB.
1. Tire um instantâneo do disco gerenciado.
    Isso pode ser feito a partir do PowerShell, usando o portal do Azure, ou de dentro Gerenciador de Armazenamento, conforme descrito em [criar um instantâneo usando o portal ou o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).
1. Na Galeria de imagens compartilhadas, crie uma definição de imagem e uma versão:
    1. [Criar uma definição de imagem](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition).
    1. Você também precisa especificar aqui se você está criando uma imagem especializada/generalizada.
1. Crie o laboratório no Azure Lab Services e selecione a imagem personalizada da Galeria de imagens compartilhadas.

    Se você expandiu o disco depois que o sistema operacional foi instalado na VM do Hyper-V original, também será necessário estender a unidade C no Windows para usar o espaço em disco não alocado. Para fazer isso, faça logon na VM de modelo após a criação do laboratório e siga as etapas semelhantes às mostradas em [estender um volume básico](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume). Há opções para fazer isso por meio da interface do usuário, bem como usando o PowerShell.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da galeria de imagens compartilhadas](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [Como usar a Galeria de imagens compartilhadas](how-to-use-shared-image-gallery.md)
