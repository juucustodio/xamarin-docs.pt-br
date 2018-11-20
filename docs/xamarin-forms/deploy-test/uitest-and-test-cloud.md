---
title: Automatizando teste do Xamarin.Forms com o App Center
description: O componente UITest do Xamarin pode ser usado com o Xamarin.Forms para gravar testes de interface do usuário a serem executados na nuvem em centenas de dispositivos.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: b674db3d-c526-4e31-a9f4-b6d6528ce7a9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/31/2016
ms.openlocfilehash: 215aa88b7e97a67242bd991dde0e66c445b47b0a
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526392"
---
# <a name="automate-xamarinforms-testing-with-app-center"></a>Automatizar teste do Xamarin.Forms com o App Center

_O componente UITest do Xamarin pode ser usado com o Xamarin.Forms para gravar testes de interface do usuário a serem executados na nuvem em centenas de dispositivos._

## <a name="overview"></a>Visão geral

O **[Teste do App Center](/appcenter/test-cloud/)** permite aos desenvolvedores gravar testes de interface do usuário automatizados para aplicativos iOS e Android. Com alguns ajustes secundários, os aplicativos Xamarin.Forms podem ser testados usando o Xamarin.UITest, incluindo o compartilhamento do mesmo código de teste. Esse artigo apresenta dicas específicas para que o Xamarin.UITest opere com o Xamarin.Forms.

Esse guia pressupõe que você tenha familiaridade com o Xamarin.UITest. Os guias a seguir são recomendados para que você obtenha familiaridade com o Xamarin.UITest:

- [Introdução ao Teste do App Center](/appcenter/test-cloud/)
- [Introdução ao UITest](/appcenter/test-cloud/preparing-for-upload/uitest/)

Uma vez que o projeto UITest tenha sido adicionado a uma solução do Xamarin.Forms, as etapas para gravação e execução dos testes em um aplicativo Xamarin.Forms serão as mesmas de um aplicativo Xamarin.Android ou Xamarin.iOS.

## <a name="requirements"></a>Requisitos

Confira [Xamarin.UITest](/appcenter/test-cloud/uitest/) para confirmar se seu projeto está pronto para os testes automatizados da interface do usuário.

## <a name="adding-uitest-support-to-xamarinforms-apps"></a>Adicionando Suporte do UITest nos Aplicativos Xamarin.Forms

O UITest automatiza a interface do usuário ao ativar controles na tela e executando a entrada de qualquer lugar que um usuário normalmente interagiria com o aplicativo. Para habilitar os testes que podem *pressionar um botão* ou *inserir texto em uma caixa*, o código de teste precisará de uma maneira para identificar os controles na tela.

Para habilitar o código UITest para fazer referência aos controles, cada controle precisa de um identificador exclusivo. No Xamarin.Forms, a maneira recomendada para definir esse identificador é usando a propriedade `AutomationId`, conforme mostrado abaixo:

```csharp
var b = new Button {
    Text = "Click me",
    AutomationId = "MyButton"
};
var l = new Label {
    Text = "Hello, Xamarin.Forms!",
    AutomationId = "MyLabel"
};
```

A propriedade `AutomationId` também pode ser definida no XAML:

```xaml
<Button x:Name="b" AutomationId="MyButton" Text="Click me"/>
<Label x:Name="l" AutomationId="MyLabel" Text="Hello, Xamarin.Forms!" />
```

Um único `AutomationId` deve ser adicionado a todos os controles que são necessários para teste (incluindo botões, entradas de texto e rótulos, cujo valor pode precisar ser consultado).

> [!NOTE]
> Observe que um `InvalidOperationException` será lançado se houver uma tentativa de definir a propriedade `AutomationId` de um `Element` mais de uma vez.

### <a name="ios-application-project"></a>Projeto de Aplicativo iOS

