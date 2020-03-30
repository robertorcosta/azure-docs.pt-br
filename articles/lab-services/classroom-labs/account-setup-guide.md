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
ms.openlocfilehash: 7b9ef32f16369bbb6c5bb69ef500ec8bb90fde20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370867"
---
# <a name="lab-account-setup-guide"></a>Guia de configuração de conta de laboratório

O primeiro passo que precisa ser concluído pelos administradores é configurar uma conta de laboratório dentro de sua assinatura do Azure.  Uma conta de laboratório é um recipiente para seus laboratórios de sala de aula e leva apenas alguns minutos para configurar.

## <a name="understand-your-schools-lab-account-requirements"></a>Entenda os requisitos da conta de laboratório da sua escola

Para entender como configurar sua conta de laboratório com base nas necessidades da sua escola, você deve considerar as seguintes perguntas:

**Tenho acesso a uma assinatura do Azure?**

Para criar uma conta de laboratório, você precisará acessar uma assinatura do Azure configurada para sua escola; sua escola pode ter uma ou mais assinaturas.  Uma assinatura é usada para gerenciar o faturamento e a segurança de todos os seus recursos do Azure\serviços que são usados dentro dele, incluindo contas de laboratório.

**Quantas contas de laboratório precisam ser criadas?**

Para começar rapidamente, uma abordagem razoável é criar uma única conta de laboratório e, posteriormente, criar contas adicionais de laboratório, conforme necessário.  Por exemplo, você pode eventualmente evoluir para ter uma conta de laboratório por departamento.

**Quem devem ser os donos e colaboradores da conta do laboratório?**

Seus administradores são normalmente os proprietários\contribuintes para uma conta de laboratório, uma vez que eles são responsáveis por gerenciar as políticas que se aplicam a todos os laboratórios contidos na conta do laboratório.  A pessoa que cria a conta do laboratório é automaticamente uma proprietária.  Você pode adicionar proprietários adicionais\contribuintes (normalmente do inquilino AAD associado à sua assinatura) para ajudar a gerenciar uma conta de laboratório, atribuindo a função Proprietário\Contribuinte no nível da conta do laboratório.

**Quem será autorizado a criar\gerenciar laboratórios?**

Você pode optar por ter seus administradores e\ou membros do corpo docente para criar e gerenciar laboratórios; esses usuários (normalmente do inquilino AAD associado à sua assinatura) são atribuídos à função de Criador do Laboratório dentro da conta do laboratório.

**Você quer dar aos criadores de laboratório a capacidade de salvar imagens que podem ser compartilhadas entre laboratórios?**

Uma Galeria de Imagens Compartilhadas é um repositório que você pode usar para salvar e compartilhar imagens.  A vantagem disso é que se você tem várias classes que precisam das mesmas imagens, os criadores de laboratórios podem criar a imagem uma vez e compartilhá-la em laboratórios.  No entanto, para começar, é perfeitamente razoável começar sem uma Galeria de Imagens Compartilhadas; e, você sempre pode optar por adicionar um mais tarde.

Se você respondeu 'Sim' a esta pergunta, então você precisará criar e\ou anexar uma Galeria de Imagens Compartilhadas à sua conta de laboratório.  Se você respondeu, "Eu não sei", então você pode adiar essa decisão até mais tarde.

Quando você tem uma Galeria de Imagens Compartilhadas anexada à sua conta de laboratório

**Quais imagens no Azure Marketplace seus laboratórios de sala de aula usarão?**

O Azure Marketplace fornece centenas de imagens que você pode habilitar para que os criadores de laboratório possam usar a imagem para criar seu laboratório.  Algumas imagens podem incluir tudo o que um laboratório já precisa.  Em outros casos, você pode usar uma imagem como ponto de partida e, em seguida, o criador do laboratório pode personalizá-la instalando aplicativos adicionais, ferramentas, etc.

Se você não sabe quais imagens você precisará usar, você sempre pode voltar a isso mais tarde para habilitá-las.  Além disso, a melhor maneira de ver quais imagens estão disponíveis é primeiro criar uma conta de laboratório – isso lhe dará acesso para que você possa rever a lista de imagens disponíveis e seus conteúdos.  Mais informações são fornecidas abaixo.
  
