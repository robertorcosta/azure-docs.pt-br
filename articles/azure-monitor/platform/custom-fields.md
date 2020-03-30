---
title: Campos personalizados no Monitor Azure (Preview) | Microsoft Docs
description: O recurso Campos personalizados do Azure Monitor permite que você crie seus próprios campos pesquisáveis a partir de registros em um espaço de trabalho do Log Analytics que adicionam às propriedades de um registro coletado.  Este artigo descreve o processo para criar um campo personalizado e fornece um passo a passo detalhado com um evento de exemplo.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: bfb0a73631564c96a4af745fe9d7540a3a84f9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655354"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Crie campos personalizados em um espaço de trabalho do Log Analytics no Azure Monitor (Preview)

> [!NOTE]
> Este artigo descreve como analisar dados de texto em um espaço de trabalho do Log Analytics à medida que são coletados. Recomendamos analisar os dados de texto em um filtro de consulta depois de coletados seguindo as orientações descritas em [dados de texto parse no Azure Monitor](../log-query/parse-text.md). Ele oferece várias vantagens sobre o uso de campos personalizados.

O recurso **Campos personalizados** do Azure Monitor permite que você amplie os registros existentes no espaço de trabalho do Log Analytics adicionando seus próprios campos pesquisáveis.  Os campos personalizados são populados automaticamente por meio dos dados extraídos de outras propriedades no mesmo registro.

![Visão geral](media/custom-fields/overview.png)

Por exemplo, o registro de exemplo abaixo tem dados úteis escondidos na descrição do evento. A extração desses dados em uma propriedade separada o torna disponível para ações como classificação e filtragem.

![Extrato de amostra](media/custom-fields/sample-extract.png)

> [!NOTE]
> Na Preview, você está limitado a 100 campos personalizados em seu workspace.  Esse limite será expandido quando essa funcionalidade atingir a disponibilidade geral.

## <a name="creating-a-custom-field"></a>Criando um campo personalizado
Quando você cria um campo personalizado, o Log Analytics deve compreender quais dados deseja usar para popular seu valor.  Ele usa uma tecnologia da Microsoft Research chamada FlashExtract para identificar rapidamente esses dados.  Em vez de exigir que você forneça instruções explícitas, o Azure Monitor aprende sobre os dados que você deseja extrair de exemplos que você fornece.

As seções a seguir fornecem o procedimento para criar um campo personalizado.  Na parte inferior deste artigo há um passo a passo de uma extração de exemplo.

> [!NOTE]
> O campo personalizado é preenchido à medida que os registros correspondentes aos critérios especificados são adicionados ao espaço de trabalho do Log Analytics, de modo que ele só aparecerá nos registros coletados após a criação do campo personalizado.  O campo personalizado não será adicionado aos registros que já estão no armazenamento de dados quando ele for criado.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Etapa 1: Identificar registros que terão o campo personalizado
A primeira etapa é identificar os registros que obterão o campo personalizado.  Você começa com uma [consulta de log padrão](../log-query/log-query-overview.md) e, em seguida, seleciona um registro para agir como o modelo que o Azure Monitor aprenderá.  Quando você especifica que extrairá dados em um campo personalizado, o **Field Extraction Wizard** (Assistente de Extração de Campo) é aberto, no qual você valida e refina os critérios.

1. Vá para **Logs** e use uma [consulta para recuperar os registros](../log-query/log-query-overview.md) que terão o campo personalizado.
2. Selecione um registro que o Log Analytics usará para atuar como um modelo para extrair dados para popular o campo personalizado.  Você identificará os dados que deseja extrair desse registro e o Log Analytics usará essas informações para determinar a lógica para popular o campo personalizado para todos os registros semelhantes.
3. Expanda as propriedades de registro, clique na elipse à esquerda da propriedade superior do registro e selecione **Campos Extrair de**.
4. O **Assistente de Extração de Campo** é aberto e o registro selecionado é exibido na coluna Exemplo **principal.**  O campo personalizado será definido para os registros com os mesmos valores nas propriedades selecionadas.  
5. Se a seleção não for exatamente o que você deseja, selecione campos adicionais para restringir os critérios.  Para alterar os valores de campo para os critérios, você deve cancelar e selecionar um registro diferente que corresponde aos critérios desejados.

### <a name="step-2---perform-initial-extract"></a>Etapa 2: Executar a extração inicial.
Depois de identificar os registros que terão o campo personalizado, você identifica os dados que deseja extrair.  O Log Analytics usará essas informações para identificar padrões semelhantes em registros semelhantes.  Na próxima etapa, você poderá validar os resultados e fornecer mais detalhes para o Log Analytics usar em suas análises.

1. Realce o texto no registro de exemplo que você deseja para popular o campo personalizado.  Em seguida, você será apresentado com uma caixa de diálogo para fornecer um nome e um tipo de dados para o campo e para executar o extrato inicial.  Os ** \_** caracteres CF serão automaticamente anexados.
2. Clique em **Extrair** para executar uma análise dos registros coletados.  
3. As seções **Resumo** e **Resultados da Pesquisa** exibem os resultados de extração para que você possa inspecionar sua precisão.  **Resumo** exibe os critérios usados para identificar registros e uma contagem de cada um dos valores de dados identificados.  **Resultados da Pesquisa** fornece uma lista detalhada dos registros que correspondem aos critérios.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Etapa 3: Verificar a precisão da extração e criar o campo personalizado
Após você ter executado a extração inicial, o Log Analytics exibirá seus resultados com base nos dados que já foram coletados.  Se os resultados parecerem precisos, você poderá criar o campo personalizado sem trabalho adicional.  Caso contrário, você pode refinar os resultados para que o Log Analytics possa melhorar sua lógica.

