---
title: Solução de problemas - Fabricante de QnA
description: A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço mais rapidamente e com melhores resultados.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: e002efe74bf7bcd3d944b01b0a25a731a2db3f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284255"
---
# <a name="troubleshooting-for-qna-maker"></a>Solução de problemas para o Fabricante de QnA

A lista com curadoria das perguntas mais frequentes sobre o serviço QnA Maker irá ajudá-lo a adotar o serviço mais rapidamente e com melhores resultados.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Gerenciar previsões

<details>
<summary><b>Como posso melhorar o desempenho de throughput para previsões de consulta?</b></summary>

**Resposta**: Os problemas de desempenho do Throughput indicam que você precisa escalar para o seu serviço de Aplicativo e sua Pesquisa Cognitiva. Considere adicionar uma réplica à sua Pesquisa Cognitiva para melhorar o desempenho.

Saiba mais sobre [os níveis de preços](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Como obter o ponto final do serviço QnAMaker</b></summary>

**Resposta**: O ponto final do serviço QnAMaker é útil para fins de depuração quando você entrar em contato com o QnAMaker Support ou userVoice. O ponto final é uma https://your-resource-name.azurewebsites.netURL neste formulário: .

1. Acesse o serviço do QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o Serviço de Aplicativo associado ao recurso QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione o Serviço de Aplicativo do QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. O URL de ponto final está disponível na seção Visão geral

    ![Ponto final de QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Gerenciar a base de conhecimento

<details>
<summary><b>Eu apaguei acidentalmente uma parte do meu QnA Maker, o que devo fazer?</b></summary>

**Resposta**: Não exclua nenhum dos serviços do Azure criados junto com o recurso QnA Maker, como Pesquisa ou Web App. Estes são necessários para que o QnA Maker funcione, se você excluir um, o QnA Maker deixará de funcionar corretamente.

Todas as exclusões são permanentes, incluindo pares de perguntas e respostas, arquivos, URLs, perguntas e respostas personalizadas, bases de conhecimento ou recursos do Azure. Certifique-se de exportar sua base de conhecimento da página **Configurações** antes de excluir qualquer parte de sua base de conhecimento.

</details>

<details>
<summary><b>Por que minhas URLs/arquivos não estão extraindo pares de pergunta-resposta?</b></summary>

**Resposta**: É possível que o QnA Maker não possa extrair automaticamente algum conteúdo de perguntas e respostas (QnA) de URLs faq válidos. Nesses casos, você pode colar o conteúdo de QnA em um arquivo .txt e verificar se a ferramenta pode ingeri-lo. Como alternativa, você pode adicionar conteúdo de modo editorial à sua base de dados de conhecimento no [Portal do QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Qual o tamanho da base de dados de conhecimento que posso criar?</b></summary>

**Resposta**: O tamanho da base de conhecimento depende da pesquisa SKU do Azure que você escolher ao criar o serviço QnA Maker. Leia [este texto](./Tutorials/choosing-capacity-qnamaker-deployment.md) para obter mais detalhes.

</details>

<details>
<summary><b>Por que eu não vejo nada na lista suspensa quando tento criar uma nova base de dados de conhecimento?</b></summary>

**Resposta**: Você ainda não criou nenhum serviço qnA maker no Azure. Leia [este artigo](./How-To/set-up-qnamaker-service-azure.md) para saber como fazer isso.

</details>

<details>
<summary><b>Como posso compartilhar uma base de dados de conhecimento com outras pessoas?</b></summary>

**Resposta**: O compartilhamento funciona ao nível de um serviço QnA Maker, ou seja, todas as bases de conhecimento no serviço serão compartilhadas. Leia [este texto](./How-To/collaborate-knowledge-base.md) sobre como colaborar em uma base de dados de conhecimento.

</details>

<details>
<summary><b>É possível compartilhar uma base de conhecimento com um colaborador que não está no mesmo locatário do AAD, para modificar uma base de Conhecimento?</b></summary>

**Resposta**: O compartilhamento é baseado no Controle de Acesso Baseado em Papéis (RBAC) do Azure. Se pode compartilhar _qualquer_ recurso do Azure com outro usuário, você também pode compartilhar o QnA Maker.

</details>

<details>
<summary><b>Se você tiver um Plano de Serviço de Aplicativo com 5 bases de conhecimento QnAMaker. Você pode atribuir direitos de leitura/gravação a 5 usuários diferentes para que cada um deles possa acessar apenas 1 base de conhecimento QnAMaker?</b></summary>

**Resposta**: Você pode compartilhar um serviço QnAMaker inteiro, não bases de conhecimento individuais.

</details>

<details>
<summary><b>Como alterar a mensagem padrão quando nenhuma correspondência boa for encontrada?</b></summary>

**Resposta**: A mensagem padrão faz parte das configurações do seu serviço de Aplicativo.
- Vá até o recurso do seu Serviço de Aplicativo no portal do Azure

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Clique na opção **Configurações**

![qnamaker appservice settings](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Alterar o valor da configuração **DefaultAnswer**
- Reinicie o serviço Aplicativo

![qnamaker appservice restart](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Por que meu link do SharePoint não está sendo extraído?</b></summary>

**Resposta**: Consulte [os locais de origem dos dados](./Concepts/knowledge-base.md#data-source-locations) para obter mais informações.

</details>

<details>
<summary><b>As atualizações que fiz à minha base de conhecimento não são refletidas na publicação. Por que não?</b></summary>

**Resposta**: Toda operação de edição, seja em uma atualização de tabela, teste ou configuração, precisa ser salva antes de ser publicada. Certifique-se de clicar no botão **Salvar e treinar** após cada operação de edição.

</details>

<details>
<summary><b>A base de dados de conhecimento oferece suporte a dados avançados ou multimídia?</b></summary>

**Resposta:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extração automática multimídia para arquivos e URLs

* URLS - recurso limitado de conversão HTML-para-Markdown.
* Arquivos - não suportados

#### <a name="answer-text-in-markdown"></a>Responder texto na marcação
Uma vez que os conjuntos de QnA estejam na base de conhecimento, você pode editar o texto de marcação de uma resposta para incluir links para mídia disponíveis em URLs públicas.


</details>

<details>
<summary><b>O QnA Maker dá suporte para idiomas além do inglês?</b></summary>

**Resposta**: Veja mais detalhes sobre [idiomas suportados](./Overview/languages-supported.md).

Se você tiver conteúdo de vários idiomas, crie um serviço separado para cada idioma.

</details>

## <a name="manage-service"></a>Gerenciar serviço

<details>
<summary><b>Quando devo reiniciar o serviço de aplicativo?</b></summary>

**Resposta**: Atualize o serviço do aplicativo quando o ícone de precaução estiver ao lado do valor da versão para a base de conhecimento na tabela **de teclas Endpoint** na [página](https://www.qnamaker.ai/UserSettings) **Configurações** do usuário .

</details>

<details>
<summary><b>Eu excluí meu serviço de pesquisa existente. Como posso consertar isso?</b></summary>

**Resposta**: Se você excluir um índice de Pesquisa Cognitiva Azure, a operação será final e o índice não poderá ser recuperado.

</details>

<details>
<summary><b>Eu excluí `testkb` meu índice no meu serviço de pesquisa. Como posso consertar isso?</b></summary>

**Resposta:** Seus dados antigos não podem ser recuperados. Crie um novo recurso do QnA Maker e crie sua base de conhecimento novamente.

</details>

<details>
<summary><b>Quando devo atualizar minhas chaves de ponto de extremidade?</b></summary>

**Resposta**: Atualize suas teclas de ponto final se você suspeitar que elas foram comprometidas.

</details>

<details>
<summary><b>Posso usar o mesmo recurso de pesquisa cognitiva do Azure para bases de conhecimento usando vários idiomas?</b></summary>

**Resposta**: Para usar várias bases de conhecimento e idioma, o usuário precisa criar um recurso QnA Maker para cada idioma. Isso criará um serviço de pesquisa azure separado por idioma. Misturar bases de dados de conhecimento de idiomas diferentes em um único serviço de pesquisa do Azure resultará em resultados com relevância degradada.

</details>

<details>
<summary><b>Como posso alterar o nome do recurso de Pesquisa Cognitiva Do Azure usado pelo QnA Maker?</b></summary>

**Resposta**: O nome do recurso azure Cognitive Search é o nome de recurso do QnA Maker com algumas letras aleatórias anexadas no final. Isso torna difícil distinguir entre vários recursos de pesquisa do QnA Maker. Crie um serviço de pesquisa separado (nomeando-o da maneira que você gostaria) e conecte-o ao seu Serviço QnA. As etapas são semelhantes às etapas que você precisa fazer para [atualizar uma pesquisa do Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Quando o QnA Maker retorna `Runtime core is not initialized,` como conserto?</b></summary>

**Resposta**: O espaço em disco para o seu serviço de aplicativo pode estar cheio. Etapas para corrigir o espaço do disco:

1. No [portal Azure,](https://portal.azure.com)selecione o serviço de aplicativo do Fabricante QnA e interrompa o serviço.
1. Enquanto ainda estiver no serviço do App, selecione **Ferramentas de Desenvolvimento,** em seguida, **Ferramentas Avançadas,** em seguida, **Go**. Isso abre uma nova janela do navegador.
1. Selecione **O console Debug**e, em seguida, **CMD** para abrir uma ferramenta de linha de comando.
1. Navegue até o _site/wwwroot/Data/QnAMaker/_ diretório.
1. Remova todas as pastas `rd`cujo nome começa com .

    **Não exclua** o seguinte:

    * Arquivo KbIdToRankerMappings.txt
    * Arquivo EndpointSettings.json
    * Pasta EndpointKeys

1. Inicie o serviço de aplicativo.
1. Acesse sua base de conhecimento para verificar se funciona agora.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integrar com outros serviços, incluindo Bots

<details>
<summary><b>Preciso usar o Bot Framework para usar o QnA Maker?</b></summary>

**Resposta**: Não, você não precisa usar o [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) com O Criador de QnA. No entanto, o QnA Maker é oferecido como um dos vários modelos no [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). O Serviço de Bot permite o desenvolvimento rápido de bots inteligentes por meio da Estrutura de Bot da Microsoft e é executado em um ambiente sem servidor.

</details>

<details>
<summary><b>Como posso criar um novo bot com o QnA Maker?</b></summary>

**Resposta**: Siga as instruções [nesta](./Quickstarts/create-publish-knowledge-base.md) documentação para criar seu Bot com o Azure Bot Service.

</details>

<details>
<summary><b>Como usar uma base de conhecimento diferente com um serviço de bot azure existente?</b></summary>

**Resposta**: Você precisa ter as seguintes informações sobre sua base de conhecimento:

* ID da base de conhecimento.
* O nome de subdomínio personalizado do ponto `host`final publicado da base de conhecimento, conhecido como , encontrado na página **Configurações** após a publicação.
* A tecla de ponto final publicado da base de conhecimento - encontrada na página **Configurações** após a publicação.

Com essas informações, acesse o serviço de aplicativo do seu bot no portal Azure. Em **Configurações -configuração > configuração - > configurações de aplicativos,** altere esses valores.

A chave de ponto final da `QnAAuthkey` base de conhecimento é rotulada no serviço ABS.

</details>

<details>
<summary><b>Dois ou mais aplicativos de clientes podem compartilhar uma base de conhecimento?</b></summary>

**Resposta**: Sim, a base de conhecimento pode ser consultada a partir de qualquer número de clientes. Se a resposta da base de conhecimento parecer lenta ou com tempo de saída, considere atualizar o nível de serviço para o serviço de aplicativo associado à base de conhecimento.

</details>

<details>
<summary><b>Como posso inserir o serviço QnA Maker no meu site?</b></summary>

**Resposta**: Siga estas etapas para incorporar o serviço QnA Maker como um controle de bate-papo web em seu site:

1. Crie seu bot de perguntas frequentes seguindo as instruções [aqui](./Quickstarts/create-publish-knowledge-base.md).
2. Habilite o chat da Web executando [estas](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) etapas

</details>

## <a name="data-storage"></a>Armazenamento de dados

<details>
<summary><b>Quais dados são armazenados e onde ele é armazenado?</b></summary>

**Resposta:**

Quando você cria seu serviço QnA Maker, você selecionou uma região do Azure. Suas bases de conhecimento e arquivos de log são armazenados nessa região.

</details>