---
title: Codificar ou decodificar arquivos planos
description: Codificar ou decodificar arquivos simples para Enterprise Integration com os Aplicativos Lógicos do Azure e o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 2d6182ba01507c2fb361628e01bb52e1ea821f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152645"
---
# <a name="encode-or-decode-flat-files-with-azure-logic-apps-and-enterprise-integration-pack"></a>Codificar ou decodificar arquivos simples com os Aplicativos Lógicos do Azure e o Enterprise Integration Pack

Talvez você queira codificar o conteúdo XML antes de enviá-lo para um parceiro de negócios em um cenário B2B (entre empresas). Em um aplicativo lógico, você pode usar o conector de codificação de arquivo simples para fazer isso. O aplicativo lógico criado por você pode obter seu conteúdo XML de diversas fontes, incluindo de um gatilho de solicitação HTTP ou de outro aplicativo, ou até mesmo de um dos vários [conectores](../connectors/apis-list.md). Para obter mais informações sobre aplicativos lógicos, consulte a documentação dos [aplicativos lógicos](logic-apps-overview.md "Saiba mais sobre os aplicativos logic").  

## <a name="create-the-flat-file-encoding-connector"></a>Como criar o conector de codificação de arquivo simples
Execute estas etapas para adicionar um conector de codificação de arquivo simples ao seu aplicativo lógico.

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração.](logic-apps-enterprise-integration-accounts.md "Aprenda a vincular uma conta de integração a um aplicativo Logic") Esta conta contém o esquema que será usado para codificar os dados XML.  

1. No Logic App Designer, adicione o **gatilho Quando uma solicitação HTTP é recebida** no seu aplicativo lógico.

1. Adicione uma ação de codificação de arquivo simples, desta maneira:

   a. Selecione o sinal **de mais.**

   b. Selecione o link **Adicionar uma ação** (que aparece após a seleção do sinal de adição).

   c. Na caixa de pesquisa, insira *Simples* para filtrar todas as ações para aquela que você deseja usar.

   d. Escolha a ação **Codificação de Arquivo Simples** na lista.   

      ![Captura de tela da opção Codificação de Arquivo Simples](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Na caixa de texto **Codificação de Arquivo Simples**, selecione a caixa de texto **Conteúdo**.  

   ![Captura de tela da caixa de texto Conteúdo](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  

1. Selecione a marca body como o conteúdo que será codificado. A marca body preencherá o campo de conteúdo.     

   ![Captura de tela da marca body](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  

1. Escolha a caixa de listagem **Nome do Esquema** e escolha o esquema que você deseja usar para codificar o conteúdo da entrada.    

   ![Captura de tela da caixa de listagem Nome do Esquema](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  

1. Salve seu trabalho.

   ![Captura de tela do ícone Salvar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Neste ponto, você já configurou seu conector de codificação de arquivo simples. Em um aplicativo real, convém armazenar os dados codificados em um aplicativo de linha de negócios, como o Salesforce. Ou você pode enviar os dados codificados para um parceiro comercial. Você pode adicionar facilmente uma ação para enviar a saída da ação de codificação para o Salesforce ou seu parceiro comercial usando qualquer um dos outros conectores fornecido.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML no corpo da solicitação.  

## <a name="create-the-flat-file-decoding-connector"></a>Como criar o conector de decodificação de arquivo simples

> [!NOTE]
> Para concluir estas etapas, você precisa ter um arquivo de esquema já carregado na conta de integração.

1. No Logic App Designer, adicione o **gatilho Quando uma solicitação HTTP é recebida** no seu aplicativo lógico.

1. Adicione a ação de decodificação de arquivo simples, desta maneira:

   a. Selecione o sinal **de mais.**

   b. Selecione o link **Adicionar uma ação** (que aparece após a seleção do sinal de adição).

   c. Na caixa de pesquisa, insira *Simples* para filtrar todas as ações para aquela que você deseja usar.

   d. Escolha a ação **Decodificação de Arquivo Simples** na lista.   

      ![Captura de tela da opção Decodificação de Arquivo Simples](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   

1. Selecione o controle **Conteúdo** . Isso produz uma lista do conteúdo das etapas anteriores que você pode usar como o conteúdo para decodificação. Observe que o *Corpo* da solicitação HTTP de entrada estará disponível para uso como o conteúdo para decodificação. Você também pode inserir o conteúdo para decodificação diretamente no controle **Conteúdo** .     

1. Selecione a marca *Body* . Observe que agora a marca body está no controle **Conteúdo** .

1. Selecione o nome do esquema que você deseja usar para decodificar o conteúdo. A captura de tela a seguir mostra que *OrderFile* é o nome do esquema selecionado. Esse nome de esquema foi carregado anteriormente na conta de integração.

   ![Captura de tela da caixa de diálogo Decodificação de Arquivo Simples](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png) 

1. Salve seu trabalho.  

   ![Captura de tela do ícone Salvar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Neste ponto, você já configurou seu conector de decodificação de arquivo simples. Em um aplicativo real, convém armazenar os dados decodificados em um aplicativo de linha de negócios, como o Salesforce. Você pode adicionar facilmente uma ação para enviar a saída da ação de decodificação para o Salesforce.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML que você deseja decodificar no corpo da solicitação.  

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Pacote de Integração Empresarial](logic-apps-enterprise-integration-overview.md "Conheça o Enterprise Integration Pack").  

