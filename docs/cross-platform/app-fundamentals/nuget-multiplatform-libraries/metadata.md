---
title: Editar metadados do NuGet
description: Este documento descreve como usar as opções de projeto para editar os metadados do NuGet de bibliotecas de várias plataformas. Ele aborda os metadados necessários e opcionais.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3680b02003a844668b0b5c97e5d4c0d296ae3500
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266849"
---
# <a name="editing-nuget-metadata"></a>Editar metadados do NuGet

_Use as opções de projeto para editar os metadados do NuGet de bibliotecas multiplataforma_

Tipos de projeto de biblioteca (como PCL ou .NET Standard ou o novo tipo de projeto NuGet) têm uma **pacote do NuGet** seção o **opções de projeto** janela.

O **metadados** seção configura os valores usados na [ **. NuSpec** arquivo de manifesto de pacote do NuGet](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Informações necessárias

O **geral** guia contém quatro campos que devem ser inseridos para gerar um pacote do NuGet:

[![](metadata-images/metadata-general-sml.png "Janela de metadados necessários de pacotes do NuGet")](metadata-images/metadata-general.png#lightbox)

- **ID** – o identificador de pacote, que deve ser exclusivo em Nuget.org (ou onde quer que o pacote será distribuído). Siga esse [orientação](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e use apenas caracteres válidos em uma URL (sem espaços e evitar a maioria dos caracteres especiais).
- **Versão** – escolha um número de versão consistente com [regras de controle de versão do NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Os autores** – lista separada por vírgulas de nomes.
- **Descrição** – visão geral dos recursos do pacote que é exibido quando os usuários selecionam o pacote.

> [!NOTE]
> Lembre-se de incrementar o número de versão ao criar novas versões para distribuição a NuGet ou outros usuários.

Para obter mais informações, consulte o [referência de elementos necessários](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) para obter mais informações, bem como essas instruções detalhadas sobre [escolhendo um identificador de pacote exclusivo e definindo o número de versão](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e [ Definindo um tipo de pacote](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Todos os campos dessa guia devem ser inseridos; Caso contrário, será exibida uma mensagem de erro: _"O projeto não tem metadados do NuGet para que um pacote do NuGet não será criado. Metadados de pacote do NuGet podem ser especificados na seção de metadados nas opções do projeto"_

## <a name="optional-metadata"></a>Metadados opcionais

O **detalhes** guia contém os campos opcionais a serem incluídos no arquivo de manifesto do pacote NuGet.

[![](metadata-images/metadata-detail-sml.png "Janela de metadados opcionais de pacotes do NuGet")](metadata-images/metadata-detail.png#lightbox)

Consulte a [referência de elementos opcionais](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) para obter mais informações sobre os campos obrigatórios e opcionais.

> [!NOTE]
> Se o pacote do NuGet está sendo distribuído na [NuGet.org](https://www.nuget.org) é recomendável fornecer o máximo possível de informações.


## <a name="related-links"></a>Links relacionados

- [Referência do .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
