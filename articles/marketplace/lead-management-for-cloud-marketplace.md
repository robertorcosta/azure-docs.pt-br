---
title: Gestão líder para mercado comercial | Azure Marketplace e AppSource
description: Uma visão geral dos vários tópicos relacionados a publicação de ofertas e artefatos técnicos para o Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 09b02d043e970c68cdff0e3dc4f97328c9d74c84
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383788"
---
# <a name="lead-management-for-commercial-marketplace"></a>Gestão líder para mercado comercial


Os clientes são a essência de qualquer bom negócio. Na transformação das aquisições de produtos atuais, os profissionais de marketing precisam se concentrar em se conectar diretamente com os clientes e construir um relacionamento. Por isso, gerar leads de alta qualidade é uma ferramenta essencial no ciclo de vendas. Depois de listar sua oferta no [Partner Center,](https://partner.microsoft.com/)existem ferramentas habilitadas para que você receba informações de contato do cliente imediatamente após um cliente expressar interesse ou implantar seu produto no mercado. 



## <a name="what-are-leads-in-the-marketplace"></a>Quais são os clientes potenciais no marketplace?

São clientes potenciais de clientes que estão interessados ou implantando seus produtos do Marketplace. Se seu produto está listado no Azure Marketplace ou no AppSource, você poderá receber leads dos clientes assim que ele for configurado corretamente do seu CRM para a lista no Partner Center. 


## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Como conectar seu sistema de CRM com o Partner Center

Para começar a obter leads, o conector lead management no Partner Center foi projetado para que ele possa ser facilmente conectado com suas informações de CRM a uma lista de sistemas de CRM disponíveis. Agora você pode aproveitar com facilidade os leads gerados pelo marketplace sem um esforço significativo de engenharia para integração a um sistema externo.

Aqui estão as instruções passo a passo sobre como se conectar a cada um dos destinos possíveis do cliente potencial:

**Dynamics CRM Online** - Consulte [configurar o gerenciamento de leads para o Dynamics 365 para engajamento do cliente para](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) obter instruções sobre como configurar o Dynamics CRM Online para obter leads.

**Marketo** - Consulte [configurar o gerenciamento de leads no Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) para obter instruções sobre a configuração de configuração de lead marketo para obter leads.

**Salesforce** - Consulte [configurar o gerenciamento de leads para salesforce para](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) obter instruções sobre como configurar sua instância de Salesforce para obter leads.

**Tabela Azure** - Consulte [configurar o gerenciamento de leads usando uma tabela do Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para obter instruções sobre como configurar sua conta de armazenamento Do Zure para obter leads em uma tabela DoZure.

**Https Endpoint** - Consulte [configurar o gerenciamento de leads usando um ponto final HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) para obter instruções sobre como configurar seu Ponto final https para obter leads.

Depois de configurar corretamente o destino do lead e acessar Publicar na oferta, validaremos a conexão e enviaremos um lead de teste. Quando você exibe a oferta antes de entrar no ar, pode também testar a conexão do lead tentando adquirir a oferta no ambiente de visualização. É importante verificar se as configurações de cliente potencial estão atualizadas para que você não perca nenhum cliente potencial. Portanto, atualize essas conexões sempre que algo for alterado do seu lado.

### <a name="what-are-the-next-steps"></a>Quais são as próximas etapas?

Uma vez que a configuração técnica esteja em vigor, você deve incorporar esses leads em sua estratégia de vendas & marketing atual e processos operacionais. Estamos interessados em entender melhor seu processo geral de vendas e queremos trabalhar estreitamente com você para fornecer leads de alta qualidade e dados suficientes para o seu sucesso. Apreciamos seus comentários sobre como otimizar e melhorar os leads que enviamos a você, com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Deixe-nos saber se você está interessado em fornecer feedback e sugestões para permitir que sua equipe de vendas seja mais bem sucedida com o Marketplace Leads.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a>Erros comuns de configuração de chumbo durante a publicação no Partner Center

**Não foi possível salvar o lead para o Dynamics CRM. Verifique as configurações da conta do CRM Dynamics. LastCRMError: Não é possível fazer login no Dynamics CRM, LastCRMException:** 

> Se a autenticação do O365 foi selecionada, verifique se a conta de usuário e senha é válida. Se AAD tiver sido selecionado, verifique a ID de locatário, ID do aplicativo e correspondências de chave secreta do aplicativo que foi configurado no AAD. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Se o nome de usuário/senha da conta for válido, certifique-se de que ele tenha acesso ao Dynamics 365 e tenha uma licença atribuída (Etapas 11-15 se usar o Azure Active Directory ou configurações de segurança se estiver usando um usuário do Office). 

**Não foi possível salvar o lead para o Dynamics CRM. O usuário não tem permissões para o atributo leadsourcecode na entidade líder** 

> O aplicativo/usuário não tem funções de segurança para o gravador de cliente potencial do Microsoft Marketplace. Siga as etapas 11 a 15 se usando o Microsoft Azure Active Directory ou as configurações de segurança se usar um usuário do Office [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível salvar o lead para o Dynamics CRM usando AAD. Exceção:: Inquilino não encontrado. Esta instância pode acontecer se não houver assinaturas ativas para o inquilino.**  

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Por favor, obtenha o ID do Diretório com base nas instruções da Etapa 2 (em Azure Active Directory, a partir [daqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível salvar o lead para o Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falha - nenhuma função é atribuída ao usuário.**  

> Resolução: atribua a função de segurança para o gravador de cliente potencial do Microsoft Marketplace. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) em Configurações de Segurança.

**Não foi possível salvar o lead para o Dynamics CRM usando AAD. Exceção:: Aplicativo com identificador não foi encontrado no diretório** 

> A Id do aplicativo fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 8 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o lead para o Dynamics CRM usando AAD. Exceção:: O identificador de inquilino solicitado não é válido e não é válido formato de domínio externo** 

> A ID de diretório fornecida na seção de gerenciamento de cliente potencial não é um diretório válido. Obtenha a ID de diretório com base nas instruções da Etapa 2 (no Microsoft Azure Active Directory de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o lead para o Dynamics CRM usando AAD. Exceção:: Verificação de erros credenciais.: O segredo do cliente inválido é fornecido.** 

> Resolução: Entre no portal do Azure, verifique se a chave do aplicativo corresponde ao que está na Central de Parceiros. Gere senha com base na instrução na Etapa 10 (no Azure Active Directory), de [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Não foi possível salvar o lead para o Dynamics CRM. LastCRMError: O canal de solicitação se esgotou enquanto aguardava uma resposta após 00:02:00. Aumente o valor de tempo limite passado para a chamada para Solicitar ou aumente o valor de Envio de tempo na Vinculação. O tempo atribuído a esta operação pode ter sido uma parte de um tempo maior.**  

> Resolução: Entre no Partner Center, verifique os detalhes do Storefront >> o destino principal >> URL, verifique se é uma instância de CRM dinâmico válida.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quais são os clientes potenciais e por que são importantes para mim como um publicador no Marketplace?** 

Clientes potenciais são clientes que estão implantando seus produtos usando o Marketplace. Se o seu produto estiver listado no [Azure Marketplace](https://azuremarketplace.microsoft.com) ou [AppSource](https://appsource.microsoft.com/), você poderá receber os clientes potenciais de clientes que estejam interessados em seu produto, se você configurou o destino de cliente potencial em sua oferta.  

**Onde posso obter ajuda na configuração de meu destino de cliente potencial?** 

Você pode encontrar a documentação aqui: [Obtenha leads de clientes](./partner-center-portal/commercial-marketplace-get-customer-leads.md) ou envie um bilhete de suporte através de aka.ms/marketplacepublishersupport selecionar tipo de oferta e gerenciamento de leads. 

**É necessário configurar um destino de cliente potencial para publicar uma oferta no Marketplace?**

Sim, se você estiver publicando um aplicativo SaaS do contato Me ou dos Serviços de Consultoria.  
 
**Como confirmar se a configuração de cliente potencial está correta?**

Depois de configurar sua oferta e o destino do cliente potencial, publique sua oferta. Na etapa de validação de cliente potencial, o Marketplace enviará um líder de teste para o destino do cliente potencial configurado em sua oferta. 

**Como encontrar o cliente potencial de teste?**

Procure por "MSFT_TEST" em seu destino principal, aqui estão os dados de chumbo do teste de amostra: 

company = MSFT_TEST_636573304831318844 

país: EUA 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

codificação = UTF-8 

codificação = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844| \<Oferta> 

oid = 00Do0000000ZHog 

telefone = 1234567890 

title = MSFT_TEST_636573304831318844 

**Tenho uma oferta ao vivo, mas não vejo pistas?**

Cada cliente potencial terá dados passados nos campos no destino do cliente potencial selecionado, os clientes potenciais virão neste formato: **Ação de origem | Oferta** 

  *Fontes:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Ações:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Oferece:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Aqui estão os dados de amostra das informações do cliente*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Saiba mais em [Informações de cliente potencial](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Configurei o BLOB do Azure como meu cliente potencial de destino, por que não vejo o cliente potencial?** 

O cliente potencial só fica gravado quando você seleciona o armazenamento de BLOBs do Azure como destino do lead. Mude para a mesa do Azure para receber o lead em tempo real.

**Recebi um email do Marketplace, por que não é possível localizar o cliente potencial no meu CRM?**  

É possível que o domínio de email do usuário final é de. edu. Por razões de privacidade, não passamos dados pessoais identificáveis do domínio .edu. Enviar um [bilhete de suporte](https://aka.ms/marketplacepublishersupport).

**Configurei a Tabela do Azure/BLOB do Azure de acordo com o destino do meu cliente potencial, eu consigo ver os clientes potenciais?** 

Você pode acessar a bolha ou a tabela do portal Azure, ou você pode baixar e instalar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para visualizar as tabelas/blobs da sua conta de armazenamento Azure. 

**Configurei a tabela do Azure como destino de cliente potencial, posso ser notificado sempre que um novo cliente potencial é enviado pelo Marketplace?** 

Sim, siga as instruções para configurar o backup de tabelas do Azure + função sobre a documentação [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Configurei o Salesforce como destino como meu cliente potencial, por que não é possível localizar os clientes potenciais?** 

Verifique se a web para o formulário de cliente potencial é um campo obrigatório com base em uma lista de seleção. Em caso afirmativo, alterne o campo para um campo de texto não obrigatório.  
 
**Houve um problema com meu destino principal, e eu perdi algumas pistas. Posso mandá-los por e-mail?** 

Devido às políticas de privacidade, não podemos compartilhar informações de chumbo por e-mail não seguro. 

**Configurei o Armazenamento do Microsoft Azure (BLOB/Tabela) como meu destino de cliente potencial, quanto isso custará?** 

Dados de geração de cliente potencial (<1 GB para quase todos os publicadores). O custo dependerá do número de clientes potenciais recebido, se 1.000 potenciais são recebidos em um mês, isso custará cerca de 50 centavos. 
