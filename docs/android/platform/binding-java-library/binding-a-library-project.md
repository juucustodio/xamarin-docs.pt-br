---
title: Associação de um projeto de biblioteca do Eclipse
description: Este passo a passo explica como usar modelos de projeto do xamarin. Android para associar um projeto de biblioteca Android do Eclipse.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a5887748eda8af89b9215e3e121db592dfa73935
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957605"
---
# <a name="binding-an-eclipse-library-project"></a>Associação de um projeto de biblioteca do Eclipse

_Este passo a passo explica como usar modelos de projeto do xamarin. Android para associar um projeto de biblioteca Android do Eclipse._


## <a name="overview"></a>Visão geral

Embora. Arquivos AAR estão se tornando a norma para distribuição de biblioteca do Android, em alguns casos, é necessário criar uma associação para um *projeto de biblioteca Android*. Projetos de biblioteca Android são projetos Android especiais que contêm código podem ser compartilhado e recursos que podem ser referenciados por projetos de aplicativo Android. Normalmente, você associa a um projeto de biblioteca Android quando a biblioteca for criada no IDE do Eclipse.
Este passo a passo fornece exemplos de como criar um projeto de biblioteca Android. ZIP da estrutura de diretório de um projeto do Eclipse.

Projetos de biblioteca Android são diferentes de projetos Android regulares, em que eles não são compilados em um APK e não são, por conta própria, pode ser implantado em um dispositivo. Em vez disso, um projeto de biblioteca do Android deve ser referenciado por um projeto de aplicativo do Android. Quando um projeto de aplicativo Android é compilado, o projeto de biblioteca Android é compilado pela primeira vez. O projeto de aplicativo Android, em seguida, ser absorvido para o projeto de biblioteca Android compilado e incluem o código e os recursos para o APK para distribuição. Por causa dessa diferença, criando uma associação de um projeto de biblioteca do Android é um pouco diferente de criar uma associação para um Java. JAR ou. Arquivo AAR.



## <a name="walkthrough"></a>Passo a passo

Para usar um projeto de biblioteca do Android em um projeto de associação do xamarin. Android Java primeiro é necessário para compilar o projeto de biblioteca do Android no Eclipse. Captura de tela a seguir mostra um exemplo de um projeto de biblioteca Android após a compilação: 

[![Exemplo de projeto de biblioteca no Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Observe que o código-fonte do projeto de biblioteca Android foi compilado para um temporário. Arquivo JAR denominado **android mapviewballoons.jar**, e que os recursos tiverem sido copiados para o **res/bin/Fragmente** pasta. 

Depois que o projeto de biblioteca Android tiver sido compilado no Eclipse, ele pode então ser associado usando um projeto de associação do xamarin. Android Java. Primeiro um. Deve ser criado o arquivo ZIP que contém o **bin** e **res** pastas do projeto de biblioteca Android. É importante que você remova a intervenção **Fragmente** subdiretório para que os recursos residem na **bin/res**. Captura de tela a seguir mostra o conteúdo de um tal. Arquivo ZIP: 

[![Conteúdo de. zip de projeto de biblioteca do Android](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Isso. Arquivo ZIP é adicionado ao projeto xamarin. Android Java de associação, conforme mostrado na seguinte captura de tela:

[![ZIP adicionado ao projeto de associação de Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Observe que a ação de compilação da. Arquivo ZIP foi automaticamente definido como **LibraryProjectZip**.

Caso haja algum. Arquivos JAR necessários para o projeto de biblioteca do Android, eles devem ser adicionados para o **Jars** pasta do projeto de biblioteca de vinculação de Java e o **Build Action** definido como **ReferenceJar**. Um exemplo disso pode ser visto na captura de tela abaixo: 

[![Ação de compilação definido como ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

Quando essas etapas forem concluídas, o projeto de associação do xamarin. Android Java pode ser usado conforme descrito anteriormente neste documento.

> [!NOTE]
> Não há suporte para a compilação de projetos de biblioteca do Android em outros IDEs neste momento. Outros IDEs não pode criar a mesma estrutura de diretórios ou arquivos na **bin** pasta do Eclipse. 


## <a name="summary"></a>Resumo

Neste artigo, percorremos o processo de associar um projeto de biblioteca Android. Criamos o projeto de biblioteca do Android no Eclipse, em seguida, criamos um arquivo zip do **bin** e **res** pastas do projeto de biblioteca Android. Em seguida, usamos esse zip para criar um projeto de associação do xamarin. Android Java. 

