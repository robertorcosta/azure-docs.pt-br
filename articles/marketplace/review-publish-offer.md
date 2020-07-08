---
title: Revisar e publicar uma oferta no Microsoft Commercial Marketplace
description: Use o Partner Center para enviar sua oferta para visualização, Visualizar sua oferta e, em seguida, publicá-la no Microsoft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/05/2020
ms.openlocfilehash: 910a886f02b386d7242bdd5e85f0710baa25408e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86090874"
---
# <a name="review-and-publish-an-offer-to-commercial-marketplace"></a>Revisar e publicar uma oferta no Marketplace comercial

Este artigo mostra como usar o Partner Center para enviar sua oferta para visualizar, Visualizar sua oferta e, em seguida, publicá-la no Microsoft Commercial Marketplace. Também abordaremos como verificar o status de publicação, pois ele prossegue pelas etapas de publicação. Você já deve ter criado uma oferta que deseja publicar.

## <a name="go-to-your-offer-in-commercial-marketplace"></a>Vá para sua oferta no Marketplace comercial

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **visão geral**do Marketplace comercial.
1. Na guia **visão geral** , em **ofertas**, um dos seguintes indicadores de status é exibido na coluna **status** para cada oferta.
 
    | Status | Descrição |
    | ------------ | ------------- |
    | Rascunho | A oferta foi criada, mas não está sendo publicada. |
    | Publicação em andamento | A oferta está funcionando de forma por meio do processo de publicação. |
    | Ação necessária | Descobrimos um problema crítico durante a certificação ou durante outra fase de publicação. |
    | Visualização | Certificamos a oferta, que agora aguarda uma verificação final pelo Publicador. Selecione **entrar em tempo real** para publicar a oferta ao vivo. |
    | Ao vivo | A oferta está ativa no Marketplace e pode ser vista e adquirida pelos clientes. |
    | Venda de parada pendente | O Publicador selecionou "parar venda" em uma oferta ou plano, mas a ação ainda não foi concluída. |
    | Não disponível no Marketplace | Uma oferta publicada anteriormente no marketplace foi removida. |
    |||

4. Na coluna **alias da oferta** , selecione a oferta que você deseja visualizar e publicar.

## <a name="submit-your-offer-to-preview"></a>Enviar sua oferta para visualização

1. Para enviar sua oferta para visualização, selecione **revisar e publicar** no canto superior direito do Portal. A página **revisar e publicar** é exibida.
1. Verifique se a coluna **status** de cada página diz **concluída**. Os três status possíveis são os seguintes:
   - **Não iniciado** – a página não foi tocada e precisa ser concluída.
   - **Incompleto** – a página não tem informações necessárias ou tem erros que precisam ser corrigidos. Você precisará voltar para a página e atualizá-la.
   - **Concluída** – a página está concluída. Todos os dados necessários foram fornecidos e não há erros.
1. Se qualquer uma das páginas tiver um status diferente de **concluído**, na coluna **página** , selecione o nome da página, corrija o problema, salve a página e, em seguida, selecione **revisar e publicar** novamente para retornar a esta página.
1. Depois que todas as páginas forem concluídas, na caixa **notas de certificação** , forneça instruções de teste para a equipe de certificação para garantir que seu aplicativo seja testado corretamente. Forneça notas suplementares úteis para entender seu aplicativo.
1. Para enviar a oferta para publicação, selecione **Publicar**. A página **visão geral da oferta** é exibida e mostra o status de publicação.

## <a name="validation-and-publishing-steps"></a>Etapas de validação e publicação

Depois de selecionar **publicar**, os processos de validação e publicação continuarão em ordem. O processo de publicação mais comum é mostrado nesta tabela:

| Fase | O que ocorre | 
| ------------ | ------------- | ------------- |
| Validação automatizada | Processamos um conjunto de validações automatizadas. | 
| Certificação | Realizamos validações manuais. | 
| Criação de visualização | A página de listagem de sua oferta de visualização está disponível para qualquer pessoa que tenha o link de visualização. Se sua oferta for vendida pela Microsoft (transformada), somente o público especificado na página **público de visualização** da sua oferta poderá comprar e acessar a oferta para teste. | 
| Aprovação do publicador | Enviamos um email com uma solicitação para você visualizar e aprovar sua oferta. | 
| Publicar | Executamos uma série de etapas para verificar se a oferta de visualização está publicada em tempo real no mercado comercial. | 
|||

