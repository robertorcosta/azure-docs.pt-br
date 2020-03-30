---
title: Logic App Test Drive | Mercado Azure
description: Explica como criar o Test Drive que se conecta com uma instância do Dynamics AX/CRM ou com qualquer outro recurso fora do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278375"
---
<a name="logic-app-test-drive"></a>Test Drive de Aplicativo Lógico
====================

Este artigo é voltado a editores que têm sua oferta no AppSource e que desejam criar um Test Drive conectado com uma instância do Dynamics AX/CRM ou com qualquer outro recurso fora Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Como criar um Test Drive de Aplicativo Lógico
-----------------------------------

A documentação do Test Drive para logic app test drives ainda está no GitHub para [operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [engajamento do cliente,](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)vá lá para ler mais.

<a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive
---------------------------

Agora que o seu Test Drive foi criado, esta seção explicará cada um dos campos necessários para publicá-lo com êxito.

![Habilitar o recurso de Test Drive](./media/azure-resource-manager-test-drive/howtopub1.png)

O primeiro e mais importante campo é alternar se você quer testar o formulário com todos os campos necessários são apresentados para você preencher. Quando você seleciona **Não,** o formulário fica desativado e se você republicar com a Unidade de Teste desativada, sua Unidade de Teste será removida da produção.

*Observação*: se houver Test Drives ativamente usados por usuários, eles continuarão sendo executados até a sessão expirar.

### <a name="details"></a>Detalhes

A próxima seção para preencher são os detalhes sobre sua oferta de Test Drive.

![Detalhes do Test Drive](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrição –** *[Campo Obrigatório]* É onde você escreve a descrição principal daquilo que consta no seu Test Drive. O cliente usará este recurso para ler sobre os cenários relacionados ao produto, incluídos no seu Test Drive. 

**Manual do Usuário –** *[Campo Obrigatório]* Trata-se do passo a passo detalhado da sua experiência de Test Drive. O cliente vai abri-lo e poderá ver exatamente o que você deseja que ele faça durante seu Test Drive. É importante que o conteúdo seja fácil de entender e seguir! Deve ser um arquivo .pdf.

**Vídeo de Demonstração do Test Drive –** \[Recomendado\] De modo semelhante ao Manual do Usuário, é melhor incluir um tutorial em vídeo da sua experiência com o Test Drive. O cliente vai assisti-lo antes ou durante o Test Drive e poderá ver exatamente o que você deseja que ele faça durante seu Test Drive. É importante que o conteúdo seja fácil de entender e seguir!

- **Nome** – Título do vídeo
- **Link** – Deve ser uma URL inserida do YouTube ou Vimeo. A seguir há um exemplo de como obter a URL inserida:
- **Miniatura** ‒ Deve ser uma imagem de alta qualidade em pixels (533 x 324). É recomendável fazer uma captura de tela de alguma parte da sua experiência com o Test Drive aqui.

Veja abaixo como esses campos aparecem para o cliente durante a experiência com o Test Drive.

![Campos e aparência do Test Drive](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuração Técnica

A próxima seção a ser preenchida é onde você pode configurar o Aplicativo Lógico de Test Drive e definir especificamente como as instâncias de Test Drive funcionam.

![Configuração de técnicas de Test Drive](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Região** - *[Campo obrigatório]* A região selecionada é onde você escolhe onde os recursos do aplicativo Test Drive Logic estão implantados.

    *Observação:* se o Aplicativo Lógico tiver recursos personalizados armazenados em uma região, verifique se essa região está selecionada aqui. A melhor maneira de fazer isso é **implantar de forma integral o Aplicativo Lógico localmente em sua assinatura do Azure no portal e verificar se ele funciona** antes de gravá-lo aqui.

- **Drives de teste simultâneos** - *máximos [campo obrigatório]* Número de instâncias de test drive que já estão implantadas e aguardam acesso por região selecionada. Os clientes podem acessar esses Test Drives instantaneamente em vez de ter que esperar por uma implantação.

    *Observação:* se você estiver executando um webinar/aula em que deseje que todos os seus N alunos façam um Test Drive, será recomendável publicar com um número N de instâncias de acesso frequente e, quando a aula terminar, republicar de volta para o número normal de instâncias de acesso frequente.

- **Duração do Test Drive (horas) –** *[Campo Obrigatório]* Período durante o qual o Test Drive permanecerá ativo, em \# de horas. O Test Drive é encerrado automaticamente após o término desse período de tempo.

- **Nome do Grupo de Recursos do Azure ‒** *[Campo Obrigatório]* Grave o nome do Grupo de Recursos onde os Test Drives de Aplicativo Lógico estão salvos.

- **Atribuir Nome de Aplicativo Lógico ‒** *[Campo Obrigatório]* Grave o Aplicativo Lógico que é usado para atribuir um usuário no Test Drive, antes que o cliente o obtenha. Grave o nome do Aplicativo Lógico aqui. Verifique se esse arquivo foi salvo no Grupo de Recursos acima.

- **Desprovisionar o Nome do Aplicativo Lógico ‒** *[Campo Obrigatório]* Grave o nome do Aplicativo Lógico para o desprovisionamento de todos os recursos criados no Test Drive. Verifique se esse arquivo foi salvo no Grupo de Recursos acima.

- **Informações de acesso ‒** *[Campo Obrigatório]* Depois que um cliente obtém seu Test Drive, as informações de acesso são apresentadas a ele. Essas instruções servem para compartilhar os parâmetros de saída úteis do seu modelo do Resource Manager para Test Drive. Para incluir parâmetros de saída, utilize chaves duplas (por exemplo, **{{outputname}}**); eles serão inseridos corretamente no local. A formatação da cadeia de caracteres HTML é recomendada aqui para renderizar no front-end.

### <a name="test-drive-deployment-subscription-details"></a>Detalhes da assinatura para implantação do Test Drive

A seção final a ser preenchida deve habilitar a implementação  automática dos Test Drives, conectando sua Assinatura do Azure e do Azure AD (Azure Active Directory).

![Detalhes da assinatura para implantação do Test Drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID da Assinatura do Azure ** *[Campo Obrigatório]* Concede acesso aos serviços do Azure e ao portal do Azure. A assinatura é onde o uso de recursos é relatado e os serviços são cobrados. Caso ainda não tenha uma Assinatura do Azure **separada** apenas para Test Drives, crie uma. Para encontrar as IDs de Assinatura do Azure, entre no portal do Azure e navegue até Assinaturas no menu do lado esquerdo.
(Exemplo: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Assinaturas do Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID do Locatário do Azure Active Directory** *[Campo Obrigatório]* Se tiver um ID do Locatário já disponível, poderá encontrá-lo abaixo em Propriedades –\> ID do Diretório.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Caso contrário, crie um novo Locatário no Azure Active Directory.

![Tela de propriedades do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

!Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Locatários do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID do Aplicativo Azure AD** *[Campo Obrigatório]* O próximo passo é criar e registrar um novo aplicativo. Usaremos esse aplicativo para realizar operações em sua instância do Test Drive.

1. Navegue até o diretório criado recentemente ou o diretório preexistente e selecione o Azure Active Directory no painel de filtro.
2. Pesquise "Registros de aplicativo" e clique em "Adicionar"
3. Forneça o nome do aplicativo.
4. Selecione o tipo como "Aplicativo Web/API"
5. Forneça qualquer valor na URL de entrada; \'não utilizaremos esse campo.
6. Clique em Criar.
7. Depois que o aplicativo tiver sido criado, acesse Propriedades ‒\> Defina o aplicativo como vários locatários e pressione Salvar.

Clique em Salvar. O último passo é pegar o ID do aplicativo para esse aplicativo registrado e colá-lo aqui, no campo Test Drive.

![ID do aplicativo do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Como estamos usando o aplicativo para implantar a assinatura, precisamos adicionar o aplicativo como colaborador na assinatura. Estas são as instruções:

1. Navegue até a folha Assinaturas e selecione a assinatura adequada que você usa apenas para o Test Drive.
1. Clique **em Acessar controle (IAM)**.
1. Clique na guia **'Atribuições de** função'.  ![Diretório ativo do Azure, adicionando um novo principal de controle de acesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Clique em **Adicionar atribuição de função**.
1. Defina o papel como **Contribuinte**.
1. Digite o nome do aplicativo do Azure AD e selecione o aplicativo para atribuir a função.
    ![Permissões do Azure Active Directory](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Clique em **Salvar**.

**Chave do Aplicativo Azure AD** *[Campo Obrigatório]* O campo final é para a geração de uma chave de autenticação. Em chaves, adicione uma Descrição da chave, defina a duração para nunca expirar e, em seguida, selecione Salvar. É **importante** evitar ter uma chave expirada, que interromperá seu Test Drive na produção. Copie esse valor e cole-o no campo Test Drive obrigatório.

![Seção de Chaves do Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Não é possível usar a visualização de registro do aplicativo Azure porque não gera atualmente uma chave codificada base64.


<a name="next-steps"></a>Próximas etapas
----------

Agora que preencheu todos os campos do Test Drive, prossiga e **Republique** sua oferta. Quando o Test Drive for aprovado no processo de certificação, você deverá testar extensivamente a experiência do cliente na **versão prévia** da sua oferta. Inicie um Test Drive na interface do usuário e verifique se os Test Drives estão sendo totalmente implantados corretamente.

É importante cuidar para não excluir nenhuma parte do Test Drive conforme elas são provisionadas para seus clientes. Assim, o serviço do Test Drive removerá automaticamente esses Grupos de Recursos depois que um cliente tiver encerrado.

Uma vez que você se sinta confortável com sua oferta de Preview, agora é hora **de ir ao ar**! Há um processo de revisão final da Microsoft depois que a oferta é publicada para nova verificação da experiência inteira, de ponta a ponta. Se, por algum motivo, a oferta for rejeitada, enviaremos uma notificação ao contato de engenharia da sua oferta, explicando o que precisará ser corrigido.

Se você tiver mais dúvidas, estiver procurando ajuda para solução de problemas ou quiser um test drive de maior sucesso, acesse [Perguntas frequentes, solução de problemas e práticas recomendadas](./marketing-and-best-practices.md).
