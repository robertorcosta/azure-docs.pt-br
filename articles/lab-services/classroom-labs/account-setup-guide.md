---
title: Guia de configuração de conta de laboratório acelerado para Azure Lab Services
description: Este guia ajuda os administradores a configurar rapidamente uma conta de laboratório para uso em sua escola.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879574"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração da conta do laboratório

Como uma primeira etapa, os administradores devem configurar uma conta de laboratório em sua assinatura do Azure. Uma conta de laboratório é um contêiner para seus laboratórios de sala de aula e leva apenas alguns minutos para ser configurada.

## <a name="understand-your-schools-lab-account-requirements"></a>Entenda os requisitos de conta do laboratório de sua escola

Para entender como configurar sua conta de laboratório com base nas necessidades de sua escola, você deve considerar essas perguntas.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Tenho acesso a uma assinatura do Azure?

Para criar uma conta de laboratório, você precisa ter acesso a uma assinatura do Azure configurada para sua escola. Sua escola pode ter uma ou mais assinaturas. Você usa uma assinatura para gerenciar a cobrança e a segurança de todos os seus recursos e serviços do Azure, incluindo contas de laboratório.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Quantas contas de laboratório precisam ser criadas?

Para começar rapidamente, crie uma única conta de laboratório e, depois, crie contas de laboratório adicionais, conforme necessário. Por exemplo, você pode eventualmente ter uma conta de laboratório por departamento.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Quem deve ser proprietários e colaboradores da conta do laboratório?

Os administradores são normalmente os proprietários e colaboradores de uma conta de laboratório. Eles são responsáveis por gerenciar as políticas que se aplicam a todos os laboratórios contidos na conta do laboratório. A pessoa que cria a conta de laboratório é automaticamente um proprietário. Você pode adicionar outros proprietários e colaboradores, normalmente do locatário do Azure Active Directory (Azure AD) associado à sua assinatura. Isso pode ser útil para ajudar a gerenciar uma conta de laboratório atribuindo a função de proprietário ou colaborador no nível da conta do laboratório.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Quem terá permissão para criar e gerenciar laboratórios?

Você pode optar por fazer com que seus administradores e membros docentes criem e gerenciem laboratórios. Esses usuários (normalmente do locatário do Azure AD associado à sua assinatura) são atribuídos à função de criador de laboratório dentro da conta do laboratório.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Deseja dar aos criadores de laboratório a capacidade de salvar imagens que podem ser compartilhadas entre os laboratórios?

Uma galeria de imagens compartilhadas é um repositório que você pode usar para salvar e compartilhar imagens. Se você tiver várias classes que precisam das mesmas imagens, os criadores de laboratório poderão criar a imagem uma vez e compartilhá-las nos laboratórios. No entanto, para começar, você não precisa necessariamente de uma galeria de imagens compartilhadas, pois você sempre pode adicionar uma mais tarde.

Se você respondeu "Sim" a essa pergunta, precisará criar ou anexar uma galeria de imagens compartilhadas à sua conta de laboratório. Se você respondeu "Eu não sei", você pode adiar essa decisão até mais tarde.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Quais imagens no Azure Marketplace serão usadas pelos laboratórios da sala de aula?

O Azure Marketplace fornece centenas de imagens que você pode habilitar para que os criadores de laboratório possam usar a imagem para criar seu laboratório. Algumas imagens podem incluir tudo o que um laboratório já precisa. Em outros casos, você pode usar uma imagem como um ponto de partida e, em seguida, o criador do laboratório pode personalizá-la Instalando aplicativos ou ferramentas adicionais.

Se você não souber quais imagens precisará usar, sempre será possível voltar a isso mais tarde para habilitá-las. Além disso, a melhor maneira de ver quais imagens estão disponíveis é primeiro criar uma conta de laboratório. Isso lhe dá acesso, para que você possa examinar a lista de imagens disponíveis e seu conteúdo.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>As máquinas virtuais do laboratório precisam ter acesso a outros recursos do Azure ou locais?

Ao configurar uma conta de laboratório, você também tem a opção de emparelhar com uma rede virtual. Para decidir se você precisa disso, considere as seguintes perguntas:

- **Você precisa fornecer acesso a um servidor de licenciamento?**
  
   Se você planeja usar as imagens do Azure Marketplace, o custo da licença do sistema operacional é agrupado nos preços dos serviços de laboratório. Portanto, você não precisa fornecer licenças para o próprio sistema operacional. No entanto, para software adicional e aplicativos que estão instalados, você precisa fornecer uma licença conforme apropriado.

- **As VMs de laboratório precisam de acesso a outros recursos locais, como um compartilhamento de arquivos ou banco de dados?**

   Você cria uma rede virtual para fornecer acesso a recursos locais, normalmente usando um gateway de rede virtual site a site. Se você não tiver uma rede virtual configurada, precisará investir tempo adicional para isso.

- **As VMs de laboratório precisam de acesso a outros recursos do Azure localizados em uma rede virtual?**

   Se precisar de acesso aos recursos do Azure que *não* estão protegidos em uma rede virtual, você poderá acessar esses recursos por meio da Internet pública, sem fazer nenhum emparelhamento.

Se você respondeu "Sim" a uma ou mais perguntas, será necessário emparelhar a conta do laboratório para uma rede virtual. Se você respondeu "Eu não sei", você pode adiar essa decisão até mais tarde. Você sempre pode optar por emparelhar uma rede virtual depois de criar a conta de laboratório.

## <a name="set-up-your-lab-account"></a>Configurar sua conta de laboratório

Depois de entender os requisitos para sua conta de laboratório, você estará pronto para configurá-lo.

1. **Crie sua conta de laboratório.** Consulte o tutorial sobre como [criar uma conta de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) para obter instruções.

   Quando você estiver criando uma conta de laboratório, talvez ache útil se familiarizar com os recursos do Azure envolvidos. Para obter mais informações, consulte os seguintes artigos:

   - [Assinatura](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupo de recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Conta de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selecionando uma região e um local](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Diretrizes de nomenclatura para recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Adicione usuários à função de criador de laboratório.** Para obter instruções, consulte [adicionando usuários à função criador de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Além disso, para obter mais informações sobre as diferentes funções que podem ser atribuídas a usuários que gerenciarão contas de laboratório e laboratórios, consulte o [guia sobre como gerenciar a identidade](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Conecte-se a uma rede virtual de mesmo nível.** Para obter instruções, consulte [conectando a rede do seu laboratório a uma rede virtual de mesmo nível](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Talvez você também precise consultar as instruções sobre como [Configurar o intervalo de endereços de VMs do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Habilitar e revisar imagens.** Para obter instruções, consulte [habilitando imagens do Azure Marketplace para criadores de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Para examinar o conteúdo de cada imagem do Azure Marketplace, selecione o nome da imagem. Por exemplo, a captura de tela a seguir mostra os detalhes da imagem do Ubuntu VM de Ciência de Dados:

   ![Captura de tela de examinar imagens do Azure Marketplace](../media/setup-guide/review-marketplace-images.png)

   Se você tiver uma galeria de imagens compartilhada anexada à sua conta de laboratório e desejar permitir que imagens personalizadas sejam compartilhadas por criadores de laboratório, conclua as etapas semelhantes àquelas mostradas na seguinte captura de tela:

   ![Captura de tela de habilitação de imagens personalizadas em uma galeria de imagens compartilhadas](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar contas de laboratório](how-to-manage-lab-accounts.md)

- [Guia de configuração do laboratório de sala de aula](setup-guide.md)