## <a name="automated-validation-phase"></a>Fase de validação automatizada

A primeira etapa no processo de publicação é um conjunto de validações automatizadas. Cada etapa de validação corresponde a um recurso escolhido quando você criou a oferta. Cada verificação de validação deve ser concluída antes que a oferta possa avançar para a próxima etapa no processo de publicação.

- **Instalação do fluxo de compra da oferta (<10 min)**

   Nesta etapa, garantimos que sua oferta pode ser atendida quando adquirida por clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas vendidas pela Microsoft.
- **Validação de dados do Test Drive (aproximadamente 5 min)**

   Nesta etapa, validamos os dados fornecidos na página de configuração técnica da oferta. A funcionalidade do Test Drive é testada e aprovada. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

-   **Provisionamento de drive de teste (~ 30 min)**

    Nesta etapa, depois de validar os dados e a funcionalidade do seu test drive na etapa anterior, implantamos e replicamos instâncias do seu test drive para que elas estejam prontas para uso do cliente. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

-   **Validação e registro de gerenciamento de leads (<15 min)**

    Nesta etapa, confirmamos que o seu sistema de gerenciamento de leads pode receber clientes potenciais com base nos detalhes fornecidos na página **instalação da oferta** . Esta etapa só é aplicável para ofertas com gerenciamento de Lead habilitado.

## <a name="certification-phase"></a>Fase de certificação

