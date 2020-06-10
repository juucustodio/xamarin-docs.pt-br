---
Título: "por que o meu Xamarin.Forms . Mapeia o projeto do Android falha com o COMPILETODALVIK de erro de nível superior inesperado? "
MS. Topic: solução de problemas MS. Prod: xamarin MS. AssetID: C0251EB1-F509-47AD-98D6-846AF46425E5 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/25/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Por que o meu Xamarin.Forms . Mapeia o projeto do Android falha com um erro de nível superior inesperado de COMPILETODALVIK?

Esse erro pode ser visto no painel de erro de Visual Studio para Mac ou na janela de saída da compilação do Visual Studio; em projetos do Android usando Xamarin.Forms . Los.

Isso é normalmente resolvido aumentando o tamanho do heap do Java para seu projeto Xamarin. Android. Siga estas etapas para aumentar o tamanho do heap:

## <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse no projeto do Android & abra as opções do projeto.
2. Vá para **Opções do Android-> avançado**
3. Na caixa de texto tamanho do heap do Java, digite 1G.
4. Recompile o projeto.

![Captura de tela das opções de projeto do Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Opções de Build do Android no Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do Android & abra as opções do projeto.
2. Vá para **compilação-> Android Build-> avançado**
3. Na caixa de texto tamanho do heap do Java, digite 1G.
4. Recompile o projeto.  

![Captura de tela das opções de projeto de Visual Studio para Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Opções de Build do Android no Visual Studio para Mac")
