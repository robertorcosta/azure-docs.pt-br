---
title: Guia de configuração de conta de laboratório acelerado para o Azure Lab Services
description: Este guia ajuda os administradores a criar rapidamente uma conta de laboratório para uso dentro de sua escola.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879574"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração de conta de laboratório

Como primeiro passo, os administradores devem configurar uma conta de laboratório dentro de sua assinatura do Azure. Uma conta de laboratório é um recipiente para seus laboratórios de sala de aula, e leva apenas alguns minutos para configurar.

## <a name="understand-your-schools-lab-account-requirements"></a>Entenda os requisitos da conta de laboratório da sua escola

Para entender como configurar sua conta de laboratório com base nas necessidades da sua escola, você deve considerar essas perguntas.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Tenho acesso a uma assinatura do Azure?

Para criar uma conta de laboratório, você precisa acessar uma assinatura do Azure configurada para sua escola. Sua escola pode ter uma ou mais assinaturas. Você usa uma assinatura para gerenciar faturamento e segurança para todos os seus recursos e serviços do Azure, incluindo contas de laboratório.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Quantas contas de laboratório precisam ser criadas?

Para começar rapidamente, crie uma única conta de laboratório e, posteriormente, crie contas adicionais de laboratório conforme necessário. Por exemplo, você pode eventualmente ter uma conta de laboratório por departamento.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Quem devem ser os donos e colaboradores da conta do laboratório?

Seus administradores são tipicamente os proprietários e contribuintes de uma conta de laboratório. Eles são responsáveis por gerenciar as políticas que se aplicam a todos os laboratórios contidos na conta do laboratório. A pessoa que cria a conta do laboratório é automaticamente uma proprietária. Você pode adicionar proprietários e colaboradores adicionais, normalmente do inquilino do Azure Active Directory (Azure AD) associado à sua assinatura. Isso pode ser útil para ajudar a gerenciar uma conta de laboratório, atribuindo o papel do proprietário ou contribuinte no nível da conta do laboratório.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Quem será autorizado a criar e gerenciar laboratórios?

Você pode optar por que seus administradores e membros do corpo docente criem e gerenciem laboratórios. Esses usuários (normalmente do inquilino Azure AD associado à sua assinatura) são atribuídos à função de Criador do Laboratório dentro da conta do laboratório.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Você quer dar aos criadores de laboratório a capacidade de salvar imagens que podem ser compartilhadas entre laboratórios?

Uma galeria de imagens compartilhadas é um repositório que você pode usar para salvar e compartilhar imagens. Se você tem várias classes que precisam das mesmas imagens, os criadores de laboratório podem criar a imagem uma vez e compartilhá-la em laboratórios. No entanto, para começar, você não precisa necessariamente de uma galeria de imagens compartilhadas, porque você sempre pode adicionar uma mais tarde.

Se você respondeu "sim" a esta pergunta, você precisa criar ou anexar uma galeria de imagens compartilhadas à sua conta de laboratório. Se você respondeu "eu não sei", você pode adiar essa decisão até mais tarde.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Quais imagens no Azure Marketplace seus laboratórios de sala de aula usarão?

O Azure Marketplace fornece centenas de imagens que você pode habilitar para que os criadores de laboratório possam usar a imagem para criar seu laboratório. Algumas imagens podem incluir tudo o que um laboratório já precisa. Em outros casos, você pode usar uma imagem como ponto de partida e, em seguida, o criador do laboratório pode personalizá-la instalando aplicativos ou ferramentas adicionais.

Se você não sabe quais imagens você precisará usar, você sempre pode voltar a isso mais tarde para habilitá-las. Além disso, a melhor maneira de ver quais imagens estão disponíveis é primeiro criar uma conta de laboratório. Isso lhe dá acesso, para que você possa rever a lista de imagens disponíveis e seus conteúdos.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>As máquinas virtuais do laboratório precisam ter acesso a outros recursos do Azure ou no local?

Quando você configura uma conta de laboratório, você também tem a opção de observar uma rede virtual. Para decidir se você precisa disso, considere as seguintes perguntas:

- **Você precisa fornecer acesso a um servidor de licenciamento?**
  
   Se você planeja usar imagens do Azure Marketplace, o custo da licença do sistema operacional é empacotado nos preços dos serviços de laboratório. Portanto, você não precisa fornecer licenças para o próprio sistema operacional. No entanto, para softwares adicionais e aplicativos que são instalados, você precisa fornecer uma licença conforme apropriado.

- **As VMs do laboratório precisam ter acesso a outros recursos locais, como um compartilhamento de arquivos ou banco de dados?**

   Você cria uma rede virtual para fornecer acesso aos recursos locais, normalmente usando um gateway de rede virtual site-a-site. Se você não tiver uma rede virtual configurada, você precisa investir mais tempo para isso.

- **As VMs do laboratório precisam ter acesso a outros recursos do Azure que estão localizados dentro de uma rede virtual?**

   Se você precisa acessar recursos do Azure que *não* estão protegidos dentro de uma rede virtual, então você pode acessar esses recursos através da internet pública, sem fazer nenhum peering.

Se você respondeu "sim" a uma ou mais perguntas, então você precisará espiar a conta do laboratório para uma rede virtual. Se você respondeu "eu não sei", então você pode adiar essa decisão até mais tarde. Você sempre pode optar por espiar uma rede virtual depois de criar a conta de laboratório.

## <a name="set-up-your-lab-account"></a>Configure sua conta de laboratório

Depois de entender os requisitos da sua conta de laboratório, você está pronto para configurá-la.

1. **Crie sua conta de laboratório.** Consulte o tutorial sobre a [criação de uma conta de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) para obter instruções.

   Quando você está criando uma conta de laboratório, você pode achar útil se familiarizar com os recursos do Azure envolvidos. Para obter mais informações, consulte os seguintes artigos:

   - [Assinatura](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupo de recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Conta de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selecionando uma região e local](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Orientação de nomeação para recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Adicione os usuários à função de criador do laboratório.** Para obter instruções, consulte [adicionar os usuários à função de criador do laboratório.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)

   Além disso, para obter mais informações sobre as diferentes funções que podem ser atribuídas aos usuários que gerenciarão contas de laboratório e laboratórios, consulte o [guia sobre o gerenciamento de identidade](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Conecte-se a uma rede virtual de pares.** Para obter instruções, consulte [conectar a rede do seu laboratório com uma rede virtual de pares.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)

   Você também pode precisar consultar instruções sobre [a configuração do intervalo de endereços vMs do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Habilite e revise imagens.** Para obter instruções, [consulte ativar imagens do Azure Marketplace para criadores de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Para revisar o conteúdo de cada imagem do Azure Marketplace, selecione o nome da imagem. Por exemplo, a captura de tela a seguir mostra os detalhes da imagem VM do Ubuntu Data Science:

   ![Captura de tela de imagens do Review Azure Marketplace](../media/setup-guide/review-marketplace-images.png)

   Se você tiver uma galeria de imagens compartilhadas anexada à sua conta de laboratório e quiser permitir que imagens personalizadas sejam compartilhadas pelos criadores do laboratório, complete etapas semelhantes às mostradas na captura de tela a seguir:

   ![Captura de tela de habilitação de imagens personalizadas em uma galeria de imagens compartilhadas](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar contas de laboratório](how-to-manage-lab-accounts.md)

- [Guia de configuração do laboratório de sala de aula](setup-guide.md)