1. Se quaisquer valores na extração inicial não estiverem corretos, clique no ícone **Editar** ao lado de um registro impreciso e selecione **Modificar esse realce** para modificar a seleção.
2. A entrada é copiada para a seção **Exemplos adicionais** sob o **Exemplo Principal**.  Você pode ajustar o realce aqui para ajudar o Log Analytics a entender a seleção que ele deveria ter feito.
3. Clique em **Extrair** para usar essas novas informações para avaliar todos os registros existentes.  Os resultados podem ser modificados para registros diferentes daqueles que você acabou de modificar com base nessa nova inteligência.
4. Continue a adicionar correções até que todos os registros na extração identifiquem corretamente os dados para popular o novo campo personalizado.
5. Clique em **Save Extract** (Salvar Extração) quando estiver satisfeito com os resultados.  O campo personalizado agora está definido, mas ele não será adicionado a nenhum registro ainda.
6. Aguarde até que novos registros correspondentes aos critérios especificados sejam coletados e, em seguida, execute novamente a pesquisa de log. Os novos registros devem ter o campo personalizado.
7. Use o campo personalizado como qualquer outra propriedade de registro.  Você pode usá-lo para agregar e agrupar dados e até mesmo usá-lo para gerar novas percepções.

## <a name="viewing-custom-fields"></a>Exibindo campos personalizados
Exiba uma lista de todos os campos personalizados do grupo de gerenciamento no menu **Configurações Avançadas** do espaço de trabalho do Log Analytics no portal do Azure.  Selecione **Dados** e **Campos personalizados** para obter uma lista de todos os campos personalizados no workspace.  

![Campos Personalizados](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Removendo um campo personalizado
Há duas maneiras de remover um campo personalizado.  O primeiro é a opção **Remover** para cada campo ao exibir a lista completa conforme descrito acima.  O outro método é recuperar um registro e clicar no botão à esquerda do campo.  O menu terá uma opção para remover o campo personalizado.

## <a name="sample-walkthrough"></a>Passo a passo de exemplo
A seção a seguir explica passo a passo um exemplo completo de criação de campo personalizado.  Este exemplo extrai o nome do serviço em eventos do Windows que indicam um serviço alterando o estado.  Isso se baseia em eventos criados pelo Service Control Manager durante a inicialização do sistema em computadores Windows.  Se você deseja acompanhar este exemplo, deve estar [coletando eventos de informações para o log do sistema](data-sources-windows-events.md).

Inserimos a seguinte consulta para retornar todos os eventos do Gerenciador de Controle de Serviço que têm uma ID de Evento de 7036, que é o evento que indica um serviço iniciando ou parando.

![Consulta](media/custom-fields/query.png)

Em seguida, selecionamos e expandimos qualquer registro com o ID de evento 7036.

![Registro de origem](media/custom-fields/source-record.png)

Definimos campos personalizados clicando na elipse ao lado da propriedade superior.

![Extrair campos](media/custom-fields/extract-fields.png)

O **Field Extraction Wizard** (Assistente de Extração de Campo) é aberto e os campos **EventLog** e **EventID** são selecionados na coluna **Main Example** (Exemplo Principal).  Isso indica que o campo personalizado será definido para eventos do log do sistema com uma ID de evento de 7036.  Isso é suficiente para que não precisemos selecionar nenhum outro campo.

![Main Example](media/custom-fields/main-example.png)

Realçamos o nome do serviço na propriedade **RenderedDescription** e usamos **Service** para identificar o nome do serviço.  O campo personalizado será chamado **Service_CF**. O tipo de campo neste caso é uma string, então podemos deixar isso inalterado.

![Título do campo](media/custom-fields/field-title.png)

Vemos que o nome do serviço é identificado corretamente para alguns registros, mas não para outros.   Os **Resultados da Pesquisa** mostram que parte do nome do **Adaptador de Desempenho WMI** não foi selecionada.  O **Resumo** mostra que um registro identificou **o Instalador** de Módulos em vez do Instalador de **Módulos do Windows**.  

![Resultados da Pesquisa](media/custom-fields/search-results-01.png)

Começamos com o registro **Adaptador de Desempenho WMI** .  Clicamos em seu ícone de edição e em **Modify this highlight**(Modificar esse realce).  

![Modificar o realce](media/custom-fields/modify-highlight.png)

Aumentaremos o realce para incluir a palavra **WMI** e, em seguida, executamos a extração novamente.  

![Exemplo adicional](media/custom-fields/additional-example-01.png)

Podemos ver que as entradas para **Adaptador de Desempenho WMI** foram corrigidas e o Log Analytics também usou essa informação para corrigir os registros para **Instalador de Módulo do Windows**.

![Resultados da Pesquisa](media/custom-fields/search-results-02.png)

Agora podemos executar uma consulta que verifica **Service_CF** é criada, mas ainda não foi adicionada a nenhum registro. Isso porque o campo personalizado não funciona contra os registros existentes, então precisamos esperar que novos registros sejam coletados.

![Contagem inicial](media/custom-fields/initial-count.png)

Depois de passar algum tempo para que novos eventos sejam coletados, podemos ver que o campo **Service_CF** agora está sendo adicionado a recursos que correspondem aos nossos critérios.

![Resultados finais](media/custom-fields/final-results.png)

Agora podemos usar o campo personalizado como qualquer outra propriedade de registro.  Para ilustrar isso, criamos uma consulta que agrupa pelo novo campo **Service_CF** para inspecionar quais serviços são os mais ativos.

![Agrupar por consulta](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para criar consultas usando campos personalizados para critérios.
* Monitorar [arquivos de log personalizados](data-sources-custom-logs.md) que você analisa usando campos personalizados.

