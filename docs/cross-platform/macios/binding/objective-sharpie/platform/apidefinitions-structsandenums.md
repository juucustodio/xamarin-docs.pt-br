---
title: Arquivos ApiDefinitions & StructsAndEnums
description: Este documento descreve os arquivos ApiDefinitions.cs e StructsAndEnums.cs que objetivam a nitidez. Esses arquivos são usados para acessar o código Objective-C do C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 8d4a05745d8d2ec6e05abd519aef4b9827655e06
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930423"
---
# <a name="apidefinitions--structsandenums-files"></a>Arquivos ApiDefinitions & StructsAndEnums

Quando a nitidez do objetivo é executada com êxito, ela `Binding/ApiDefinitions.cs` gera `Binding/StructsAndEnums.cs` arquivos e.
Esses dois arquivos são adicionados a um projeto de associação no Visual Studio para Mac ou passados diretamente para `btouch` as `bmac` ferramentas ou para produzir a associação final.

Em *alguns* casos, esses arquivos gerados podem ser tudo o que você precisa. no entanto, com mais frequência, o desenvolvedor precisará modificar manualmente esses arquivos gerados para corrigir quaisquer problemas que não puderam ser manipulados automaticamente pela ferramenta (como aqueles sinalizados com um [ `Verify` atributo](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Algumas das próximas etapas incluem:

- **Ajustando nomes**: às vezes, você desejará ajustar os nomes de métodos e classes para corresponder às diretrizes de Design de .NET Framework.
- **Métodos ou Propriedades**: a heurística usada pela nitidez objetiva às vezes escolherá um método para ser transformado em uma propriedade. Neste ponto, você pode decidir se esse é o comportamento pretendido ou não.
- **Eventos de conexão**: você pode vincular suas classes com suas classes de representante e gerar eventos automaticamente para elas.
- **Notificações de conexão**: não é possível extrair o contrato de API de notificações dos arquivos de cabeçalho puros. isso exigirá uma viagem para a documentação da API. Se você quiser notificações com rigidez de tipos, será necessário atualizar o resultado.
- Organização de **API**: neste ponto, você pode optar por fornecer construtores extras, adicionar métodos (para permitir a sintaxe de inicialização na construção do C#), o operador está sobrecarregando e implementando suas próprias interfaces no arquivo de definições extras.

Consulte a descrição [ligando uma API](~/cross-platform/macios/binding/objective-c-libraries.md) para ver como esses arquivos se encaixam no processo de ligação, conforme mostrado no diagrama a seguir:

![O processo de associação é mostrado neste diagrama](apidefinitions-structsandenums-images/binding-flowchart.png)

Consulte a [referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md) para obter mais informações sobre o conteúdo desses arquivos.