Antes de ser publicado, as ofertas enviadas ao Marketplace comercial devem ser certificadas. As ofertas enviadas passam por testes rigorosos, alguns manuais automatizados e outros. Examine as [políticas de certificação do Marketplace comercial](https://aka.ms/commercial-marketplace-certification-policies) para saber mais.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação
Há três níveis de validação incluídos no processo de certificação para cada oferta enviada.
-   Elegibilidade de negócios do Publicador
-   Validação de conteúdo
-   Validação técnica

#### <a name="publisher-business-eligibility"></a>Elegibilidade de negócios do Publicador
Cada tipo de oferta verifica um conjunto de critérios de qualificação base que o Publicador deve atender. Os critérios de qualificação podem incluir o status do MPN do editor, as competências mantidas, os níveis de competência e assim por diante.

#### <a name="content-validation"></a>Validação de conteúdo

As informações inseridas quando você criou sua oferta são verificadas quanto à qualidade e relevância. Essas verificações revisarão suas entradas para o Marketplace listar detalhes, preços, disponibilidade, planos associados e assim por diante. Para atender aos critérios de listagem do Azure Marketplace e Microsoft AppSource, validaremos que sua oferta inclui:
-   Um título que descreve com precisão a oferta
-   Descrições bem escritas que fornecem uma visão geral completa e uma proposta de valor
-   Capturas de tela e vídeos de qualidade
-   Uma explicação de como a oferta utiliza plataformas e ferramentas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdo lendo as [políticas de listagem geral](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Validação técnica
Durante a validação técnica, a oferta (pacote ou binário) passa pelas verificações a seguir.
-   Verificação de malware
-   Chamadas de rede monitoradas
-   Pacote analisado
-   Verificação completa da funcionalidade da oferta

A oferta é testada em várias plataformas e versões para garantir que ela seja robusta.

### <a name="certification-failure-report"></a>Relatório de falha de certificação

Se sua oferta falhar em qualquer lista, técnica ou verificação de política, ou se você não estiver qualificado para enviar uma oferta desse tipo, enviaremos um relatório de falha de certificação por email para você.

Este relatório contém descrições de todas as políticas que falharam, juntamente com as notas de revisão. Examine este relatório de email, resolva quaisquer problemas, faça atualizações em sua oferta quando necessário e reenvie a oferta usando o [portal do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. Você pode reenviar a oferta quantas vezes forem necessárias até passar a certificação.

## <a name="preview-creation-phase"></a>Fase de criação de visualização

Durante a fase de criação de versão prévia, criamos uma versão de sua oferta que será acessível somente para o público especificado na página do **público de visualização** de sua oferta, se houver. A versão de visualização da sua oferta não estará disponível para ninguém fora do público de visualização até que você publique a oferta ao vivo.

> [!NOTE]
> Não use o público de visualização para dar às pessoas fora da visibilidade da sua organização uma oferta. Em vez disso, use a opção oferta privada. Neste ponto, sua oferta não foi totalmente testada e validada e não está pronta para distribuição externa. 

## <a name="publisher-signoff-phase"></a>Fase de aprovação do Publicador

Quando a oferta estiver pronta para você revisar e aprovar, enviaremos um email para solicitar que você examine e aprove sua versão prévia da oferta. Você também pode atualizar a página **visão geral da oferta** em seu navegador para ver se sua oferta atingiu a fase de aprovação do Publicador. Se tiver, o botão **Go Live** e os links de visualização estarão disponíveis.

A captura de tela a seguir mostra a página **visão geral da oferta** para uma oferta de SaaS. As etapas de validação que você verá nessa página variam de acordo com o tipo de oferta e as seleções feitas quando você criou a oferta.

![Ilustra a página Visão geral da oferta para uma oferta no Partner Center. O botão Go Live e os links de visualização são mostrados.](./partner-center-portal/media/publish-status-publisher-signoff.png)

**Para visualizar sua oferta e aprovação**
1. Na página **visão geral da oferta** , para visualizar sua oferta, selecione o link no botão **ir ao vivo** .
   > [!NOTE]
   > Haverá um link para a visualização do AppSource, visualização do Azure Marketplace ou ambos, dependendo das opções escolhidas ao criar sua oferta. Se você optar por vender sua oferta pela Microsoft, qualquer pessoa que tenha sido adicionada ao público de visualização poderá testar a aquisição e a implantação de sua oferta para garantir que ela atenda às suas necessidades durante esse estágio.

1. Se você quiser fazer alterações depois de visualizar a oferta, poderá editar e reenviar para publicar uma nova visualização. Para obter mais informações, consulte [atualizar uma oferta existente no Marketplace comercial](./partner-center-portal/update-existing-offer.md).

1. Depois de aprovar sua versão prévia, para publicar sua oferta ao vivo no mercado comercial, selecione **Go Live**.
   > [!TIP]
   > Se sua oferta já estiver ativa e disponível para o público no Marketplace, todas as atualizações que você fizer não entrarão em funcionamento até que você selecione **entrar em ativação**.

## <a name="publish-phase"></a>Fase de publicação

Agora que você optou por entrar em funcionamento com sua oferta, disponibilizando-a no mercado comercial, há uma série de verificações finais de validação que fazemos para garantir que a oferta ao vivo seja configurada da mesma forma que a versão prévia da oferta.

-   **Instalação do fluxo de compra da oferta (>10 min)**

    Nesta etapa, garantimos que sua oferta pode ser atendida quando adquirida por clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas vendidas pela Microsoft.
-   **Validação de dados do Test Drive (aproximadamente 5 min)**

    Nesta etapa, validamos os dados fornecidos na página de configuração técnica da oferta. A funcionalidade do Test Drive é testada e aprovada. Esta etapa é aplicável somente para ofertas com um test drive habilitado.

-   **Provisionamento de drive de teste (~ 30 min)**

      Nesta etapa, implantamos e replicamos instâncias do seu test drive para que elas estejam prontas para uso do cliente. Esta etapa é aplicável somente para ofertas com um test drive habilitado.
-   **Validação e registro de gerenciamento de leads (>15 min)**

    Nesta etapa, confirmamos que o seu sistema de gerenciamento de leads pode receber clientes potenciais com base nos detalhes fornecidos na página de **instalação** da oferta. Esta etapa só é aplicável para ofertas com gerenciamento de Lead habilitado.

-   **Publicação final (>30 minutos)**

    Nesta etapa, garantimos que sua oferta se torne publicamente disponível no Marketplace.

Depois que essas verificações de validação forem concluídas, sua oferta estará ativa no Marketplace.

## <a name="next-step"></a>Próxima etapa

[Acessar relatórios analíticos para o mercado comercial no Partner Center](./partner-center-portal/analytics.md)