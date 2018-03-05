---
title: Teste de unidade
ms.topic: article
ms.prod: xamarin
ms.assetid: BD959779-3239-79B6-5289-3A9ECDFBD973
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a113a386f1312253fda8913cea3cb608a65e1061
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="unit-testing"></a>Teste de unidade

Este documento descreve como criar testes de unidade para seus projetos do Xamarin.iOS.
Os testes de unidade com o Xamarin.iOS são feitos usando a estrutura Touch.Unit que inclui um executor de teste iOS, bem como uma versão modificada do NUnit chamado [Touch.Unit](https://github.com/xamarin/Touch.Unit) que fornece um conjunto familiar de APIs para gravar testes de unidade.

## <a name="setting-up-a-test-project"></a>Configurando um Projeto de Teste

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para configurar uma estrutura de teste de unidade para seu projeto, tudo o que você precisa fazer é adicionar à solução um projeto do tipo **Projeto de Testes de Unidade iOS**. Faça isso clicando com o botão direito do mouse na sua solução e selecionar **Adicionar > Adicionar Novo Projeto**. Na lista, selecione **iOS > Testes > API Unificado > Projeto de Testes de Unidade iOS** (você pode escolher C# ou F#).

![](touch.unit-images/00.png "Escolha C# ou F#")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para configurar uma estrutura de teste de unidade para seu projeto, tudo o que você precisa fazer é adicionar à solução um projeto do tipo **Projeto de Testes de Unidade iOS**. Faça isso clicando com o botão direito do mouse na sua solução e selecionar **Adicionar > Novo Projeto...**. Na lista, selecione **Visual C# > iOS > Aplicativo de Teste de Unidade (iOS)**.

![](touch.unit-images/00a.png "Aplicativo de Teste de Unidade do iOS")

-----

O item acima criará um projeto básico que contém um programa de execução básico e que faz referência ao novo assembly MonoTouch.NUnitLite, assim seu projeto terá esta aparência:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](touch.unit-images/01.png "O projeto no Gerenciador de Soluções")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](touch.unit-images/01a.png "O projeto no Gerenciador de Soluções")

-----

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

[ ![](touch.unit-images/02.png "A lista de testes registrados")](touch.unit-images/02.png) 

[ ![](touch.unit-images/03.png "Um texto individual")](touch.unit-images/03.png) 

[ ![](touch.unit-images/04.png "Os resultados da execução")](touch.unit-images/04.png)

Você pode executar acessórios de teste individuais selecionando o acessório texto nas exibições aninhadas ou você pode executar todos os seus testes com "Executar tudo". Se você executar o teste padrão, ele deverá incluir um teste aprovado, um não aprovado e um ignorado. Esta é a aparência do relatório e você pode fazer uma busca detalhada diretamente nos testes não aprovados e descobrir mais informações sobre a falha:

[ ![](touch.unit-images/05.png "Um relatório de exemplo")](touch.unit-images/05.png) [ ![](touch.unit-images/06.png "Um relatório de exemplo")](touch.unit-images/06.png) [ ![](touch.unit-images/07.png "Um relatório de exemplo")](touch.unit-images/07.png)

Você também pode examinar a janela Saída do Aplicativo no seu IDE para ver quais testes estão sendo executados e seus status atuais.

## <a name="writing-new-tests"></a>Gravando novos testes

O NUnitLite é uma versão modificada do NUnit denominada projeto [Touch.Unit](https://github.com/xamarin/Touch.Unit). Ele é uma estrutura de teste leve para .NET, com base em ideias em [NUnit](http://nunit.com/) e fornece um subconjunto de seus recursos.
Ele usa o mínimo de recursos e é executada em plataformas de recursos restritos, como aquelas usados no desenvolvimento incorporado e móvel. Você pode [Procurar a API NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/) disponível para você no Xamarin.iOS. Com o esqueleto básico fornecido pelo modelo de teste de unidade, o ponto de entrada principal é o método [Classe assert](https://developer.xamarin.com/api/type/NUnit.Framework.Assert/).

Além dos métodos de classe assert, a funcionalidade de teste de unidade é dividida nos namespaces a seguir que fazem parte do NUnitLite:

-   [NUnit.Framework](https://developer.xamarin.com/api/namespace/NUnit.Framework/)
-   [NUnit.Constraints](https://developer.xamarin.com/api/namespace/NUnit.Framework.Constraints/)
-   [NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/)
-   [NUniteLite.Runner](https://developer.xamarin.com/api/namespace/NUnitLite.Runner/)


O executor de teste de unidade específico do Xamarin.iOS está documentado aqui:

-   [NUnit.UI.TouchRunner](https://developer.xamarin.com/api/type/NUnit.UI.TouchRunner/)
