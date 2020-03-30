---
title: Parâmetros de serviço web - Azure Machine Learning Studio (clássico) | Microsoft Docs
description: Como usar Parâmetros de Serviço Web de Azure Machine Learning para modificar o comportamento do seu modelo quando o serviço Web é acessado.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204402"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Use parâmetros de serviço web do Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Um serviço Web de Azure Machine Learning é criado pela publicação de um experimento com módulos com parâmetros configuráveis. Em alguns casos, talvez você queira alterar o comportamento do módulo durante a execução do serviço Web. *Parâmetros de serviço Web* permitem que você execute essa tarefa. 

Um exemplo comum é a configuração do módulo [Importar Dados][reader] para que o usuário do serviço Web publicado possa especificar outra fonte de dados quando o serviço Web for acessado. Ou então, configurar o módulo [Exportar Dados][writer] para que um destino diferente possa ser especificado. Alguns outros exemplos incluem a alteração do número de bits para o módulo [Hash de Recurso][feature-hashing] ou o número de recursos desejados para o módulo [Seleção de Recursos Baseada em Filtros][filter-based-feature-selection]. 

Você pode definir os Parâmetros do Serviço Web e associá-los a um ou mais parâmetros de módulo no seu teste, podendo também especificar se eles são obrigatórios ou opcionais. O usuário do serviço Web pode então fornecer valores para esses parâmetros quando chamar o serviço Web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Como definir e usar parâmetros de serviço Web
Você pode definir um parâmetro de serviço Web, clicando no ícone ao lado do parâmetro para um módulo e selecionando "Definir como parâmetro de serviço Web". Isso cria um novo Parâmetro de Serviço Web e o conecta a esse parâmetro do módulo. Em seguida, quando o serviço Web for acessado, o usuário poderá especificar um valor para o Parâmetro de Serviço Web e ele será aplicado ao parâmetro do módulo.

Depois que você definir um Parâmetro de Serviço Web, ele estará disponível para qualquer outro parâmetro de módulo do experimento. Se você definir um parâmetro de serviço da Web associado a um parâmetro para um módulo, poderá usar esse mesmo parâmetro de serviço da Web para qualquer outro módulo, desde que o parâmetro espere o mesmo tipo de valor. Por exemplo, se o Parâmetro de Serviço Web for um valor numérico, só poderá ser usado para parâmetros do módulo que esperem um valor numérico. Quando o usuário definir um valor para o Parâmetro de Serviço Web, ele será aplicado a todos os parâmetros do módulo associados.

Você pode optar por fornecer um valor padrão para o Parâmetro de Serviço Web. Nesse caso, o parâmetro será opcional para o usuário do serviço da Web. Se você não fornecer um valor padrão, o usuário deverá inserir um valor quando o serviço Web for acessado.

A documentação da API para o serviço Web incluirá informações para o usuário do serviço Web sobre como especificar o Parâmetro de Serviço Web programaticamente ao acessar o serviço Web.

> [!NOTE]
> A documentação da API para um serviço web clássico é fornecida através do link da página de ajuda da **API** no web service **DASHBOARD** in Machine Learning Studio (clássico). A documentação da API para um novo serviço Web é fornecida por meio do portal [Serviços Web do Azure Machine Learning](https://services.azureml.net/Quickstart) nas páginas **Consumo** e **API do Swagger** para o seu serviço Web.
> 
> 

## <a name="example"></a>Exemplo
Por exemplo, vamos supor que temos um experimento com um módulo [Exportar Dados][writer] que envia informações para o armazenamento de blobs do Azure. Definiremos um Parâmetro de Serviço Web denominado "Caminho do blob", que permite que o usuário do serviço Web altere o caminho para o armazenamento de blobs quando o serviço for acessado.

1. No Machine Learning Studio (clássico), clique no módulo [Dados de Exportação][writer] para selecioná-lo. Suas propriedades são mostradas no painel Propriedades à direita da tela do experimento.
2. Especifique a conta de armazenamento:
   
   * Em **Especifique o destino de dados**, selecione "Armazenamento de Blobs do Azure".
   * Em **Especifique o tipo de autenticação**, selecione "Conta".
   * Insira as informações de conta para o armazenamento de blobs do Azure. 

3. Clique no ícone à direita de **Caminho para o blob que começa com o parâmetro contêiner**. Ele tem esta aparência:
   
   ![Ícone do Parâmetro de Serviço Web](./media/web-service-parameters/icon.png)
   
   Selecione "Definir como parâmetro de serviço Web".
   
   Uma entrada é adicionada sob **Parâmetros de Serviço Web** na parte inferior do painel Propriedades com o nome "Caminho para o blob que começa com contêiner". Esse é o Parâmetro de Serviço Web que agora está associado a esse parâmetro do módulo [Exportar Dados][writer].
4. Para renomear o Parâmetro do Serviço Web, clique no nome, insira "Caminho do blob" e pressione a tecla **Enter** . 
5. Para fornecer um valor padrão para o Parâmetro do Serviço Web, clique no ícone à direita do nome, selecione "Fornecer o valor padrão", insira um valor (por exemplo, "contêiner1/saída1.csv") e pressione a tecla **Enter** .
   
   ![Parâmetro de Serviço Web](./media/web-service-parameters/parameter.png)
6. Clique em **Executar**. 
7. Clique em **Implantar Serviço Web** e selecione **Implantar Serviço Web [clássico]** ou **Implantar Serviço Web [novo]** para implantar o serviço Web.

> [!NOTE] 
> Para implantar um novo serviço Web, você precisa ter permissões suficientes na assinatura na qual o serviço Web está sendo implantado. Para obter mais informações, [gerencie um serviço web usando o portal Azure Machine Learning Web Services](manage-new-webservice.md). 

O usuário do serviço Web agora pode especificar um novo destino para o módulo [Exportar Dados][writer] ao acessar o serviço Web.

## <a name="more-information"></a>Mais informações
Para obter um exemplo mais detalhado, veja a entrada [Parâmetros de Serviço Web](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) no [Blog de Machine Learning](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para obter mais informações sobre como acessar um serviço Web do Machine Learning, consulte [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

