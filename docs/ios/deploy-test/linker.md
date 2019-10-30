---
title: Usando Linker em aplicativos Xamarin.iOS
description: Este documento descreve o Linker do Xamarin.iOS, que é usado para eliminar código não utilizado de um aplicativo Xamarin.iOS a fim de reduzir seu tamanho.
ms.prod: xamarin
ms.assetid: 3A4B2178-F264-0E93-16D1-8C63C940B2F9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: 284705973f9c0ec606572170f7e927ed4745ddd1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030233"
---
# <a name="linking-xamarinios-apps"></a>Usando Linker em aplicativos Xamarin.iOS

Ao compilar seu aplicativo, o Visual Studio para Mac ou o Visual Studio chama uma ferramenta chamada **mtouch**, que inclui um linker para código gerenciado. Ele é usado para remover os recursos que o aplicativo não está usando das class libraries. A meta é reduzir o tamanho do aplicativo, que será fornecido com apenas as partes necessárias.

O linker usa análise estática para determinar os diferentes caminhos de código que seu aplicativo é suscetível a seguir. Ela é um pouco pesada, já que precisa passar por todos os detalhes de cada assembly, para verificar se nada detectável foi removido. Ele não é habilitado por padrão em builds do simulador para acelerar o tempo de compilação durante a depuração. No entanto, já que ele produz aplicativos menores e que pode acelerar a compilação AOT e o upload para o dispositivo, todos os *builds de dispositivos (Release)* estão usando o linker por padrão.

Já que o linker é uma ferramenta estática, ele não pode marcar para inclusão tipos e métodos que são chamados por meio de reflection, ou instanciados dinamicamente. Há várias opções de soluções alternativas para essa limitação.

<a name="Linker_Behavior" />

## <a name="linker-behavior"></a>Comportamento do Linker

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

O processo de _linking_ pode ser personalizado por meio do menu suspenso de comportamento do linker em **Opções do Projeto**. Para acessá-lo, clique duas vezes no projeto iOS e navegue até **Build do iOS > Opções do Linker**, conforme ilustrado abaixo:

