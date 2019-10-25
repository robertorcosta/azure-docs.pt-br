---
title: Exportação contínua de telemetria do Application Insights | Microsoft Docs
description: Exportar dados de uso e diagnóstico para armazenamento no Microsoft Azure e baixá-los de lá.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/25/2019
ms.openlocfilehash: 6504661c2df66bda81af03a6364703b4b10f7485
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819543"
---
# <a name="export-telemetry-from-application-insights"></a>Exportar telemetria do Application Insights
Deseja manter a telemetria por mais tempo que o período de retenção padrão? Ou processá-la de alguma forma especializada? Exportação contínua é ideal para isso. Os eventos que você vê no portal do Application Insights podem ser exportados para armazenamento no Microsoft Azure no formato JSON. A partir daí, você pode baixar seus dados e escrever qualquer código necessário para processá-los.  

Antes de configurar a exportação contínua, há algumas alternativas que você talvez queira considerar:

* O botão Exportar na parte superior de uma guia métricas ou Pesquisar permite transferir tabelas e gráficos para uma planilha do Excel.

* O [Analytics](../../azure-monitor/app/analytics.md) fornece uma linguagem de consulta eficiente para telemetria. Ele também pode exportar os resultados.
* Se desejar [explorar seus dados no Power BI](../../azure-monitor/app/export-power-bi.md ), é possível fazer isso sem usar a Exportação Contínua.
* A [API REST de acesso a dados](https://dev.applicationinsights.io/) permite que você acesse a telemetria programaticamente.
* Você também pode acessar a configuração da [exportação contínua por meio do Powershell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Depois que a exportação contínua copia os dados para o armazenamento (onde eles podem permanecer pelo tempo desejado), eles ainda ficam disponíveis no Application Insights pelo [período de retenção](../../azure-monitor/app/data-retention-privacy.md) normal.

## <a name="continuous-export-advanced-storage-configuration"></a>Configuração de armazenamento avançado de exportação contínua

A exportação contínua **não dá suporte** aos seguintes recursos/configurações do armazenamento do Azure:

* Uso de [firewalls de armazenamento VNET/Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security) em conjunto com o armazenamento de BLOBs do Azure.

* [Armazenamento imutável](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) para o armazenamento de BLOBs do Azure.

* [Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="setup"></a> Criar uma Exportação Contínua

1. No recurso de Application Insights para seu aplicativo em configurar à esquerda, abra exportação contínua e escolha **Adicionar**:

2. Escolha a telemetria de tipos de dados que você deseja exportar.

3. Crie ou selecione uma [Conta de armazenamento do Azure](../../storage/common/storage-introduction.md) onde você deseja armazenar os dados. Para obter mais informações sobre as opções de preços de armazenamento, visite a [página de preços oficial](https://azure.microsoft.com/pricing/details/storage/).

     Clique em Adicionar, exportar destino, conta de armazenamento e, em seguida, crie um novo repositório ou escolha um repositório existente.

    > [!Warning]
    > Por padrão, o local de armazenamento será definido como a mesma região geográfica que seu recurso Application Insights. Armazenar em uma região diferente poderá incorrer em encargos de transferência.

4. Crie ou selecione um contêiner no armazenamento.

Depois de criar sua exportação, ela começa a ser realizada. Você só obtém os dados que chegam após a criação da exportação.

Pode haver um atraso de aproximadamente uma hora antes de os dados aparecem no armazenamento.

### <a name="to-edit-continuous-export"></a>Para editar a exportação contínua

Clique em exportação contínua e selecione a conta de armazenamento a ser editada.

### <a name="to-stop-continuous-export"></a>Para interromper a exportação contínua

Para interromper a exportação, clique em Desabilitar. Quando você clicar em Habilitar novamente, a exportação será reiniciada com novos dados. Você não obterá os dados recebidos no portal enquanto a exportação estava desabilitada.

Para interromper a exportação permanentemente, basta excluí-la. Isso não exclui seus dados do armazenamento.

### <a name="cant-add-or-change-an-export"></a>Não consegue adicionar nem alterar uma exportação?
* Para adicionar ou alterar as exportações, você precisa de direitos de acesso de proprietário, colaborador ou Application Insights colaborador. [Saiba mais sobre as funções][roles].

## <a name="analyze"></a> Quais eventos você recebe?
Os dados exportados são a telemetria bruta que recebemos de seu aplicativo, exceto que adicionamos dados de local, que calculamos a partir do endereço IP do cliente.

Dados que foram descartados por [amostragem](../../azure-monitor/app/sampling.md) não são incluídos nos dados exportados.

Outras métricas calculadas não são incluídas. Por exemplo, nós não exportamos a utilização média de CPU, mas exportamos a telemetria bruta por meio da qual a média é computada.

Os dados também incluem os resultados de todos os [testes da Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) que você configurou.

> [!NOTE]
> **Amostragem.** Se seu aplicativo enviar muitos dados, a funcionalidade de amostragem poderá operar e enviar apenas uma parte da telemetria gerada. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a> Inspecionar os dados
Você pode inspecionar o armazenamento diretamente no portal. Clique em início no menu à extrema esquerda, na parte superior em que diz "serviços do Azure" selecionar **contas de armazenamento**, selecione o nome da conta de armazenamento, na página Visão geral, selecione **BLOBs** em serviços e, por fim, selecione o nome do contêiner.

Para inspecionar o armazenamento do Azure no Visual Studio, abra **Exibir** e **Cloud Explorer**. (Se você não tiver esse comando de menu, precisará instalar o SDK do Azure: abra o diálogo **Novo Projeto**, expanda Visual C#/Nuvem e escolha **Obter o SDK do Microsoft Azure para .NET**.)

Quando você abrir o armazenamento de blob, verá um contêiner com um conjunto de arquivos de blob. O URI de cada arquivo deriva o nome do recurso Application Insights, da chave de instrumentação e do tipo/data/hora de telemetria. (O nome do recurso está todo em letras minúsculas e a chave de instrumentação omite traços.)

![Inspecione o repositório de blob com uma ferramenta adequada](./media/export-telemetry/04-data.png)

A data e hora são em formato UTC, e referentes a quando a telemetria foi depositada no repositório - não à hora em que essa telemetria foi gerada. Então, se você escrever código para baixar os dados, ele pode percorrer os dados linearmente.

Veja o formato do caminho:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Where

* `blobCreationTimeUtc` é a hora em que o blob foi criado no armazenamento de preparo interno
* `blobDeliveryTimeUtc` é a hora em que o blob foi copiado para o armazenamento de destino de exportação

## <a name="format"></a> Formato dos dados
* Cada blob é um arquivo de texto que contém várias linhas separadas por “ \n”. Ele contém a telemetria processada durante um período de tempo de aproximadamente metade um minuto.
* Cada linha representa um ponto de dados de telemetria como uma solicitação ou uma exibição de página.
* Cada linha é um documento JSON não formatado. Se você quiser ficar olhando para ele, abra-o no Visual Studio e escolha Editar, Avançado, Arquivo de Formato:

![Veja a telemetria com uma ferramenta adequada](./media/export-telemetry/06-json.png)

As durações de tempo são em tiques, onde 10.000 tiques = 1 ms. Por exemplo, esses valores mostram um tempo de 1 ms para enviar uma solicitação do navegador, 3 ms recebê-la e 1,8 s para processar a página no navegador:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Referência de modelo de dados detalhados para os tipos de propriedades e valores.](export-data-model.md)

## <a name="processing-the-data"></a>Processamento dos dados
Em pequena escala, você pode escrever um código para extrair e separar seus dados, lê-los em uma planilha e assim por diante. Por exemplo:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Para obter um exemplo de código maior, consulte [usando uma função de trabalho][exportasa].

## <a name="delete"></a>Excluir dados antigos
Você é responsável por gerenciar sua capacidade de armazenamento e excluir os dados antigos, se necessário.

## <a name="if-you-regenerate-your-storage-key"></a>Se você regenerar sua chave de armazenamento...
Se você alterar a chave para seu armazenamento, a exportação contínua deixará de funcionar. Você verá uma notificação em sua conta do Azure.

Abra a guia exportação contínua e edite a exportação. Edite o destino de exportação, mas mantenha o mesmo armazenamento selecionado. Clique em OK para confirmar.

A exportação contínua será reiniciada.

## <a name="export-samples"></a>Exemplos de exportação

* [Exportar para o SQL usando o Stream Analytics][exportasa]
* [Exemplo do Stream Analytics 2](export-stream-analytics.md)

Em escalas maiores, considere usar o [HDInsight](https://azure.microsoft.com/services/hdinsight/) - clusters de Hadoop na nuvem. O HDInsight fornece várias tecnologias para gerenciar e analisar Big Data, e você pode usá-lo para processar dados que foram exportados do Application Insights.

## <a name="q--a"></a>P e R
* *Mas tudo o que eu quero é um download único de um gráfico.*  

    Sim, você pode fazer isso. Na parte superior da guia, clique em **exportar dados**.
* *Eu configuro uma exportação, mas não há nenhum dado no meu repositório.*

    O Application Insights recebeu qualquer telemetria do seu aplicativo desde que você configurou a exportação? Você receberá apenas novos dados.
* *Eu tentei configurar uma exportação, mas o acesso foi negado*

    Se a conta pertence à sua organização, você precisa ser membro do grupo de proprietários ou do grupo de colaboradores.
* *Eu posso exportar diretamente para meu próprio repositório local?*

    Não, infelizmente. Nosso mecanismo de exportação funciona apenas com o armazenamento do Azure no momento.  
* *Há qualquer limite para a quantidade de dados que você coloca em meu repositório?*

    Não. Continuaremos a enviar dados por push até que você exclua a exportação. Interromperemos o envio se atingirmos os limites externos para o armazenamento de blob, mas são limites enormes. Cabe a você controlar a quantidade de armazenamento que usa.  
* *Quantos blobs devo ver no armazenamento?*

  * Para cada tipo de dados selecionado para exportação, um novo blob é criado a cada minuto (se os dados estiverem disponíveis).
  * Além disso, para aplicativos com tráfego intenso, são alocadas unidades de partição adicionais. Nesse caso, cada unidade cria um blob a cada minuto.
* *Eu regenerei a chave para o meu armazenamento ou alterei o nome do contêiner, e agora a exportação não funciona.*

    Edite a exportação e abra a guia destino de exportação. deixe o mesmo armazenamento selecionado como antes e clique em OK para confirmar. A exportação será reiniciada. Se a alteração foi realizada nos últimos dias, você não perderá dados.
* *Posso pausar a exportação?*

    Sim. Clique em Desabilitar.

## <a name="code-samples"></a>Exemplos de código

* [Exemplo do Stream Analytics](export-stream-analytics.md)
* [Exportar para o SQL usando o Stream Analytics][exportasa]
* [Referência de modelo de dados detalhados para os tipos de propriedades e valores.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
