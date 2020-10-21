---
title: Obtenha suporte para o portal do Marketplace comercial no Partner Center
description: Saiba mais sobre suas opções de suporte no Partner Center, incluindo como arquivar uma solicitação de suporte.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/18/2020
ms.openlocfilehash: 983f7e400a33cb36f30505e5a8a28811d9d4cdfb
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283538"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Suporte para o programa do Marketplace comercial no Partner Center

A Microsoft fornece suporte para uma ampla variedade de produtos e serviços. Encontrar a equipe de suporte correta é importante para garantir uma resposta apropriada e oportuna. Considere os seguintes cenários, que podem ajudá-lo a rotear sua consulta para a equipe apropriada:

- Se você for um Publicador e tiver uma pergunta de um cliente, peça ao cliente para solicitar suporte usando os links de suporte no [portal do Azure](https://portal.azure.com/).
- Se você for um Publicador e tiver uma pergunta relacionada ao seu aplicativo ou serviço, examine as opções de suporte a seguir.

## <a name="support-options-for-publishers"></a>Opções de suporte para Publicadores

1. Entre no programa do [Marketplace comercial no Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) com sua conta corporativa (se você ainda não tiver feito isso, será necessário [criar uma conta do Partner Center](partner-center-portal/create-account.md).

2. No menu superior no lado direito da página, selecione o ícone de **suporte** .

3. O painel **ajuda e suporte** será exibido no lado direito da página.

   ![Menu suspenso de suporte](./media/support/commercial-marketplace-support-pane.png)

    Ou vá até o painel **página inicial** e selecione **ajuda e suporte**.

   ![Ajuda e suporte da Home Page](./media/support/homepage-help-support.png)

4. Selecione a **[documentação](../index.yml)** para examinar as respostas abrangentes para perguntas e recursos.

5. Selecione **[Fórum da comunidade de parceiros do Marketplace](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/bd-p/2222)** para responder suas perguntas, aproveitando o conhecimento de outros publicadores da Microsoft.

6. Selecione **[ajuda adicional](https://aka.ms/marketplacepublishersupport)** para abrir um **novo** tíquete de solicitação de suporte.  

## <a name="how-to-open-a-support-ticket"></a>Como abrir um tíquete de suporte

Agora você está pronto para abrir um tíquete de suporte na tela de **ajuda e suporte** .

![Ajuda e suporte](./media/support/help-and-support.png)

>[!Note]
>Se você estiver conectado ao Partner Center, receberá uma experiência melhor com o suporte.

**Opção 1:** Insira palavras-chave como: Marketplace, aplicativo do Azure, oferta de SaaS, gerenciamento de conta, gerenciamento de Lead, problema de implantação, pagamento, etc.

**Opção 2:** Procurar tópicos-> selecione **categoria** = Marketplace comercial – > selecione o **tópico** apropriado e **subtópico**.

Depois de encontrar o tópico de sua escolha, selecione **examinar soluções**.

![Próxima etapa](./media/support/next-step.png)

As seguintes opções ficarão disponíveis:

- Para selecionar um tópico diferente, selecione um link de tópico diferente em **problema selecionado**.
- Examine a descrição desse problema, se disponível.  É o texto mostrado acima das **etapas recomendadas**.
- Examine **as etapas recomendadas**, se disponíveis.
- Examine os **documentos recomendados**, se disponíveis.

![Soluções recomendadas](./media/support/recommended-solutions.png)

Se você não conseguir encontrar sua resposta em **soluções recomendadas**, selecione **fornecer detalhes do problema**. Preencha todos os campos obrigatórios para acelerar o processo de resolução e selecione **Enviar**.

>[!Note]
>Se você não tiver feito logon no Partner Center e o tópico exigir autenticação, será solicitado que você faça logon antes de continuar.  Para tópicos públicos, a autenticação não é necessária.

## <a name="track-your-existing-support-requests"></a>Acompanhe suas solicitações de suporte existentes

Para examinar todos os tíquetes abertos e fechados, vá para o **Marketplace comercial** na barra de navegação à esquerda e selecione **suporte**.

## <a name="record-issue-details-with-a-har-file"></a>Registrar detalhes do problema com um arquivo HAR

Para ajudar os agentes de suporte a solucionar o problema, considere anexar um arquivo HAR (formato de arquivo HTTP) ao seu tíquete de suporte. Os arquivos HAR são logs de solicitações de rede em um navegador da Web.

> [!WARNING]
> Os arquivos HAR podem registrar dados confidenciais sobre sua conta do Partner Center.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge e Google Chrome

Para gerar um arquivo HAR usando **o Microsoft Edge ou o** **Google Chrome**:

1. Vá para a página da Web onde você está enfrentando o problema.
2. No canto superior direito da janela, selecione o ícone de reticências e, **em seguida, ferramentas de**  >  **desenvolvedor**ferramentas. Você pode pressionar F12 como um atalho.
3. No painel Ferramentas de desenvolvedor, selecione a guia **rede** .
4. Selecione **parar a gravação do log de rede** e **desmarque** para remover os logs existentes. O ícone de registro ficará cinza.

    ![Como remover os logs existentes no Microsoft Edge ou no Google Chrome](media/support/chromium-stop-clear-session.png)

5. Selecione **registrar log de rede** para iniciar a gravação. Quando você iniciar a gravação, o ícone de registro ficará vermelho.

    ![Como iniciar a gravação no Microsoft Edge ou no Google Chrome](media/support/chromium-start-session.png)

6. Reproduza o problema que você deseja solucionar.
7. Depois de reproduzir o problema, selecione **parar de gravar o log de rede**.
8. Selecione **Exportar Har**, marcado com um ícone de seta para baixo e salve o arquivo.

    ![Como exportar um arquivo HAR no Microsoft Edge ou no Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Para gerar um arquivo HAR usando o **Mozilla Firefox**:

1. Vá para a página da Web onde você está enfrentando o problema.
1. No canto superior direito da janela, selecione o ícone de reticências e, em seguida, ferramentas de alternância de **desenvolvedor da Web**  >  **Toggle Tools**. Você pode pressionar F12 como um atalho.
1. Selecione a guia **rede** e, em seguida, selecione **limpar** para remover os logs existentes.

    ![Como remover os logs existentes no Mozilla Firefox](media/support/firefox-clear-session.png)

1. Reproduza o problema que você deseja solucionar.
1. Depois de reproduzir o problema, selecione **Har exportar/importar**  >  **salvar tudo como Har**.

    ![Como exportar um arquivo HAR no Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Para gerar um arquivo HAR usando o **Safari**:

1. Habilitar as ferramentas de desenvolvedor no Safari: selecione Preferências do **Safari**  >  **Preferences**. Vá para a guia **avançado** e, em seguida, selecione **Mostrar menu desenvolver na barra de menus**.
1. Vá para a página da Web onde você está enfrentando o problema.
1. Selecione **Desenvolver** e, em seguida, selecione **Mostrar Inspetor da Web**.
1. Selecione a guia **rede** e, em seguida, selecione **limpar itens de rede** para remover os logs existentes.

    ![Como remover os logs existentes no Safari](media/support/safari-clear-session.png)

1. Reproduza o problema que você deseja solucionar.
1. Depois de reproduzir o problema, selecione **Exportar** e salve o arquivo.

    ![Como exportar um arquivo HAR no Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no Marketplace comercial](partner-center-portal/update-existing-offer.md)
