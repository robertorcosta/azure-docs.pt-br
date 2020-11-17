---
title: Configurar o líder do projeto como laboratórios com Azure Lab Services
description: Saiba como configurar os laboratórios para ensinar ao projeto liderando as classes do Way.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: e3783ae4fa07bf783841022903c4bcf3ab6fbe23
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647997"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Configurar laboratórios para o líder de projetos como classes

[O PLTW (líder do projeto)](https://www.pltw.org/) é uma organização sem fins lucrativos que fornece um currículo PreK-12 em toda a Estados Unidos em ciência da computação, engenharia e ciência biomédica.  Em cada classe PLTW, os alunos usam uma variedade de aplicativos de software como parte de sua experiência prática de aprendizado.  Muitos dos aplicativos de software exigem uma CPU rápida ou, em alguns casos, uma GPU.  Este artigo mostra como configurar os laboratórios para as seguintes classes PLTW que normalmente são oferecidas aos alunos em notas de 9-12:

- **Introdução ao design de engenharia**

    Os alunos são introduzidos no processo de design de engenharia, que inclui o uso do software de [desenho auxiliado por computador de inventário da Autodesk (CAD)](https://www.autodesk.com/products/inventor/new-features) para modelagem 3D.

- **Princípios de engenharia**
    
    Os alunos aprendem sobre mecanismos de engenharia, força structural\material e automação.  Essa classe usa software como [MD sólidos](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), designer de [ponte de ponto oeste](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)e [simulação de exército da América](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Engenharia e arquitetura civil**

    Os alunos são ensinados a criar e desenvolver design e desenvolvimento usando software de design de arquitetura [Revit da Autodesk](https://www.autodesk.com/products/revit/overview) para modelagem de informações de construção 3D (BIM).

- **Fabricação integrada do computador**

    Os alunos exploram processos de fabricação modernos que envolvem robótica e automação.   Nessa classe, os alunos usam o [CAD de inventário do Autodesk](https://www.autodesk.com/products/inventor/new-features) e o software de [manufatura (CAM) de fabricação auxiliada por computador de inventário do Autodesk](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Eletrônica Digital**

    Os alunos analisam circuitos e dispositivos lógicos eletrônicos usando a simulação de [Multisim do instrumento nacional](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) e o software de design de circuito.

- **Design e desenvolvimento de engenharia**

    Os alunos contribuem para uma solução de ponta a ponta que combina pesquisa, design e testes que eles apresentam a um painel de engenheiros.  Nessa classe, os alunos usam o software [CAD de inventário do Autodesk](https://www.autodesk.com/products/inventor/new-features) .

- **Noções básicas sobre ciência da computação**

    Os alunos são apresentados aos conceitos e ferramentas computacionais.  Eles começam com a programação baseada em bloco e, em seguida, movem para usar a codificação baseada em texto usando ambientes de codificação, como [blocos VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Princípios de ciência da computação**
    
    Os alunos crescem sua experiência em programação com o [Python](https://www.python.org/) usando [o ambiente de desenvolvimento Visual Studio Code da Microsoft](https://code.visualstudio.com/). 

- **Ciência da computação A**

    Os alunos aumentam sua experiência em programação nessa classe aprendendo o desenvolvimento de aplicativos móveis.  Nessa classe, eles aprendem o [Java](https://www.java.com/) usando [o ambiente de desenvolvimento de Visual Studio Code da Microsoft](https://code.visualstudio.com/).  Os alunos também usam um emulador que permite executar e testar o código do aplicativo móvel.  Para obter informações sobre como configurar um emulador nos laboratórios do Azure, entre em contato conosco em azlabspilot@microsoft.com .

Consulte o site do PLTW para obter a [lista completa de softwares](https://www.pltw.org/pltw-software) para cada classe.

## <a name="lab-configuration"></a>Configuração do laboratório
Para configurar os laboratórios para PLTW, você precisa de uma assinatura do Azure e uma conta de laboratório para começar. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services. Para obter mais informações sobre como criar uma nova conta de laboratório, consulte o tutorial sobre [como configurar uma conta de laboratório](./tutorial-setup-lab-account.md). Você também pode usar uma conta de laboratório existente.

Depois de ter uma conta de laboratório, você deve criar laboratórios separados para cada sessão de uma classe PLTW que sua escola oferece.  Também recomendamos que você crie imagens separadas para cada tipo de classe PLTW.  Para obter mais detalhes sobre como estruturar seus laboratórios e imagens, leia a postagem do blog: [mudando de um laboratório físico para Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Configurações de conta do laboratório
Habilite as configurações descritas na tabela a seguir para a conta do laboratório. Para obter mais informações sobre como habilitar imagens do Marketplace, consulte o artigo sobre [como especificar imagens do Marketplace disponíveis para criadores de laboratório](./specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| -------------------- | ----- |
| Imagem do Marketplace | Habilite a imagem do Windows 10 pro para uso em sua conta de laboratório. |

### <a name="lab-settings"></a>Configurações do laboratório
O tamanho da VM que recomendamos usar para classes PLTW depende dos tipos de cargas de trabalho que os alunos estão fazendo na classe.  Para as classes acima, é recomendável usar tamanhos de VM grandes e pequenos de GPU (visualização).  Consulte as diretrizes na tabela abaixo ao configurar laboratórios para suas classes PLTW.

| Configuração de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| **GPU pequena (visualização)**.  Essa VM é mais adequada para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX.  É recomendável usar esse tamanho para as seguintes classes PLTW: Engenharia e arquitetura civis, eletrônicos digitais, fabricação integrada ao computador; e design e desenvolvimento de engenharia.
|Tamanho da Máquina Virtual| **Grande**.  Este tamanho é mais adequado a aplicativos que precisam de CPUs mais rápidas, melhor desempenho do disco local, bancos de dados grandes, caches de memória grandes.  É recomendável usar esse tamanho para as seguintes classes PLTW: introdução ao design de engenharia, princípios de engenharia, ciência da computação do computador, princípios da ciência da computação e ciência da computação A.

### <a name="licensing-server"></a>Servidor de licenciamento
A maioria dos softwares usados nas classes PLTW *_acima não são_* necessárias para o acesso a um servidor de licenciamento.  No entanto, será necessário acessar um servidor de licenciamento se você planeja usar o modelo de licenciamento de rede do Autodesk para o seguinte software:
-   Revit
-   Linguagem
-   CAM de inventário

Para usar o licenciamento de rede com o software do Autodesk, o [PLTW fornece etapas detalhadas](https://www.pltw.org/pltw-software) para instalar o Gerenciador de licenças do Autodesk no seu servidor de licenciamento.  Esse servidor de licenciamento normalmente está localizado na sua rede local ou hospedada em uma VM (máquina virtual) do Azure dentro da VNet (rede virtual) do Azure.

Depois que o servidor de licença estiver configurado, você precisará [emparelhar a VNet](./how-to-connect-peer-virtual-network.md) com sua [conta de laboratório](./tutorial-setup-lab-account.md). O emparelhamento de rede deve ser feito _before * criar o laboratório para que as máquinas virtuais do laboratório possam acessar o servidor de licença e o contrário.

Os arquivos de licença gerados pelo Autodesk incorporam o endereço MAC do servidor de licenciamento.  Se você decidir hospedar o servidor de licenciamento usando uma VM do Azure, é importante garantir que o endereço MAC do servidor de licenciamento não seja alterado.   Caso contrário, quando o endereço MAC for alterado, os arquivos de licenciamento precisarão ser regenerados.  Siga estas dicas para impedir que seu endereço MAC seja alterado:

- [Defina um IP privado estático e um endereço MAC](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) para a VM do Azure que hospeda o servidor de licenciamento.
- Certifique-se de configurar sua conta de laboratório e VNet do servidor de licenciamento em um region\location que tenha capacidade de VM suficiente para que você não precise mover esses recursos para um novo region\location mais tarde.

Além disso, leia o artigo sobre [como configurar um servidor de licenciamento como um recurso compartilhado](./how-to-create-a-lab-with-shared-resource.md) para obter mais informações.

### <a name="template-machine"></a>Computador de modelo
Alguns dos arquivos de instalação que você precisa para PLTW são grandes e levam muito tempo para serem copiados quando você os baixa para o computador de modelo de um laboratório.

Em vez de baixar os arquivos de instalação para o computador de modelo e instalar tudo isso, é recomendável criar suas imagens PLTW em seu ambiente físico.  Em seguida, você pode importar as imagens para a Galeria de imagens compartilhadas para que possa usar essas imagens personalizadas para criar seus laboratórios.  Leia o seguinte artigo para obter detalhes: [carregar uma imagem personalizada na Galeria de imagens compartilhadas](./upload-custom-image-shared-image-gallery.md).

Seguindo essa recomendação, aqui estão as principais tarefas para configurar um laboratório:

1. Em seu ambiente físico, crie a imagem para a classe.

    a.  Use as etapas detalhadas do PLTW para baixar arquivos de instalação e instalar o software necessário.

    > [!NOTE]    
    > Quando você instala os aplicativos do Autodesk, o computador no qual você está instalando o Autodesk precisa ser capaz de se comunicar com o servidor de licenciamento (o assistente de instalação do Autodesk solicitará que você especifique o nome do computador no qual o servidor de licença está hospedado).  Se você estiver hospedando seu servidor de licenciamento em uma VM do Azure, talvez seja necessário aguardar para instalar o Autodesk no computador de modelo do laboratório para que o assistente de instalação do Autodesk possa acessar seu servidor de licenciamento

    b.  [Instalar e configurar o onedrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) (ou outras opções de backup que sua escola pode usar).
    
    c.  [Instalar e configurar as atualizações do Windows](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Carregue a imagem personalizada na [Galeria de imagens compartilhadas anexada à sua conta de laboratório](./how-to-attach-detach-shared-image-gallery.md).

1.  Crie um laboratório e selecione a imagem personalizada que você carregou na etapa anterior.

1.  Depois que o laboratório for criado, inicie e conecte-se ao computador de modelo para validar se a imagem funciona conforme o esperado.

1.  Por fim, publique o computador de modelo para criar as VMs dos alunos.

## <a name="student-devices"></a>Dispositivos de aluno
Seus alunos podem se conectar às VMs de laboratório de computadores Windows\Mac e Chromebooks.  Aqui estão os links para instruções para cada uma dessas opções:

- [Conecte-se do Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Conectar do Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Conectar do Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Custo
Vamos abordar uma possível estimativa de custo para as classes PLTW acima.  Essa estimativa não inclui o custo de executar um servidor de licenciamento ou usar a Galeria de imagens compartilhadas.  Usaremos uma classe de 25 alunos.  Há 20 horas de tempo de classe agendada.  Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora da hora da classe agendada.  Consulte as estimativas de custo abaixo para os tamanhos de GPU **grande** e **pequena (visualização)** .

- **VM grande**

    25 alunos x (20 horas agendadas + 10 horas de cota) x 70 unidades de laboratório x 0, 1 USD por hora = 525, 0 USD

- **GPU Pequena (Visualização)**

    25 alunos x (20 horas agendadas + 10 horas de cota) x 160 unidades de laboratório x 0, 1 USD por hora = 1200, 0 USD

> [!IMPORTANT] 
> A estimativa de custo é apenas para fins de exemplo.  Para obter detalhes atuais sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Muitas das classes PLTW usam aplicativos que são acessados por meio de um navegador, como o inventário de aplicativo do MIT.  Esses aplicativos baseados em navegador não exigem uma CPU ou GPU rápida e podem ser acessados de qualquer dispositivo que tenha uma conexão com a Internet.  Quando os alunos estão usando esses tipos de aplicativos, recomendamos que eles usem o navegador em seu dispositivo físico em vez de usar o navegador em suas VMs de laboratório.  Isso ajudará a manter os custos baixos usando apenas as VMs de laboratório para aplicativos que exigem uma CPU ou GPU rápida.

## <a name="next-steps"></a>Próximas etapas
As próximas etapas são comuns à configuração de qualquer laboratório:

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Enviar links de registro por email para alunos](how-to-configure-student-usage.md#send-invitations-to-users).