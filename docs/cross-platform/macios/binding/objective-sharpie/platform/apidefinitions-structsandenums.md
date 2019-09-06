---
title: Arquivos ApiDefinitions & StructsAndEnums
description: Este documento descreve os arquivos ApiDefinitions.cs e StructsAndEnums.cs que objetivam a nitidez. Esses arquivos são usados para acessar o código Objective-C de C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 950f9149744cb8aa2abaed60ccefb416405ab110
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290786"
---
# <a name="apidefinitions--structsandenums-files"></a>Arquivos ApiDefinitions & StructsAndEnums

Quando a nitidez do objetivo é executada com êxito, ela `Binding/ApiDefinitions.cs` gera `Binding/StructsAndEnums.cs` arquivos e.
Esses dois arquivos são adicionados a um projeto de associação no Visual Studio para Mac ou passados diretamente para `btouch` as `bmac` ferramentas ou para produzir a associação final.

Em *alguns* casos, esses arquivos gerados podem ser tudo o que você precisa. no entanto, com mais frequência, o desenvolvedor precisará modificar manualmente esses arquivos gerados para corrigir quaisquer problemas que não puderam ser manipulados automaticamente pela ferramenta (como aqueles sinalizados com um [ `Verify`atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algumas das próximas etapas incluem:

- **Ajustando nomes**: Às vezes, você desejará ajustar os nomes de métodos e classes para corresponder às diretrizes de design de .NET Framework.
- **Métodos ou Propriedades**: Às vezes, a heurística usada pela nitidez objetiva escolherá um método a ser transformado em uma propriedade. Neste ponto, você pode decidir se esse é o comportamento pretendido ou não.
- **Eventos de conexão**: Você pode vincular suas classes às suas classes de representante e gerar eventos automaticamente para elas.
- **Notificações de conexão**: Não é possível extrair o contrato de API de notificações dos arquivos de cabeçalho puros, isso exigirá uma viagem para a documentação da API. Se você quiser notificações com rigidez de tipos, será necessário atualizar o resultado.
- Organização de **API**: Neste ponto, você pode optar por fornecer construtores extras, adicionar métodos (para permitir a sintaxe de C# inicialização na construção), o operador está sobrecarregando e implementando suas próprias interfaces no arquivo de definições extras.

Consulte a descrição [ligando uma API](~/cross-platform/macios/binding/objective-c-libraries.md) para ver como esses arquivos se encaixam no processo de ligação, conforme mostrado no diagrama a seguir:

![](apidefinitions-structsandenums-images/binding-flowchart.png "O processo de associação é mostrado neste diagrama")

Consulte a [referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md) para obter mais informações sobre o conteúdo desses arquivos.