**As máquinas virtuais (VMs) do laboratório precisam ter acesso a outros recursos do Azure ou on-prem?**

Quando você configura uma conta de laboratório, você também tem a opção de fazer um peer com uma rede virtual (VNet).  Para decidir se você precisa consultar um VNet, considere as seguintes perguntas:

- **Você precisa fornecer acesso a um servidor de licenciamento?**
  
   Se você planeja usar imagens do Azure Marketplace, o custo da licença do SO é empacotado nos preços dos Serviços de Laboratório, portanto você *não* precisa fornecer licenças para o próprio SO.  No entanto, para softwares adicionais\aplicativos instalados, você precisará fornecer uma licença conforme apropriado.

- **As VMs do laboratório precisam ter acesso a outros recursos on-prem, como um compartilhamento de arquivos, banco de dados, etc.?**

   Um VNet precisa ser criado para fornecer acesso a recursos on-prem, normalmente usando um Gateway de Rede Virtual site-to-site.  Se você não tiver um VNet configurado, será necessário investir um tempo adicional para isso.  Mais informações sobre como configurá-lo são fornecidas abaixo.

- **As VMs do laboratório precisam ter acesso a outros recursos do Azure que estão localizados dentro de um VNet?**

    Se você precisar de acesso aos recursos do Azure que *não* estão protegidos dentro de um VNet, então você pode acessar esses recursos através da internet pública sem fazer nenhum peering.

    Se você respondeu 'Sim' a uma ou mais perguntas, então você precisará olhar a conta do laboratório para um VNet.  Se você respondeu: "Eu não sei", então você pode adiar essa decisão até mais tarde, já que você sempre pode optar por um VNet depois de criar a conta do laboratório.

## <a name="set-up-your-lab-account"></a>Configure sua conta de laboratório

Uma vez que você entenda os requisitos para sua conta de laboratório, você está pronto para configurá-la.  Siga os links nesta seção para ver como configurar sua conta de laboratório:

1. **Crie sua conta de laboratório**

   Consulte o tutorial sobre a [criação de uma conta de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) para obter instruções.

   Ao criar uma conta de laboratório, você pode achar útil se familiarizar com os recursos do Azure envolvidos; consulte a lista a seguir para obter mais informações e orientações sobre a criação desses recursos:

   - [Assinatura](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Grupo de recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Conta de laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Laboratório de Sala de Aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Selecionando uma região\Localização](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations)
   - [Orientação de nomeação para recursos](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Adicionar usuários à função de Criador de Laboratório**

   Consulte o tutorial sobre como [adicionar usuários à função De Criador do Laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) para obter instruções.

   Além disso, para obter mais informações sobre as diferentes funções que podem ser atribuídas aos usuários que gerenciarão contas de laboratório e laboratórios, consulte o [guia sobre o gerenciamento de identidade](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Conecte-se a um VNet peer**

   Consulte o guia de como conectar a [rede do seu laboratório com um VNet de pares](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) para obter instruções.

   Você também pode precisar consultar instruções sobre [a configuração do intervalo de endereços vMs do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab).

4. **Habilitar e revisar imagens**

    Consulte o guia de como [habilitar imagens do Marketplace para os criadores de laboratório para](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images) obter instruções.

    Para revisar o conteúdo de cada imagem do Marketplace, clique no nome da imagem.  Por exemplo, consulte a captura de tela a seguir que mostra os detalhes da imagem vM do Ubuntu Data Science:

    ![Revisar imagens do Marketplace](../media/setup-guide/review-marketplace-images.png)

    Se você tiver uma Galeria de Imagens Compartilhadas anexada à sua conta de laboratório e quiser permitir que imagens personalizadas sejam compartilhadas pelos criadores do laboratório, você precisará concluir etapas semelhantes como mostrada na captura de tela a seguir:

    ![Habilitando imagens personalizadas na Galeria de Imagens Compartilhadas](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Gerenciar contas de laboratório](how-to-manage-lab-accounts.md)

- [Guia de configuração do laboratório de sala de aula](setup-guide.md)
