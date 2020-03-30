---
title: incluir arquivo
description: incluir arquivo
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321788"
---
A guia **Test drive** permite configurar uma demonstração (ou "test drive") que permitirá que os clientes experimentem sua oferta antes de se comprometerem a comprá-la. Saiba mais no artigo [O que é test drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Se você não quiser mais fornecer um test drive para sua oferta, retorne à página **de configuração da Oferta** e não verifique ativar o test **drive**.

### <a name="technical-configuration"></a>Configuração técnica
Os seguintes tipos de test drives estão disponíveis, cada um com seus próprios requisitos de configuração técnica.

- [Gerente de Recursos do Azure](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicativo lógico](#technical-configuration-for-logic-app-test-drive)
- [BI de](#technical-configuration-not-required-for-power-bi-test-drives) potência (configuração técnica não necessária)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para test drive do Azure Resource Manager

Um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos que se encaixam neste cenário usam apenas recursos do Azure. Saiba mais sobre como configurar [um test drive do Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiões (necessárias):** Atualmente existem 26 regiões suportadas pelo Azure onde seu test drive pode ser disponibilizado. Normalmente, você vai querer disponibilizar seu test drive nas regiões onde você antecipa o maior número de clientes, para que eles possam selecionar a região mais próxima para obter o melhor desempenho. Você precisará ter certeza de que sua assinatura está autorizada a implantar todos os recursos necessários em cada uma das regiões que você está selecionando.

- **Instâncias**: Selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicados pelo número de regiões onde sua oferta está disponível.

**Hot**: Este tipo de instância é implantada e aguarda acesso por região selecionada. Os clientes podem acessar instantaneamente *instâncias quentes* de um test drive, em vez de ter que esperar por uma implantação. A desvantagem é que tais instâncias estão sempre em execução na sua assinatura do Azure; portanto, incorrerão em um maior custo de tempo de atividade. É altamente recomendável ter pelo menos uma instância *Hot,* já que a maioria dos clientes não quer esperar por implantações completas, resultando em uma queda no uso do cliente se nenhuma instância *Hot* estiver disponível.

**Frio**: Este tipo de instância representa o número total de instâncias que podem ser implantadas por região. As instâncias frias exigem que todo o modelo do Test Drive Resource Manager seja implantado quando um cliente solicita o test drive, de modo que as instâncias *frias* são muito mais lentas de carregar do que *as instâncias hot.* A compensação é que você só tem que pagar pela duração do test drive, *nem* sempre está sendo executado na sua assinatura do Azure como com uma instância *Hot.*

- **Test drive Modelo do Gerenciador de recursos do Azure**: Carregue o .zip contendo o modelo do Azure Resource Manager.  Saiba mais sobre a criação de um modelo do Azure Resource Manager no artigo quickstart [Criar e implantar modelos do Azure Resource Manager usando o portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração da unidade de teste** (necessária): Insira o tempo que o Test Drive permanecerá ativo, em # de horas. O Test Drive é encerrado automaticamente após o término desse período de tempo. Esta duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica para test drive Dynamics 365

A Microsoft pode remover a complexidade de configurar um test drive hospedando e mantendo o provisionamento e implantação do serviço usando este tipo de test drive. A configuração para este tipo de test drive hospedado é a mesma, independentemente de o test drive estar mirando um público da Business Central, Customer Engagement ou Operations.

- **Máximo de test drives simultâneos** (obrigatório): Defina o número máximo de clientes que podem usar seu test drive de uma só vez. Cada usuário simultâneo consumirá uma licença Dynamics 365 enquanto o test drive estiver ativo, então você precisará garantir que você tenha licenças suficientes disponíveis para suportar o limite máximo definido. O valor recomendado é de 3 a 5.

- **Duração da unidade de teste** (necessária): Insira o tempo que o Test Drive permanecerá ativo definindo o número de horas. Depois de tantas horas, a sessão terminará e não consumirá mais uma de suas licenças. Recomendamos um valor de 2-24 horas, dependendo da complexidade de sua oferta. Esta duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).  O usuário pode solicitar uma nova sessão se ficar sem tempo e quiser acessar o test drive novamente.

- **URL de instância** (obrigatório): A URL onde o cliente iniciará seu test drive. Normalmente, a URL da sua instância Dynamics 365 executando seu `https://testdrive.crm.dynamics.com`aplicativo com dados de amostra instalados (por exemplo).

- **Url de API da Web (obrigatório):** Recupere a URL da API da Web para sua instância Dynamics 365 fazendo login em sua conta Microsoft 365 e navegando para **Configurações** \&gt; **Personalização** \&gt; **Recursos do** \&desenvolvedor gt; **API da Web de instância (Service Root URL)**, `https://testdrive.crm.dynamics.com/api/data/v9.0`copie a URL encontrada aqui (por exemplo).

- **Nome da função** (obrigatório): Forneça o nome da função de segurança que você definiu em seu test drive dynamics 365 personalizado. Isso será atribuído ao usuário durante o test drive (por exemplo, test-drive-role).

#### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para test drive do aplicativo Logic

Qualquer produto personalizado deve usar esse tipo de modelo de implantação de test drive que abrange uma variedade de arquiteturas de soluções complexas. Para obter mais informações sobre como configurar os test drives do Logic App, visite [Operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Engajamento do Cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (lista suspensa de seleção única necessária): Atualmente existem 26 regiões suportadas pelo Azure onde seu test drive pode ser disponibilizado. Os recursos para o seu aplicativo Logic serão implantados na região selecionada. Se o seu App Logic tiver algum recurso personalizado armazenado em uma região específica, certifique-se de que a região esteja selecionada aqui. A melhor maneira de fazer isso é implantar totalmente seu App Logic localmente em sua assinatura do Azure no portal e verificar se ele funciona corretamente antes de fazer essa seleção.

- **Máximo de test drives simultâneos** (obrigatório): Defina o número máximo de clientes que podem usar seu test drive de uma só vez. Esses test drives já estão implantados, permitindo que os clientes os acessem instantaneamente sem esperar por uma implantação.

- **Duração da unidade de teste** (necessária): Insira o tempo que o Test Drive permanecerá ativo, em # de horas. O test drive termina automaticamente após o término desse período de tempo.

- **Nome do grupo de recursos do Azure** (obrigatório): Digite o nome do [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) onde sua unidade de teste logic app é salva.

- **Nome do aplicativo lógico do Azure** (obrigatório): Digite o nome do aplicativo Logic que atribui a unidade de teste ao usuário. Este aplicativo Logic deve ser salvo no grupo de recursos do Azure acima.

- **Nome do aplicativo lógico de desprovisionamento** (obrigatório): Digite o nome do aplicativo Logic que desprovisiona o test drive assim que o cliente estiver concluído. Este aplicativo Logic deve ser salvo no grupo de recursos do Azure acima.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste Power BI

Os produtos que desejam demonstrar um visual interativo do Power BI podem usar um link incorporado para compartilhar um painel personalizado como seu test drive, sem necessidade de configuração técnica adicional. Saiba mais sobre como configurar aplicativos de modelo[power bi.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Detalhes da assinatura de implantação

Para implantar o Test Drive em seu nome, crie e forneça uma assinatura azure separada e exclusiva. (Não é necessário para unidades de teste power bi).

- **ID de assinatura do Azure** (necessário para aplicativos Azure Resource Manager e Logic): Digite o ID de assinatura para conceder acesso aos serviços de sua conta do Azure para relatórios e faturamento de uso de recursos. Recomendamos que você considere [criar uma assinatura azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) para usar em test drives se você ainda não tiver uma. Você pode encontrar seu ID de assinatura do Azure fazendo login no portal do [Azure](https://portal.azure.com/) e navegando na guia **Assinaturas** do menu do lado esquerdo. A seleção da guia exibirá seu ID de assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID do inquilino Azure AD** (obrigatório): Insira seu ID de inquilino do Azure Active [Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)(AD). Para encontrar esse ID, entre no [portal Dozure,](https://portal.azure.com/)selecione a guia Diretório Ativo no menu esquerdo, selecione **Propriedades** e procure o número **de ID do diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar o ID de inquilino da [https://www.whatismytenantid.com](https://www.whatismytenantid.com)sua organização usando o URL do seu nome de domínio em: .

- **Nome do inquilino Azure AD** (necessário para o Dynamic 365): Digite o nome do Azure Active Directory (AD). Para encontrar esse nome, entre no [portal Azure,](https://portal.azure.com/)no canto superior direito seu nome de inquilino será listado com o nome da sua conta.

- **ID do aplicativo Azure AD** (obrigatório): Insira o ID do aplicativo Azure Active [Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)(AD). Para encontrar esse ID, entre no [portal do Azure,](https://portal.azure.com/)selecione a guia Diretório Ativo no menu esquerdo, selecione **inscrições do aplicativo**e procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD app client secret** (obrigatório): Insira o segredo do [cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)do aplicativo Azure AD . Para encontrar esse valor, faça login no [portal Azure](https://portal.azure.com/). Selecione a guia Diretório ativo do **Azure** no menu à esquerda, selecione **Registros do aplicativo**e selecione o aplicativo test drive. Em seguida, **selecione Certificados e segredos,** selecione **Novo segredo do cliente,** digite uma descrição, **selecione Nunca** em **Expirado**e escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para longe da página antes de fazer isso, ou então você não terá acesso ao valor.)

Lembre-se **de salvar** antes de passar para a próxima seção!

### <a name="test-drive-listings-optional"></a>Listas de test drive (opcional)

A opção **de listagem test drive** encontrada na guia Test **drive** exibe os idiomas (e mercados) onde sua test drive está disponível, atualmente o inglês (Estados Unidos) é o único local disponível. Além disso, esta página exibe o status da listagem específica do idioma e a data/hora em que foi adicionada. Você precisará definir os detalhes do test drive (descrição, manual do usuário, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (necessária): Descreva seu test drive, o que será demonstrado, objetivos para o usuário experimentar, recursos a explorar e quaisquer informações relevantes para ajudar o usuário a determinar se deve adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

- **Informações de acesso** (necessárias para o Azure Resource Manager e logic test drives): Explique o que um cliente precisa saber para acessar e usar este test drive. Caminhe por um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos durante todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do Usuário** (obrigatório): Um passo a passo de sua experiência de test drive. O Manual do Usuário deve cobrir exatamente o que você deseja que o cliente ganhe ao experimentar o test drive e servir de referência para quaisquer perguntas que eles possam ter. O arquivo deve estar em formato PDF e ser nomeado (255 caracteres no máximo) após o upload.

- **Vídeos: Adicione vídeos** (opcional): Os vídeos podem ser carregados no YouTube ou Vimeo e referenciados aqui com um link e imagem em miniatura (533 x 324 pixels) para que o cliente possa visualizar uma caminhada através de informações para ajudá-los a entender melhor o test drive, incluindo como usar com sucesso os recursos de sua oferta e entender cenários que destacam seus benefícios.
  - **Nome** (obrigatório)
  - **URL (somente YouTube ou Vimeo)** (obrigatório)
  - **Miniatura (533 x 324px)**: O arquivo de imagem deve estar no formato PNG.

Selecione **Salvar** depois de concluir esses campos.
