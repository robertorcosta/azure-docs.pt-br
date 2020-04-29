---
title: Configurar leads do cliente | Azure Marketplace
description: Configure clientes potenciais no Portal do Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280313"
---
<a name="get-customer-leads"></a>Obter clientes potenciais
==================

Este artigo explica como criar clientes potenciais usando o Portal do Cloud Partner. Você pode se conectar esses clientes potenciais ao seu sistema de CRM e integrá-los ao pipeline de vendas.

## <a name="leads"></a>Clientes potenciais

Clientes potenciais são aqueles que estão interessados ou estão implantando seus produtos usando o [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou o [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  O cliente usa um "test drive" da sua oferta. Os Test Drives são uma oportunidade acelerada de compartilhar seus negócios instantaneamente com clientes potenciais sem as barreiras à entrada. Todos os test drives geram um cliente potencial para o cliente que está interessado em experimentar seu produto para saber mais sobre ele. Saiba mais sobre as unidades de teste no [test drive do Azure Marketplace](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Exemplos de test drive do Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. O cliente consente em compartilhar suas informações depois de selecionar "Obter Agora". Esse cliente potencial é um **interesse inicial**, em que podemos compartilhar informações sobre o cliente que demonstrou interesse em obter o produto. O cliente potencial está no topo do funil de aquisição.

   ![Opção Obter agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Cliente seleciona "Comprar" no [Portal do Azure](https://portal.azure.com/) para obter seu produto. Esse cliente potencial é **ativo**, no qual podemos compartilhar informações sobre um cliente que começou a implantar seu produto.

   ![Opção de compra](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  O cliente faz um "Test Drive" da sua oferta. Os Test Drives são uma oportunidade acelerada de compartilhar seus negócios instantaneamente com clientes potenciais sem as barreiras à entrada. Todos os test drives gerarão um cliente potencial de um cliente que ficou interessado em experimentar o produto para conhecê-lo melhor. Saiba mais sobre Test Drives no [Test Drive do AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exemplo de test drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  O cliente consente em compartilhar suas informações depois de selecionar "Obter Agora". Esse cliente potencial é um **interesse inicial**, no qual podemos compartilhar informações sobre o cliente que expressa interesse em obter o produto. O cliente potencial está no topo do funil de aquisição.

      ![Opção Obter agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  O cliente seleciona "Entrar em contato comigo" em sua oferta. Esse é um cliente potencial **ativo**, no qual podemos compartilhar informações sobre um cliente que solicita acompanhar as informações sobre seu produto.

    ![Opção Entrar em contato comigo](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Dados do lead
---------

Cada cliente potencial que você recebe durante o processo de aquisição de cliente tem dados em campos específicos. Como você obterá clientes potenciais de várias etapas, a melhor maneira de lidar com eles é eliminar a duplicação e personalizar os acompanhamentos. Dessa forma, cada cliente recebe uma mensagem apropriada, e você cria uma relação exclusiva.

### <a name="lead-source"></a>Origem do cliente potencial

O formato de uma fonte de Lead é uma**oferta** de**ação** |  de **origem**-

**Fontes**: "AzureMarketplace", "AzurePortal", "TestDrive" e "SPZA (AppSource)"

**Ações**:
- "INS" – instalação. Essa ação ocorre no Azure Marketplace ou no AppSource quando um cliente compra seu produto.
- "PLT" – significa Partner Led Trial (parceiro lidera a avaliação). Essa ação ocorre no AppSource quando um cliente usa a opção Entrar em contato comigo.
- "DNC" – Do Not Contact (Não entrar em contato). Essa ação ocorre no AppSource quando um parceiro que foi listado de forma cruzada na página do aplicativo solicitado ser contatado. Estamos compartilhando a informação de que esse cliente foi listado de forma cruzada em seu aplicativo, mas ele não precisa ser contatado.
- "Criar" – essa ação ocorre apenas dentro do Portal do Azure e é gerada quando um cliente compra sua oferta na conta dele.
- "StartTestDrive" – essa ação ocorre apenas para test drives e é gerada quando um cliente inicia o test drive.

**Ofertas**

Os exemplos a seguir mostram identificadores exclusivos que são atribuídos a um Publicador e uma oferta específica: Checkpoint. Check-Point-R77-10SG-byol, BitNami. openedxcypress e docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informações do cliente

Os campos no exemplo a seguir mostram as informações do cliente que estão contidas em um cliente potencial.
- Nome: Carlos
- Sobrenome: Lima
- Email: crlima\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Cargo: CTO

>[!Note]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial.

Estamos trabalhando ativamente para aperfeiçoar os leads, portanto, se houver um campo de dados que você não visualizou aqui, mas gostaria de ter, [envie-nos seus comentários](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como conectar o sistema CRM com o Portal do Cloud Partner
------------------------------------------------------------

Para começar a conquistar clientes potenciais, criamos o conector de Gerenciamento de Clientes Potenciais no Portal do Cloud Partner para que você possa inserir facilmente as informações de CRM e deixar que a gente faça a conexão para você. Agora você pode aproveitar com facilidade os leads gerados pelo marketplace sem um esforço significativo de engenharia para integração a um sistema externo.

![Conector de Gerenciamento de Clientes Potenciais](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Podemos gravar clientes potenciais em uma variedade de sistemas de CRM ou diretamente em uma Tabela do Armazenamento do Azure, na qual você pode gerenciar os leads da forma que quiser. Cada um dos links a seguir fornecem instruções para conectar a possíveis destinos de clientes potenciais:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) para obter instruções de como configurar o Dynamics CRM Online para obter leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) para obter instruções de como definir configurações de cliente potencial do Marketo para obter clientes potenciais.
-    [SalesForce](./cloud-partner-portal-lead-management-instructions-salesforce.md) para obter instruções de como configurar sua instância do Salesforce para obter clientes potenciais.
-    [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md) para obter instruções de como configurar a conta de Armazenamento do Azure para obter clientes potenciais em uma Tabela do Azure.
-   [Ponto de extremidade HTTPS](./cloud-partner-portal-lead-management-instructions-https.md) para obter instruções de como configurar o ponto de extremidade HTTPS para obter clientes potenciais.

Depois de configurar o destino do cliente potencial e publicar sua oferta, validaremos a conexão e enviaremos um cliente potencial de teste. Quando você exibe a oferta antes de entrar no ar, pode também testar a conexão do lead tentando adquirir a oferta no ambiente de visualização. É importante verificar se as configurações de cliente potencial estão atualizadas para que você não perca nenhum cliente potencial. Portanto, atualize essas conexões sempre que algo for alterado do seu lado.

<a name="what-next"></a>Próximos passos
----------

Quando a instalação técnica estiver em vigor, incorpore esses leads à estratégia atual de vendas e marketing e aos processos operacionais. Estamos muito interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer leads de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se você estiver interessado em [fornecer comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que sua equipe de vendas seja mais bem-sucedida com leads do Marketplace.