[![](linker-images/image1.png "Linker Options")](linker-images/image1.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

O processo de _linking_ pode ser personalizado por meio do menu suspenso de comportamento do linker nas **Propriedades do Projeto** no Visual Studio.

Faça o seguinte:

1. Clique com o botão direito do mouse no **Nome do Projeto** no **Gerenciador de Soluções** e selecione **Propriedades**:

    ![](linker-images/linking01w.png "Right-click on the Project Name in the Solution Explorer and select Properties")
2. Nas **Propriedades do Projeto**, selecione **Build do iOS**:

    ![](linker-images/linking02w.png "Select IOS Build")
3. Siga as instruções abaixo para alterar as opções do linker.

-----

As três principais opções oferecidas são descritas a seguir:

### <a name="dont-link"></a>Não Vincular

Desabilitar o _linker_ assegura que nenhum assembly seja modificado. Por motivos de desempenho, essa é a configuração padrão quando o seu IDE se destina ao simulador de iOS. Para builds de dispositivos, isso só deve ser usado como uma solução alternativa sempre que o linker contém um bug que impede que o aplicativo seja executado. Se o aplicativo funciona apenas com *-nolink*, envie um [relatório de bug](https://github.com/xamarin/xamarin-macios/issues/new).

Isso corresponde à opção *-nolink* ao usar a ferramenta de linha de comando mtouch.

<a name="Link_SDK_assemblies_only" />

### <a name="link-sdk-assemblies-only"></a>Vincular somente os assemblies do SDK

Nesse modo, o _linker_ deixará seus assemblies inalterados e reduzirá o tamanho dos assemblies do SDK (ou seja, o que é fornecido com o Xamarin.iOS) removendo tudo o que seu aplicativo não usa. Essa é a configuração padrão quando seu IDE se destina a dispositivos iOS.

Essa é a opção mais simples, pois não requer nenhuma alteração ao código. A diferença observada vinculando-se tudo é que o _linker_ não pode executar algumas otimizações nesse modo, portanto, então trata-se de uma compensação entre o trabalho necessário para vincular tudo e o tamanho final do aplicativo.

Isso corresponde à opção *-linksdk* ao usar a ferramenta de linha de comando mtouch.

<a name="Link_all_assemblies" />

### <a name="link-all-assemblies"></a>Vincular todos os assemblies

Ao vincular tudo, o _linker_ pode usar o conjunto completo de suas otimizações para tornar o aplicativo o menor possível. Ele modificará o código do usuário, que poderá falhar sempre que o código usar recursos de uma maneira não detectável pela análise estática do _linker_. Nesses casos, por exemplo, webservices, _reflection_ ou serialização, alguns ajustes podem ser necessários em seu aplicativo para vincular tudo.

Isso corresponde à opção *-linkall* ao usar a ferramenta de linha de comando **mtouch**.

<a name="Controlling_the_Linker" />

## <a name="controlling-the-linker"></a>Controlar o vinculador

Quando você usar o _linker_, ele às vezes removerá código que você pode ter chamado dinamicamente, ainda que indiretamente. Para abranger esses casos, o _linker_ fornece alguns recursos e opções para permitir maior controle sobre as ações dele.

<a name="Preserving_Code" />

### <a name="preserving-code"></a>Preservação do código

Quando você usa o _linker_, ele pode às vezes remover código que você pode ter chamado dinamicamente usando System.Reflection.MemberInfo.Invoke, ou então exportando seus métodos para Objective-C usando o atributo `[Export]` e, em seguida, invocando o seletor manualmente.

Nesses casos, você pode instruir o _linker_ para considerar classes inteiras para serem usadas ou então membros individuais a serem preservados, aplicando o atributo `[Xamarin.iOS.Foundation.Preserve]` no nível de classe ou no nível de membro. Cada membro que não está vinculado estaticamente pelo aplicativo está sujeito a ser removido. Portanto, este atributo é usado para marcar membros que não são referenciados estaticamente, mas que ainda são necessários para o seu aplicativo.

Por exemplo, se você cria uma instância de tipos dinamicamente, convém preservar o construtor padrão de seus tipos. Se você usar a serialização de XML, você talvez queira preservar as propriedades de seus tipos.

Você pode aplicar esse atributo a todos os membros de um tipo ou ao tipo propriamente dito. Se você quiser preservar o tipo inteiro, poderá usar a sintaxe `[Preserve
(AllMembers = true)]` no tipo.

Às vezes você deseja preservar a certos membros, mas somente se o tipo recipiente foi preservado. Nesses casos, use `[Preserve (Conditional=true)]`

Se você não desejar executar uma dependência em bibliotecas do Xamarin – por exemplo, digamos que você está criando uma PCL (biblioteca de classes portátil) de plataforma cruzada – você ainda poderá usar esse atributo.

Para fazer isso, você apenas deve declarar uma classe PreserveAttribute e usá-la em seu código, deste modo:

```csharp
public sealed class PreserveAttribute : System.Attribute {
    public bool AllMembers;
    public bool Conditional;
}
```

Não importa em qual namespace isso é definido, o _linker_ procura esse atributo pelo nome do tipo.

 <a name="Skipping_Assemblies" />

### <a name="skipping-assemblies"></a>Ignorar assemblies

É possível especificar os assemblies que devem ser excluídos do processo do _linker_, permitindo simultaneamente que outros assemblies sejam vinculados normalmente. Isso é útil se usar `[Preserve]` em alguns assemblies é impossível (por exemplo, com código de terceiros) ou como uma solução temporária para um bug.

Isso corresponde à opção `--linkskip` ao usar a ferramenta de linha de comando mtouch.

Ao usar opção **Vincular Todos os Assemblies**, se você quiser dizer ao vinculador para ignorar todos assemblies, coloque o seguinte nas opções de **Argumentos adicionais do mtouch** do seu assembly de nível superior:

```csharp
--linkskip=NameOfAssemblyToSkipWithoutFileExtension
```

Se você deseja que o _linker_ ignore vários assemblies, inclua vários argumentos `linkskip`:

```csharp
--linkskip=NameOfFirstAssembly --linkskip=NameOfSecondAssembly
```

Não há nenhuma interface do usuário para usar essa opção, mas ela pode ser fornecida na caixa de diálogo Opções do Projeto do Visual Studio para Mac ou no painel Propriedades do projeto do Visual Studio, dentro do campo de texto **Argumentos adicionais do mtouch**. (por exemplo, *--linkskip=mscorlib* não vincularia mscorlib, mas vincularia outros assemblies na solução).

<a name="Disabling_Link_Away" />

### <a name="disabling-link-away"></a>Desabilitar a Remoção

O _linker_ removerá código que tem uma chance muito baixa de ser usado em dispositivos, por exemplo, que não é compatível ou não é permitido. Em raras ocasiões, é possível que um aplicativo ou a biblioteca dependa desse código (funcionando ou não). Desde o Xamarin.iOS 5.0.1, o _linker_ pode ser instruído a ignorar essa otimização.

Isso corresponde à opção *-nolinkaway* ao usar a ferramenta de linha de comando mtouch.

Não há nenhuma interface do usuário para usar essa opção, mas ela pode ser fornecida na caixa de diálogo Opções do Projeto do Visual Studio para Mac ou no painel Propriedades do projeto do Visual Studio, dentro do campo de texto **Argumentos adicionais do mtouch**. (por exemplo, *--nolinkaway* não removeria o código extra (cerca de 100 KB)).

### <a name="marking-your-assembly-as-linker-ready"></a>Marcar o assembly como pronto para o Linker

Os usuários podem selecionar a opção de apenas vincular os assemblies do SDK, sem nenhuma vinculação ao seu código.  Isso também significa que todas as bibliotecas de terceiros que não fazem parte do SDK principal do Xamarin não serão vinculadas.

Isso normalmente acontece porque eles não desejam adicionar manualmente atributos `[Preserve]` ao seu código.  O efeito colateral é que bibliotecas de terceiros não serão vinculadas, o que geralmente é um bom padrão, pois não é possível saber se uma outra biblioteca de terceiros é compatível com o _linker_ ou não.

Se você tiver uma biblioteca no seu projeto ou se você for um desenvolvedor de bibliotecas reutilizáveis e desejar que o _linker_ trate o assembly como vinculável, tudo o que você precisa fazer é adicionar o atributo no nível de assembly [`LinkerSafe`](xref:Foundation.LinkerSafeAttribute), assim:

```csharp
[assembly:LinkerSafe]
```

A biblioteca não precisa efetivamente fazer referência às bibliotecas Xamarin para isso.  Por exemplo, se você está compilando uma biblioteca de classes portátil que será executada em outras plataformas, pode ainda usar um atributo `LinkerSafe`.
O _linker_ do Xamarin procura o atributo `LinkerSafe` por nome, não pelo seu tipo.  Isso significa que você pode escrever este código e ele também funcionará:

```csharp
[assembly:LinkerSafe]
// ... assembly attribute should be at top, before source
class LinkerSafeAttribute : System.Attribute {}
```

## <a name="custom-linker-configuration"></a>Configuração personalizada do Linker

Siga as [instruções para criar um arquivo de configuração do vinculador](~/cross-platform/deploy-test/linker.md).

## <a name="related-links"></a>Links relacionados

- [Configuração personalizada do Linker](~/cross-platform/deploy-test/linker.md)
- [Usando Linker no Mac](~/mac/deploy-test/linker.md)
- [Usando Linker no Android](~/android/deploy-test/linker.md)