Para executar testes no iOS, o [Pacote NuGet do Agente Test Cloud do Xamarin](https://www.nuget.org/packages/Xamarin.TestCloud.Agent/) deve ser adicionado ao projeto. Depois que ele tiver sido adicionado, copie o seguinte código no método `AppDelegate.FinishedLaunching`:

```csharp
#if ENABLE_TEST_CLOUD
// requires Xamarin Test Cloud Agent
Xamarin.Calabash.Start();
#endif
```

O assembly Calabash faz uso de APIs não públicas da Apple, o que fará com que os aplicativos sejam rejeitados pela App Store. No entanto, o vinculador Xamarin.iOS removerá o assembly Calabash da IPA final se ela não tiver sido referenciada de forma explícita do código.

> [!NOTE]
> Os builds da versão não têm a variável do compilador `ENABLE_TEST_CLOUD`, que fará com que o assembly Calabash seja removido do lote de aplicativo. No entanto, os builds da depuração têm a diretiva de compilador definida, impedindo que o vinculador remova o assembly.

A captura de tela a seguir mostra a variável do compilador `ENABLE_TEST_CLOUD` definida para os builds da depuração:

::: zone pivot="windows"

![](uitest-and-test-cloud-images/12-compiler-directive-vs.png "Opções de build")

::: zone-end
::: zone pivot="macos"

![](uitest-and-test-cloud-images/11-compiler-directive-xs.png "Opções do compilador")

::: zone-end

### <a name="android-application-project"></a>Projeto de Aplicativo Android

Ao contrário do iOS, os projetos do Android não precisam de nenhum código de inicialização especial.

## <a name="writing-uitests"></a>Gravando UITests

Para obter informações sobre como gravar UITests, consulte a [Documentação do UITest](/appcenter/test-cloud/preparing-for-upload/uitest/). As etapas a seguir são um resumo, que descrevem especialmente como o [demo do Xamarin.Forms **UsingUITest** ](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/) é criado.

### <a name="use-automationid-in-the-xamarinforms-ui"></a>Usar o AutomationId na interface do usuário do Xamarin.Forms

Antes que quaisquer UITests possam ser gravados, a interface do usuário do aplicativo Xamarin.Forms deve ser passível de script. Certifique-se de que todos os controles na interface do usuário tenham um `AutomationId` para que ele possa ser referenciado no código de teste.

#### <a name="referring-to-the-automationid-in-uitests"></a>Consultando a AutomationId nos UITests

Ao gravar UITests, o valor de `AutomationId` é exposto de forma diferente em cada plataforma:

- O **iOS** usa o campo `id`.
- O **Android** usa o campo `label`.

Para gravar UITests de plataforma cruzada que encontrarão o `AutomationId` tanto no iOS quanto no Android, use a consulta de teste `Marked`:

```csharp
app.Query(c=>c.Marked("MyButton"))
```

A forma mais curta `app.Query("MyButton")` também funciona.

### <a name="adding-a-uitest-project-to-an-existing-solution"></a>Adicionando um Projeto UITest a uma Solução Existente

::: zone pivot="windows"

O Visual Studio tem um modelo para ajudar a adicionar um projeto Xamarin.UITest em uma solução do Xamarin.Forms existente:

1. Clique com o botão direito do mouse na solução e selecione **Arquivo > Novo Projeto**.
1. Dos modelos do **Visual C#**, selecione a categoria **Teste**. Selecione o modelo **Aplicativo de Teste da Interface do Usuário > Plataforma Cruzada**:

    ![Adicionar Novo Projeto](uitest-and-test-cloud-images/08-new-uitest-project-vs.png "Adicionar Novo Projeto")

    Isso adicionará um novo projeto com os pacotes NuGet **NUnit**, **Xamarin.UITest** e **NUnitTestAdapter** na solução:

    ![Gerenciador de Pacotes NuGet](uitest-and-test-cloud-images/09-new-uitest-project-xs.png "Gerenciador de Pacotes NuGet")

    O **NUnitTestAdapter** é um executor de teste de terceiros que permite que o Visual Studio execute os testes NUnit do Visual Studio.

    O novo projeto também tem duas classes. O **AppInitializer** contém o código para auxiliar os testes de inicialização e instalação. A outra classe, **Testes**, contém o código clichê para ajudar a iniciar o UITests.

1. Adicione uma referência de projeto do projeto UITest ao projeto Xamarin.Android:

    ![Gerenciador de Referências de Projeto](uitest-and-test-cloud-images/10-test-apps-vs.png "Gerenciador de Referências de Projeto")

    Isso permitirá que o **NUnitTestAdapter** execute os UITests no aplicativo Android do Visual Studio.

::: zone-end
::: zone pivot="macos"

É possível adicionar manualmente um novo projeto do Xamarin.UITest a uma solução existente:

1. Comece ao adicionar um novo projeto selecionando a solução e, em seguida, clicando em **Arquivo > Adicionar novo projeto**. Na caixa de diálogo **Novo Projeto**, selecione **Plataforma cruzada > Testes > Xamarin Test Cloud > Aplicativo de teste da interface do usuário**:

    ![Escolha um Modelo](uitest-and-test-cloud-images/02-new-uitest-project-xs.png "Escolha um Modelo")

    Isso adicionará um novo projeto que já tenha os pacotes NuGet **NUnit** e **Xamarin.UITest** na solução:

    ![Pacotes do NuGet de UITest do Xamarin](uitest-and-test-cloud-images/03-new-uitest-project-xs.png "Pacotes do NuGet de UITest do Xamarin")

    O novo projeto também tem duas classes. O **AppInitializer** contém o código para auxiliar os testes de inicialização e instalação. A outra classe, **Testes**, contém o código clichê para ajudar a iniciar o UITests.

1. Selecione **Exibição > Painéis > Testes de Unidade** para exibir o painel Teste de Unidade. Expanda **UsingUITest > UsingUITest.UITests > Aplicativos de Teste**:

    ![Painel de Teste de Unidade](uitest-and-test-cloud-images/04-unit-test-pad-xs.png "Painel de Teste de Unidade")

1. Clique com o botão direito do mouse em **Aplicativos de Teste**, clique em **Adicionar Projeto de Aplicativo** e selecione os projetos do iOS e do Android na caixa de diálogo que aparece:

    ![Caixa de Diálogo de Aplicativos de Teste](uitest-and-test-cloud-images/05-add-test-apps-xs.png "Caixa de Diálogo de Aplicativos de Teste")

    O painel **Teste de Unidade** agora deve ter uma referência aos projetos do Android e do iOS. Isso permitirá que o executor de teste do Visual Studio para Mac execute os UITests localmente em relação aos dois projetos do Xamarin.Forms.

#### <a name="adding-uitest-to-the-ios-app"></a>Adicionando o UITest ao Aplicativo iOS

Há algumas alterações adicionais que precisam ser executadas no aplicativo iOS antes que o Xamarin.UITest comece a funcionar:

1. Adicionar o pacote NuGet do **Agente do Xamarin Test Cloud**. Clique com o botão direito do mouse em **Pacotes**, selecione **Adicionar Pacotes**, pesquise no NuGet pelo **Agente do Xamarin Test Cloud** e adicione-o ao projeto Xamarin.iOS:

    ![Adicionar Pacotes do NuGet](uitest-and-test-cloud-images/07-add-test-cloud-agent-xs.png "Adicionar Pacotes do NuGet")

1. Edite o método `FinishedLaunching` da classe **AppDelegate** para inicializar o Agente do Xamarin Test Cloud quando o aplicativo iOS inicia e para definir a propriedade `AutomationId` dos modos de exibição. O método `FinishedLaunching` deve se parecer com o exemplo de código a seguir:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    #if ENABLE_TEST_CLOUD
    Xamarin.Calabash.Start();
    #endif

    global::Xamarin.Forms.Forms.Init();

    LoadApplication(new App());

    return base.FinishedLaunching(app, options);
}
```

::: zone-end

Depois de adicionar o Xamarin.UITest à solução do Xamarin.Forms, é possível criar UITests, executá-los localmente e enviá-los ao Xamarin Test Cloud.

## <a name="summary"></a>Resumo

Os aplicativos do Xamarin.Forms podem ser facilmente testados com o **Xamarin.UITest** usando um mecanismo simples para expor o `AutomationId` como um identificador exclusivo do modo de exibição para a automação de teste. Uma vez que o projeto UITest tenha sido adicionado a uma solução do Xamarin.Forms, as etapas para gravação e execução dos testes em um aplicativo Xamarin.Forms serão as mesmas de um aplicativo Xamarin.Android ou Xamarin.iOS.

Para obter informações sobre como enviar testes para o Teste do App Center, consulte [Submitting UITests](/appcenter/test-cloud/preparing-for-upload/uitest/) (Enviando UITests). Para obter informações sobre o UITest, consulte a [documentação do Teste do App Center](/appcenter/test-cloud/).

## <a name="related-links"></a>Links relacionados

- [UITestSample](https://developer.xamarin.com/samples/xamarin-forms/UsingUITest/)
- [Amostras do Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Teste do App Center](/appcenter/test-cloud/)
- [Xamarin.UITest](/appcenter/test-cloud/uitest/)
- [NUnit](http://www.nunit.org)
