---
title: Referência de atributo SAP SuccessFactors | Microsoft Docs
description: Saiba quais atributos de SuccessFactors são suportados pelo provisionamento controlado por SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971104"
---
# <a name="sap-successfactors-attribute-reference"></a>Referência de atributo SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Atributos e entidades SuccessFactors com suporte

A tabela a seguir captura a lista de atributos SuccessFactors com suporte nos dois aplicativos de provisionamento a seguir: 
* [SuccessFactors para Active Directory provisionamento de usuário](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors para provisionamento de usuário do Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entidade SuccessFactors                  | Atributo SuccessFactors     | Tipo de operação |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Perpessoa                              | personIdExternal             | Leitura           |
| 2  | Perpessoa                              | personId                     | Leitura           |
| 3  | Perpessoa                              | perPersonUuid                | Leitura           |
| 4  | Entre pessoas                            | displayName                  | Leitura           |
| 5  | Entre pessoas                            | firstName                    | Leitura           |
| 6  | Entre pessoas                            | gender                       | Leitura           |
| 7  | Entre pessoas                            | lastName                     | Leitura           |
| 8  | Entre pessoas                            | middleName                   | Leitura           |
| 9  | Entre pessoas                            | preferência                | Leitura           |
| 10 | Usuário                                   | addressLine1                 | Leitura           |
| 11 | Usuário                                   | Endereço2                 | Leitura           |
| 12 | Usuário                                   | addressLIne3                 | Leitura           |
| 13 | Usuário                                   | Ficha                | Leitura           |
| 14 | Usuário                                   | Celular                    | Leitura           |
| 15 | Usuário                                   | city                         | Leitura           |
| 16 | Usuário                                   | country                      | Leitura           |
| 17 | Usuário                                   | custom01                     | Leitura           |
| 18 | Usuário                                   | custom02                     | Leitura           |
| 19 | Usuário                                   | custom03                     | Leitura           |
| 20 | Usuário                                   | custom04                     | Leitura           |
| 21 | Usuário                                   | custom05                     | Leitura           |
| 22 | Usuário                                   | custom06                     | Leitura           |
| 23 | Usuário                                   | custom07                     | Leitura           |
| 24 | Usuário                                   | custom08                     | Leitura           |
| 25 | Usuário                                   | custom09                     | Leitura           |
| 26 | Usuário                                   | personalizado10                     | Leitura           |
| 27 | Usuário                                   | custom11                     | Leitura           |
| 28 | Usuário                                   | custom12                     | Leitura           |
| 29 | Usuário                                   | custom13                     | Leitura           |
| 30 | Usuário                                   | custom14                     | Leitura           |
| 31 | Usuário                                   | empId                        | Leitura           |
| 32 | Usuário                                   | homePhone                    | Leitura           |
| 33 | Usuário                                   | jobFamily                    | Leitura           |
| 34 | Usuário                                   | nickname                     | Leitura           |
| 35 | Usuário                                   | state                        | Leitura           |
| 36 | Usuário                                   | timeZone                     | Leitura           |
| 37 | Usuário                                   | Nome de Usuário                     | Leitura           |
| 38 | Usuário                                   | zipCode                      | Leitura           |
| 11,8 | Por telefone                               | areaCode                     | Leitura           |
| 40 | Por telefone                               | countryCode                  | Leitura           |
| 41 | Por telefone                               | extensão                    | Leitura           |
| 42 | Por telefone                               | phoneNumber                  | Leitura           |
| 43 | Por telefone                               | telefonetype                    | Leitura           |
| 44 | Enviar por email                               | emailAddress                 | Leitura, Gravação    |
| 45 | Enviar por email                               | emailtype                    | Leitura           |
| 46 | EmpEmployment                          | firstDateWorked              | Leitura           |
| 47 | EmpEmployment                          | lastDateWorked               | Leitura           |
| 48 | EmpEmployment                          | userId                       | Leitura           |
| 49 | EmpEmployment                          | isContingentWorker           | Leitura           |
| 50 | EmpJob                                 | countryOfCompany             | Leitura           |
| 51 | EmpJob                                 | emplStatus                   | Leitura           |
| 52 | EmpJob                                 | endDate                      | Leitura           |
| 53 | EmpJob                                 | startDate                    | Leitura           |
| 54 | EmpJob                                 | jobTitle                     | Leitura           |
| 55 | EmpJob                                 | position                     | Leitura           |
| 65 | EmpJob                                 | customString13               | Leitura           |
| 56 | EmpJob                                 | managerId                    | Leitura           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Leitura           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Leitura           |
| 59 | EmpJob\.empresa                        | company                      | Leitura           |
| 60 | EmpJob\.empresa                        | companyId                    | Leitura           |
| 61 | EmpJob\.empresa\.CountryOfRegistration | twoCharCountryCode           | Leitura           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Leitura           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Leitura           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Leitura           |
| 65 | Departamento de\.de EmpJob                     | department                   | Leitura           |
| 66 | Departamento de\.de EmpJob                     | departmentId                 | Leitura           |
| 67 | Divisão de\.de EmpJob                       | division                     | Leitura           |
| 68 | Divisão de\.de EmpJob                       | divisionId                   | Leitura           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Leitura           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Leitura           |
| 71 | EmpJob\.local                       | LocalName                 | Leitura           |
| 72 | EmpJob\.local                       | officeLocationAddress        | Leitura           |
| 73 | EmpJob\.local                       | officeLocationCity           | Leitura           |
| 74 | EmpJob\.local                       | officeLocationCustomString4  | Leitura           |
| 75 | EmpJob\.local                       | officeLocationZipCode        | Leitura           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Leitura           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Leitura           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Leitura           |


## <a name="default-attribute-mapping"></a>Mapeamento de atributo padrão

A tabela a seguir fornece o mapeamento de atributo padrão entre os atributos SuccessFactors listados acima e os atributos AD/Azure AD. Na folha "mapeamento" do aplicativo de provisionamento do Azure AD, você pode modificar esse mapeamento padrão para incluir atributos da lista acima. 

| \# | Entidade SuccessFactors                  | Atributo SuccessFactors | Mapeamento de atributo padrão AD/AD do Azure   | Comentário de processamento                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Perpessoa                              | personIdExternal         | employeeId                              | Usado como atributo correspondente                                                                   |
| 2  | Perpessoa                              | perPersonUuid            | \[não mapeado \- usado como âncora de origem\] | Durante a sincronização inicial, o serviço de provisionamento vincula o personUuid a objectGuid\. existentes  |
| 3  | Entre pessoas                            | displayName              | displayName                             | ND                                                                                           |
| 4  | Entre pessoas                            | firstName                | givenName                               | ND                                                                                           |
| 5  | Entre pessoas                            | lastName                 | sn                                      | ND                                                                                           |
| 6  | Usuário                                   | addressLine1             | streetAddress                           | ND                                                                                           |
| 7  | Usuário                                   | city                     | l                                       | ND                                                                                           |
| 8  | Usuário                                   | country                  | co                                      | ND                                                                                           |
| 9  | Usuário                                   | state                    | st                                      | ND                                                                                           |
| 10 | Usuário                                   | Nome de Usuário                 | samAccountName                          | ND                                                                                           |
| 11 | Usuário                                   | zipCode                  | postalCode                              | ND                                                                                           |
| 12 | Enviar por email                               | emailAddress             | mail                                    | ND                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | título                                   | ND                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | ND                                                                                           |
| 15 | EmpJob\.empresa\.CountryOfRegistration | twoCharCountryCode       | c                                       | ND                                                                                           |
| 16 | Departamento de\.de EmpJob                     | department               | department                              | ND                                                                                           |
| 17 | Divisão de\.de EmpJob                       | division                 | company                                 | ND                                                                                           |
| 18 | EmpJob\.local                       | officeLocationAddress    | streetAddress                           | ND                                                                                           |
| 19 | EmpJob\.local                       | officeLocationZipCode    | postalCode                              | ND                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | se activeEmploymentsCount = 0, desabilite o account\.                                           |

