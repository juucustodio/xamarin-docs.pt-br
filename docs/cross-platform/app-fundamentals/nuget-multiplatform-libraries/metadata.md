---
title: Edição de metadados do NuGet
description: Use as opções de projeto para editar os metadados de NuGet de bibliotecas em várias plataformas
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6d30f564d54b96d358d37059f9dababaf8f3314e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
---
# <a name="editing-nuget-metadata"></a>Edição de metadados do NuGet

_Use as opções de projeto para editar os metadados de NuGet de bibliotecas em várias plataformas_

Tipos de projeto de biblioteca (como PCL .NET Standard ou o novo tipo de projeto do NuGet) tem um **pacote NuGet** seção o **opções de projeto** janela.

O **metadados** seção configura os valores usados o [ **. NuSpec** arquivo de manifesto de pacote do NuGet](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Informações necessárias

O **geral** guia contém quatro campos que devem ser inseridos para gerar um pacote do NuGet:

[![](metadata-images/metadata-general-sml.png "Janela de metadados necessários de pacote do NuGet.")](metadata-images/metadata-general.png#lightbox)

- **ID** – o identificador de pacote, que deve ser exclusivo em Nuget.org (ou onde quer que o pacote será distribuído). Siga este [orientação](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e use somente caracteres válidos em uma URL (sem espaços e evitar a maioria dos caracteres especiais).
- **Versão** – escolha um número de versão consistente com [regras de controle de versão do NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Os autores** – lista separada por vírgulas de nomes.
- **Descrição** – visão geral dos recursos do pacote que é exibido quando os usuários selecionam o pacote.

> [!NOTE]
> Lembre-se de incrementar o número de versão ao criar novas versões para distribuição a outros usuários ou NuGet.

Para obter mais informações, consulte o [referência de elementos necessários](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) para obter mais informações, bem como essas instruções detalhadas em [escolhendo um identificador exclusivo do pacote e definindo o número de versão](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e [ Um tipo de pacote de configuração](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Todos os campos dessa guia devem ser inseridos; Caso contrário, aparecerá uma mensagem de erro: _"o projeto não tem metadados de NuGet para um pacote do NuGet não será criado. Metadados de pacote do NuGet podem ser especificado na seção de metadados nas opções de projeto"_

## <a name="optional-metadata"></a>Metadados opcionais

O **detalhes** guia contém campos opcionais a serem incluídos no arquivo de manifesto do pacote NuGet.

[![](metadata-images/metadata-detail-sml.png "Janela de metadados opcionais de pacote do NuGet.")](metadata-images/metadata-detail.png#lightbox)

Consulte o [referência de elementos opcionais](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) para obter mais informações sobre os campos obrigatórios e opcionais.

> [!NOTE]
> Se o pacote do NuGet está sendo distribuído em [NuGet.org](https://www.nuget.org) é recomendável fornecer o máximo possível de informações.


## <a name="related-links"></a>Links relacionados

- [Referência do .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
