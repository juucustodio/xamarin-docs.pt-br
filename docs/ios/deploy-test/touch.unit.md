---
title: Teste de unidade de aplicativos Xamarin.iOS
description: Este documento fornece uma visão geral de como fazer teste de unidade de um aplicativo Xamarin.iOS. Ele descreve como criar um projeto de teste de unidade, escrever testes e executar testes.
ms.prod: xamarin
ms.assetid: BD959779-3239-79B6-5289-3A9ECDFBD973
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 98855bb51552edcd567e3b99740f0be04034e388
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628963"
---
# <a name="unit-testing-xamarinios-apps"></a>Teste de unidade de aplicativos Xamarin.iOS

Este documento descreve como criar testes de unidade para seus projetos do Xamarin.iOS.
Os testes de unidade com o Xamarin.iOS são feitos usando a estrutura Touch.Unit que inclui um executor de teste iOS, bem como uma versão modificada do NUnit chamado [Touch.Unit](https://github.com/xamarin/Touch.Unit) que fornece um conjunto familiar de APIs para gravar testes de unidade.

## <a name="setting-up-a-test-project-in-visual-studio-for-mac"></a>Configurar um projeto de teste no Visual Studio para Mac

 Para configurar uma estrutura de teste de unidade para seu projeto, tudo o que você precisa fazer é adicionar à solução um projeto do tipo **Projeto de Testes de Unidade iOS**. Faça isso clicando com o botão direito do mouse na sua solução e selecionar **Adicionar > Adicionar Novo Projeto**. Na lista, selecione **iOS > Testes > API Unificado > Projeto de Testes de Unidade iOS** (você pode escolher C# ou F#).

![Escolha C# ou F#](touch.unit-images/00.png)

O item acima criará um projeto básico que contém um programa de execução básico e que faz referência ao novo assembly MonoTouch.NUnitLite, assim seu projeto terá esta aparência:

![O projeto no Gerenciador de Soluções](touch.unit-images/01.png)

A classe `AppDelegate.cs` contém o executor de teste e tem esta aparência:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TouchRunner runner;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow (UIScreen.MainScreen.Bounds);
        runner = new TouchRunner (window);

        // register every tests included in the main application/assembly
        runner.Add (System.Reflection.Assembly.GetExecutingAssembly ());

        window.RootViewController = new UINavigationController (runner.GetViewController ());

        // make the window visible
        window.MakeKeyAndVisible ();

        return true;
    }
}
```

> [!NOTE]
> O tipo de projeto de teste de unidade do iOS não está disponível no Visual Studio 2019 ou no Visual Studio 2017 no Windows.

## <a name="writing-some-tests"></a>Gravando alguns testes

Agora que você tem o shell básico in-loco, você deve gravar seu primeiro conjunto de testes.

Testes são gravados ao criar classes que têm o atributo `[TestFixture]` aplicado a elas. Dentro de cada classe de TestFixture, você deve aplicar o atributo `[Test]` a cada método que você deseja que o executor de teste invoque. Os acessórios de teste reais podem residir em qualquer arquivo no seu projeto de testes.

Para começar a usar rapidamente selecione **Adicionar/Adicionar Novo Arquivo** e selecione o grupo **UnitTests** do Xamarin.iOS. Isso adicionará um arquivo esqueleto que contém um teste aprovado, um teste não aprovado e um teste ignorado e tem esta aparência:

```csharp
using System;
using NUnit.Framework;

namespace Fixtures {

    [TestFixture]
    public class Tests {

        [Test]
        public void Pass ()
        {
                Assert.True (true);
        }

        [Test]
        public void Fail ()
        {
                Assert.False (true);
        }

        [Test]
        [Ignore ("another time")]
        public void Ignore ()
        {
                Assert.True (false);
        }
    }
}
```

## <a name="running-your-tests"></a>Executando seus testes

Para executar este projeto dentro de sua solução, clique com botão direito do mouse nele e selecione **Depurar Item** ou **Executar Item**.

O executor de teste permite que você veja quais testes são registrados e selecione individualmente os testes que podem ser executados.

[ ![ A lista de testes registrados](touch.unit-images/02-sml.png)](touch.unit-images/02.png#lightbox)  
 [ ![ de um texto individual](touch.unit-images/03-sml.png)](touch.unit-images/03.png#lightbox) 

[![Os resultados da execução](touch.unit-images/04-sml.png)](touch.unit-images/04.png#lightbox)

Você pode executar acessórios de teste individuais selecionando o acessório texto nas exibições aninhadas ou você pode executar todos os seus testes com "Executar tudo". Se você executar o teste padrão, ele deverá incluir um teste aprovado, um não aprovado e um ignorado. Esta é a aparência do relatório e você pode fazer uma busca detalhada diretamente nos testes não aprovados e descobrir mais informações sobre a falha:

[![Captura de tela mostra um exemplo de relatório, Test Runner Screen.](touch.unit-images/05-sml.png)](touch.unit-images/05.png#lightbox) [![A captura de tela mostra um exemplo de relatório de amostra, testes.](touch.unit-images/06-sml.png)](touch.unit-images/06.png#lightbox) [![Captura de tela mostra um exemplo de relatório, Test screen com status do teste.](touch.unit-images/07-sml.png)](touch.unit-images/07.png#lightbox)

Você também pode examinar a janela Saída do Aplicativo no seu IDE para ver quais testes estão sendo executados e seus status atuais.

## <a name="writing-new-tests"></a>Gravando novos testes

O NUnitLite é uma versão modificada do NUnit denominada projeto [Touch.Unit](https://github.com/xamarin/Touch.Unit). Ele é uma estrutura de teste leve para .NET, com base em ideias em [NUnit](https://nunit.com/) e fornece um subconjunto de seus recursos.
Ele usa o mínimo de recursos e é executada em plataformas de recursos restritos, como aquelas usados no desenvolvimento incorporado e móvel. A API NUnitLite está disponível para você no Xamarin.iOS. Com o esqueleto básico fornecido pelo modelo de teste de unidade, o ponto de entrada principal é o método [Classe assert](xref:NUnit.Framework.Assert).

Além dos métodos de classe assert, a funcionalidade de teste de unidade é dividida nos namespaces a seguir que fazem parte do NUnitLite:

- [NUnit.Framework](xref:NUnit.Framework)
- [NUnit.Constraints](xref:NUnit.Framework.Constraints)
- [NUniteLite.Runner](xref:NUnitLite.Runner)
